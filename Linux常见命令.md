# Linux常见命令

### ls: 文件与目录的查看

```shell
-A ：全部的档案，连同隐藏档，但不包括. 与.. 这两个目录
-d ：仅列出目录本身，而不是列出目录内的档案资料(常用)
-f ：直接列出结果，而不进行排序(ls 预设会以档名排序！)
-F ：根据档案、目录等资讯，给予附加资料结构，例如：
      *:代表可执行档； /:代表目录； =:代表socket 档案； |:代表FIFO 档案；
-h ：将档案容量以人类较易读的方式(例如GB, KB 等等)列出来；
-i ：列出inode 号码，inode 的意义下一章将会介绍；
-l ：长资料串列出，包含档案的属性与权限等等资料；(常用)
-n ：列出UID 与GID 而非使用者与群组的名称(UID与GID会在帐号管理提到！)
-r ：将排序结果反向输出，例如：原本档名由小到大，反向则为由大到小；
-R ：连同子目录内容一起列出来，等于该目录下的所有档案都会显示出来；
-S ：以档案容量大小排序，而不是用档名排序；
-t ：依时间排序，而不是用文件名。
--color=never ：不要依据档案特性给予颜色显示；
--color=always ：显示颜色
--color=auto ：让系统自行依据设定来判断是否给予颜色
--full-time ：以完整时间模式(包含年、月、日、时、分) 输出
--time={atime,ctime} ：输出access 时间或改变权限属性时间(ctime) 
                       而非内容变更时间(modification time)
```

> 1、不带任何参数则列出当前工作目录文件 ls

```shell
[root@localhost usr-bin]# ls
centos          filelist       nginx-1.13.11.tar.gz  redis-check-aof   redis-cli       redis-server  yy     yy1ab   yyy.txt
docker-compose  nginx-1.13.11  redis-benchmark       redis-check-dump  redis-sentinel  shell         yy1aa  yy.tar

```

> 2、列出指定目录文件 ls /bin

```shell
[root@localhost usr-bin]# ls
centos          filelist       nginx-1.13.11.tar.gz  redis-check-aof   redis-cli       redis-server  yy     yy1ab   yyy.txt
docker-compose  nginx-1.13.11  redis-benchmark       redis-check-dump  redis-sentinel  shell         yy1aa  yy.tar

```

> 3、展示所有文件(包含隐藏文件) ls -a

```shell
[root@localhost usr-bin]# ls -a
.   centos          filelist       nginx-1.13.11.tar.gz  redis-check-aof   redis-cli       redis-server  yy     yy1ab   yyy.txt
..  docker-compose  nginx-1.13.11  redis-benchmark       redis-check-dump  redis-sentinel  shell         yy1aa  yy.tar

```

> 4、展示文件详细信息 ls -l(常用)

```shell
[root@localhost usr-bin]# ll
total 16756
drwxr-xr-x.  3 root root       18 Jun 25 09:58 centos
-rwxr-xr-x.  1 root root 12737304 Jul  2 10:40 docker-compose
drwxr-xr-x.  3 root root      100 Jul 26 17:39 filelist
drwxr-xr-x. 16 1001 1001     4096 Mar  2 22:29 nginx-1.13.11
-rw-r--r--.  1 root root  1016189 Apr  3  2018 nginx-1.13.11.tar.gz
-rwxr-xr-x.  1 root root   289952 Jul 19  2020 redis-benchmark
```

> 5、根据修改时间排序 ll -t

