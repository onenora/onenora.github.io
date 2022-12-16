# Docker安装trojan-go

## 1.安装Docker 环境

```shell
curl -fsSL https://get.docker.com | bash -s docker
```

## 2.在当前目录创建trojan-go文件夹 

 `mkdir -p trojan-go` 

## 3.创建并映射trojan-go的配置文件config.json
```bash
cat > trojan-go/config.json <<EOF
{
    "run_type": "server",
    "local_addr": "0.0.0.0",
    "local_port": 443,
    "remote_addr": "1.1.1.1",
    "remote_port": 80,
    "password": [
        "your_awesome_password"
    ],
    "ssl": {
        "cert": "server.crt",
        "key": "server.key",
        "sni": "your-domain-name.com",
        "fallback_port": 443
    }
}
EOF
```
## 4 运行trojan-go

```bash
docker run -d --network host --name trojan-go --restart=always -v /trojan-go:/etc/trojan-go teddysun/trojan-go
```


