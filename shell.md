# ls:

ls -F：区分文件和目录

ls -a：显示隐藏文件

ls -R：显示目录下和子目录下的文件

ls -l：显示详细信息

ls -l 正则表达式：进行过滤

ls -l --time=atime:显示访问时间

ls -i:查看目录或文件的inode(唯一标识)

第一个位置表示：

![image-20220318205709169](shell/image-20220318205709169.png)

三组三字符表示：

![image-20220318205744323](shell/image-20220318205744323.png)



# touch：

可新建一个空文件；可更改文件的修改时间但不改变内容

# cp：

cp -i: 当目录下已有文件时，shell询问是否覆盖

# file：查看文件类型

# cat、more、less：

cat -n ：加行号

cat -b：只给有文本的行加行号

# basename

```
basename /usr/local/myshell/test.sh 
#output test.sh
```

# tail、head：

tail:默认查看文件末10行

tail -n 20:查看末20行

tail -f：监控文件

head：默认前十行

head -5:前5行

# PS：动态监控进程

# df -h：查看磁盘空间

# du：显示单个目录下磁盘使用

-h：用户易读

-c：显示所列出的所有文件的总大小

-s显示每个输出参数的总计

# sort：

![image-20220311204507060](shell/image-20220311204507060.png)

# grep：搜索数据

# tar：

![image-20220311214309981](shell/image-20220311214309981.png)

![image-20220311214317141](shell/image-20220311214317141.png)

# chmod：改变权限

chmod options mode file

mode可以是八进制模式也可以是字符模式

chmod 777 filename

![image-20220318211344469](shell/image-20220318211344469.png)

 字符模式：	chmod o+r filename

![image-20220318214524828](shell/image-20220318214524828.png)

u代表用户、g代表组、o代表其他、a代表上述所有

+-=：在原有权限基础上 增加、移除、设置成后面的值

![image-20220318214743871](shell/image-20220318214743871.png)

# chown：改变所属关系

chown options owner[.group] file  

chgrp 组 filename：改变属组

# VIM：

ctrl+f：上翻

ctrl+b：下翻

![image-20220318222317600](shell/image-20220318222317600.png)

# Shell脚本：

## 命令替换：在shell中获取命令输出

反引号：a=\`date\`

$() : a=$(date)

## 重定向：

输出重定向：

\>:将输出覆盖到文件

\>>：将输出追加到文件

输入重定向

\<：将文件内容给命令

\<<：内联输入重定向

```shell
#需要指定起止字符，如flag
   cat << flag
   666
6  66
6  66
flag
```

## $[ operation ]：数学运算

## bc：浮点运算

先设置小数点位数 scale=4，然后表达式

```shell
#!/bin/bash
var1=100
var2=45
var3=$(echo "scale=4; $var1/$var2" | bc)
echo $var3
```

```shell
variable=$(bc << EOF
options
statements
expressions
EOF
)
var5=$(bc << EOF
scale = 4
a1 = ( $var1 * $var2)
b1 = ($var3 * $var4)
a1 + b1
EOF
)
```



## if-then-else:

if后是命令，当命令退出码为0时执行

```shell
#两种方式
if command
then
	commands
else
	commands
fi


if command; then
	commands
elif commands
then
	commands
else
	commands
fi
```

### test:

conditions是一系列参数和值，如果条件成立，返回状态码0

如果test后面不写，则返回非零状态码

```
test conditions

if test conditions
then
if
```

### if [空格condition空格]:

#### 数值比较：

![image-20220324152520342](shell/image-20220324152520342.png)

#### 字符串比较：

![image-20220324154153388](shell/image-20220324154153388.png)

#### 文件比较：

![image-20220324160947338](shell/image-20220324160947338.png)

### &&，||

[ condition ] && [ condition ]

[ condition ] || [ condition ]

### （（  expression ））：比较过程使用数学表达式

![image-20220324220624973](shell/image-20220324220624973.png)

```shell
#!/bin/bash
val1=10
if (( $val1**2 > 90 ))
then 
	(( val2 = $val1 ** 2))
	echo "The square of $val1 is $val2"
