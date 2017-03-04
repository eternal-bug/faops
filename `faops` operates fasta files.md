# `faops` operates fasta files 
  利用`faops`操作fasta文件



## Compiling  
   编译

`faops` can be compiled under Linux, macOS (gcc or clang)and Windows (MinGW). 
`faops`可以在Linux、macOS或者Windows系统中被编译

```bash
git clone https://github.com/wang-q/faops
使用git命令从以上网站摘取faops，其中包括了test文件。
ps: 
需要先安装 git（sudo apt-get install git）

cd faops
进入faops目录中，可以查看文件（ls）：

make
使用make命令编译faops
ps: 
make的过程中需要先安装gcc（安装：sudo apt-get  install  build-essential，查看版本：gcc --version）
如果再次输入make会出现make: Nothing to be done for'all'.说明faops已经编译好了。
如果想要删除编译，可以输入make clean。
```



## Test  
   测试



### 1.   Help 
    查看Faops信息

```bash
./faops help

Usage:     faops <command> [options]<arguments> 
用法： faops <命令> [选项] <参数>

Version:   0.3.0

Commands:
            help           print this message
                           打印相关信息
                            
            count          count base statistics in FA file(s)
                           计算Fasta文件中的碱基数
                            
            size           count total bases in FA file(s)
                           计算Fasta文件的大小
                            
            frag           extract sub-sequences from a FA file
                           提取Fasta文件中子序列
                            
            rc             reverse complement a FA file
                           得到一个Fasta文件的反向互补序列
                            
            some           extract some fa records
                           提取Fasta文件中特定的序列
                            
            order          extract some fa records by the given order
                           通过给定的顺序提取一些fa序列
             
            replace        replace headers from a FA file
                           替换fa文件中的标题名字
             
            filter         filter fa records
                           提取特定长度区间的基因序列
                            
            split-name     splitting by sequence names
                           将序列按名称分割
                            
            split-about    splitting to chunks about specified size
                           将序列按特定大小分割
                            
            n50            compute N50 and other statistics
                           计算N50和其他数据

Options:
    There're no global options.
    Type "faops command-name" fordetailed options of each command.
    Options MUST be placed justafter command.
```



### 2.  count
    计算

```bash
用于统计一个基因序列文件中各个不同序列的碱基、N的数量。

输入：
faops count <in.fa> [more_files.fa]

实例：
./faops count test/ufasta.fa
```



### 3.  size
    大小

```bash
可以得到每一个不同序列的大小。

输入：
faops size <in.fa> [more_files.fa]

实例：
./faops size test/ufasta.fa
```



### 4.  frag
    子序列提取

```bash
frag 功能用于摘取一个基因序列中的特定片段，片段的定位由用户以数字的形式给出（如1--160）。如果一个基因序列文件中含有多个基因组序列，该软件将输出第一个基因序列的相应片段。

输入：
faops frag [options] <in.fa><start> <end> <out.fa>

实例：
./faops frag test/ufasta.fa 1 160 faFrag.fa
```



### 5.   Reverse complement
    反向互补

```bash
在基因分析中，有时需要得到一条基因序列的反向链或互补链， faops rc功能就是输出基因序列文件中各个基因序列的反向，互补序列。该功能有三个选项。即：反向、互补、既互补又反向。对这三个功能的选择将在输入时候体现。

输入：
faops rc [options] <in.fa> <out.fa>

   Options:
                -n         keep name identical (don't prependRC_)
                -r         just Reverse, prepends R_
                -c         just Complement, prepends C_
                -l INT     sequence line length [80] 

实例：
./faops rc test/ufasta.fa out.fa     
添加RC_ 到每一个序列名称前
./faops rc -n test/ufasta.faout.fa    
保留原有的序列名称

ps：
可输入 cat ufasta.fa，$ cat out.fa 查看文件内容变化。out.fa会自动保存在操作目录下。
```



### 6.  some
    提取特定的序列

```bash
在基因组序列分析中，有时需要按照名字将特定的序列输出。在该功能中，需要把需要输出的序列的名字存在一个名为“listfile”的文件中，并以回车将其分开。

输入：
faops some [options] <in.fa> <list.file> <out.fa>

   Options:
                -i         Invert, output sequences not in thelist
                -l INT     sequence line length [80]

实例：
./faops some test/ufasta.fa /mnt/f/../listfile out.fa
win10下bash输入的命令 “/mnt/f/../listfile”指的是在Linux系统下挂载F盘中名为listfile的文件，listfile中每一个序列名称要用回车分隔。

ps：
也可以使用WinSCP将listfile文件先上传到服务器中

pps：
cat test/ufasta.fa | ./faops some stdin /mnt/f/../listfile stdout  
与上实例中作用相同，但是直接在屏幕中输出
```



#### 6.1.  Sort by header strings
     按照首字母分类

