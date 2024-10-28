# Linux 命令

本文档记录开发过程中涉及到的常见命令。



---



## 网络

查看拥塞控制算法 

```bash
# 查看当前使用的拥塞算法：
sysctl net.ipv4.tcp_congestion_control
# 也可以结合grep
sysctl -a | grep tcp
# 查看系统上可用的所有拥塞控制算法
cat /proc/sys/net/ipv4/tcp_available_congestion_control
```





