#Put this source to 'package/yourfolder' folder of OpenWRT/LEDE SDK
#Build(make menuconfig, make defconfig, make)

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=mlnx-ofed
PKG_VERSION:=5.8-6.0.4.2
PKG_RELEASE:=1

# kernel 5.x = use 5.x ¿
ifeq ($(word 1,$(subst .,$(space),$(LINUX_VERSION))),5)
	PKG_VERSION:=5.8-6.0.4.2
	PKG_HASH:=070c0f1b936681e21df2ac7be1ea490fcbc9adbc04b4b616fc74f322bf1aee90
# kernel 6.x = use 24.04 ¿ (uuu24.04 = 6.8)
else ifeq ($(word 1,$(subst .,$(space),$(LINUX_VERSION))),6)
	PKG_VERSION:=24.10-1.1.4.0
	PKG_HASH:=07dd7e856c94d8f3f33d1cfb357c28fde9e3f4326994ea93fbee32c6dfa59060
endif

# https://network.nvidia.com/products/infiniband-drivers/linux/mlnx_ofed/
# https://content.mellanox.com/ofed/MLNX_OFED-24.04-0.7.0.0/MLNX_OFED_SRC-debian-24.04-0.7.0.0.tgz
# PS: There is no source code in 
# https://www.mellanox.com/downloads/ofed/MLNX_OFED-5.8-5.1.1.2/MLNX_OFED_SRC-debian-5.8-5.1.1.2.tgz

PKG_SOURCE:=MLNX_OFED_SRC-debian-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://www.mellanox.com/downloads/ofed/MLNX_OFED-$(PKG_VERSION)/

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define KernelPackage/mlnx-ofed
	TITLE:=Mellanox ConnectX(R) mlx5 vpi Network Driver
	SUBMENU:=Network Devices
	VERSION:=$(LINUX_VERSION)+$(PKG_VERSION)-$(BOARD)-$(PKG_RELEASE)
	FILES:= `find $(PKG_BUILD_DIR)/ \( -name "*.ko" -o -name "*.ko.gz" \)`
	DEPENDS:=@PCI_SUPPORT +kmod-ptp
	CONFLICTS:=kmod-mlxfw kmod-mlx4 kmod-mlx4-core kmod-mlx5 kmod-mlx5-core \
		kmod-mlxsw-core kmod-mlxsw-i2c kmod-mlxsw-minimal kmod-mlxsw-pci kmod-mlxsw-spectrum	
	KCONFIG:= CONFIG_MLX5_CORE \
		CONFIG_MLX5_CORE_EN=y \
		CONFIG_MLX5_CORE_EN_DCB=n \
		CONFIG_MLX5_EN_ARFS=n \
		CONFIG_MLX5_EN_IPSEC=n \
		CONFIG_MLX5_EN_RXNFC=y \
		CONFIG_MLX5_EN_TLS=n \
		CONFIG_MLX5_ESWITCH=n \
		CONFIG_MLX5_FPGA=n \
		CONFIG_MLX5_FPGA_IPSEC=n \
		CONFIG_MLX5_FPGA_TLS=n \
		CONFIG_MLX5_MPFS=y \
		CONFIG_MLX5_SW_STEERING=n \
		CONFIG_MLX5_TC_CT=n \
		CONFIG_MLX5_TLS=n \
		CONFIG_INFINIBAND=m \
		CONFIG_INFINIBAND_IPOIB=m \
		CONFIG_INFINIBAND_IPOIB_CM=y \
		CONFIG_INFINIBAND_SRP=m \
		CONFIG_INFINIBAND_USER_MAD=m \
		CONFIG_INFINIBAND_USER_ACCESS=m \
		CONFIG_INFINIBAND_USER_ACCESS_UCM=m \
		CONFIG_INFINIBAND_ADDR_TRANS=y \
		CONFIG_INFINIBAND_USER_MEM=y \
		CONFIG_MLX5_ACCEL=y \
		CONFIG_MLX5_IPSEC=n \
		CONFIG_MLX5_CORE=m \
		CONFIG_MLX5_CLS_ACT=y \
		CONFIG_MLX5_TC_SAMPLE=y \
		CONFIG_MLX5_CORE_IPOIB=y \
		CONFIG_MLX5_EN_SPECIAL_SQ=y \
		CONFIG_MLX5_INFINIBAND=m \
		CONFIG_MLX5_DEBUG=y \
		CONFIG_BACKPORT_LRO=m \
		CONFIG_AUXILIARY_BUS=m \
		CONFIG_INFINIBAND_IPOIB_DEBUG=y \
		CONFIG_SCSI_SRP_ATTRS=m \
		CONFIG_INFINIBAND_ON_DEMAND_PAGING=y
	AUTOLOAD:=$(call AutoLoad,35,mlx5_core mlx4_core ib_ipoib)
	#AUTOLOAD:=$(call AutoProbe,35,scsi_transport_srp.ko mlx4_ib.ko efa.ko mlx5_ib.ko iw_cm.ko ib_core.ko ib_cm.ko rdma_cm.ko rdma_ucm.ko ib_ucm.ko ib_umad.ko ib_uverbs.ko ib_ipoib.ko ib_srp.ko ib_iser.ko mlx4_en.ko mlx4_core.ko mlx5_core.ko mlx_compat.ko)
endef

define Package/mlnx-ofed/description
	This package contains a OFED driver for Mellanox CX3 and above chipsets.
endef


mlnx_CFGOPTS= --kernel-version=$(LINUX_VERSION) \
	--with-linux=$(LINUX_DIR) \
	--kernel-sources=$(LINUX_DIR) \
	--with-core-mod --with-user_mad-mod --with-user_access-mod --with-addr_trans-mod --with-mlx5-mod --with-ipoib-mod --with-srp-mod --with-mlx4_en-mod --with-mlx4-mod

mlnx_MAKEOPTS= PATH="$(TARGET_PATH)" \
	ARCH="$(LINUX_KARCH)" \
	CROSS_COMPILE="$(TARGET_CROSS)" \
	TARGET="$(HAL_TARGET)" \
	TOOLPREFIX="$(KERNEL_CROSS)" \
	TOOLPATH="$(KERNEL_CROSS)" \
	LDOPTS=" " \
	DOMULTI=1

define Build/Prepare
	tar -xzf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR)
	$(TAR) -zxf $(PKG_BUILD_DIR)/MLNX_OFED_SRC*/SOURCES/mlnx-ofed-kernel_*.tar.gz -C $(PKG_BUILD_DIR)
endef

# it's cost about 10m
define Build/Configure
	cd $(PKG_BUILD_DIR)/mlnx-ofed-kernel* && ./configure $(mlnx_CFGOPTS)
endef

define Build/Compile
	cd $(PKG_BUILD_DIR)/mlnx-ofed-kernel* && $(MAKE) $(mlnx_MAKEOPTS) -j4 kernel
endef

$(eval $(call KernelPackage,mlnx-ofed))
