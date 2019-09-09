# Linux下tar,gz,bz2,zip,rar等压缩，解压缩命令小结

## tar命令

1. 打包

    `tar -cf [生成文件.tar] [目标文件]`
    
    例如:
    
    `tar -cf jpg.tar *.jpg`
    
    这是把所有的jpg文件打包为jpg.tar，注意，tar仅仅是将不同文件打包
    在一起，并没有对文件进行压缩
    
2. 添加

    `tar -rf [生成文件.tar] [目标文件]`
    
    例如:
    
    `tar -rf jpg.tar *.gif`
    
    这是把所有gif文件添加到jpg.tar包中
    
3. 更新

    `tar -uf [生成文件.tar] [目标文件]`
    
    例如:
    
    `tar -uf jpg.tar 11.jpg`
    
    如果jpg.tar中没有11.jpg，则加入11.jpg，如果有，则添加11.jpg到包里，
    但是注意，此时没有替换包内原来的11.jpg，因此，包内会有两个名字相同的文件
    
4. 显示

    `tar -tf [目标文件.tar]`
    
    例如:
    
    `tar -tf jpg.tar`
    
    会显示包内的所有文件
    
5. 解包

    `tar -xf [目标文件.tar]`
    
    例如:
    
    `tar -xf jpg.tar`
    
> 注意:以上所有命令都要加-f参数，这是指定目标包文件

## 压缩

1. **gz压缩** 
   
    gzip是GNU开发的一个压缩程序，后缀往往是.gz，tar中使用参数-z调用gzip
    
    + **压缩** `tar -czf [生成文件.tar.gz] [目标文件]`
        
    + **解压** `tar -xzf [目标文件.tar.gz]`
        
2. **bz2压缩** 
   
    bzip2是一个压缩能力更强的程序，后缀往往是.bz2，tar中使用参数-j调用bzip2
   
    + **压缩** `tar -cjf [生成文件.tar.bz2] [目标文件]`
        
    + **解压** `tar -xjf [目标文件.tar.bz2]`
        
3. **compress** 
   
   compress也是一个压缩文件，但使用的人往往较少,后缀往往使用.Z，tar中使用-Z调用compress
   
   **压缩** `tar -cZf [生成文件.tar.Z] [目标文件]`
   
   **解压** `tar -xZf [目标文件.tar.Z]`
   
> 注意：使用tar进行压缩往往自带一个打包的过程，产生的后缀往往是.tar.*，而对于单纯的压缩文件
> 例如.gz,.Z,.bz2等等，则使用专有的压缩程序进行压缩和解压缩
>
> gz        -压缩:gzip      -解压缩:gunzip   
> bz2       -压缩:bzip2     -解压缩:bunzip2   
> compress  -压缩:compress  -解压缩:uncompress   

## 补充

1. tar的基本用法

    tar的用法上面已经详细讨论过了，实际中往往还会添加-v参数，以显示过程，因此:
    
    **压缩** `tar -cvzf [生成文件.tar.gz] [目标文件]`
    
    **解压** `tar -xvzf [目标文件.tar.gz]`
    
    以上为最基本的用法
    
2. zip压缩文件

    对于zip压缩文件，Linux也有对应的处理:
    
    **压缩** `zip [生成文件.zip] [目标文件]`
    
    **解压** `unzip [目标文件.zip]`
    
    zip还有很多参数，这里不再详述
    
3. rar压缩文件

    对于rar压缩文件，Linux需要下载相应的软件RAR for Linux
    
    **下载方法**:
    
    `sudo apt-get install rar unrar`
        
    或者:
        
    [进入官网](http://www.rarsoft.com/download.htm "http://www.rarsoft.com/download.htm")下载相应的文件
    
    **压缩** `rar a [生成文件.rar] [目标文件]`
    
    **解压** `rar e [目标文件.rar]`  `rar x [目标文件.rar]`
    
4. 7z压缩文件

    对于7z压缩文件，Linux需要下载指定软件
    
    **下载方法** 
    
    `sudo apt-get install p7zip`
    
    **压缩** `7z a [生成文件.7z] [目标文件]`
    
    **解压** `7z e [目标文件.7z]` `7z x [目标文件.7z]`
    
> 最后要提的是:rar和7z的解压缩参数是e和x两者之间的区别是:   
> e: Extract files from archive (without using diretory names)   
> x: Extract files with full paths   