```shell
[root@localhost usr-bin]# ll -t 
total 16756
drwxr-xr-x.  5 root root      226 Jul 27 08:44 shell
-rw-r--r--.  1 root root    10240 Jul 26 18:22 yy.tar
drwxr-xr-x.  3 root root      100 Jul 26 17:39 filelist
-rw-r--r--.  1 root root       79 Jul 25 17:32 yy1aa
-rw-r--r--.  1 root root       12 Jul 25 17:32 yy1ab
-rwxr-xr-x.  1 root root 12737304 Jul  2 10:40 docker-compose
drwxr-xr-x.  3 root root       18 Jun 25 09:58 centos
drwxr-xr-x. 16 1001 1001     4096 Mar  2 22:29 nginx-1.13.11
lrwxrwxrwx.  1 root root       18 Feb  4 05:58 yy -> ./filelist/test.sh
lrwxrwxrwx.  1 root root       18 Feb  4 05:56 yyy.txt -> ./filelist/xxx.txt
lrwxrwxrwx.  1 root root       12 Jul 19  2020 redis-sentinel -> redis-server
```

### cp: 复制

```shell
-d ：若来源档为l的属性(link file)，则复制连结档属性而非档案本身；
-f ：为强制(force)的意思，若目标档案已经存在且无法开启，则移除后再尝试一次；
-i ：若目标档(destination)已经存在时，在覆盖时会先询问动作的进行(常用)
-l ：进行硬式连结(hard link)的连结档建立，而非复制档案本身；
-p ：连同档案的属性(权限、用户、时间)一起复制过去，而非使用预设属性(备份常用)；
-r : 递归复制，用于目录的复制操作
-s ：复制成为符号连结档(symbolic link)，亦即『捷径』档案；
-u ：destination 比source 旧才更新destination，或destination 不存在的情况下才复制。
--preserve=all ：除了-p 的权限相关参数外，还加入SELinux 的属性, links, xattr 等也复制了。
最后需要注意的，如果来源档有两个以上，则最后一个目的档一定要是『目录』才行！
```

> 1、 复制单个文件 和 单个文件夹

```shell
# 现有两个文件夹cp_test cp_test1
# cp_test 下的 hello.txt 复制到 cp_test1
# cp [file] [destination]
[root@localhost cp_test]# 
[root@localhost cp_test]# pwd
/root/usr-bin/cp_test
[root@localhost cp_test]# ll
total 4
-rw-r--r--. 1 root root 13 Jul 27 21:21 hello.txt
[root@localhost cp_test]# cp hello.txt ../cp_test1/
[root@localhost cp_test]# cd ../cp_test1/
[root@localhost cp_test1]# pwd
/root/usr-bin/cp_test1
[root@localhost cp_test1]# ll
total 4
-rw-r--r--. 1 root root 13 Jul 27 21:23 hello.txt

# cp -f [dir] [destination]
[root@localhost cp_test]# pwd && ll
/root/usr-bin/cp_test
total 4
drwxr-xr-x. 2 root root  6 Jul 27 21:25 dir1
-rw-r--r--. 1 root root 13 Jul 27 21:21 hello.txt
[root@localhost cp_test]# cp -r dir1/ ../cp_test1/
[root@localhost cp_test]# cd ../cp_test1/ && ll
total 4
drwxr-xr-x. 2 root root  6 Jul 27 21:25 dir1

```

> 2、复制多个文件

```shell
# 同时复制多个文件 
# cp [file1] [file2] .... [destination]

/root/usr-bin/cp_test
[root@localhost cp_test]# touch file1
[root@localhost cp_test]# touch file2
[root@localhost cp_test]# ll
total 4
drwxr-xr-x. 2 root root  6 Jul 27 21:25 dir1
-rw-r--r--. 1 root root  0 Jul 27 21:28 file1
-rw-r--r--. 1 root root  0 Jul 27 21:28 file2
-rw-r--r--. 1 root root 13 Jul 27 21:21 hello.txt
[root@localhost cp_test]# cp file1 file2 ../cp_test1/
[root@localhost cp_test]# cd ../cp_test1/ && ll
total 4
drwxr-xr-x. 2 root root  6 Jul 27 21:25 dir1
-rw-r--r--. 1 root root  0 Jul 27 21:28 file1
-rw-r--r--. 1 root root  0 Jul 27 21:28 file2
-rw-r--r--. 1 root root 13 Jul 27 21:23 hello.txt

# 根据正则去复制多个文件 如果文件名满足一定的前缀 则可以
[root@localhost cp_test]# touch file3
[root@localhost cp_test]# touch file4
[root@localhost cp_test]# cp file* ../cp_test1/  # [root@localhost cp_test]# cp file[0-9] ../cp_test1/也可以
[root@localhost cp_test]# cd ../cp_test1/ && ll
total 4
drwxr-xr-x. 2 root root  6 Jul 27 21:25 dir1
-rw-r--r--. 1 root root  0 Jul 27 21:30 file1
-rw-r--r--. 1 root root  0 Jul 27 21:30 file2
-rw-r--r--. 1 root root  0 Jul 27 21:30 file3
-rw-r--r--. 1 root root  0 Jul 27 21:30 file4
-rw-r--r--. 1 root root 13 Jul 27 21:23 hello.txt
```

