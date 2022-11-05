# Acme.sh申请免费证书


## 安装 acme.sh

- 运行安装脚本（不要求 Root 环境）

```bash
wget -O -  https://get.acme.sh | sh
```

安装过程不会污染已有的系统任何功能和文件, 所有的修改都限制在安装目录中: ~/.acme.sh/

- 然后重新载入一下 .bashrc
```bash
source ~/.bashrc
```

- 开启`acme.sh` 的自动升级

```bash
acme.sh --upgrade --auto-upgrade
```

## 证书申请

1.修改默认 CA 证书供应商`acme.sh --set-default-ca --server letsencrypt`

2.证书申请

- 2.1 _80 端口未占用_：
  
`acme.sh --issue -d www.yourdomain.com --standalone --keylength ec-256 --debug`

- 2.2 _80 端口占用_，使用 DNS 申请：

`acme.sh --issue --dns dns_cf -d www.yourdomain.com --keylength ec-256 --debug`

```bash
# 不推荐使用全局 API 密钥（最小权限原则）
# 使用新建一个cloudflare api 令牌:
nano ~/.acme.sh/account.conf
export CF_Token="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Account_ID="xxxxxxxxxxxxx"
export CF_Zone_ID="xxxxxxxxxxxxx"   //此项非必须
```
详见：[acme.sh wiki dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)

> ECC 证书的主要优势在于它的 Keysize 更小，意味着同等大小下安全性的提升和加密解密速度的加快。如 ECC-256bit 的强度大约相当于 RSA-3072bit 。

## 证书安装

```bash
 acme.sh --install-cert -d www.yourdomain.com \
--fullchain-file ~/docker/nginx/ssl/yourdomain.crt  \
--key-file  ~/docker/nginx/ssl/yourdomain.key  --ecc
```

将~/docker/nginx/ssl/改成你想要的路径即可，建议证书以.crt 结尾，密钥以.key 结尾
