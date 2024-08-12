# kmod-mlnx-ofed

`kmod-mlnx-ofed` is an OpenWrt package that integrates [Nvidia's Mellanox OFED drivers](https://network.nvidia.com/support/mlnx-ofed-public-repository/) into OpenWrt.

> This project is currently only focused on kernel module (kmod) support and has been tested with OpenWrt 23.05/snapshot versions and ConnectX-5.

## compile

1. Navigate to the build directory:

    ```sh
    cd build_dir
    ```

2. Clone the package repository:

    ```sh
    git clone https://github.com/makeding/kmod-mlnx-ofed.git package/huggy
    ```

3. Configure the build environment:

    ```sh
    make menuconfig
    ```

4. Enable the package in the configuration menu:
    - Go to `Kernel modules` -> `Network Devices` -> `kmod-mlnx-ofed`.
    - **Important**: Ensure that no other `kmod-mlx*` modules are selected to avoid conflicts.

### Memory Considerations

The build process may causing memory leaks.  
It is recommended to use a single thread or limit the build to `-j4` to prevent excessive memory leak(Only use this after first building without `kmod-mlnx-ofed` without it may be couse file missing)
> For reference, it's ate my 20GB memory and 4GB swap.
```sh
make package/huggy/kmod-mlnx-ofed/compile V=s
```
If the build completes without errors, you can proceed to generate your custom OpenWrt images using:

```sh
make -j
```

## credits

This project is a fork by https://github.com/allegro0132/Openwrt-mlnx-ofed