> 3、复制文件到多个目的地

```shell
# 需要用到一个 其他的命令 xargs 后文会介绍
# 需要把文件从cp_test 同时复制到 cp_test1 cp_test2
[root@localhost cp_test]# echo ../cp_test1 ../cp_test2 | xargs -p -n 1 cp file* 
cp file1 file2 file3 file4 ../cp_test1 ?...yes
cp file1 file2 file3 file4 ../cp_test2 ?...yes
```



### rm: 删除

```shell
-f ：就是force 的意思，忽略不存在的档案，不会出现警告讯息；
-i ：互动模式，在删除前会询问使用者是否动作
-r ：递回删除啊！最常用在目录的删除了！这是非常危险的选项！！！
```

> 1、强制删除 rm -f / rm -rf

```shell
# 如果不加 -f 则删除的时候会进行询问 
# 强制删除单个文件 rm -f
[root@localhost cp_test1]# ll
total 0
-rw-r--r--. 1 root root 0 Jul 27 21:38 file1
-rw-r--r--. 1 root root 0 Jul 27 21:38 file2
-rw-r--r--. 1 root root 0 Jul 27 21:38 file3
-rw-r--r--. 1 root root 0 Jul 27 21:38 file4
[root@localhost cp_test1]# rm file1 
rm: remove regular empty file ‘file1’? 
[root@localhost cp_test1]# rm -f file1
[root@localhost cp_test1]# ll
total 0
-rw-r--r--. 1 root root 0 Jul 27 21:38 file2
-rw-r--r--. 1 root root 0 Jul 27 21:38 file3
-rw-r--r--. 1 root root 0 Jul 27 21:38 file4
# 强制递归删除单个文件夹 rm -rf
[root@localhost cp_test1]# mkdir dir1
[root@localhost cp_test1]# rm -f dir1/
rm: cannot remove ‘dir1/’: Is a directory
[root@localhost cp_test1]# rm -rf dir1/

```

> 2、删除多个文件 rm [file1] [file2] [filen]

```shell
# 可以通过追加文件名 进行删除
[root@localhost cp_test1]# ll
total 0
-rw-r--r--. 1 root root 0 Jul 27 21:38 file2
-rw-r--r--. 1 root root 0 Jul 27 21:38 file3
-rw-r--r--. 1 root root 0 Jul 27 21:38 file4
[root@localhost cp_test1]# rm -f file2 file3 file4
[root@localhost cp_test1]# ll
total 0

# 也可以通过正则进行删除 
[root@localhost cp_test1]# ll
total 0
-rw-r--r--. 1 root root 0 Jul 27 21:47 file1
-rw-r--r--. 1 root root 0 Jul 27 21:47 file2
-rw-r--r--. 1 root root 0 Jul 27 21:47 file3
-rw-r--r--. 1 root root 0 Jul 27 21:47 file4
[root@localhost cp_test1]# rm -f file*  # rm -f file[0-9]亦可以
[root@localhost cp_test1]# ll
total 0

```

### mv: 文件移动

```shell
-f ：force 强制的意思，如果目标档案已经存在，不会询问而直接覆盖；
-i ：若目标档案(destination) 已经存在时，就会询问是否覆盖！
-u ：若目标档案已经存在，且source 比较新，才会更新(update)
```

