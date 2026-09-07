# CMake 与工具链

## 工具链

项目使用 `arm-none-eabi-gcc`，目标参数：

```text
-mcpu=cortex-m4
-mthumb
-mfloat-abi=hard
-mfpu=fpv4-sp-d16
```

宏定义包括 `STM32F407xx`、`USE_HAL_DRIVER`、`ARM_MATH_CM4`。

## 源文件

CMake 使用 `GLOB_RECURSE` 自动加入：

- `Drivers/*.c`
- `Src/*.c`
- `Middlewares/*.c`
- `bsp/*.c`
- `modules/*.c`
- `application/*.c`

优点是新增 `.c` 不必手动列出；缺点是空模块、实验模块和暂时不用的源码也会参与编译，例如新版 BMI088。

## 头文件

CMake 递归把各层所有子目录加入 include path。这很方便，但如果不同目录存在同名头文件，最终选择可能依赖 include 顺序，应尽量避免重名。

## Debug 配置

当前默认：

```text
-Og -g -gdwarf-2
-Wall -Werror
```

`-Werror` 把警告当错误，因此未使用变量也会让构建失败。保持零警告是好习惯，但实验/占位源码必须及时清理或不加入目标。

## 链接

- 链接脚本：`STM32F407IGHx_FLASH.ld`；
- 使用 nano/newlib-nosys；
- 启用 section garbage collection 和 LTO；
- 链接预编译 CMSIS-DSP 库；
- 构建后用 objcopy 生成 HEX/BIN，并用 size 显示内存占用。

## 两套构建系统

仓库还保留 CubeMX 风格 Makefile。开发时最好明确只维护一套主构建配置，避免“Makefile 能编译、CMake 不能”或源文件列表不一致。

