---
layout: post
title: "Linux 使用记录"
author: "Bin Li"
tags: [Linux, Programming]
comments: true
style: | 
  .container {
        max-width: 44rem;
    } 
published: true
---

本文记录平时常用的 Linux 相关的命令记录。

## 找到对应路径下所有包含关键词的命令

```shell
find /dir/to/root/ -name "keywor*"
```

## 找到某个库装在哪个地方
例如我要找到cuda装在哪儿了

```shell
locate cuda | grep cuda$
```

这里的`$`表示结尾，是正则表达式的部分。

## 更改一个文件夹下所有文件的后缀
替换对应的 oldextension 和 newextension 即可。

```shell
find -L . -type f -name "*.oldextension" -print0 | while IFS= read -r -d '' FNAME; do
    mv -- "$FNAME" "${FNAME%.oldextension}.newextension"
done
```

## 显示特定目录下的文件夹大小

```shell
du -sh /dir/to/root/*
```

一般进到要查的目录下：
```shell
cd /dir/to/root
du -sh */
```



## 解压文件
### .gz 后缀
```
gunzip file.gz
```

如果解压之后需要保留原压缩包，可用：

```
gunzip -k file.gz
```

### .bz2 后缀
```
bzip2 -d file.bz2
```

如果解压之后需要保留原压缩包，可用：

```
bzip2 -dk file.bz2
```

### .tar 后缀


## 命令行
### `uniq`去除重复行
`uniq` 只能过滤掉相邻的重复行，所以要想搞定全部的重复行，就要先 `sort` 一下。
```shell
cat text.txt | sort | uniq
```
#### 列出第一列不重复的个数：
 ```
 awk -F ',' '{print $1}' test1.csv | sort | uniq | wc -l
 ```

### `>` 和 `>>` 重定向
重定向的用一个 greater-than (>)，会替换掉之前的同名文件（如果有的话），两个 greater-than (>) 会在重定向指向的文件后面 append 对应的内容（如果存在同名文件的话）。

```
wc -l uid_stat_test.csv

tail -n 2 uid_stat_test.csv > uid_only_test.csv

head -n -4 uid_stat_test.csv >> uid_stat_test.csv
```

### `axel` vs `curl`
`axel` 下载某链接的东西时如果出现 too many redirections，可以尝试用 `curl`。


## 系统相关
### 检查还剩多少内存
```shell
# Run "free" to see RAM information in KB.
free
# Run "free -m" to see RAM information in MB.
# watch -n 1 free -m
free -m
# Run "free -g" to see RAM information in GB.
free -g
```
或者采用如下的方式：
```
# watch -n 1
cat /proc/meminfo
```
#### Free Up Unused Memory
Command 1:
```shell
sudo sysctl -w vm.drop_caches=3
```
这命令不能加快系统运行速度或提高系统稳定性，只是清空 cache 就好。

Command 2:
```shell
sudo sync && echo 3 | sudo tee /proc/sys/vm/drop_caches
```

### 查看硬件设备
If we are not super-user, we can use `lspci`, otherwise we can use `sudo lshw`.

### 安装软件
#### 离线安装
平时用 apt-get install 装的软件，可以去 [ubuntu packages](https://packages.ubuntu.com) 上对应找到 deb 包下载下来，然后手动安装：
```shell
# -f, --fix-broken
#           Fix; attempt to correct a system with broken dependencies in place.
sudo apt-get install -f
sudo dpkg -i /path/to/deb/file 
```

### awk

### 递归查找目录下特定后缀结尾的文件个数
```shell
find ./ -name "*.jpg" | wc -l
```
### 将某个文件夹下（包括子目录）特定后缀的所有文件都挪到另一个文件夹中
```shell
find ./ -iname '*.jpg' -exec mv '{}' ../JPEGImages/ \;
```

## Shell Programming
### list to dict：
```
dict((el,0) for el in a)
```

## 参数
### Verbose Mode
A verbose mode is an option available in many computer operating systems, including Microsoft Windows, macOS and Linux that provides additional details as to what the computer is doing and what drivers and software it is loading during startup.

verbose 模式就是能够输出比较详细的运行信息。

## 第三方库
### tmux
```
tmux new -s session_name
tmux ls
tmux a -t session_name
tmux kill-session -t myname
```