> 1、移动文件/文件夹到指定位置 mv [file1] [file2] [file3] [filen] destination

```shell
[root@localhost cp_test]# mv file1 file2 file3 dir1/ ../cp_test1/
[root@localhost cp_test]# cd ../cp_test1 && ll
total 0
drwxr-xr-x. 2 root root 6 Jul 27 21:25 dir1
-rw-r--r--. 1 root root 0 Jul 27 21:28 file1
-rw-r--r--. 1 root root 0 Jul 27 21:28 file2
-rw-r--r--. 1 root root 0 Jul 27 21:30 file3

```



### find:查询

```bash
-amin<分钟>：查找在指定时间曾被存取过的文件或目录，单位以分钟计算；
-anewer<参考文件或目录>：查找其存取时间较指定文件或目录的存取时间更接近现在的文件或目录；
-atime<24小时数>：查找在指定时间曾被存取过的文件或目录，单位以24小时计算；
-cmin<分钟>：查找在指定时间之时被更改过的文件或目录；
-cnewer<参考文件或目录>查找其更改时间较指定文件或目录的更改时间更接近现在的文件或目录；
-ctime<24小时数>：查找在指定时间之时被更改的文件或目录，单位以24小时计算；
-daystart：从本日开始计算时间；
-depth：从指定目录下最深层的子目录开始查找；
-empty：寻找文件大小为0 Byte的文件，或目录下没有任何子目录或文件的空目录；
-exec<执行指令>：假设find指令的回传值为True，就执行该指令；
-false：将find指令的回传值皆设为False；
-fls<列表文件>：此参数的效果和指定“-ls”参数类似，但会把结果保存为指定的列表文件；
-follow：排除符号连接；
-fprint<列表文件>：此参数的效果和指定“-print”参数类似，但会把结果保存成指定的列表文件；
-fprint0<列表文件>：此参数的效果和指定“-print0”参数类似，但会把结果保存成指定的列表文件；
-fprintf<列表文件><输出格式>：此参数的效果和指定“-printf”参数类似，但会把结果保存成指定的列表文件；
-fstype<文件系统类型>：只寻找该文件系统类型下的文件或目录；
-gid<群组识别码>：查找符合指定之群组识别码的文件或目录；
-group<群组名称>：查找符合指定之群组名称的文件或目录；
-help或——help：在线帮助；
-ilname<范本样式>：此参数的效果和指定“-lname”参数类似，但忽略字符大小写的差别；
-iname<范本样式>：此参数的效果和指定“-name”参数类似，但忽略字符大小写的差别；
-inum<inode编号>：查找符合指定的inode编号的文件或目录；
-ipath<范本样式>：此参数的效果和指定“-path”参数类似，但忽略字符大小写的差别；
-iregex<范本样式>：此参数的效果和指定“-regexe”参数类似，但忽略字符大小写的差别；
-links<连接数目>：查找符合指定的硬连接数目的文件或目录；
-iname<范本样式>：指定字符串作为寻找符号连接的范本样式；
-ls：假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出；
-maxdepth<目录层级>：设置最大目录层级；
-mindepth<目录层级>：设置最小目录层级；
-mmin<分钟>：查找在指定时间曾被更改过的文件或目录，单位以分钟计算；
-mount：此参数的效果和指定“-xdev”相同；
-mtime<24小时数>：查找在指定时间曾被更改过的文件或目录，单位以24小时计算；
-name<范本样式>：指定字符串作为寻找文件或目录的范本样式；
-newer<参考文件或目录>：查找其更改时间较指定文件或目录的更改时间更接近现在的文件或目录；
-nogroup：找出不属于本地主机群组识别码的文件或目录；
-noleaf：不去考虑目录至少需拥有两个硬连接存在；
-nouser：找出不属于本地主机用户识别码的文件或目录；
-ok<执行指令>：此参数的效果和指定“-exec”类似，但在执行指令之前会先询问用户，若回答“y”或“Y”，则放弃执行命令；
-path<范本样式>：指定字符串作为寻找目录的范本样式；
-perm<权限数值>：查找符合指定的权限数值的文件或目录；
-print：假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式为每列一个名称，每个名称前皆有“./”字符串；
-print0：假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式为全部的名称皆在同一行；
-printf<输出格式>：假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式可以自行指定；
-prune：不寻找字符串作为寻找文件或目录的范本样式;
-regex<范本样式>：指定字符串作为寻找文件或目录的范本样式；
-size<文件大小>：查找符合指定的文件大小的文件；
-true：将find指令的回传值皆设为True；
-type<文件类型>：只寻找符合指定的文件类型的文件；
-uid<用户识别码>：查找符合指定的用户识别码的文件或目录；
-used<日数>：查找文件或目录被更改之后在指定时间曾被存取过的文件或目录，单位以日计算；
-user<拥有者名称>：查找符和指定的拥有者名称的文件或目录；
-version或——version：显示版本信息；
-xdev：将范围局限在先行的文件系统中；
-xtype<文件类型>：此参数的效果和指定“-type”参数类似，差别在于它针对符号连接检查。
```

