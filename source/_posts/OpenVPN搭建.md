---
title: OpenVPN搭建
date: 2017-12-08 15:59:07
tags: [openvpn, linux]
categories: VPN搭建
---

&emsp;&emsp;<font size=4 color=red>注：</font>1. 本次搭建环境为archlinux arm平台， 但是通过本人多次实践以及对不同平台的了解，基本操作和原理完全相同。</br>
&emsp;&emsp;&emsp;&emsp; 2.OpenVPN 需要内核模块tun支持。使用命令 modprobe tun 检测是否有此模块，若无可能需要重新便宜linux kernel.

**Step 1: 安装**</br>
&emsp;&emsp;安装openvpn和easy-rsa。 
```
    pacman -S openvpn easy-rsa
```

**Step 2:证书生成**
```
    cp -r /etc/easy-rsa ~/easy-rsa     //将easyrsa配置拷贝到home目录
    cd ~/easy-rsa
    easy-rsa init-pki                  //初始化pki(Public Key Infrastructure)环境
    easy-rsa build-ca nopass           //生成根证书，即将自己作为CA，此处nopass选择不加密生成的密钥
    //正常的证书签发流程是：生成客户私钥以及向CA发起证书签发申请，然后由CA签发证书。
    //这里我们省略了gen-req和sign-req,使用一条命令生成证书。以上只是说明其内部实际是两个过程。
    easy-rsa build-server-full server nopass   //生成server端证书
    easy-rsa build-client-full client nopass   //生成client端证书
    easy-rsa gen-dh                   //生成Diffie-Hellman密钥交换协议的参数
    openvpn --genkey --secret pki/ta.key  //生成“HMAC firewall”,防止DoS攻击和UDP port flooding
```
&emsp;&emsp;至此，便生成了说需要的所有证书和密钥。</br>

**Step 3: server conf配置**
```
    sudo cp pki/ca.crt pki/issued/server.crt pki/ta.key pki/dh.pem pki/private/server.key /etc/openvpn/server/
    sudo cp /usr/share/openvpn/examples/server.conf /etc/openvpn/server
    sudo vim /etc/openvpn/server
```
&emsp;&emsp;以下为我的配置，可作为参考：
```
    port 1194
    proto udp6  #使用ipv6， 否则使用proto udp
    dev tun0
    ca ca.crt
    cert server.crt
    key server.key 
    dh dh.pem
    server 10.10.0.0 255.255.255.0
    ifconfig-pool-persist ipp.txt
    push "redirect-gateway def1 bypass-dhcp"
    push "dhcp-option DNS 114.114.114.114"
    push "dhcp-option DNS 8.8.8.8"
    duplicate-cn #如果每个client使用相同的证书，必须开启此项
    keepalive 10 120
    tls-auth ta.key 0
    cipher AES-256-CBC
    comp-lzo
    max-clients 20
    user nobody
    group nobody
    persist-key
    persist-tun
    status openvpn-status.log
    log openvpn.log
    verb 3
    mute 20
```

**Step 4: 测试**</br>
&emsp;&emsp;到目前为止，server基本配置完毕。临时启动server进行测试。
```
    sudo su
    cd /etc/openvpn/server
    openvpn --config /etc/openvpn/server/server.conf
```
&emsp;&emsp;然后查看是否有tun0网卡，以及网卡信息与配置是否一致。
```
    ifconfig
```

**Step 5: 通过VPN上网**</br>
&emsp;&emsp;现在我们的openvpn server已经可以接受client连入了，但是client还不能上网。所以需要将openvpn子网的连接转发到可上网的网卡，如eth0.
```
    sudo vim /etc/sysctl.d/99-sysctl.conf  #我使用的archlinux已经没有/etc/sysctl.conf了,已经改为了这种结构
```
&emsp;&emsp;配置如下：
```
    net.ipv4.ip_forward=1
```
&emsp;&emsp;然后就可以用iptable进行转发了。
```
    sudo su
    iptables -t nat -A POSTROUTING -s 10.10.0.0/24 -o eth0 -j MASQUERADE
    iptables-save > /etc/iptables/iptables.rules
    systemctl enable iptables    
```

**Step 6: 开机启动**</br>
&emsp;&emsp;OpenVPN在安装时自带system service，但是使用的是模板语法。在我的平台上，该模板名称为openvpn-server@.service。具体可到/usr/lib/systemd/system目录下查看。该模板使用%i对conf文件进行输入，所以在本平台上创建开机启动项如下(可根据实际情况修改)：
```
    sudo systemctl enable openvpn-server@server.service
```

**Step 7: Client配置**</br>
&emsp;&emsp;我的client配置采用windows上openvpn目录下sample-config中的client.ovpn作为模板，下面是我的配置，可作为参考，根据自身情况修改。
```
    client
    dev tun
    remote ipv6-address 1194 udp6  #使用ipv6时这样配置，1194是对应server的端口。
    remote ipv4-address 1194 udp   #ipv4配置
    
    ##如果只使用ipv4,使用下面格式即可
    #proto udp
    #remote ipv4-address 1194
    
    resolv-retry infinite
    nobind
    persist-key
    persist-tun
    ca ca.crt
    cert client.crt
    key client.key
    remote-cert-tls server
    tls-auth ta.key  #该文件与server端的相同
    cipher AES-256-CBC
    comp-lzo
    verb 3
    mute 20
    auth-nocache
```
&emsp;&emsp;在client使用前，需要将之前easy-rsa生成的ca.crt, client.key, client.crt, ta.key拷贝到客户端openvpn config目录下。

