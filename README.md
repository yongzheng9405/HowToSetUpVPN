# HowToSetUpVPN

> How to use an Azure Virtual Machine to set up your own VPN on Windows.

## 1. Create a virtual machine in your own resource group in Azure.

1. Go to the Azure portal.
2. Size: `Standard B1ls` (1 vCPU, 0.5 GiB memory) is enough for personal use.
3. System: Ubuntu, the latest version. For example, Ubuntu 24.04 LTS.
4. Region: Use Southeast Asia (Singapore).
5. Connection: Set up your username and password. For example, username `dummyUser` and password `dummyPassword`.
6. Leave the other options as default values.
7. Open the virtual machine resource and get the IP address, for example `10.xxx.xxx.xx`.

## 2. Configure the networking of the virtual machine.

1. In the Azure portal, open your virtual machine resource and click **Networking** on the left side.
2. For inbound rules, add port `6789` (or any port you want). This port will be used by the VPN settings, so remember it.

## 3. Connect to the virtual machine with the username and password from step 1.

1. In Windows, open Terminal and run `ssh dummyUser@10.xxx.xxx.xx`, then enter `dummyPassword` when prompted.
2. Switch to the root user with `sudo su -`.
3. Follow the tutorial at https://itlanyan.com/v2ray-tutorial/ to set up your VPN. Remember to use the port from step 2 in `/usr/local/etc/v2ray/config.json`.
4. The basic process is:
   1. Run `bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)` to install V2Ray.
   2. Run `/usr/local/bin/v2ray uuid` to generate a unique ID. You will use it later.
   3. Run `vim /usr/local/etc/v2ray/config.json` to modify the config file:

      ```json
      {
        "inbounds": [
          {
            "port": 6789,
            "protocol": "vmess",
            "settings": {
              "clients": [
                {
                  "id": "id generated using /usr/local/bin/v2ray uuid"
                }
              ]
            }
          }
        ],
        "outbounds": [
          {
            "protocol": "freedom",
            "settings": {}
          }
        ]
      }
      ```

## 4. Enable the BBR algorithm (optional).

1. Connect to your virtual machine and check the kernel version with `uname -r`. Make sure it is `>= 4.9`.
2. Run `lsmod | grep bbr` and make sure `bbr` is not in the result.
3. Run `echo "net.core.default_qdisc=fq" | sudo tee --append /etc/sysctl.conf` and `echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee --append /etc/sysctl.conf`.
4. Run `sudo sysctl -p`.
5. Run `sudo sysctl net.ipv4.tcp_available_congestion_control` and `sudo sysctl net.ipv4.tcp_congestion_control`. If both commands return `bbr`, BBR is enabled.
6. Run `lsmod | grep bbr` and make sure `bbr` is in the result.
7. Reboot your virtual machine.
