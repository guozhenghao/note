# centos 7 最小版本安装
## 安装后网络配置
### 安装遇到错误
- Loaded plugins: fastestmirror
````
vim  /etc/yum/pluginconf.d/fastestmirror.conf 
enabled = 0

vim /etc/yum.conf
plugins=0

yum clean dbcache
````

- Could not retrieve mirrorlist
````
# 查看网卡
nmcli connection show

vi /etc/sysconfig/network-scripts/ifcfg-网卡
ONBOOT=yes

systemctl restart network
````