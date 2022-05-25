# Wireshark网卡无法找到或没有显示的问题
## 问题背景
最近在处理公司内网域名解析的问题，发现配置好一个新域名在内网环境可以正常解析成内网IP，但使用深信服VPN却无法正常解析，并且其他域名使用深信服VPN可以正常解析，所以参考[《内网域名解析配置和简单排错》](https://bbs.sangfor.com.cn/forum.php?mod=viewthread&tid=11237)排查问题，结果抓包时遇到无法找到网卡的问题，特此记录。

## 故障现象
* 运行环境：Windows11 
* 软件版本：Wireshark Version 3.6.5
* Wireshark可以正常显示网卡列表，但缺失深信服的虚拟网卡
![20220525174440](https://ldoc-1258951625.cos.ap-shanghai.myqcloud.com/Wireshark网卡无法找到或没有显示的问题/20220525174440.png)

## 解决方案
### 调整网卡设置
1. 参考[《解决wireshark抓热点的包，没有虚拟网卡的问题》](https://codeantenna.com/a/W2ZEol7DZb)，查看网卡配置，果然发现VPN虚拟网卡的连接配置没有勾选NPCAP选项
![20220525180846](https://ldoc-1258951625.cos.ap-shanghai.myqcloud.com/Wireshark网卡无法找到或没有显示的问题/20220525180846.png)
2. 重启Wireshark或者Wireshark界面选择 `捕获-刷新接口列表（F5）` 即可看到网卡

### 重启npcap
与网上大部分通过重启npcap的解决无法找到网卡方案不同，重启的方案一般是解决所有网卡都看不到的情况，为方便大家解决问题，这里也放出重启npcap的方法
1. 管理员运行CMD，不能使用PowerShell，命令在PowerShell会无法显示输出
2. 执行 `sc query npcap` 查询npcap状态
~~~
C:\Users\Bruce>sc query npcap

SERVICE_NAME: npcap
        TYPE               : 1  KERNEL_DRIVER
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
~~~
3. 执行 `sc stop npcap` 暂停npcap
~~~
C:\Users\Bruce>sc stop npcap

SERVICE_NAME: npcap
        TYPE               : 1  KERNEL_DRIVER
        STATE              : 1  STOPPED
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
~~~
4. 执行 `sc start npcap` 启动npcap
~~~
C:\Users\Bruce>sc start npcap

SERVICE_NAME: npcap
        TYPE               : 1  KERNEL_DRIVER
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
        PID                : 0
        FLAGS              :
~~~
