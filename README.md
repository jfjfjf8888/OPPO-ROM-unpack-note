# OPPO-ROM-unpack-note
记录拆OPPO ROM的过程

# 前言
    总所周知，安卓由于开源，厂商繁多，各个厂商瞎JB改，兼容性问题给Android app开发人员造成了挺大的困扰。本记录也产生于解决兼容性BUG的过程中。

# 摘要
     近期公司开发了一个Android app，在OPPO某些机型上运行app出现奇怪了BUG(具体就不写了，与本记录无关)，为了彻底解决问题，我想把OPPO 的ROM
     拿过来，拆开，查看app使用到的一些功能的系统实现。拆包的过程我在此记录下来，希望能帮到后来者。
     
# ROM来源
ROM是从OPPO官网找的，对应的手机型号是：OPPO A53，下载地址是:[CPH2127EU_11_OTA_0580_all_ou1osUyWrqeu.ozip][2]

# 拆包的过程：
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
### 走过的弯路：
最初我图方便，在一个论坛找到了一个名为***IZArc***的可视化工具解压缩ozip文件，其中***system.new.dat.br***解压出来是正常的，***system.transfer.list***不正常，没有正常
解密，***文件签名是"OPPOENCRYPT!"***。由于最初的偷懒，挣扎了许久，看到文件都解压出来了，我都没有怀疑是解压工具的问题。后来实在不知道为什么，才重新
找到了上述***ozipdecrypt.py***来试试，试过才知道，这个真香！！！

## 2. system.img挂载的问题。
使用mount命令挂载system.img始终出现错误。错误类似与如下的样子:
```
    mount: wrong fs type, bad option, bad superblock on /dev/loop21,
           missing codepage or helper program, or other error
```


[1]:https://github.com/bkerler/oppo_ozip_decrypt/blob/master/ozipdecrypt.py
[2]:https://assorted.downloads.oppo.com/firmware/CPH2127/CPH2127EU_11_OTA_0580_all_ou1osUyWrqeu.ozip
[3]:https://github.com/google/brotli
