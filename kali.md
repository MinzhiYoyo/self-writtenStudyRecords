## 基础命令

```shell
netstat -rn   # 查看网关和路由

dig @8.8.8.8 baidu.com    # 通过域名服务器8.8.8.8来查询baidu.com的IP地址

traceroute -n baidu.com    # 查找到达百度的路由跳数，并且-n是不解析IP

dig txt chaos VERSION.BIND @ns3.dnsv4.com  
# 查询DNS版本，有时候会查询不成功，并且会被记录下IP
# 之后通过Google搜索，找到该版本的漏洞大全进行操作

dig @8.8.8.8 -x 220.181.38.148 		# ptr查询，通过IP地址反查询域名

whois baidu.com    # 查看baidu.com这个域名的whois信息，也可以使用whois.aliyun.com来进行查询，或者站长之间来查找

# 查询备案信息
# 通过 beianbeian.com 进行查询
# 或者通过天眼查来查询
```

---

#### 使用`Maltego`收集子域名信息

> 通过子域名来逐步接近主域

> 方法：
>
> - （1）子域名挖掘工具：`Maltego`子域名挖掘机
> - （2）搜索引擎挖掘：如在Google中输入  site:qq.com
> - （3）第三方网站查询：[`chinaz`](http://tool.chinaz.com/subdomain) 、[`dnsdumpster`](https://dnsdumpster.com)
> - （4）证书透明度公开日志枚举: [`crt`](htts://crt.sh) 、[`censys`](http://censys.io)
> - （5）其他路径：[`phpinfo`](https://phpinfo.me/domain) 、 [`dnsaizhan`](http://dns.aizhan.com)
>
> 网站：
>
> - `Maltego`注册网站：[`Maltego`](https://www.paterva.com/web7/community/community.php)
> - `Maltego`官网：[`Maltego`](https://www.maltego.com/)

#### 使用 `FOFA` 搜索引擎收集信息

> 还有一款国外的是`shodan`
>
> 官网：[`FOFA`](https://fofa.so) 、[`shodan`](https://www.shodan.io/)

```shell
title="beijing"
从标题中搜索“北京”
-
header="elastic"
从http头中搜索“elastic”
-
body="网络空间测绘"   # html中的<body>
从html正文中搜索“网络空间测绘”


# 具体去网上搜或者fofa官网

# icon搜索，如官网顶部左侧图标(html<head>中的)
```

#### 主动收集

> `TCP/IP`五层模型的协议
>
> `TCP/IP`五层模型扫描
>
> - 第二层：速度快，可靠；但，不可路由
> - 第三层：可路由，速度快；但，可能被防火墙墙掉
> - 第四层扫面：可路由，可靠，不会被防火墙；但，全端口扫描慢

![img](https://gitee.com/lmz2498369702/image-repository/raw/master/202111301642626.png)

> 基于`ping`命令探测
>
> 基于`arping`命令检测

```shell
#!/bin/bash

if [ $# -ne 1 ];then  # 判断用户至少输入一个参数
	echo "Usage - ./arping.sh [interface]"
	echo "Excample - ./arping.sh eth0"
	echo "Example will perform an ARP scan of the local subnet to which eth0 is assigned"
	exit
fi

interface=$1		# 输入interface，即网卡
prefix=$(ifconfig $interface | grep "inet " | cut -d 't' -f 2 | cut -d '.' -f 1-3)
# 循环ping某一个网卡
for addr in $(seq 1 254);do
	echo -n "$prefix.$addr :"
	arping -c 1 $prefix.$addr | grep "bytes from" | cut -d" " -f 5 | cut -d "(" -f 2 | cut -d ")" -f 1
done
```

> 基于`netdiscover`命令检测
>
> ```shell 
> netdiscover -i eth0 -r 192.168.1.0/24  # 探测192.168.1.0这个网段的IP，子网掩码是255.255.255.0
> ```

#### 被动收集

> 基于`netdiscover`: 就是干等着，反正局域网肯定会有数据包的
>
> ```shell
> netdiscover -p
> ```

> `hping3`: 可以进行`dos`攻击，通常服务器用来做压力测试，是一个命令行下使用的`TCP/IP`数据包组装/分析工具
>
> - 压力测试
>
> - ```shell
>   hping3 -c 1000 -d 120 -S -w 64 -p 80 --flood --rand-source xuegod.cn
>   # -c 数据包的数量
>   # -d 每个数据包的长度
>   # -S 设置 SYN 数据包，SYN 数据包和三次握手有关
>   # -w 流量
>   # -p 端口
>   # --flood 洪水式攻击，不管收没收到回复
>   # --rand-source 伪造IP，但是这个智能伪造局域网的IP地址，出了路由器，那就是真实IP地址了
>   # 所以容易被锁IP地址
>   ```

> `fping`查看局域网，可以对一个`IP`段扫描
>
> - ```shell
>   fping -g 192.168.152.0/24 -c 1 > fping.txt
>   # 会先发数据包，然后再接收
>   ```

#### 基于`Nmap`的扫描

> `Nmap` : `Network Mapper`, 最早是 `Linux` 下的网络扫描和嗅探工具
>
> - ```shell
>   nmap -sn 192.168.152.0/24
>   nmap -sn 192.168.152.1-254
>   # 表示只ping扫描，不会扫描端口
>   # 上面两个都一样的
>   ```

> 使用`nmap`进行半连接扫描：其实就是不进行完整的三次握手，完成两次即可
>
> - 不会留下记录
> - 全连接与半连接：显然，少了一次 `ACK` 的回应
>
> <center class="half">
> 	 <img src="https://gitee.com/lmz2498369702/image-repository/raw/master/202111302126513.png" height="200" /><img src="https://gitee.com/lmz2498369702/image-repository/raw/master/202111302126339.png" height="200"/>
> </center>
>
>
> - ```shell
>   nmap -sS 192.168.152.1 -p 80,81,82,21,500-600
>   # 不加 -sS就是全连接扫描
>   ```
>

> 使用`nc`扫描端口
>
> - `netcat`的简写
>
> - 实现任意`TCP/UDP`端口的侦听，可以作为服务端侦听端口
>
> - 端口的扫描，作为客户端发起`TCP/UDP`连接
>
> - 传输文件，网络测速等
>
> - ```shell
>   -nv # 表示扫描的目标是个IP地址不做域名解析
>   -w # 表示超时时间
>   -z # 表示进行端口扫描
>   ```

> 使用 `scapy` 定制数据包进行高级扫描
>
> - 是一个`python`脚本
>
> - ```shell
>   scapy # 直接进入
>   ```
>   
> - 定制 `ARP`协议
>
>   > ```python
>   > >>> ARP().display()
>   > ###[ ARP ]###
>   >   hwtype= 0x1  			# 硬件协议
>   >   ptype= IPv4			# 协议类型
>   >   hwlen= None			# 硬件地址长度（MAC）
>   >   plen= None			# 协议地址长度（IP）
>   >   op= who-has			# who-has 查询
>   >   hwsrc= 00:0c:29:d1:9c:bc		# 源 MAC 地址
>   >   psrc= 192.168.152.128			# 源 IP 地址
>   >   hwdst= 00:00:00:00:00:00		# 目标 MAC 地址
>   >   pdst= 0.0.0.0					# 向谁发送请求
>   > 
>   > 
>   > # 例如，定义向 192.168.152.1 发送 arp 请求数据包
>   > # sr1: 包含了发送和接收数据包的能力
>   > >>> sr1(ARP(pdst="192.168.152.1"))
>   > ```
>
> - 定制`ping`数据包
>
> > ```shell
> > >>> IP().display()
> > ###[ IP ]###
> >   version= 4    # 即 IPv4
> >   ihl= None		# 首部长度
> >   tos= 0x0		# 服务
> >   len= None		# 总长度
> >   id= 1			# 标识
> >   flags=		#
> >   frag= 0		# 标志
> >   ttl= 64		# 生存时间
> >   proto= hopopt		# 传输控制协议 IPv6 逐跳选项
> >   chksum= None		# 首部校验和
> >   src= 127.0.0.1	# 源地址
> >   dst= 127.0.0.1	# 目的地址
> >   \options\
> >   
> > >>> ICMP().display()
> > ###[ ICMP ]###
> >   type= echo-request   	# 类型，标识 ICMP 报文的类型
> >   code= 0				# 代码
> >   chksum= None			# 校验和
> >   id= 0x0				# 标识
> >   seq= 0x0				#
> > 
> > # 注意：IP()生成ping包的源IP和目标IP，ICMP()生ping包的类型。使用IP()和ICMP()两个函数，可以生成ping包
> > >>> sr1(IP(dst="192.168.152.1")/ICMP(),timeout=1)
> > ```
>
> - 定制`TCP`的`SYN`请求
>
> > ```shell
> > >>> TCP().display()
> > ###[ TCP ]###
> >   sport= ftp_data		# TCP源端口
> >   dport= http			# TCP目的端口
> >   seq= 0				# 32位序号
> >   ack= 0				# 32位确认序号
> >   dataofs= None			# 4位首部长度
> >   reserved= 0			# 保留6位
> >   flags= S				# 标志域、紧急标志、有意义的应答标志、推、重置连接标志、同步序列号标志、完成发送数据标志。按照顺序排列是：URG、ACK、PSH、RST、SYN、FIN
> >   window= 8192			# 窗口大小
> >   chksum= None			# 16位校验和
> >   urgptr= 0				# 优先指针
> >   options= []			# 选项
> >   
> >   >>> sr1(IP(dst="192.168.152.1")/TCP(flags="S",dport=80),timeout=1)
> > ```
> >
> > 