# 构建与调试

## 文件

- [CMake 与工具链](01-CMake与工具链.md)
- [常见编译错误](02-常见编译错误.md)
- [阅读和调试方法](03-阅读和调试方法.md)

项目目标是生成：

```text
basic_framework.elf
basic_framework.hex
basic_framework.bin
basic_framework.map
```

ELF 用于调试，HEX/BIN 用于烧录，MAP 用于查看符号和内存分布。

