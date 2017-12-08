# 更改Docker源

## 国内源
在`/etc/docker/daemon.json`中写入：
````
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
````
然后重启
````
sudo systemctl daemon-reload
sudo systemctl restart docker
````

## 阿里源
将下面代码直接复制在命令行：
````
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://uiqbexkb.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
````