# OPPO-ROM-unpack-note
记录拆OPPO ROM的过程

# 前言
总所周知，安卓由于开源，厂商繁多，各个厂商瞎JB改，兼容性问题给Android app开发人员造成了挺大的困扰。本记录也产生于解决兼容性BUG的过程中。

# 摘要
近期公司开发了一个Android app，在OPPO某些机型上运行app出现奇怪了BUG(具体就不写了，与本记录无关)，为了彻底解决问题，我想把OPPO 的ROM那过来，拆开，查看app使用到的一些功能的系统实现。

# 拆包过程中遇到的问题：
 1. ozip解压问题。
 2. 通过system.new.dat和system.transfer.list得到system.img问题。
 3. system.img挂载的问题。

## 1. ozip解压问题。
    结论：ozip解压可以通过ozipdecrypt.py搞定。ozipdecrypt.py 来自于:
    [Github]:<https://github.com/bkerler/oppo_ozip_decrypt/blob/master/ozipdecrypt.py>
## 2. 通过system.new.dat和system.transfer.list得到system.img问题。

## 3. system.img挂载的问题。
