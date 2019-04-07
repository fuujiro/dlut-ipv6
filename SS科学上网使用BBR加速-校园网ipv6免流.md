
# SS科学上网使用BBR加速 & 校园网IPv6免流

### 1. 搭建ss服务器端

#### 1.1 前期准备工作

##### 1.1.1 领抵金券

我们可以选用一家美国的代理商[Digital Ocean](https://cloud.digitalocean.com)

![0](https://raw.githubusercontent.com/fuujiro/pictures/master/BBR/0.png)

> Q: 为什么选这家？
> A: 因为如果你是学生，你可以上GitHub领取你的[student pack](https://education.github.com/pack)，送你免费`50`🔪

你注册需要验证你的校园邮箱，验证这个步骤相信会点英文都能搞定，不废话，

注册好后，可以下拉找到合作伙伴`Digital Ocean`领取`50$`的抵金券。

##### 1.1.2 注册`Digital Ocean`

注册一个账户，这个过程中有个验证身份环节。

需要你用信用卡 / PayPal 付`5$`来说明自己不是个褥羊毛的。

所以，建议你绑定一张卡到PayPal上。完成。

进入界面后，点击`Billing`，会有输入`promote code`这里，

贴上`GitHub`的抵金券，你会发现你的账户余额有`55$`。

我们只需要最便宜的服务器，即`5$`/m，所以够你用11个月。

#### 1.2 搭建步骤

##### 1.2.1 配置选择

* 选择Ubuntu 18.04 - 64位系统

* `5$`/m的最低配置

* 选择`San Francisco`节点

* 勾选`IPv6` (ssh免密登录，看自己有没有这个需求)

##### 1.2.2 服务器配置参数

打开你的终端（macOS/Linux：Terminal，Windows：自己找，似乎有个叫Putty很好用）

```bash
ssh -p 22 hostname@host # 替换hostname和host成你自己的
```

首次登陆，会提示你载入你的ipconfig list，输入`yes`就行。

接着，输入密码后，进入服务器，身份是root。

接着，输入一系列命令：

```bash
apt-get update # 更新软件源
apt-get install python-gevent python-pip python-m2crypto python-wheel python-setuptools # 安装pip以及包
pip install shadowsocks #安装shadowsocks
```

完成者一系列命令后，进入服务器参数阶段：

```bash
{
    "server":"your_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"yourPassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

注：如果你要使用`chacha20`作为加密方式，记得要安装 libsodium

```bash
apt-get update
apt-get install libsodium-dev
```

最后是启动：

```bash
ssserver -c /etc/shadowsocks.json -d start # 启动
ssserver -c /etc/shadowsocks.json -d stop # 停止（一般没有用到过）
```

提示`started`后，说明你已经可以科学上网了。

### 2. BBR加速

> TCP BBR 是Google给出的一个改良版的tcp网络协议，相当于在已有TCP协议的基础上打了个补丁的意思，这个改良版TCP协议对拥塞控制有很好的支持，对于网络较差的环境有不错的应用场景。

因为bbr协议仅支持Linux系统内核版本大于4.9，而Ubuntu18.04的内核是4.15，已满足条件，比起之前的`Ubuntu LTS`版本需要更换内核，`18.04`可以直接启用bbr功能。

#### 2.1 修改系统变量

```bash
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
```

![1](https://raw.githubusercontent.com/fuujiro/pictures/master/BBR/1.png)

#### 2.2 保存生效，配置内核

```bash
sysctl   -p
```

![2](https://raw.githubusercontent.com/fuujiro/pictures/master/BBR/2.png)

#### 2.3 查看内核是否已开启BBR

```bash
sysctl net.ipv4.tcp_available_congestion_control
```

![3](https://raw.githubusercontent.com/fuujiro/pictures/master/BBR/3.png)


```bash
sysctl net.ipv4.tcp_congestion_control
```

![4](https://raw.githubusercontent.com/fuujiro/pictures/master/BBR/4.png)

#### 2.4 验证BBR是否已经启动

```bash
lsmod | grep bbr
```

![5](https://raw.githubusercontent.com/fuujiro/pictures/master/BBR/5.png)

### 3. 校园网`IPv6`免流

在满足第一部分后，即可实现`IPv6`免流

搭建好后需要修改一下ss配置文件，一般ss的配置文件是这样的（配置文件路径在 /etc/shadowsocks.json）：


```bash
{
    "server":"your_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"yourPassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

将`your_server_ip`改成`::`即可，即：

```bash
{
    "server":"::",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"yourPassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

`::`的意思是同时监听IPv4和IPv6。之后重启ss：`ssserver -c /etc/shadowsocks.json -d restart`。

至此，服务器就支持ss的IPv6访问了。

还可以对相应的软件，去实现`Socks5`代理，`IPv4`转`IPv6`代理免流。

支持的软件有：

* TIM
* 网易云音乐
* 迅雷
* 百度网盘
* etc

都可以在设置中，照上面的配置填入即可。

### 4. 参考链接

1. [DigitalOcean+Shadowsocks VPS搭建 | Mac端+iOS端全自动科学上网方案](http://daipei.me/posts/cross_firewall_with_digitalocean_shadowsocks/)
2. [Shadowsocks 服务器搭建比较全的纪录](https://kylindc.github.io/post/63883/)
3. [Ubuntu 18.04开启TCP网络协议BBR加速的方法（Google BBR 拥塞控制算法）](https://www.cnblogs.com/devilmaycry812839668/p/10353621.html)
4. [vps+ss实现校园网IPv6免流](https://www.flyzy2005.com/tech/ss-ipv6-no-traffic/)

