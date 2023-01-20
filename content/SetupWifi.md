# Setup WiFi

After you plug the supported WiFi adapter into the USB port, you can manually bring up the device with `ifconfig wlan0 up`. Confirm with `ifconfig` that your device is up:
```
# ifconfig
lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:96 errors:0 dropped:0 overruns:0 frame:0
          TX packets:96 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:6048 (5.9 KiB)  TX bytes:6048 (5.9 KiB)

wlan0     Link encap:Ethernet  HWaddr 18:A6:F7:0A:65:2D
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```
## Scan

In case you want to scan for every Wifi access point in your environment, you can use the command below to list their SSIDs:

    iw wlan0 scan | egrep -w "signal:|SSID:" | paste -d ' ' - - | sort
      
If your Wifi module is working correctly, the output of the command above should be similar to this:

    signal: -42.00 dBm      SSID: MistyWest
    signal: -43.00 dBm      SSID: MistyDeploy
    signal: -43.00 dBm      SSID: MistyGuest
    signal: -60.00 dBm      SSID: TELUS1924
    signal: -64.00 dBm      SSID: TELUSE55B
    signal: -70.00 dBm      SSID: TELUSB840
    signal: -71.00 dBm      SSID: SHAW-714340

# Configuration

To connect to a specific Wifi access point, you need to provide the `wpa_supplicant` with the SSID and its passphrase using the command below:

    mkdir /etc/wpa_supplicant
    wpa_passphrase {YOUR-SSID} {YOUR-PASSPHRASE} > /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
    
The newly created `wpa_supplicant-wlan0.conf` file will look like this:

    network={
            ssid="{YOUR-SSID}"
            #psk="{YOUR-PASSPHRASE}"
            psk={64-hash-characters}
    }
    
In case you are using WPA-Enterprise or other advanced configuration, see the [wpa_supplicant.conf(5) examples](https://man.archlinux.org/man/wpa_supplicant.conf.5) and modify the file accordingly.
    
# Connect

After the configuration file is ready, enable the two services below to automatically connect to the SSID on each boot:
    
    systemctl enable --now wpa_supplicant@wlan0
    systemctl enable --now dhcpcd@wlan0
    
### Need a Static IP?
You will most likely be using a dynamic IP address and the DHCP client service would obtain the IP per each connection. In case you want to use a static IP, create or edit the `/etc/dhcpcd.conf` file. For example:

    interface wlan0
    static ip_address=192.168.1.10/24	
    static routers=192.168.1.1
    static domain_name_servers=192.168.1.1 8.8.8.8
    
More complicated configurations are possible, for example combining with the `arping` option. See [dhcpcd.conf(5)](https://man.archlinux.org/man/dhcpcd.conf.5) for details.

A restart will be required for the new configuration to work.
    
# Check Connection

To check if you are on a working connection, you can check the output of `ip a` command and make sure your wlan0 device obtained a correct IP:

    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
    2: can0: <NOARP40000> mtu 72 qdisc noop qlen 10
        link/[280] 
    3: can1: <NOARP40000> mtu 72 qdisc noop qlen 10
        link/[280] 
    4: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop qlen 1000
        link/ether 06:87:9a:a2:e3:4f brd ff:ff:ff:ff:ff:ff
    5: eth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop qlen 1000
        link/ether a2:69:d7:df:09:5d brd ff:ff:ff:ff:ff:ff
    6: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue qlen 1000
        link/ether 18:a6:f7:0a:65:2d brd ff:ff:ff:ff:ff:ff
        inet 192.168.1.10/24 brd 192.168.9.255 scope global dynamic wlan0     <------------- This line should exist
           valid_lft 560sec preferred_lft 271sec

Additionally, the `ping mistywest.com` command should show you that the domain name is resolved to a destination IP and you should receive responses once per second:

    PING mistywest.com (172.67.213.204): 56 data bytes
    64 bytes from 172.67.213.204: seq=0 ttl=59 time=27.031 ms
    64 bytes from 172.67.213.204: seq=1 ttl=59 time=20.089 ms
    64 bytes from 172.67.213.204: seq=2 ttl=59 time=17.214 ms
    64 bytes from 172.67.213.204: seq=3 ttl=59 time=22.551 ms
    
# Troubleshooting

TBD
