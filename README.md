# 原仓库说明位于最下方

1. 合并了递归仓库, 无需使用递归git子仓库`git submodule update --init --recursive`

2. 原仓库链接 [lv_port_linux_frame_buffer](https://github.com/lvgl/lv_port_linux_frame_buffer)

3. 原仓库子仓库链接:

    - [lv_drivers @ 4e26](https://github.com/littlevgl/lv_drivers/tree/4e26c37e7ba7518c4f48cabf31c53aeeace35b7d)
    - [lv_examples @ 0ef71b6](https://github.com/littlevgl/lv_examples/tree/0ef71b6a5efd4a4a8050e3b4b16f203480abc383)
    - [lvgl @ 46376a4](https://github.com/littlevgl/lvgl/tree/46376a4a1181b3d8b2eb943887c060b54afbf4f0)

4. 添加`dosc`文件夹,记录移植过程

----

# LVGL for frame buffer device

LVGL configured to work with /dev/fb0 on Linux.

~~When cloning this repository, also make sure to download submodules (`git submodule update --init --recursive`) otherwise you will be missing key components.~~

Check out this blog post for a step by step tutorial:
https://blog.lvgl.io/2018-01-03/linux_fb