> 指定目录下查询.txt结尾的文件 -name

```bash
[root@oss-sw-demo usr-bin]# find /usr/local/bin/ -name  *.txt
/usr/local/bin/filelist/yyy.txt
/usr/local/bin/filelist/file1/yyy.txt
/usr/local/bin/yyy.txt

# 忽略大小写 -iname
[root@oss-sw-demo usr-bin]# find /usr/local/bin/ -iname  *.txt 
/usr/local/bin/filelist/yyy.txt
/usr/local/bin/filelist/file1/yyy.txt
/usr/local/bin/yyy.txt
```

> 当前目录及子目录下 查询.txt结尾和.tar结尾的文件 -name -o

```bash
[root@oss-sw-demo usr-bin]# find . -name "*.txt" -o -name "*.tar"
./filelist/xxx.txt
./yyy.txt
./centos/home/date.txt
./yy.tar
```

> 匹配文件路径或者文件 -path

```bash
[root@oss-sw-demo usr-bin]# find /usr/ -path "*local"
/usr/bin/abrt-action-analyze-ccpp-local
/usr/share/doc/postfix-2.10.1/examples/qmail-local
/usr/share/aclocal
/usr/libexec/postfix/local
/usr/local
```

> 基于正则表达式 -regex  -iregex(忽略大小写)

```bash
[root@oss-sw-demo usr-bin]# find . -regex ".*\(\.txt\|\.tar\)$"
./filelist/xxx.txt
./filelist/yyy.txt
./filelist/file1/xxx.txt
./filelist/file1/yyy.txt
./filelist/file1/baidu.txt
./filelist/file1/date1.txt
./filelist/file1/date2.txt
./filelist/file1/date.txt
./filelist/file1/lll.txt
./filelist/file1/kkk.txt
./yyy.txt
./centos/home/date.txt
./yy.tar
```

> 否定参数 ! 

```bash
# 查找非txt文件
[root@oss-sw-demo usr-bin]# find ! -name "*.txt" 

```

> 根据文件类型查找 -type

```bash
f 普通文件
l 符号连接
d 目录
c 字符设备
b 块设备
s 套接字
p Fifo
# 查找文件类型
[root@oss-sw-demo usr-bin]# find -type d
.
./filelist
./filelist/file1
./nginx-1.13.11
```

> 指定深度 -depth

```bash
# 最大深度 -maxdepth
[root@oss-sw-demo usr-bin]# find -maxdepth 1  -type d 
.
./filelist
./nginx-1.13.11
./centos
./shell
./test
./project
./jdk
./flink-1.12.5

# 最小深度 -mindepth
[root@oss-sw-demo usr-bin]# find -maxdepth 3 -mindepth 2 -type d 
./filelist/file1
./nginx-1.13.11/auto
./nginx-1.13.11/auto/cc
./nginx-1.13.11/auto/lib
./nginx-1.13.11/auto/os
```

