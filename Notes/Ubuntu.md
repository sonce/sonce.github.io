### BBR加速
``` shell
lsb_release -a
apt-get update -y
apt-get upgrade -y
lsb_release -a

#BBR，查看内核
uname -r
sysctl net.ipv4.tcp_available_congestion_control
sysctl net.ipv4.tcp_congestion_control
echo net.core.default_qdisc=fq >> /etc/sysctl.conf
echo net.ipv4.tcp_congestion_control=bbr >> /etc/sysctl.conf
sysctl -p

#查看是否生效
lsmod | grep bbr
sysctl net.ipv4.tcp_available_congestion_control
sysctl net.ipv4.tcp_congestion_control
```

### Root登录
``` shell
echo root:newpasword|sudo chpasswd root
sudo -i
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin yes/g' /etc/ssh/sshd_config;
sudo sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication yes/g' /etc/ssh/sshd_config;

: > /root/.ssh/authorized_keys
cat /home/ubuntu/.ssh/authorized_keys >> /root/.ssh/authorized_keys
# 删除UBUNTU用户
deluser ubuntu --remove-home
sudo service sshd restart
```