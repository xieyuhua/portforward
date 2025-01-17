使用golang实现的tcp udp端口转发

# 使用 iptables 配置端口转发
 假设我们要将外部访问的 TCP 端口 8080 转发到内网的某个服务器的 80 端口
```
echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf sysctl -p
iptables -t nat -A PREROUTING -p tcp --dport 8080 -j DNAT --to-destination 192.168.1.2:80 iptables -t nat -A POSTROUTING -j MASQUERADE
```


Fork https://github.com/csznet/goForward

目前已实现：

 - 规则热加载
 - web管理面板
 - 流量统计
 - 空闲时长断开连接设置
 - 端口白名单、黑名单配置
 - 端口批量转发，本地端口设置，如 80,443,3306,6379
 - 端口转发限速（todo）

**使用**
![419e60a3365740bb38a238c02c44ead](https://github.com/user-attachments/assets/8ddfcd77-0332-435e-ac65-3a4d4c3530e9)
![image](https://github.com/user-attachments/assets/8b5fda56-543a-4faf-9800-5db04ec9cfd3)

![image](https://github.com/xieyuhua/port-forward/assets/29120060/834cabc3-e461-4adb-a3eb-d8220fac9f5f)
![image](https://github.com/xieyuhua/port-forward/assets/29120060/3e026b8a-22a3-41cc-bbc7-a64cc568bc94)


运行
```
./goForward -h

  -debug
    	Print connection
  -pass string
    	Web Password
  -port string
    	Web Port (default "8889")

```

**参数**

设置web管理访问密码

```
./goForward  -port 8899 -pass 666
```

当24H内同一IP密码试错超过3次将会ban掉

## 开机自启

**创建 Systemd 服务**

```
sudo nano /etc/systemd/system/goForward.service
```

**输入内容**

```
[Unit]
Description=Start goForward on boot

[Service]
ExecStart=/full/path/to/your/goForward

[Install]
WantedBy=default.target
```

其中的```/full/path/to/your/goForward```改为二进制文件地址，后面可接参数

**重新加载 Systemd 配置**
```
sudo systemctl daemon-reload
```

**启用服务**
```
sudo systemctl enable goForward
```
**启动服务**
```
sudo systemctl start goForward
```
**检查状态**
```
sudo systemctl status goForward.service
```
