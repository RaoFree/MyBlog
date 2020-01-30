title: Linux常用操作
date: 2018-1-10 19:14:43
---
# 基本操作
1. 修改用户所属组
```
//添加组
groupadd groupName
//为用户指定组
usermod -g groupName userName
//验证显示用户所属组
groups userName
```
 
2. 更改文件/目录属主属组
```
//更改文件属主属组
chown userName:groupName fileName
//递归更改目录属主属组（目录结尾无需/）
chown -R userName:groupName dirName
```
<!--more-->
 
3. 递归更改目录权限
```
chmod -R 775 dirName
```
￼ 
4. 压缩/解压
```
tar -cvf filename.tar dir
tar -zxvf filename.tar -C dir 
```

5. 删除文件
```
//除指定文件外，全部删除
rm -rf !()
ls |grep -v [filename] |xargs rm -rf
//当目录下文件过多，使用以下方式删除
find . -name “*” -print|xargs rm -rf
```

6. 统计当前目录下的文件个数
```
//含子文件夹
ls -lR|grep “^-“|wc -l
//不含子文件夹
ls -l|grep “^-“|wc -l
```

7. 统计当前目录下的文件夹个数
```
ls -l|grep “^d”|wc -l
```

# 文件操作 
1. 统计文件行数
```
wc -l filename
```

2. 删除文件行
```
//1-10行
sed -i ‘1,10d’ filename
//2-9行，不含上下界(1，10)
cut -b -1,10- filename  
//最后一行
sed -i ‘$d’ filename 
```

3. 替换文件中的字符串
```
sed -i ‘s/old/new/g’ filename
```
 
4. 对文件中的指定列（如列1，列2）进行提取
```
cut -d”分隔符” -f1,2 sourcefile >> directfile
```
 
5. 按行切分文件
```
split -l linecount filename
```
 
6. 文件行级去重
```
//统计文件中每行内容的重复次数
sort filename |uniq -c 
//去除文件中的重复行
awk ‘!x[$0]++’ beforeFilename>>afterFilename
```

7. 文件格式（dos/unix）转换
```
vi filename
:set fileformat=unix
:set fileformat=dos
```
 
8. 后台执行脚本并将结果输出至文件
```
//执行后控制台将显示进程号
nohup ./filename >>resultFilename &
//确认该进程是否正在后台运行
ps aux|grep pid
```

# 机器信息类操作
1. 查看ip
```
ip addr
```

2.  查看操作系统版本
```
cat /etc/issue
```

3. 查看内存使用情况
```
cat /proc/meminfo
```

4. 查看当前目录占用空间
```
//详细信息
du -a
//按分区
du -h
//总占用空间
du -s
```

5. 查看占用端口的进程号（最后一列）
```
netstat -nlp|grep port
```

6. 查看使用某文件的进程号
```
lsof|grep filename
fuser -m -v filename
```

7. 永久修改ip
```
vim /etc/sysconfig/network/ifcfg-eth-id-XXX
修改IPADDR项的值
重启机器生效
```

8. 永久修改主机名
```
vim /etc/hosts
vim /etc/HOSTNAME
重启生效
```
 
9. 永久修改dns
```
vim /etc/resolv.conf
￼修改nameserver项的值
立即生效
```

10. 配置NAS开机自动挂载
```
vim /etc/fstab
NAS   挂载目录   文件系统   defaults   0   0
```