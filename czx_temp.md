#### 2.1. `sed`定址

地址用于决定对哪些行进行编辑。地址形式可以是数字，正则表达式或二者的结合。如果没有指定地址，`sed`将处理输入文件中的所有行。

* 数字：

  ```bash
  sed -r 'd' /etc/passwd
  sed -r '3d' /etc/passwd	# 操作第三行
  sed -r '5,$d' /etc/passwd # 从第5行到最后一行
  sed -r '1,3d' /etc/passwd
  sed -r '1~2d' /etc/passwd	# 删除所有奇数行 odd-numbered(从0行开始没隔两行删)
  sed -r '0~2d' /etc/passwd	# 删除所有偶数行 even-numbered
  ```

* 正则表达式：

  ```bash
  sed -r '/root/d' /etc/passwd	# 查找
  sed -r '/root/!d' /etc/passwd	# 取反，除了/root/行
  sed -r 's/root/alice/g' /etc/passwd	# 查找替换
  ```

* 二者的结合

  ```bash
  sed -r '/^adm/, 20d' /etc/passwd # 到第20行
  sed -r '/^adm/, +20d' /etc/passwd # 往后20行
  sed -r '/^adm/, -20d' /etc/passwd # 往前20行
  ```

  

#### 2.2. `sed`命令

`sed`命令告诉`sed`对指定行进行任何操作，包括打印、删除、修改等。

* a：在当前行后添加一行或多行
* c：用新文本修改（替换）当前行中的文本
* d：删除行
* i：在当前行之前插入文本
* l：列出非打印字符
* p：打印行
  * n：读入下一输入行，并从下一条命令而不是第一条命令开始对其的处理（？？？
* q：结束会退出`sed`
* ！：对所选行以外的所有行应用命令
* s：用一个字符串替换另一个
  * s：替换标志
  * g：在**行内**进行全局替换
  * i：忽略大小写
* r：从文件中读出内容，并放到该行
* w：将行写入文件
* y：将字符转换为另一个字符（不支持正则表达式）
* h：把模式空间里的内容复制到暂存缓冲区（覆盖）
* H：把模式空间里的内容复制到暂存缓冲区（累加）
* g：取出暂存缓冲区的内容，将其复制到模式空间，覆盖该处原有内容
* G：取出暂存缓冲区的内容，将其复制到模式空间，追加在原有内容后面
* x：交换暂存缓冲区与模式空间的内容



```bash
sed -r '3d' datafile	# 删除第3行
sed -r '3{d;}' datafile	# 可以对第三行做多个操作
sed -r '3{h;d}' datafile	# 对第三行做多个操作
sed -r '3{d}' datafile

sed -r '3, $d' datafile	# 删除3到最后一行
sed -r '$d' datafile
sed -r '/north/d' datafile	# 删除满足正则的行
sed -r '/sout/d' datafile

# 替换命令 s
sed -r 's/west/north/g' datafile
sed -r 's/^west/north/' datafile
sed -r 's/[0-9][0-9]$/&.5/' datafile	# &代表在查找串中匹配到的内容
sed -r '3,5s/\(.*\)/#\1/'	# 添加注释
sed -r '3,5s/.*/#&/' # 给3到5行添加注释
sed -r '3,5s/^/#/' # 给3到5行加注释
sed -r '/root/s/^/#/'	# 给带有root的行加注释
sed -r 's/(.)(.)(.*)/\1#\2\3/'	# 在第二字母前面加#
sed -r 's/Hemenway/Jones/g' datafile
sed -r 's/(Mar)got/\1ianne/g' datafile
sed -r 's#3#88#g' datafile

# 读文件命令： r
sed -r   
```







temp: 

`/`可以换成`\#`

`sed -r '\#root#d' /etc/passwd`