fi
```

### 双方括号【【 expression 】】：

提供了模式匹配和针对字符串的高级特性。

## case

```shell
case variable in
pattern1 | pattern2) commands1;;
pattern3) commands2;;
*) commands;;
esac
```

## for

```shell
for var in list
do
	commands
done
```

```shell
for test in Alabama Alask Arizona Arkansas Clalisonia Colorado
do
	echo The next state is $test
done
#$test 在for循环外可当正常变量使用
#另一种从变量中读取list形式
list="Alabama Alaska Arizona Arkansas Colorado"
list=$list" Connection"
for state in $list

#从命令中读取list
file="states"
for state in $(cat file)
```

```shell
for test in I don't konw if this'll work
#shell会把单引号包起来的字符串看成一个值
#可用反斜杠\或者双引号“”解决
for test in I don\'t konw if "this'll" work

#for循环的list使用空格隔开的，如果单词中有空格，
#可以用双引号或单引号将单词包起来
for test in Nevada 'New Hampshire' "New Mexico" "New York"
```

### 更改字段分隔符

```shell
#默认bash shell分隔符为：空格、制表符、换行符
#可在脚本中   IFS=$'\n' 使得只能识别换行符为分隔符
#还可以指定 IFS=:等为分隔符,
#指定多个分隔符 IFS=$'\n':;"这将换行符，冒号，分号，双引号作为分隔符

