# nginx

nginx是一种 web 服务器，应该是目前最常用的 web server。其主要有三大功能：

- 反向代理
- 负载均衡
- 静态资源服务器

接下来详细介绍 nginx 的相关知识点，主要分为安装和配置两部分。在日常使用中最常用的是根据实际需求进行 nginx 的配置工作，因此相关的配置内容会重点进行介绍。另外也会给出相应的源码分析的介绍，这里引用淘宝团队的内容。

## 安装


## 配置

## 源码分析


django是**多线程**服务器。

在 linux 中安装c源程序的步骤：

1. ./config [配置选项]
2. make
3. sudo make install

查看当前的 nginx 是否启动成功；

```shell
sudo netstat -apn | grep 80
```

```shell
# 查看两个文件有差别的地方
diff a.txt b.txt

# Linux一个进程能同时打开的最大数目的socket描述符
cat /proc/sys/fs/file-max
```

nginx在重启时是属于平滑重启，不会对当前的业务产生影响。