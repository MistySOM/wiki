# Configuration

To connect to a Wifi access point, we need to provide the `wpa_supplicant` with the SSID and passphrase using the command below:

    mkdir /etc/wpa_supplicant
    wpa_passphrase {YOUR-SSID} {YOUR-PASSPHRASE} > /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
    
- In case you want to scan for every SSID in your environment, you can use this command:

      iw wlan0 scan | egrep -w "signal:|SSID:" | paste -d ' ' - - | sort
      
  Here is the example output:
  
        signal: -42.00 dBm      SSID: MistyWest
        signal: -43.00 dBm      SSID: MistyDeploy
        signal: -43.00 dBm      SSID: MistyGuest
        signal: -60.00 dBm      SSID: TELUS1924
        signal: -64.00 dBm      SSID: TELUSE55B
        signal: -70.00 dBm      SSID: TELUSB840
        signal: -71.00 dBm      SSID: SHAW-714340

    
After the configuration file is created, enable the two services below to automatically connect to the SSID on each boot:
    
    systemctl enable --now wpa_supplicant@wlan0
    systemctl enable --now dhcpcd@wlan0
    
# Check Connection

To check if both services are working, you can check the output of `ip a` command and make sure your wlan0 device obtained a correct IP:

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
        inet 192.168.9.115/24 brd 192.168.9.255 scope global dynamic wlan0     <------------- This line should exist
           valid_lft 560sec preferred_lft 271sec

Additionally, the `ping mistywest.com` command should show you that the domain name is resolved to a destination IP and you should receive responses once per second:

    PING mistywest.com (172.67.213.204): 56 data bytes
    64 bytes from 172.67.213.204: seq=0 ttl=59 time=27.031 ms
    64 bytes from 172.67.213.204: seq=1 ttl=59 time=20.089 ms
    64 bytes from 172.67.213.204: seq=2 ttl=59 time=17.214 ms
    64 bytes from 172.67.213.204: seq=3 ttl=59 time=22.551 ms
    
# Troubleshooting

TBD
