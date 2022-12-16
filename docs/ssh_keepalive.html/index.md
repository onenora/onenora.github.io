# SSH“保活”措施

## 方法1 脚本

&#8195;&#8195;IT 开发、运维、调试等工作经常需要 ssh 登录远程服务器。现在很多 ssh server 端设置了空闲超时自动断开。例如，人离开大于 30 分钟无操作，服务器判断空闲时间超过，于是断开连接，其实这很不利于开发调试工作的连续性。用户往往没有服务端配置权限。此外，客户端 xshell，putty 等的”保持活动状态“功能企图解决这个问题，但不一定能生效。例如，国内使用堡垒机登录的情况越来越普遍，访问一台内部服务器实际都经过了堡垒机的转发，堡垒机判断空闲超时，从而导致 ssh”保活“措施对内部服务器不起作用。

&#8195;&#8195;这里给出一个通用的解决办法。ssh 登录之后，在 bash shell 环境执行。

```bash
while true;do \
  for x in $(ls -1 /dev/pts |grep "^[0-9]");do \
    echo -en "\0x07" > /dev/pts/$x;\
  done;\
  sleep 1777;\
done &
```

&#8195;查看 ssh 登录情况，

```shell
$ who
root     tty1         2021-01-07 14:38
root     pts/0        2022-01-07 17:55 (192.168.1.3)
root     pts/2        2022-01-07 07:28 (192.168.1.3)
```

- 其中 pts/0，pts/5，pts/6，pts/7 都是 ssh 登录终端设备。

**说明:**

1. 若要 ssh session 保持活跃，只要在规定时间间隔内接收到的字节数不为零，则该 session 不会被服务端认为空闲。

2. 向 pts 设备随意发送数据会影响 ssh 终端窗口的显示，例如在一个窗口用 wall 命令发消息而用户正在其他终端窗口 vim 编辑文档，在编辑区域会出现广播过来的消息，对用户工作造成一定干扰。为避免该问题，向 pts 设备发送 BEL（振铃），该字符没有任何回显，也就不会把显示搞乱。

3. 设置发送间隔小于服务端设定的空闲间隔。例如，服务端设置半小时自动断开，那么我们设置 1777 秒是足够的。

4. 如果同时用了 expect 自动登录，登录之后一般不要改变终端窗口大小，因为 expect 缺省忽略 SIGWINCH 信号，窗口 size 变化导致 VIM 编辑器排版混乱。

## 方法2 配置客户端
修改客户端中的 ssh 连接配置：
本地ssh每隔30s向server端sshd发送keep-alive包，如果连续发送60次，server仍然无回应断开连接

```shell
 #编辑.ssh目录下config文件
cat <<EOF > ~/.ssh/config 
host *
    ServerAliveInterval 30
    ServerAliveCountMax 60
EOF
```
然后重启本地ssh：`sudo service ssh restart `

## 方法3 配置服务器端

可以在服务端配置，让server每隔30秒向client发送一个keep-alive包来保持连接：`sudo vim /etc/ssh/sshd_config`
添加以下内容：
```shell
ClientAliveInterval 30
ClientAliveCountMax 60

```
然后重启本地ssh：`sudo service ssh restart `
第一行配置让server每隔30秒向client发送一个keep-alive包来保持连接；第二行配置表示如果连续发送keep-alive包数量达到60次，客户端依然没有反应，则服务端sshd断开连接。如果什么都不操作，该配置可以让连接保持30s*60，即30分钟

