---
title: "Starting SSH"
---

# SSH Server
### Revision History

<table>
  <tr>
   <td>Version
   </td>
   <td>Description of Changes
   </td>
   <td>Date
   </td>
  </tr>
  <tr>
   <td>
	   1.0
   </td>
   <td>
	   Initial Version
   </td>
   <td>
	   12/27/2022
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td>
   </td>
  </tr>
</table>

### Start SSH Server

To enable the ssh daemon, login with the serial terminal and enter:
```
# systemctl start sshd
```

#### Autostart SSH Server on Reboot

To start the ssh server automatically after subsequent reboots, enter:
```
# systemctl enable sshd
```

#### Allow Clients to Connect Using RSA


Some ssh clients neeed top be configured explicitly allow to connect with rsa encryption. To do do so, create a file `~/.ssh/config` and add the following contents:

```
Host 192.168.1.15
    User git
    PubkeyAcceptedAlgorithms +ssh-rsa
    HostkeyAlgorithms +ssh-rsa
```

Where the IP address following `Host ` has to be the same as the one that MistySOM leased from your DHCP server. To find the address on MistySOM, type `# ip a` and look for the address listed under the interface `eth2:`

