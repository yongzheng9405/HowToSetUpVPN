# HowToSetUpVPN
How to use Azure Virtual Machine to set up your own VPN(for Windows machine)

1. Creat a virtual machine in your own resource group in Azure. Go to Azure portal.
    1. Size: Standard B1ls (1 vcpu, 0.5 GiB memory) is enough for personal use.
    2. System: Ubuntu, the lastest version. For example, Ubuntu 20.04.4 LTS.
    3. Region: Use East Asia, since you are in China, this region is close to you, East Asia is Hong Kong, and southeast asia is Singapore.
    4. Connection: Set up your username and password. Let's say, username is `dummyUser`, password is `dummyPassword`.
    5. Leave other options as default value.
    6. Go the virtual machine resource, you will get the IP address, e.g. 10.xxx.xxx.xx
2. Config the networking of the virtual machine.
    1. Go to Azure portal, under your virtual machine resource, click Networking on left side. For inbound rules, add 8388 and 8389 port(whatever you want), which will be used by VPN settings, remember the port.
3. Connect to virtual machine with 1.3 username and password.
    1. In Windows, open terminal and type in `ssh dummyUser@10.xxx.xxx.xx` and type in `dummyPassword` when it needs.
    2. Use root user, type in `sudo su -` to let you become a root user.
    3. Follow the instruction https://itlanyan.com/v2ray-tutorial/ to setup your vpn. Remember use the port in Step 2 to the /usr/local/etc/v2ray/config.json file.
 4. BBR algorithm (optional).
    1. Connect your virtual machine, check version, `uname -r`, make sure it is >= 4.9.
    2. Type in `lsmod | grep bbr` and make sure bbr is not in result.
    3. Type in `echo "net.core.default_qdisc=fq" | sudo tee --append /etc/sysctl.conf` and `echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee --append /etc/sysctl.conf`.
    4. Type in `sudo sysctl -p`.
    5. Type in `sudo sysctl net.ipv4.tcp_available_congestion_control` and `sudo sysctl net.ipv4.tcp_congestion_control`, if both command return some bbr result, that means you are using BBR algorithm to accelerate networking.
    6. Type in `lsmod | grep bbr` and make sure bbr in in result.
    7. Reboot your virtual machine!
