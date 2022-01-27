

# 虚拟机ip


| 服务器         | ip           |
| -------------- | ------------ |
| ubuntu-desktop | 10.211.55.3  |
| ubuntu1804-a   | 10.211.55.10 |
| ubuntu1804-b   | 10.211.55.11 |
| ubuntu1804-c   | 10.211.55.12 |

 



# 网络配置

- ubuntu1804-b：10.211.55.11

```
# This is the network config written by 'subiquity'
#network:
#  ethernets:
#    enp0s5:
#      dhcp4: true
#  version: 2

# Let NetworkManager manage all devices on this system
network:
  version: 2
  #renderer: NetworkManager
  ethernets:
    enp0s5:
      addresses: [10.211.55.11/24]  # 固定ip/子网掩码（与宿主机保持一致,22表示255.255.252.0）
      gateway4: 10.211.55.1  # 网关（与宿主机保持一致）
      dhcp4: no  # no代表不是用dhcp动态获取ip，yes代表使用dhcp动态获取ip
      nameservers:
        addresses: [10.211.55.1]  # DNS
        #search: [localdomin]  # 虚拟机所在的domain（这项不用貌似也可以）
```



- ubuntu1804-c：10.211.55.12

```
# This is the network config written by 'subiquity'
#network:
#  ethernets:
#    enp0s5:
#      dhcp4: true
#  version: 2

# Let NetworkManager manage all devices on this system
network:
  version: 2
  #renderer: NetworkManager
  ethernets:
    enp0s5:
      addresses: [10.211.55.12/24]  # 固定ip/子网掩码（与宿主机保持一致,22表示255.255.252.0）
      gateway4: 10.211.55.1  # 网关（与宿主机保持一致）
      dhcp4: no  # no代表不是用dhcp动态获取ip，yes代表使用dhcp动态获取ip
      nameservers:
        addresses: [10.211.55.1]  # DNS
        #search: [localdomin]  # 虚拟机所在的domain（这项不用貌似也可以）
```

