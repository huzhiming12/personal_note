---
title: Linux 系统命令
date: 2017-06-20 09:39:24
categories: 操作系统
tags: shell命令
---

### 一 、文件安全与权限

1. #### 文件

   ```shell
   ls - l
   total 145456
   -rwxr-xr-x 1 root root   9338896 Dec 22 21:24 apache-tomcat-7.0.53.zip
   -rwxr-xr-x 1 root root 139444114 Dec 22 21:26 jdk-7u51-linux-i586.tar.gz
   ```

   **total 145456**：表示这个目录中所有文件占用的大小

   **-rwxr-xr-x**：

   **-**：这个位表示文件类型

   其余9个字符分别对应9个权限位，r：可读  w:可写   x:可执行    -：禁止该项操作

   **rwx**：文件属主的权限  前三位

   **r-x**：同组用户权限   中间三位

   **r-x**：其他用户权限  最后三位

   **1**：该文件的硬链接的数目

   **root**：文件的属主

   **root**：文件数组所在的缺省组

   **9338896**：表示文件长度，单位是字节

   **Dec 22 21:24**：文件最后更新时间

   **apache-tomcat-7.0.53.zip**：文件名

2. #### 文件类型

   d：目录

   l：符号链接

   s：套接字文件

   b:块设备文件

   c:字符设备文件

   p:命名管道文件

   -：普通文件

3. #### 改变文件权限

   ##### 符号模式

   ```shell
   chmod [who] operator [permission] filename
   ```

   who的含义：

   * u：文件属主的权限
   * g：同组用户权限
   * o：其他用户权限
   * a：所有用户权限

   operator的含义：

   * +：增加
   * -：取消权限
   * =：设定权限

   permission的含义：

   * r：读
   * w:写
   * x:执行

   ##### 绝对模式

   ```shell
   chmod [mode] file
   ```

   mode含义：Mode中包含3个数， 741

   第一个数代表文件属主的权限   

   第二个数代表文件属主同组用户权限

   第三个数代表其他用户权限

   每个数转换成3位的二进制，如7 转换成111，

   第一位：表示读的权限，0：不可读 1：表示可读

   第二位：表示写的权限    0：不可写  1：可写

   第三位：表示执行权限    0：不可执行  1：可执行

   ```shell
   chmod 741 code
   #执行之后权限 7->111:可读 可写 可执行
   #			4->100:可读 不可写 不可执行
   #           1->001:不可读  不可写 可执行
   -rwxr----x 1 root root 0 Jun 20 10:40 code
   ```

   可同过 -R选项连同子目录下的文件一起设置权限

   ```shell
   chmod -R 741 /root/test/*
   ```

   ​

4. #### 更改文件所属用户chown,更改用户所属组chgrp

   ```shell
   chown user filename
   ```

   ```shell
   chgrp group filename
   ```

5. 符号链接

   有两种不同的链接，软连接和硬链接。我们知道文件都有文件名与数据，这在 Linux 上被分成两个部分：用户数据 (user data) 与元数据 (metadata)。用户数据，即文件数据块 (data block)，数据块是记录文件真实内容的地方；而元数据则是文件的附加属性，如文件大小、创建时间、所有者等信息。在 Linux 中，元数据中的 inode 号（inode 是文件元数据的一部分但其并不包含文件名，inode 号即索引节点号）才是文件的唯一标识而非文件名。文件名仅是为了方便人们的记忆和使用，系统或程序通过 inode 号寻找正确的文件数据块。

   * 硬链接： 与普通文件没什么不同，`inode` 都指向同一个文件在硬盘中的区块
   * 软链接： 保存了其代表的文件的绝对路径，是另外一种文件，在硬盘上有独立的区块，访问时替换自身路径。

   ```shell
   #创建硬链接 source_path:真实的文件  target:创建的硬链接
   ln source_path target
   #创建软链接
   ln -s source_path target
   #查看文件属性,包括inode
   ls -li

   total 8
   303106 -rwxr----x 2 root root 55 Jun 21 10:23 code
   303106 -rwxr----x 2 root root 55 Jun 21 10:23 hard.link
   303108 lrwxrwxrwx 1 root root 22 Jun 21 10:31 soft -> /root/apache-tomcat-7/
   303107 lrwxrwxrwx 1 root root  4 Jun 21 10:22 soft.link -> code
   ```

   303106:文件的inode，真实文件code 和硬链接hard.link的iNode是相同的

   软链接文件soft.link的inode与code是不相同的，它指向了code文件

### 二、使用find和xargs

#### find命令选项

```shell
find pathname -option [-print -exec -ok]
```

pathname:表示要查找的目录路径

1. name选项

   ```shell
   # 查找自己根目录下以 .txt结尾的文件
   find ~ -name "*.txt" -print
   ```

   ```shell
   # 查找etc目录下以Host 开头的文件
   find /etc -name "host*" -print
   ```

2. 使用perm选项

   ```
   #查找当前目录下 权限是755的文件
   find . -perm 755 -print
   ```

3. 忽略某个目录 prune

   ```shell
   #在当前目录下找含有 . 的文件，其中忽略 ./app这个文件夹
   find ./  -path ./app -prune -o -name "*.*" -print
   ```

4. 使用user和nouser选项

   ```shell
   #在自己的根目录下找所属用户是Ubuntu的所有文件
   find ~ -user ubuntu -print
   ```

   ```shell
   #在自己的根目录下查找 属主账户被删除的所有文件
   find ~ -nouser -print
   ```

5. 使用group和nogroup选项

   ```shell
   #在当前目录下查找 所属组是root的所有文件
   find ./ -group root -print
   ```

   ```shell
   #在当前目录下查找 所属组被删除的所哟文件
   find ./ -nogroup -print
   ```

6. 按照更改时间查找文件

   ```shell
   #在当前目录下查找最后更改时间在5日以内的文件
   find ./ -mtime -5 -print
   ```

   ```shell
   #在当前目录下查找最后更改时间在3日之前的文件
   find ./ -mtime +3 -print
   ```

7. 使用type选项

   ```shell
   #在当前目录下查找所有目录
   find ./ -type d -print
   ```

   ```shell
   #在当前目录下查找所有除目录以外的文件
   find ./ ! -type d -print
   ```

8. 使用size选项

   文件长度可以有两种衡量方式，一种是一块为单位（一块=512字节），另一种是以字节为单位，表达方式 N c(如1000c，表示1000字节)。

   ```shell
   #在当前目录下查找长度大于1000字节的所有文件
   find ./ -size +1000c
   ```


   ```shell
   # 在当前目录下查找长达小于1000字节的所有文件
   find ./ -size -1000c
   ```

   ```shell
   # 在当前目录下查找文件长度等于1000块的文件(1块=512字节)
   find ./ -size 1000
   ```

9. depth命令，在目录中查找，首先匹配文件，然后再在子目录中查找。

   ```shell
   find ./ -name "*.txt" -depth -print
   ```

10. mount，查找是不进入其他文件系统，只在当前文件系统中查找

   ```shell
   find ./ -name "*.txt" -mount -print
   ```

