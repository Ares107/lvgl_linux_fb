# **lvgl开发环境搭建**

> 主体环境 :Ubuntu 1804 LTS  
> 下位机: 三星S5p6818

----

## **1.下载仓库**

获取[该仓库](https://github.com/lvgl/lv_port_linux_frame_buffer),解压到上位机目录,根据README.md文档下载子模块.

```bash
cd lv_port_linux_frame_buffer/
git submodule update --init --recursive.
```

## **2.修改平台相关配置**

- 使用linux下freambuffer设备 ```#define USE_FBDEV 1```  
- 加入input设备,触摸屏 ```#define USE_EVDEV 1```  
- 修改触摸屏设备文件 ```#  define EVDEV_NAME   "/dev/input/event1"``` (按理来说应该是0,不知道驱动厂商咋安排的,为什么整成1)

## **3.使用开发板运行一个demo**

### **1. 选择一个demo**

修改```lv_ex_conf.h```, 使用wigetdemo. ```#define LV_USE_DEMO_WIDGETS 1```.

### **2. 修改Makefile**  

修改编译器 ```CC = arm-cortex_a9-linux-gnueabi-gcc```  
加入输出目录 ```OUTDIR = /home/socbis/sansung/rootfs/root```  
在default目标下最后一行添加 ``` mv ${BIN} ${OUTDIR} ```

```makefile
default: $(AOBJS) $(COBJS) $(MAINOBJ)
    $(CC) -o $(BIN) $(MAINOBJ) $(AOBJS) $(COBJS) $(LDFLAGS)
    mv ${BIN} ${OUTDIR}
```

### **3. 修改main.c, 添加触摸屏初始化代码**

修改之后的代码如下:

```c
#include "lvgl/lvgl.h"
#include "lv_drivers/display/fbdev.h"
#include "lv_examples/lv_examples.h"
#include "evdev.h"
#include <unistd.h>
#include <pthread.h>
#include <time.h>
#include <sys/time.h>

#define DISP_BUF_SIZE (80 * LV_HOR_RES_MAX)

lv_indev_t * indev_touchpad;


int main(void)
{
    lv_indev_drv_t indev_drv;
    lv_disp_t * disp;

    /*LittlevGL init*/
    lv_init();

    /*初始化Linux frame buffer设备*/
    fbdev_init();

    /*A small buffer for LittlevGL to draw the screen's content*/
    static lv_color_t buf[DISP_BUF_SIZE];

    /*Initialize a descriptor for the buffer*/
    static lv_disp_buf_t disp_buf;
    lv_disp_buf_init(&disp_buf, buf, NULL, DISP_BUF_SIZE);

    /*Initialize and register a display driver*/
    lv_disp_drv_t disp_drv;
    lv_disp_drv_init(&disp_drv);
    disp_drv.buffer   = &disp_buf;
    disp_drv.flush_cb = fbdev_flush;

    /* 注册显示设备 */
    disp = lv_disp_drv_register(&disp_drv);

    /* 触摸屏初始化 */
    lv_indev_drv_init(&indev_drv);          /*Basic initialization*/
    /*Register a touchpad input device*/
    evdev_init();
    indev_drv.type = LV_INDEV_TYPE_POINTER;
    indev_drv.read_cb = evdev_read;
    /* 为触摸屏绑定显示设备 */
    indev_drv.disp = disp;
    /* 注册触摸屏设备 */
    indev_touchpad = lv_indev_drv_register(&indev_drv);

    /*Create a Demo*/
    lv_demo_widgets();

    /*Handle LitlevGL tasks (tickless mode)*/
    while(1) {
        lv_task_handler();
        usleep(5000);
    }

    return 0;
}
```

make代码,得到demo可执行文件,到开发板执行该文件.

## **3. 安装lvgl在PC端的模拟器**

    查看[linux-lvgl-sim](https://gitee.com/socbis/linux-lvgl-sim)