```bash
for word in $(cat test/ufasta.fa | grep '>'| sed 's/>//' | sort); do
> ./faopssome test/ufasta.fa <(echo ${word}) stdout
> Done
```



#### 6.2.  Sort by lengths
     按照长度分类

```bash
for word in $(./faops size test/ufasta.fa | sort -n -r -k2,2 | cut -f 1); do
> ./faops some test/ufasta.fa <(echo ${word}) stdout
> done
```



### 7.   order
    排序

```bash
通过给定的顺序提取多个fa序列，加载内存中所有的序列需要耗费更多的内存。

输入：
faops order [options] <in.fa> <list.file> <out.fa>

   options:
                -l INT     sequence line length [80]

                in.fa  == stdin  means reading from stdin
                out.fa == stdout means writing to stdout

实例：
./faops order test/ufasta.fa listfile out.fa
```



#### 7.1.  Sort by header strings
     按照首字母排序

```bash
./faops order -l 0 test/ufasta.fa \
    <(cat test/ufasta.fa | grep '>' | sed 's/>//' | sort) \
    out.fa
```



#### 7.2.  Sort by lengths
     按照长度排序

```bash
./faops order -l 0 test/ufasta.fa \
    <(./faops size test/ufasta.fa | sort -n -r -k2,2 | cut -f 1) \
    out2.fa
```



### 8.   replace        
    替换

```bash
输入：
faops replace [options] <in.fa> <replace.tsv> <out.fa>

   options:
                -l INT     sequence line length [80]

                <replace.tsv> is a tab-separated file containing two fields
                    # original_name	replace_name
实例：
./faops replace -l 0 test/ufasta.fa replace.tsv out.fa


例：replace.tsv:
read1	ChrI
read4	S288c.I
```



### 9.   filter
    提取特定长度区间的基因序列

```bash
在基因组分析中，我们常常需要输出特定长度区间的基因序列，以便进行一些特殊分析，该功能可以输出特定长度的序列，并且可以输出Gap 长的为一定长度的序列。

输入：
faops filter [options] <in.fa> <out.fa>

   options:
                -a INT     pass sequences at least this big('a'-smallest)
                -z INT     pass sequences this size or smaller('z'-biggest)
                -n INT     pass sequences with fewer than this numberof N's
                -u         Unique, removes duplicate ids, keepingthe first
                -l INT     sequence line length [80]

实例：
./faops filter -a 50 test/ufasta.fa fafilter.fa
所有序列长度≥50的序列保存到fafilter.fa中
./faops filter -z 30 test/ufasta.fa fafilter.fa
所有序列长度≤30的序列保存到fafilter.fa中
```



#### 9.1.  Tidy fasta file to 80 characters ofsequence per line
     将fasta文件按照80字母一行的规则输出

```bash
./faops filter -l 80 test/ufasta.fa out.fa
```



#### 9.2.  All content written on one line
     将fasta文件中每个基因按一行的规则输出

```bash
./faops filter -l 0 test/ufasta.fa out.fa
```



#### 9.3.  Convert fastq to fasta
     将fastq文件转换成fasta文件

```bash
./faops filter -l 0 in.fq out.fa
```



### 10.  split-name
    将序列按名称分割

```bash
在基因组序列分析中，有时会遇到很长的fasta文件，需要将其进行一定的分割。

输入：
faops split-name [options] <in.fa><outdir>

   options:
               -l INT     sequence line length [80]

实例：
./faops split-name test/ufasta.fa faspln
将ufasta.fa中的序列按照名字分割成多个小的文件，并保存在名为“faspln”文件夹中
```



### 11.  split-about
    将序列按大小分割

```bash
在基因组序列分析中，有时会遇到很长的fasta文件，需要将其进行一定的分割。

输入：
faops split-about [options] <in.fa><approx_size> <outdir>
    
   options:
               -l INT     sequence line length [80]

实例：
./faops split-about test/ufasta.fa 73 faspla
将ufasta.fa中的序列按照大小分割成多个小的文件，并保存在名为“faspla”文件夹中
```



### 12.  n50
    计算N50和其他数据

```bash
输入：
faops n50 [options]<in.fa> [more_files.fa]

   options:
               -H         do not display header
               -N INT     compute Nx statistic [50]
               -S         compute sum of size of all entries
               -A         compute average length of all entries
               -E         compute the E-size (from GAGE)
               -g INT     size of genome, instead of total size infiles

实例：
./faops n50 test/ufasta.fa
计算ufasta.fa文件的N50值
```



#### 12.1.    Compute N50, clean result
     计算N50，但不显示首字符，只显示数值

```bash
./faops n50 -H test/ufasta.fa
```



#### 12.2.    Compute N75
     计算N75

```bash
./faops n50 -N 75 test/ufasta.fa
```



#### 12.3.    Compute N90, sum and average ofcontigs with estimated genome size
     计算估计基因组小大的N90、长度总和、长度平均

```bash
./faops n50 -N 90 -S -A -g 10000 test/ufasta.fa
```



