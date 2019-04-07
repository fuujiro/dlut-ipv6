# dlut-ipv6

Hello, Every Guys!

I'm sure that the new way use `IPv6` is more useful.

More detail, can see at [SS科学上网使用BBR加速 & 校园网IPv6免流](https://github.com/fuujiro/dlut-ipv6/blob/master/SS%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91%E4%BD%BF%E7%94%A8BBR%E5%8A%A0%E9%80%9F-%E6%A0%A1%E5%9B%AD%E7%BD%91ipv6%E5%85%8D%E6%B5%81.md)

But now, so sorry that this blog is only support **Chinese**.

---

利用ipv6免流蹭网，大连理工都气哭惹

### step 1 准备

open your terminal, macOS/Linux -> terminal, windows -> powershell

打开你的终端，macOS/Linux打开terminal, windows打开powershell

### step 2 配置

```
# 配置防火墙（只需运行一次）
    New-NetFirewallRule -DisplayName "Block IPv6 Router Advertisement" -Protocol ICMPv6 -IcmpType 134 -Action Block

# 配置路由（一路无脑按'y'）
    New-NetRoute -AddressFamily IPv6 -DestinationPrefix "::/0" -InterfaceAlias 以太网 -NextHop "fe80::2a0:a50f:fc7d:bf00" -RouteMetric 0 -Confirm -ErrorAction Ignore
    New-NetRoute -AddressFamily IPv6 -DestinationPrefix "::/0" -InterfaceAlias 以太网 -NextHop "fe80::2a0:a50f:fc7d:bf01" -RouteMetric 0 -Confirm -ErrorAction Ignore
    Get-NetRoute -AddressFamily IPv6 -InterfaceAlias 以太网 -DestinationPrefix "::/0"
```

**macOS & Linux maybe fail...**


like this：

就像这样：

![1](https://raw.githubusercontent.com/fuujiro/pictures/master/dlut-ipv6/1.png)

### step 3 验证

上 [ipv6测试网站](https://test-ipv6.com/index.html.zh_CN)

if like this // 如果这样子的话：

![2](https://raw.githubusercontent.com/fuujiro/pictures/master/dlut-ipv6/2.png)

    Congratulation！You success!

else // 非也

    You can push a issue at this repo~
