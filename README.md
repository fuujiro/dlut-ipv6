# dlut-ipv6
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
