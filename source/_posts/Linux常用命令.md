title: Linux常用命令
date: 2015-08-02 21:05:44
tags:
---
## 重要的快捷键

>连续两个Tab键，在第一个命令后可提示 第二个后是文件补齐

>ctrl + c终止
>ctrl + d退出终端
<!--more-->
```java

	切换终端	alt + ctrl + F1~F7 其中F7是图形界面

	退出当前用户	exit

	Linux系统中大小写敏感

	ls -ltr 根据时间列出文件

	ls -a列出全部的 包括隐藏的
	
	ls -l 列出详细的
	
	pwd显示当前目录路径

	mkdir建立一个目录

	rmdir 删除一个空目录
	
	rm 删除文件 无法删除目录
	
	rm -rf dir1 dir2 删除一个叫做dir1和dir2的目录并同时删除其内容
	
	cp dir1/* .：复制dir1目录下的所有文件到当前工作目录
	cp -a /tmp/dir1 .：复制/tmp/dir1目录到当前工作目录
	
	
	date显示日期和时间

	echo $LANG显示当前语言
	LANG=en_US切换成英文

	cal 显示日历
	cal 2009显示2009年的日历
	cal 10 2009显示相应月份的
	
	bc 进入计算器 quit退出 默认是输出整数，可加scale=3 控制输出小数位数为3

	man + 命令 查询命令的用法	和info类似，但是info需要以info的格式来写才可以
	
	who来查看当前谁在线

	netstat查看网络状态

	sync将数据从内存写入磁盘，防止意外关机丢失数据

	shutdown -t 3600 3600秒后关机
			 -r 在系统服务停掉后重启
			 -h 在系统服务停掉后关机	
	init + 0-6切换执行等级 其中runlevel 3是命令行 5是图形界面
	
	passwd更改密码

 	mv filename1 filename2 //将filename1 改名为filename2
	
	mv qib.tgz ../qib.tgz //移到上一级目录
	cd进入当前用户的目录下

	cat 1.c 或more 1.c //都可以查看里面的内容。

	chmod 命令 权限修改 用法：chmod 一位8进制数 filename。
	eg: chmod u+x filenmame //只想给自己运行，别人只能读
	//u表示文件主人， g 表示文件文件所在组。 o 表示其他人 a代表all;r 表可读，w 表可写，x 表可以运行
	其中 rwx分别为4 2 1

	可以chmod 741 filename 来设置自己组其他人的权限 
	chmod u=rwx,go=rx filename//
	chmod g+x filename //同组的人来执行
	chmod oug+wrx filename //全部的人都有读写执行权限

	改变所有者（chown）和用户组（chgrp）命令 
	chown xiaoming abc：改变abc的所有者为xiaoming 
	chgrp root abc：改变abc所属的组为root 
	chown root ./abc：改变abc这个目录的所有者是root 
	chown ‐R root ./abc：改变abc这个目录及其下面所有的文件和目录的所有者是root 

	su + name切换用户 缺省参数为root
	clear 清屏

	//whoami:确认自己身份

	//whereis:查询命令所在目录以及帮助文档所在目录
 
	//which:查询该命令所在目录(类似whereis)
	grep:文本内容搜索;find:文件或者目录名以及权限属主等匹配搜索

	eg: grep success * 　　 /*查找当前目录下面所有文件里面含有success字符的文件
	
	ps查询进程
	kill 可以杀死某个正在进行或者已经是dest状态的进程
	
	最常用ps aux
	
	history查看命令历史记录
		
	tar -zcvf filename 压缩
	tar -jxvf filename 解压缩

	# find /usr -size +10M 在/usr目录下找出大小超过10MB的文件	

	find /home -mtime +120 在/home目录下找出120天之前被修改过的文件
	
	env，查看环境变量

	LINUX创建用户的命令
	useradd -g test -d /home/test1 -s /etc/bash -m test1
	注解：-g 所属组  -d 家目录  -s 所用的SHELL 
	删除用户命令
	userdel -r test1
	创建密码命令
	passwd

	grep -[acinv] '要查找的字符串' filename 字符串查找

	

echo $PATH这个是查看当前登入用户的path环境变量。如果是临时设置（即下次登入还是和以前一样）你可以在命令行中输入export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin ,如果是想永久保存，进入到/etc/profile，最后添加输入export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin ，保存修改。
	
```