> 根据文件时间戳搜索 

```bash
UNIX/Linux文件系统每个文件都有三种时间戳：

访问时间（-atime/天，-amin/分钟）：用户最近一次访问时间。
修改时间（-mtime/天，-mmin/分钟）：文件最后一次修改时间。
变化时间（-ctime/天，-cmin/分钟）：文件数据元（例如权限等）最后一次修改时间

# 查询最近20天内访问过的文件
[root@oss-sw-demo usr-bin]# find -type f -atime -20
./nginx-1.13.11/conf/mime.types
./nginx-1.13.11/conf/nginx.conf
./nginx-1.13.11/html/50x.html
./nginx-1.13.11/sbin/nginx
./nginx-1.13.11/logs/nginx.pid

# 查询正好20天前访问的文件
[root@oss-sw-demo usr-bin]# find -maxdepth 2  -type f -atime 20
./shell/healthCheck.sh
./shell/.healthCheck.sh.swp
./project/nohup.out
./project/watchLog
./jdk/jdk-8u181-linux-x64.tar.gz

# 查看超过7天内被访问的文件
[root@oss-sw-demo usr-bin]# find -maxdepth 2  -type f -atime +7
./redis-server
./redis-benchmark
./redis-cli
./redis-check-dump
./redis-check-aof

# 查看访问时间超过10分钟前的文件
[root@oss-sw-demo usr-bin]# find -maxdepth 2  -type f -amin +10
./redis-server
./redis-benchmark
./redis-cli
./redis-check-dump
./redis-check-aof

# 修改时间比yy.tar更近的
[root@oss-sw-demo usr-bin]# find . -maxdepth 1 -newer yy.tar 
.
./filelist
./shell
./yy
./test

```

> 根据文件大小进行查询 -size

```bash
# 查询大于10KB的文件
[root@oss-sw-demo usr-bin]# find -maxdepth 1 -type f -size +10k

# 查询小于10KB的文件
[root@oss-sw-demo usr-bin]# find -maxdepth 1 -type f -size -10k
./yy1ab

# 查询
[root@oss-sw-demo usr-bin]# find -maxdepth 1 -type f -size 10k
./yy.tar
```

> 获取文件最近修改时间 stat -c  %Y [filename]

```bash
[root@oss-sw-demo ~]# stat -c %Y 2021-07-16.log 
1626407221
```



### date(日期)

```bash
%H 小时(以00-23来表示)。 
%I 小时(以01-12来表示)。 
%K 小时(以0-23来表示)。 
%l 小时(以0-12来表示)。 
%M 分钟(以00-59来表示)。 
%P AM或PM。 
%r 时间(含时分秒，小时以12小时AM/PM来表示)。 
%s 总秒数。起算时间为1970-01-01 00:00:00 UTC。 
%S 秒(以本地的惯用法来表示)。 
%T 时间(含时分秒，小时以24小时制来表示)。 
%X 时间(以本地的惯用法来表示)。 
%Z 市区。 
%a 星期的缩写。 
%A 星期的完整名称。 
%b 月份英文名的缩写。 
%B 月份的完整英文名称。 
%c 日期与时间。只输入date指令也会显示同样的结果。 
%d 日期(以01-31来表示)。 
%D 日期(含年月日)。 
%j 该年中的第几天。 
%m 月份(以01-12来表示)。 
%U 该年中的周数。 
%w 该周的天数，0代表周日，1代表周一，异词类推。 
%x 日期(以本地的惯用法来表示)。 
%y 年份(以00-99来表示)。 
%Y 年份(以四位数来表示)。 
%n 在显示时，插入新的一行。 
%t 在显示时，插入tab。 
MM 月份(必要) 
DD 日期(必要) 
hh 小时(必要) 
mm 分钟(必要)
ss 秒(选择性) 
```

> 常见用法

```bash
date +"%Y-%m-%d" 
2015-12-07
```

