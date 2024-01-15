---
title: WSL2 設定固定 IP
date: 2023-04-29 21:00:00
categories: Wsl2
tags: [Wsl2]
---

# 設定 DNS
```bash
# 刪除原始的 resolv.conf 設定
sudo rm /etc/resolv.conf

# 建立 resolv.conf 設定，加入自訂 DNS
sudo bash -c 'echo "nameserver 192.168.50.1" > /etc/resolv.conf'

# 加入 Google 的 DNS 加速
sudo bash -c 'echo "nameserver 8.8.8.8" >> /etc/resolv.conf'
sudo bash -c 'echo "nameserver 8.8.4.4" >> /etc/resolv.conf'

# 設定 /etc/wsl.conf
sudo bash -c 'echo "[network]" > /etc/wsl.conf'
sudo bash -c 'echo "generateResolvConf = false" >> /etc/wsl.conf'

# 鎖定 resolv.conf 設定
sudo chattr +i /etc/resolv.conf
```

<!--more-->

# Powershell 設定 Ububtu 網卡
```powershell
# 搜尋 eth0 網卡 IP 設定
$ipAddr = wsl -u root /bin/bash -c "ip addr show eth0 | grep 'inet\b'"

$ipAddr = $ipAddr.Trim().Split(" ")[1]

# 確認是否為自訂的網卡設定
if(!$ipAddr.Contains("192.168.50.2")){
  # 移除 Ububtu 網卡設定
  wsl -u root /bin/bash -c "ip addr del $ipAddr dev eth0"
  
  # 加入自訂的網卡
  wsl -u root /bin/bash -c "ip addr add 192.168.50.2/24 broadcast 192.168.50.255 dev eth0"
  
  # 加入自訂的路由
  wsl -u root /bin/bash -c "ip route add 0.0.0.0/0 via 192.168.50.1 dev eth0"
}
```
<!-- sudo ip addr del $(ip addr show eth0 | grep 'inet\b' | awk '{print $2}' | head -n 1) dev eth0 -->
<!-- sudo ip addr add 192.168.50.2/24 broadcast 192.168.50.255 dev eth0 -->
<!-- sudo ip route add 0.0.0.0/0 via 192.168.50.1 dev eth0 -->

# Powershell 設定 vEthernet (WSL)
```powershell
# 確認是否為自訂的 vEthernet (WSL) 設定
if(!(Get-NetAdapter 'vEthernet (WSL)' | Get-NetIPAddress).IPAddress.Contains('192.168.50.1')){
  # 移除 vEthernet (WSL)設定
  Get-NetAdapter 'vEthernet (WSL)' | Get-NetIPAddress | Remove-NetIPAddress -Confirm:$False
  
  # 加入自訂的 vEthernet (WSL)
  New-NetIPAddress -IPAddress 192.168.50.1 -PrefixLength 24 -InterfaceAlias 'vEthernet (WSL)'
  
  # 移除 WSLNat 設定
  Get-NetNat | ? Name -Eq WSLNat | Remove-NetNat -Confirm:$False
  
  # 加入自訂的 WSLNat
  New-NetNat -Name WSLNat -InternalIPInterfaceAddressPrefix 192.168.50.0/24;
}
```

{% note warning %}
📜 參考資料
1. [知乎-給 WSL2 設置靜態IP地址](https://zhuanlan.zhihu.com/p/380779630)
{% endnote %}