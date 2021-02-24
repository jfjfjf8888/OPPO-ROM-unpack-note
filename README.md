# OPPO-ROM-unpack-note
记录拆OPPO ROM的过程

# 前言
    总所周知，安卓由于开源，厂商繁多，各个厂商瞎JB改，兼容性问题给Android app开发人员造成了挺大的困扰。
    本记录也产生于解决兼容性BUG的过程中。

# 摘要
     近期公司开发了一个Android app，在OPPO某些机型上运行app出现奇怪了BUG(具体就不写了，与本记录无关)，
     为了彻底解决问题，我想把OPPO 的ROM 拿过来，拆开，查看app使用到的一些功能的系统实现。拆包的过程我
     在此记录下来，希望能帮到后来者。
     
# ROM来源
ROM是从OPPO官网找的，对应的手机型号是：***OPPO A53***，下载地址是:[CPH2127EU_11_OTA_0580_all_ou1osUyWrqeu.ozip][2]

# 操作系统
Ubuntu 18.04.5 LTS

# 拆包的流程：
     1. ozip解压。
     2. *.new.dat.br 文件解压。这里我只需要system.new.dat.br，所以，我只将system.new.dat.br解压了。
     3. *.new.dat 和*.transfer.list文件转换成*.img。这里我将system.new.dat和system.transfer.list转成了system.img
     4. *.img 在Ubuntu 18.04.5 LTS上挂载。

# 拆包过程中遇到的问题：
      1. ozip解压问题。
      2. system.img挂载的问题。

## 1. ozip解压问题。
### 结论：
ozip解压可以通过ozipdecrypt.py搞定。ozipdecrypt.py 来自于: [ozipdecrypt.py][1]
### 走弯路的过程:
最初我图方便，在一个论坛找到了一个名为***IZArc***的可视化工具解压缩ozip文件，其中***system.new.dat.br***解压出来是正常的，***system.transfer.list***不正常，没有正常
解密，***文件签名是"OPPOENCRYPT!"***。由于最初的偷懒，挣扎了许久，看到文件都解压出来了，我都没有怀疑是解压工具的问题。后来实在不知道为什么，才重新
找到了上述***ozipdecrypt.py***来试试，试过才知道，这个真香！！！

## 2. system.img挂载的问题。
### 结论:
OPPO的system.img 太大了(我这个弄出来4.3GB)，直接mount是不行的，得以***只读方式***mount。mount 的时候参数里面加一个"-r"就可以了
### 走弯路的过程:
使用mount命令挂载system.img始终出现错误。错误类似与如下的样子:
```
    mount: wrong fs type, bad option, bad superblock on /dev/loop21,
           missing codepage or helper program, or other error
```
上面是我的Ubuntu的报错，看到这个错误我也不知道怎么办，只好复制下来去Google，我看到了别人也有过这个错，但是后面似乎多点东西，如下:
```
mount: wrong fs type, bad option, bad superblock on /dev/sdf1,
       missing codepage or helper program, or other error
       In some cases useful info is found in syslog - try
       dmesg | tail  or so
```
看到这个提示，抱着试一试的心态，在我的终端里敲出了***dmesg***，然后回车。之后发现了我在mount system.img时发生的错误，如下：
```
EXT4-fs (loop21): couldn't mount RDWR because of unsupported optional features (4000)
```
又把上面这一堆玩意儿丢进了Google，发现很多人遇到这个东西，不过后面的错误码400比较多，而我的是4000。于是继续在Google上溜达。之后发现
一篇名为[无法挂载的Ext4，because of unsupported optional features][4]的文章。这个帖子作者看样子是找到linux系统源码里去了，他遇到的错误码
是200。看到如下内容我感觉豁然开朗:
```
#define EXT2_MOUNT_NO_UID32     0x0200  /* Disable 32-bit UIDs */
#define EXT4_FEATURE_COMPAT_SPARSE_SUPER2   0x0200
#define EXT4_FEATURE_RO_COMPAT_BIGALLOC     0x0200
#define EXT4_FEATURE_INCOMPAT_FLEX_BG       0x0200
#define EXT4_DEFM_BLOCK_VALIDITY 0x0200
```
目测，上面这些define就是出自系统源码，于是随便复制了一行丢进Google，直接定位到了***ext4.h***中。[ext4.h][5]
随后，我按Ctrl + F搜索 0x4000，排除了几个明显不是的，我找到了如下内容：
```
#define 	EXT4_FEATURE_INCOMPAT_LARGEDIR   0x4000 /* >2GB or 3-lvl htree */
```
看到这个，就明确了，system.img太大了。继续Google后发现，大文件要想mount，得以只读方式去mount，以只读方式mount也比较简单了，mount的
帮助里面有写，参数加一个-r就搞定了。

# 最后
详细拆包过程我就没写了，网上太多了，泛滥了。

到这里，我就可以拿到系统frameworks的jar文件了，再见～

[1]:https://github.com/bkerler/oppo_ozip_decrypt/blob/master/ozipdecrypt.py
[2]:https://assorted.downloads.oppo.com/firmware/CPH2127/CPH2127EU_11_OTA_0580_all_ou1osUyWrqeu.ozip
[3]:https://github.com/google/brotli
[4]:https://www.jianshu.com/p/3af1ee22821f
[5]:https://docs.huihoo.com/doxygen/linux/kernel/3.7/fs_2ext4_2ext4_8h.html
