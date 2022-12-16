# PagerMaid_Pyro的搭建教程

> 有幸抢到一台ARM，更新下pagermaid_pyro3.0，支持ARM架构

## 前提
- [x] 注意:warning:,这是手动教程，要自动的可以关闭本教程了
- [x] 宿主机已经安装 docker
- [x] 未安装请使用官方脚本安装 `curl -fsSL https://get.docker.com | bash -s docker`
- [x] 无需配置更多环境依赖,打开即用～
- [x] 基于此docker image仅264MB:

| 名称 | 版本 | 说明 |
| :----: |:----: |:----: |
| altriabot/pagermaid_pyro | 2.0 |支持 AMD64 |
| altriabot/pagermaid_pyro | 3.0 |支持 ARM64 |

## 使用方法

AMD:

`docker run -itd --name pgmpyro --privileged=true --restart=always altriabot/pagermaid_pyro:2.0`

ARM:

`docker run -itd --name pgmpyro --privileged=true --restart=always altriabot/pagermaid_pyro:3.0`

## 配置 PagerMaid_Pyro

> 说明：配置 PagerMaid_Pyro 过程中使用的命令，都是在宿主机上执行。不需要进入容器(Container)内的 shell。

在宿主机，执行以下2条命令：
### 1. 填写 **api_id 和 api_hash**
```shell
docker exec -it pgmpyro nano config.yml
```
键盘️:arrow_up::arrow_left::arrow_right::arrow_down: 控制光标

`Ctrl+O`，保存当前文件

提示`File Name to Write:config.yml` 按回车**Enter**确认

![](https://cdn.jsdelivr.net/gh/onenora/picgo@main/img/202211051812417.png)

`Ctrl+X`，退出编辑文本

- tips：如果直接Ctrl+X，会提示`Save modified buffer?`按 **Y** 确认后再回车即可

![](https://cdn.jsdelivr.net/gh/onenora/picgo@main/img/202211051812700.png)

### 2. 登陆TG
```shell
docker exec -it pgmpyro python3 -m pagermaid
```
根据提示填写信息后，会提示你在 TG 客户端内输入 ,help 指令，根据提示输入后，如果能看到消息被编辑，
![](https://raw.githubusercontent.com/onenora/picgo/main/img/202211051804964.png)
Ctrl+C 结束掉即可。

### 3. 进程守护
- **一定要依次输入这三条代码**
- 此步骤可以方便 pagermaid_pyro 的自动运行，而无需在 pagermaid_pyro 意外退出后重新登录主机进行操作。

```shell
docker exec -it pgmpyro systemctl daemon-reload
docker exec -it pgmpyro systemctl start pagermaid_pyro.service
docker exec -it pgmpyro systemctl enable pagermaid_pyro.service
```
### 4. 其它
> :hand:Stop不用继续执行下面这个命令了

***当且仅当你的PagerMaid_Pyro长期运行后，遇到未知原因而无法正常启动的情况***，需要手动重启。命令如下：

`docker exec -it pgmpyro systemctl restart pagermaid_pyro.service`

- **请注意保护好您已登录的 pagermaid.session,此文件可以进行账号所有操作，请不要分享给他人使用。**

## 扩展

- [官方教程](https://xtaolabs.com/)
- [PagerMaid-Pyro](https://github.com/TeamPGM/PagerMaid-Pyro) _新的项目_
- [ixiaocai的PGM教程](https://blog.ixiaocai.net/2022/05/28/PagerMaid-ARM-Docker.html) _基于Docker AMD/ARM适用_
- [owo PGM 教程](https://owo.cab/32) _基于宿主机_

