# linux 面试问题整理

1. 在 linux 中安装c源程序的步骤：

- ./config [配置选项]
- make
- sudo make install

2. 查看两个文件有差别的地方

```shell
diff a.txt b.txt
```

3. Linux一个进程能同时打开的最大数目的socket描述符

```shell
cat /proc/sys/fs/file-max
```