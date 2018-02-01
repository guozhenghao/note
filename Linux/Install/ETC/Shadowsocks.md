# Shadowsocks

## 搭建 shadowsocks 服务端
1. shadowsocks 下载和安装
    - 下载`git clone https://github.com/shadowsocks/shadowsocks.git`
    - 切换分支: `git checkout origin/master -b master`
    - 安装 `sudo python setup.py install`
2. json配置文件参考
    ````
    {
        "server":"0.0.0.0",
        "server_port":50000,
        "password":"***",
        "timeout":300,
        "method":"aes-256-cfb",
        "fast_open":false,
        "workers": 1
    }
    ````
3. 启动服务: `ssserver -c /etc/shadowsocks/shadowsocks.json -d start`
4. 终止服务: `ssserver -c /etc/shadowsocks/shadowsocks.json -d stop`
5. 安全组或者防火墙打开
6. 建议:
    - 如果是自己搭建, 建议端口设置的随机一点. 不然会被其他的陌生人侦测到, 无节制的蹭网影响正常使用
    - 如果不想自己搭建,可以去 shadowsocks 官网购买账号, 很稳定. 一年16刀(需要翻墙才能访问)

## 如何连接
查看如下网址：

    `https://github.com/shadowsocks/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E`

ios可以使用wingy

## 客户端连接 socket 代理服务
1. win下,启动 shadowsocks客户端 会直接将socket代理转为HTTP代理.
1. linux下, 使用 `sslocal -c /etc/shadowsocks/shadowsocks.json -d start` 启动 本地socket代理
    - 如果系统不支持 socket代理,那么可以使用privoxy程序实现socket代理转http代理
1. 建议:
    - 使用 chrome 的 SwitchyOmega 插件, 支持 socket 代理, 如果不需要全局代理的话.
    - 使用 `auto switch` 模式. 这里推荐一个长期更新的代理规则列表 `https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt` ,可以很好的实现路由分流,提升访问速度
    - 建议在一台稳定的云服务器上将socket代理转为http代理后使用,原因如下: 在客户端直接使用socket代理或者转http代理时,如果断网或者休眠等情况会导致本地socket/http代理服务断开或者其他问题, 从而需要重启代理服务或者客户端才能正常使用. 而在服务器上搭建好后,本地不论重启还是断网, 只要服务器正常运行,端口不变,则本地不需要额外操作也可以正常使用. 而且,如此也可以多台机器同时使用一个 shadowsocks 账户(虽然影响网速)

### 搭建HTTP代理服务
> 使用 privoxy 程序实现
1. 下载privoxy, 并安装
2. 配置 config 文件,添加以下行,实现socket代理转http代理
    ````
    # 监听socket服务的端口
    forward-socks5t / 127.0.0.1:1080    .
    # http服务的监听端口,建议取一个随机的五位数值
    listen-address  0.0.0.0:8118
    ````
3. 启动privoxy服务 `systemctl restart privoxy`
4, 注意防火墙不要禁用相关端口

## 问题
1. `0.0.0.0` 是什么
    - [参考网站](http://www.cnblogs.com/hnrainll/archive/2011/10/13/2210101.html)
    - RFC: 0.0.0.0/8 Addresses in this block refer to source hosts on "this" network. Address 0.0.0.0/32 may be used as a source address for this host on this network; other addresses within 0.0.0.0/8 may be used to refer to specified hosts on this network _([RFC1122], Section 3.2.1.3)_
    - 根据RFC文档描述，它不只是代表本机，0.0.0.0/8可以表示本网络中的所有主机，`0.0.0.0/32` 可以用作本机的源地址，`0.0.0.0/8`也可表示本网络上的某个特定主机,综合起来可以说`0.0.0.0` 表示整个网络
    - `0.0.0.0` 表示当前网络(仅作为源地址有效). [参考](https://en.wikipedia.org/wiki/IPv4)
    - 在路由器配置中可用`0.0.0.0/0`表示默认路由，作用是帮助路由器发送路由表中无法查询的包
    - 通过测试，可得知ECS是不能ping通自身的公网IP