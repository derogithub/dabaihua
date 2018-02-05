**查看文件编码**
1. 在Vim 中可以直接查看 文件 编码
    - :set fileencoding
2. enca(如果你的系统中没有安装这个命令，可以用sudo yum install -y enca 安装 )
    - enca filename
    - enca -L zh_CN filename

**文件转码**

1. 在Vim中直接进行转换文件 编码 ,比如将一个文件 转换成utf-8格式
    - :set fileencoding=utf-8
2. enconv 转换文件 编码 ，比如要将一个GBK编码 的文件 转换成UTF-8编码 ，操作如下
    - enconv -L zh_CN -x UTF-8 filename
3. iconv 转换，iconv的命令格式如下：
    - iconv -f encoding -t encoding inputfile
    - 比如将一个UTF-8 编码 的文件 转换成GBK编码 iconv -f GBK -t UTF-8 file1 -o file2
