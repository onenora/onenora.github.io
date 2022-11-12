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

`--debug`是可选参数,输出的信息更详细，用来排除错误。

如下，申请成功：

![](https://cdn.jsdelivr.net/gh/Altriabot/picgo@main/img/SCR-20221109-h5n.png)

配置文件位置` ~/.acme.sh/account.conf`可以用Vim或者nano 打开编辑

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

将~/docker/nginx/ssl/改成你想要安装到的路径即可，建议证书以.crt 结尾，密钥以.key 结尾。

## 为电报设置通知

一旦您同时拥有TG Bot API令牌和您希望机器人设置通知的聊天ID，

请在`~/.acme.sh/account.conf` 设置以下两个变量供通知钩子脚本使用：

```bash
export TELEGRAM_BOT_APITOKEN="..."   # Token returned by @BotFather during bot creation above.
export TELEGRAM_BOT_CHATID="..."     # Chat ID fetched above.
```

然后设置通知钩子：`acme.sh --set-notify --notify-hook telegram`

如下电报机器人通知设置成功：

![](https://cdn.jsdelivr.net/gh/Altriabot/picgo@main/img/20221109195751.png)

默认acme.sh每天自动检测证书是否过期，过期会自动续约，并安装到上一次安装证书的位置。

没过期，Renew Skipped。过期，续约通知 Success!!!

![](https://cdn.jsdelivr.net/gh/Altriabot/picgo@main/img/20221109200444.png)

详见：[acme.sh wiki notify](https://github.com/acmesh-official/acme.sh/wiki/notify)

## 其它

**想到再写吧！！**
- `acme.sh --set-notify --notify-level 3 --notify-mode 1`TG Bot通知频率设置设置为高，每天通知。默认续约成功才通知，现在是每天检测证书是否过期也会发通知,没过期会跳过续约。
