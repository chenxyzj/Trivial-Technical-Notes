 * 来源：https://www.linuxidc.com/Linux/2016-05/131116.htm
## 1. scp 远程拷贝
### scp 拷贝本地文件filename  到远程机器 192.168.188.188 服务器的/data/tmp目录下 
```   
  scp -P 61204 -l 40000 filename username@192.168.188.188:/data/tmp/  

 -P port   
             Specifies the port to connect to on the remote host.  Note that this option is written with a capital ‘P’, because -p is already reserved
             for preserving the times and modes of the file in rcp(1).
 -P指定远程服务器ssh服务的端口  如：ssh端口为61204

 -l limit
             Limits the used bandwidth, specified in Kbit/s.
 -l 指定拷贝的速度限制 单位是ct/s   如：-l 40000 表示40000Kbit/s=40000/8KB=5MB的速度
```
* 注：scp不支持断点续传

## 2. rsync + ssh 断点续传
### rsync同步本地文件 filename  到远程机器 192.168.188.188 服务器的/data/tmp目录下
``` 
  rsync -avzP -e 'ssh -p 61204' --bwlimit=5000 filename username@10.20.90.101:/data/tmp/ >> scp_to_101.log
-a：以archive模式操作，复制目录、符号连接，等价于 -rlptgoD 。
-v：详细提示 
-z：压缩
-P：是综合了--partial --progress两个参数
    --partial
    如果在拷贝文件的过程中，传输被中断，rsync的默认操作是撤消前操作，即从目标机上删除已拷贝的部分文件。
    如果想在下次拷贝时续传文件,不需要重新拷贝所有文件的话,可以使用-partial选项阻止rsync在传输中断时删除已拷贝的部分
    --progress 显示进度条
-e:参数的作用是可以使用户自由选择欲使用的shell程序来连接远端服务器
     ssh -p 61204 指定ssh的端口（非默认22）61204
--bwlimit: --bwlimit=5000  限制带宽为5000k Bytes/s =5MB
```
* 上例：使用rsync传输本地文件filename到192.168.188.188的/data/tmp目录下  使用压缩归档传输、限速5MB、支持断点续传 使用ssh协议 

### 详情 可以参考 man scp 和man rsync 手册
