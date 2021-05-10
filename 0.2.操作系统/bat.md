## echo

echo：输出

echo off：关闭后续的命令显示

## dir

dir：列出当前目录下的文件

dir /? ： dir命令后可以跟的参数

dir window /h：输出window文件下的隐藏文件

## for

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

## findstr

findstr：在文件中搜索字符串

```txt
--输出data.txt文件中1到9的数字
find /R "[1-9]" data.txt
```

## set 

set：接受一个用户的输入

```txt
set /P x=
echo %x%
```

## goto

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

## mklink

mklink：创建符号链接，以新的文件出现，不是快捷方式，但是改变符号链接原始文件也会改变

## nul

nul：doc中一个设备文件名，表示空设备

```txt
>nul 是屏蔽标准输出在屏幕上的显示，
2>nul 是屏蔽出错显示
>nul 2>nul 是无论对错，都屏蔽屏幕显示
```

## ping

ping 默认4次，可以指定次数

```txt
ping 127.0.0.1 
```

## rd

删除目录

/s：使用此参数删除目录树

/q：

## del

删除文件

