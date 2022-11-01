# PagerMaid_Pyro的搭建教程


## 前提
- 宿主机已经安装 docker
- 未安装请使用官方脚本安装 `curl -fsSL https://get.docker.com | bash -s docker`
- 无需配置更多环境依赖,打开即用～
- 基于此docker image仅264MB:
| 名称 | 版本 | 说明 |
| :----: |:----: |:----: |
| altriabot/pagermaid_pyro | 2.0 |仅支持 AMD64 |

## 使用方法

`docker run -itd --name pgmpyro --privileged=true --restart=always altriabot/pagermaid_pyro:2.0`

## 配置 PagerMaid-Pyro

说明：配置 PagerMaid-Pyro 过程中使用的命令，都是在宿主机上执行。不需要进入容器(Container)内的 shell。

在宿主机，使用 nano 编辑配置文件 config.yml 中的 2 个值 **(api_id 和 api_hash)** 以后，
即可使用 `python3 -m pagermaid` 尝试跑起来了。

```shell
docker exec -it pgmpyro nano config.yml
docker exec -it pgmpyro python3 -m pagermaid
```

根据提示填写信息后，会提示你在 TG 客户端内输入 ,help 指令，根据提示输入后，如果能看到消息被编辑，Ctrl+C 结束掉即可。

- 进程守护：一定要依次输入这三条代码
- 此步骤可以方便 pagermaid_pyro 的自动运行，而无需在 pagermaid_pyro 意外退出后重新登录主机进行操作。

```shell
docker exec -it pgmpyro systemctl daemon-reload
docker exec -it pgmpyro systemctl start pagermaid_pyro.service
docker exec -it pgmpyro systemctl enable pagermaid_pyro.service
```

若 PagerMaid-Pyro 在长期运行后遇到未知原因而无法正常运行的情况，需要手动重启。命令如下：

`docker exec -it pgmpyro systemctl restart pagermaid_pyro.service`

**请注意保护好您已登录的 pagermaid.session,此文件可以进行账号所有操作，请不要分享给他人使用。**

## 扩展

- [官方教程](https://xtaolabs.com/)
- [PagerMaid-Pyro](https://github.com/TeamPGM/PagerMaid-Pyro) _新的项目_



