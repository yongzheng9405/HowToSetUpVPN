# HowToSetUpVPN
How to use Azure Virtual Machine to set up your own VPN(for Windows machine)

1. Creat a virtual machine in your own resource group in Azure. Go to Azure portal.
    1. Size: Standard_B2s is enough for personal use.
    2. System: Ubuntu, the lastest version. For example, Ubuntu 20.04.4 LTS.
    3. Region: Use East Asia, since you are in China, this region is close to you.
    4. Connection: Set up your username and password. Let's say, username is dummyUser, password is dummyPassword.
    5. Leave other options as default value.
    6. Go the virtual machine resource, you will get the IP address, e.g. 10.xxx.xxx.xx
2. Config the networking of the virtual machine.
    1. Go to Azure portal, under your virtual machine resource, click Networking on left side. For inbound rules, add 8388 and 8389 port, which will be used by VPN settings.
3. Connect to virtual machine with 1.3 username and password.
    1. In Windows, open terminal and type in `ssh dummyUser@10.xxx.xxx.xx` and type in `dummyPassword` when it needs.
    2. Check the system version, and install Docker, I follow the instruction: https://www.runoob.com/docker/ubuntu-docker-install.html
    3. Docker run, `sudo docker run --name ss-libev -e PASSWORD=<password>-e SERVER_PORT=8388 -p8388:8388 -e ARGS=--fast-open -e TZ=Asia/Shanghai -d --restart always shadowsocks/shadowsocks-libev`, this command will set up the VPN setting. Repleace <password> with your own password, e.g. 1234567, this password is for VPN, it can be different from virtual machine password. Thanks to Shadowsocks!
    4. Check the port is working, `sudo docker ps -a`, you will find the port is working now.
    5. If you want to speed up the networking, you can use BBR, this will be explained later.
 4. Install Shadowsocks.
    1. Go to https://github.com/shadowsocks/shadowsocks-windows/releases and download the latest release.
    2. Extract all files from the zipped folder. Double click shadowsocks.exe.
    3. Add virtual machine IP address, port, and password(in step 3.3).
    4. Open global proxy, then you can watch videos from Youtube.
 5. BBR algorithm.
    1. Connect your virtual machine, check version, `uname -r`, make sure it is >= 4.9.
    2. Type in `lsmod | grep bbr` and make sure bbr is not in result.
    3. Type in `echo "net.core.default_qdisc=fq" | sudo tee --append /etc/sysctl.conf` and `echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee --append /etc/sysctl.conf`.
    4. Type in `sudo sysctl -p`.
    5. Type in `sudo sysctl net.ipv4.tcp_available_congestion_control` and `sudo sysctl net.ipv4.tcp_congestion_control`, if both command return some bbr result, that means you are using BBR algorithm to accelerate networking.
    6. Type in `lsmod | grep bbr` and make sure bbr in in result.
    7. Reboot your virtual machine!