## 其他常用
1. cat：可以显示文件的内容（经常和more搭配使用），或将多个文件合并成一个文件。
2. chgrp：用来改变文件或目录所属的用户组，命令的参数以空格分开的要改变属组的文件列表，文件名支持通配符，如果用户不是该文件的所有者，则不能改变该文件的所属组。
3. chmod：用于改变文件或目录的访问权限，该命令有两种用法：一种是使用图形化的方法，另一种是数字设置法。
4. chown：用来将指定用户或组为特定的所有者。用户可以设置为用户名或用户ID，组可以是组名或组ID。特定的文件是以空格分开的可以改变权限的文件列表，文件名支持通配符。
5. clear：用来清除终端屏幕。
6. cmp：用来比较两个文件的大小。
7. cp：（copy）可以将文件或目录复制到其他目录中，就如同Dos下的copy命令一样，功能非常强大。在使用cp命令时，只需要指定源文件名或目标目录即可。
8. cut：用来移除文件的部分内容。
9. diff：用来找出两个文件的不同之处。
10. du： 用来显示磁盘的剩余空间的大小。
11. file：用来显示文件的类型。
12. find：用来在目录中搜索文件，并执行指定的操作。
13. head：只查看文件的头几行内容，而不必浏览整个文件。
14. ln：可以在文件之间创建链接，实际上是给某个文件指定一个访问它的别名。
15. less：用法与more类似，可以查看超过一屏的文件内容，不同的是less除了可以按空格键向下显示文件外，还可以利用方向键来滚动显示文件，要结束浏览，只要在less的提示符“：”后按Q即可。
16. locate：可用于查找文件，且比find命令的搜索速度快。
17. ls（list）：用来显示当前目录中的文件和子目录列表。
18. mkdir(make directory)：建立子目录。
19. more：用于显示内容超过一屏的文件，为了避免文件内容显示瞬间就消失，可以使用more命令让文件显示满一屏时暂停，在按下任意键的时候继续显示下一屏的内容。
20. rmkdir(remove directory)：用来删除“空”的子目录或无用的目录文件。
21. mv(move)：可以将文件以及目录移到其他位置，或更改文件以及目录的名称。
22. pico：可指定文本的编辑方式。
23. pwd(print working directory)：可显示用户当前所在的目录。
24. rm：用来删除系统中过时或无用的文件，可以删除目录中的文件或目录本身，对于链接文件，原有文件保持不变。
25. sort：将文本文件自动分类。
26. stat：用于显示文件或文件系统的状态。
27. Strings：显示文件中要打印的字符串。
28. tail：输出文件的结尾。
29. touch：改变文件的时间戳。
30. umask：用于启动bash shell。
31. uniq：移除已分类文件中的重复文本行。
32. vi：启动vi文本编辑器。
33. wc：显示文件中字节、词组合文本行的数目。
34. whereis：查找特定目录下的原始程序，二进制程序或用户手册等文件的位置。
35. man：如果用户对某个命令想了解更详细的话，可使用此命令。
36. dd：复制一个文件。
37. df：查看一个文件系统的磁盘空间使用情况。
38. edquoat：设置用户与用户组的磁盘空间限制，即Windows系统中的配额。
39. fdformat：格式化软盘。
40. fdisk：执行Linux下的磁盘分区。
41. mkfs：建立一个Linux文件系统。
42. mkswap：创建一个Linux交换分区。
43. mount：挂载一个文件系统。
44. quota：限制和显示用户可用的磁盘空间。
45. swapon，swapoff：启用或取消设备和文件的交换页。
46. quotaon，quotaoff：启用或取消quota限制。
47. umount：取消文件系统的装备。 



