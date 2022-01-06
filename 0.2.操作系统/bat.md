# 一：常见命令

echo

echo：输出

echo off：关闭后续的命令显示

dir

dir：列出当前目录下的文件

dir /? ： dir命令后可以跟的参数

dir window /h：输出window文件下的隐藏文件

for

for循环

for /?：显示可以跟的参数

```txt
在cmd窗口中 ：
for %I in (command1) do command2
在批处理文件中：
for %%I in (command1) do command2
```

批处理文件：

```txt
--不显示输出
@echo off
-- 匹配(*.*)匹配到的放到%%i中然后执行 echo 直接输出
for %%i in (*.*) do echo %%i
--暂停
pause
```

/D：显示目录，不显示文件

```txt
for /d %%i in (*.*) do echo %%i
```

/R：递归显示所有的子目录

/L：类似编程里的for循环

```txt
--从1开始，步长为2，一直到9 取出输出
for /l  %%i in (1,2,9) do echo %%i
```

/F：对文件里的变量字符串进行操作

```txt
--输出a.txt的每一行
for /f  %%i in (a.txt) do echo %%i
```

findstr

findstr：在文件中搜索字符串

```txt
--输出data.txt文件中1到9的数字
find /R "[1-9]" data.txt
```

set 

set：接受一个用户的输入

```txt
set /P x=
echo %x%
```

goto

跳转到某一段

```txt
set /P x=
echo %x%
if %x%=a goto aaa
if %x%=b goto bbb

:aaa
echo 运行到了:aaa
goto end

:bbb
echo 运行到了:bbb
goto end

:end
pause
```

mklink

mklink：创建符号链接，以新的文件出现，不是快捷方式，但是改变符号链接原始文件也会改变

nul

nul：doc中一个设备文件名，表示空设备

```txt
>nul 是屏蔽标准输出在屏幕上的显示，
2>nul 是屏蔽出错显示
>nul 2>nul 是无论对错，都屏蔽屏幕显示
```

ping

ping 默认4次，可以指定次数

```txt
ping 127.0.0.1 
```

rd

删除目录

/s：使用此参数删除目录树

/q：

del

删除文件

rem

注释命令；

::也具有rem的功能；

pause

暂停命令

set：显示设置或删除变量



# 二：常见符号

@回显屏蔽：表示不显示@后的命令

` >; ` 与 ` >> ` 重定向：将信息重定向到指定设备或文件，系统默认输出到显示器；

```bat
echo aaaa > a.txt
```

` <  ` 重定向：将信息来源重定向为指定的设备或文件；

管道符号 ` | ` : 将管道符号前面命令的输出结果重定向到管道符号后边的命令中去；作为后边命令的输入；

` ^ ` 转义符：



# 三：命令释义

### 文件夹管理：

md创建目录。

rd 删除一个目录。

dir显示目录中的文件和子目录列表。

tree 以图形显示驱动器或路径的文件夹结构。

path 为可执行文件显示或设置一个搜索路径。

copy复制文件和目录树。

### 文件管理

type 显示文本文件的内容。

copy将一份或多份文件复制到另一个位置。

de1删除一个或数个文件。

move移动文件并重命名文件和目录。(Windows XP Home Edition中没有)ren重命名文件。

replace替换文件。

attrib 显示或更改文件属性。

find搜索字符串。

fc比较两个文件或两个文件集并显示它们之间的不同

### 网络命令

ping进行网络连接测试、名称解析ftp文件传输

net 网络命令集及用户管理telnet远程登陆

ipconfig显示、修改TCP/IP设置msg 给用户发送消息

arp显示、修改局域网的IP地址-物理地址映射列表

### 系统管理

at安排在特定日期和时间运行命令和程序

shutdown立即或定时关机或重启

tskill结束进程

taskkill结束进程（比tskil1高级，但WinXPHome版中无该命令)tasklist显示进程列表（Windows XP Home Edition中没有)

sc系统服务设置与控制

reg 注册表控制台工具

powercfg控制系统上的电源设置