file="states"
IFS=$'\n'
for state in $(cat $file)
do
echo "Visit beautiful $state"
done
```

### 用通配符读取文件目录

```shell
for file in /usr/local/*
do
#用双引号是防止出现空格问题，因为linux目录和文件名有空格是可以的
	if [ -d "$file" ];then
		echo "$file is a directory"
	elif [ -f "$file" ]
	then 
		echo "$file is a file"
	fi
done

#还可以多个目录
for file in /usr/local/* /etc/*
```

### C语言风格的for

```shell
#可以不用空格
for ((a=1,b=10; a<=10&&b<=10;a++,b--))
do
	echo "$a - $b"
	b=20
done
```

## while

```shell
#!/bin/bash
var1=10
while [ $var1 -gt 0 ]
do
	echo $var1 
	var1=$[ $var1 - 1 ]
done
```

```shell
#while可以定义多个测试命令，只有最后一个测试命令的
#退出状态码用来决定什么时候结束循环
#!/bin/bash
var1=10
while echo $var1 
#必须要换行
[ $var1 -ge 0 ]
do
	echo "this is inside the loop" 
	var1=$[ $var1 - 1 ]
done
```

## until

直到达到这个条件的时候就停止，用法跟while一样

## 嵌套循环

```shell
for ((a=1;a<=3;a++))
do
	echo "Starting loop $a:"
	for ((b=1;b<=3;b++))
	do
		echo "	Inside loop: $b"
	done
done
```

```
while [ conditions ]
do
	for i in list
	do
		commands
	done
done
```

## 循环处理文件数据

```shell
IFS.OLD=$IFS
IFS=$'\n'
for entry in $(cat /etc/passwd)
do
	echo "Values in $entry -"
	IFS=:
	for value in $entry
	do
		echo "	$value"
	done
done
```

## 控制循环

- break命令
- continue命令

**跳出多层循环**

break n   其中n是想跳出的循环层数

continue n 表示继续第几层的循环

## 处理循环的输出

可以对循环的输出使用管道或进行重定向

```shell
for file in /usr/local/*
do
	if [ -d $file ]
	then
	echo "$file is a directory"
	else
		echo "$file is a file"
	fi
done > output.txt
#for会将结果重定向到文件，而不在屏幕显示
```

# 处理用户输入

## 命令行参数

### 读取参数

位置参数，$0是程序名，$1~$9是参数名。若是给定的参数超过9个，那么使用变量可这样${10},${11}...。 $#是所有传进来的参数个数

**看参数是否为空**

```shell
#!/bin/bash

if [ -n "$1" ]
then 
	echo not non-zero
else
	echo yes! is zero
fi
```

## 特殊参数变量

**想获取最后一个参数，可以这样**

```shell
echo The last parameter was ${!#}
#花括号里只能用！，不能用$
```

$*把所有参数当成一个单词，$@对单独处理每个变量

```shell
echo 
count=1

for param in "$*"
do 
	echo "\$* parameter #$count = $param"
	count=$[$count+1]
done

echo
count=1
for param in "$@"
do
	echo "\$@ parameter #$count = $param"
	count=$[ $count+1 ]
	done
```

## 移动变量

shift命令，默认情况下会将每个参数变量向左移动一个位置。所以位置$3的值移到$2中，$2的值移到$1中，$1的值会被删除。

```
echo
count=1
while [ -n "$1" ]
do
	echo "Parameter #$count = $1"
	count=$[$count+1]
	shift
done
```

也可以指定往前移动几个位置，比如shift 6

## 处理选项

### 处理简单选项

```shell
echo
while [ -n "$1" ]
do
	case "$1" in
		-a) echo "Found the -a option";;
-b) echo "Found the -b option" ;;
-c) echo "Found the -c option" ;;
*) echo "$1 is not an option";;
esac
	shift
	done
#./test.sh -a -b -c -d
```

### 分离参数和选项

```shell
#遇到双破折号（--）时候
echo
while [ -n "$1" ]
do
	case "$1" in
		-a) echo "Found -a";;
-b) echo "Found -b";;
-c) echo "Found -c";;
--) shift
break;;
*) echo "$1 is not an option"
esac
shift
done

count=1
for param in $@
do
	echo "Parameter #$count: $param"
	count=$[$count+1]
done
```

### 处理带值的选项

也是while  case命令，不写了

### 使用getopt命令

```shell
getopt optstring parameters
#格式
```

optstring是命令行有效的选项字符，如果选项需要参数则在这个参数后面加冒号，例如

```shell
getopt ab:cd -a -b test1 -cd test2 test3
#output：-a -b test1 -c -d -- test2 test3
```

解析时候，会自动吧-cd分开，并插入双破折线来分隔行中的额外参数。如果制订了一个不在opstring中的选项，那么会报错，可以加-q来忽略错误信息

```shell
getopt ab:cd -a -b test1 -cde test2 test3

getopt -q ab:cd -a -b test1 -cde test2 test3
```

**在脚本中使用getopt**

```shell
set -- $(getopt ab:cd "$@")
```

这种方法，首先将原始脚本的命令行参数传给getopt，然后getopt格式化后的参数传给set，用getopt格式化后的参数来代替原始的命令行参数

```shell
set -- $(getopt -q ab:cd "$@")

echo 
while [ -n "$1" ]
do
	case "$1" in
		-a) echo "Found the -a option";;
-b) param="$2"
echo "Found the -b option, with parameter value $param"
shift;;
-c)echo Found the -c option;;
--) shift
break;;
*) echo $1 is not an option;;
esac
shift
done

count=1
for param in "$@"
do
	echo "parameter #$count: $param"
	count=$[$count+1]
done
```

```shell
./test.sh -a -b test1 -cd "test2 test3" test4
#output
Found the -a option
Found the -a option, with parameter value 'test1'
Found the -c option
-d is not an option
parameter #1: 'test2
parameter #2: test3'
parameter #3: 'test4'
#getopt不擅长处理空格和引号的参数值，它会将空格当做参数分隔符，而不是根据双引号将二者当做一个参数
```

### 使用跟高级的getopts

getopts一次只处理命令行上检测到的一个参数。处理完所有参数后会返回一个大于0的状态码

```shell
#格式
getopts optstring variable
```

optstring类似getopt，但是如果要是去掉错误信息，在optstring前面加一个冒号。

getopts会将当前参数保存在variable中。其还会用到两个环境变量，OPTARG保存参数值，OPTIND保存参数位置

getopts解析选项时会移除选项前的单破折线，所以在case里就不能有单破折线

```shell
echo
while  getopts :ab:c opt; do
	case "$opt" in
		a ) echo "Find a"
			;;
			b) echo "Find b, with value $OPTARG, with index $OPTIND";;
c) echo "Find c";;
*) echo "Unkonwn $opt"
	esac
	#statements
done

#./test.sh -ab test1 -c
```

一些有用的功能，包括，可以在参数中包含空格

```shell
./test.sh -b "test1 test2" -a

#Find b, with value test1 test2, with index 3
#Find a
```

还有一个，可将选项字母与参数放在一起

```shell
./test.sh -abtest1 
#Find a
#Find b, with value test1, with index 2
```

## 将选项标准化

有些字母选项在Linux里已拥有了某种程度的标准含义，我们编写脚本是最好也是按照这些意思

<img src="shell/image-20221009212629637.png" alt="image-20221009212629637" style="zoom:50%;" />

## 获得用户输入

想在脚本运行时获得一些输入，可用read命令，它从键盘或另一个文件描述符中接受输入

```shell
echo -n "Enter your name:"
read name
echo "Hello $name, welcome to my program."

#./test.sh 
#Enter your name:Rich Blum
#Hello Rich Blum, welcome to my program.
```

```shell
#加-p显示提示文字
read -p "Please enter your age:" age
day=$[$age*365]
echo That makes you over $day days old!
```

```shell
read -p "Enter your name:	" first last
echo $last $first
#./test.sh 
#Enter your name:	Rich Blum
#Blum Rich
```

也可对read不指定变量，那么read会把读到的数据都放在环境变量REPLY中

```shell
read -p "Enter your name:	" 
echo $REPLY
```

### 超时

可以对read命令指定一个等待时间，用-t，若超出了等待时间，则read会返回一个非0状态码

```shell
if read -t 5 -p "Please enter your name:" name
then
	echo "Hello $name, welcome to my script"
else
	echo
	echo "Sorry, too slow!"
fi
```

也可对read命令指定要读入的字符数

```shell
read -n1 -p "Do you want to continue [Y/N]? " answer
case $answer in
	Y | y) echo
echo "fine, continue on...";;
N | n) echo
echo OK,goodbye
exit;;
esac
echo "This is the end of the script"
```

### 隐藏方式读取

隐藏输入的字符

```shell
read -s -p "Enter your password: " pass
echo
echo "your password is : $pass"
```

### 从文件中读取

用read命令读取系统中文件，每次读取一行，当文件中没有数据时，read退出并返回非零状态码

先用cat，然后通过管道传给read

```shell
count=1
cat test.sh | while read line
do
	echo "Line $count: $line"
	count=$[$count+1]
done
echo "Finished processing the file"
```

# 呈现数据

![image-20221010110515257](shell/image-20221010110515257.png)

### 重定向错误

**只重定向错误**

*把2紧紧的放在重定向符号前，这样就只把错误信息放到了test4中*

```shell
ls -al test badtest test.sh 2> test4
```

**重定向错误和数据**

重定向错误和正常输出，必须用两个重定向符号

```shell
ls -al test test2 test.sh 2> test4 1>test7
```

也可以用&>，来将STDERR和STDOUT重定向到同一文件中

```shell
la -al test test2 test.sh &> test7
```

## 在脚本中重定向输出

### 临时重定向

在脚本中生成了错误信息，可用>&2，将信息传送到STDERR

```shell
echo "This is an error" >&2
echo "This is normal output"
```

然后在命令行中将STDERR重定向，就可以显示错误信息

```shell
./test.sh 2> test7
```

### 永久重定向

```shell
exec 2> testerror

echo "This is the start of the script"
echo "now redirecting all output to another location"

exec 1> testout

echo "This output should go to the testout file"
echo "bute this should go to the testerror file" >&2
```

## 在脚本中重定向输入

```shell
exec 0< testfile
```

上面的这个命令意思是，告诉shell应该从文件testfile中获得输入，而不是STDIN

```shell
exec 0< testfile
count=1

while read line
do
	echo "Line #$count: $line"
	count=$[$count+1]
done
```

## 创建自己的重定向

Linux最对可同时存在9个打开的文件描述符

### 创建输出文件描述符

```shell
exec 3> test13out
#exec 3>> test13out
echo "one"
echo "two"
echo "output" >&3
```

### 重定向文件描述符

这块讲述怎么恢复已重定向的文件描述符

```shell
exec 3>&1
exec 1> test14out

echo "This should store in the output file"
echo "along with this line."

exec 1>&3
echo "Now things should be back to normal"
```

首先3重定向到1,1重定向到文件test14out，但是3仍然是重定向到STDOUT，也就是说传递给3的数据还是会显示在屏幕上，那么最后1又重定向到3，即1有变成了STDOUT。

### 创建输入文件描述符

```shell
exec 6<&0
exec 0< test.sh

count=1
while read line
do
	echo "Line #$count: $line"
	count=$[$count+1]
done
exec 0<&6
read -p "Are you done now?" answer
case $answer in
Y|y) echo "Goodbye";;
N|n) echo "Sorry, this is th end.";;
esac
```

### 关闭文件描述符

创建了新的输入或输出文件描述符，shell脚本退出时会自动关闭它们。也可用下面的命令

```shell
exec 3>&- #关闭了3
```

## 阻止命令输出

将所有的输出都重定向到/etc/null中，这些输出会消失

```shell
ls -al > /etc/null
ls -al 2> /etc/null
```

## 创建临时文件

/tmp目录专门用来存放临时文件，并且系统启动时会自动删除里面的内容

### 创建本地临时文件

mktemp可以创建一个唯一的临时文件，其属主就对它有了读写权限，其他的用户除了root外都无法使用。

```shell
mktemp test.XXXXXX
#这个是模板，后面的六个大X必须要带
```

### 在/tmp目录创建临时文件

加-t选项即可

```shell
tempfile=$(mktemp -t tmp.XXXXXX)
echo "This is a test file" > $tempfile
echo "This is the second line of the test" >> $tempfile

echo "The temp file is localted at: $tempfile"
cat $tempfile
rm -r $tempfile
```

### 创建临时目录

```shell
mktemp -d dir.XXXXXX
```

## 记录消息

将输出同时发送到显示器和日志文件，可以用tee命令。

tee命令相当于管道的T型接口，它将STDIN过来的数据同时发往两处，一处是STDOUT，另一处是tee所指向的文件

```shell
date | tee test.sh
#每次使用tee都会覆盖原始文件，加-a选项可追加数据
date | tee -a test.sh
```

# 控制脚本

## 以后台模式运行脚本

```shell
./test.sh &
```

后台程序运行时，它仍然会使用终端显示器显示STDOUT和STDERR

后台程序会和终端会话绑定，终端会话结束那么后台程序也会结束。

## 在非控制台下运行脚本

```shell
nohup ./test.sh &
```

使用nohup，进程就解除与终端会话的绑定，同时不再关联STDOUT和STDERR。

但是为了保存该命令产生的输出，nohup会自动将STDOUT和STDERR重定向到一个名为nohup.out的文件中

# 函数

## 基本的脚本函数

### 创建函数

```shell
function name {
	commands
}
#name为函数名
name() {
	commmands
}
```

函数需要是先定义后使用，且不存在重载

## 返回值

### 默认退出码

默认情况下，函数的退出状态码是函数中最后一条命令返回的退出状态码。函数执行结束后，可以用$?来确定函数的退出状态码

### 使用return命令

return命令来退出函数并返回特定的退出状态码

```shell
function db1 {
	read -p "Enter a value:	" value
	echo "doubling the value"
	return $[$value*2]
}

db1
echo "The new value $?"
```

状态码只能是0~255

### 使用函数输出

```shell
function db1 {
	read -p "Enter a value:" value
	echo $[$value*2]
}

result=$(db1)
echo "The new value is $result"
```

## 在函数中使用变量

函数无法直接获得脚本参数，需要将脚本参数传进函数才行

```shell
function addem {
	if [ $# -eq 0 ] || [ $# -gt 2 ]
	then
		echo -1
	elif [ $# -eq 1 ]
		then
			echo $[$1+$1]
	else
		echo $[ $1 + $2]
	fi
}

echo -n "Adding 10 and 15:"
value=$(addem 10 15)
echo $value
echo -n "Let's try adding just one number: "
value=$(addem 10)
echo $value
echo -n "Now trying adding no numbers: "
value=$(addem)
echo $value
echo -n "Finally, try adding three numbers: "
value=$(addem 10 15 20)
echo $value
```

### 局部变量

用local，这样就可以区分同名的全局变量和局部变量

```shell
local temp
#或者
local temp=$[$value+5]
```

## 数组变量和函数

### 向函数传数组参数

```shell
	local sum=0
local newarray
newarray=($(echo "$@"))
for value in ${newarray[*]}
do
	sum=$[$sum+$value]
done
echo $sum
}

myarray=(1 2 3 4 5)
echo "The original array is: ${myarray[*]}"
arg1=$(echo ${myarray[*]})
result=$(testit $arg1)
echo "The result is $result"
```

### 从函数返回数组

```shell
function arraydblr {
	local origarray
	local newarray
	local elements
	local i
	origarray=($(echo "$@"))
	newarray=($(echo "$@"))
	elements=$[$#-1]
	for ((i=0;i<=$elements;i++))
	{
		newarray[$i]=$[${origarray[$i]}*2]
	}
	echo ${newarray[*]}
}

myarray=(1 2 3 4 5)
echo "the original array is: ${myarray[*]}"
result=($(arraydblr ${myarray[*]}))
echo "the new array is: ${result[*]}"
```

## 创建库

```shell
#函数库 可以不带#!/bin/bash
function addem {
        echo $[$1+$2]
}
function multem {
        echo $[$1*$2]
}
function divem {
        if [ $2 -ne 0 ]
        then
                echo $[$1/$2]
        else
                echo -1
        fi
}
```

```shell
#!/bin/bash

. ./mufuncs

value1=10
value2=5
result1=$(addem $value1 $value2)
result2=$(multem $value1 $value2)
result3=$(divem $value1 $value2)
echo "The result of adding them is: $result1"
echo "The result of multiplying them is: $result2"
echo "The result of dividing them is: $result3"

```

# 正则表达式

特殊字符：.*[]^${}\+?|()，要使用他们需要加转义字符:\

脱字符(^)表示行首，$表示行尾，[]表示字符组([ab]表示在这个位置上只匹配a或b)，(.)表示匹配这个位置的任意一个字符

[a-b],[1-9],[a-bc-d] 表示区间，匹配a到b的字符，1-9的数字，a到b和c到d的字符

<img src="shell/image-20221014094936398.png" alt="image-20221014094936398" style="zoom:50%;" />

*：在字符后面放置星号，表示匹配时这个字符可出现0次或多次

？：类似*，表示匹配是这个字符可出现0次或1次

+：出现1次或多次，至少1次

{}：为可重复的正则表达式指定一个上限。

1. m：正则表达式出现m次
2. m，n：至少出现m次，至多n次

```
/be{1}     /be{1,2}
```

|：逻辑或

（）：用圆括号进行分组。将正则表达式分组时，这个组会被看成是一个标准字符，这一对他们使用那写特殊字符。
