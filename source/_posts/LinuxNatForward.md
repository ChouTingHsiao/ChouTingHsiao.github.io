---
title: 使用 NAT 轉發請求
date: 2024-07-25 18:00:00
categories: Linux
tags: [Linux, Ubuntu, Nat]
sticky: 100
---

# iptables 介紹

## 封包進入規則

* ***PREROUTING鏈*** (通常用於DNAT)<br> 處理剛到本機並在路由轉送前的資料包，它會轉換封包中的目標IP位址

* ***INPUT鏈*** <br> 處理入站資料包

* ***FORWARD鏈*** (不包含 NAT)<br> 處理轉送封包

* ***OUTPUT鏈*** <br> 處理出站資料包

* ***POSTROUTING鏈*** (通常用於SNAT)<br> 處理即將離開本機的資料包，它會轉換封包中的來源IP位址

<!--more-->

## 封包處理方式

* ***DNAT***<br>發生在 natTable 的 PREROUTING 鏈上，用於修改資料包的目標 IP 位址，在剛接收到封包的之後一步，對應的是 inbound 流量

* ***SNAT***<br>發生在 natTable 的 POSTROUTING 鏈上，要求您為其提供一個靜態 IP 位址以套用於傳出封包，在傳送到網路卡前的最後一步，對應的是 outbound 流量

* ***Masquerading***<br>當配置完成後，發自該接口的封包，其來源 IP 會被替換為該接口的 IP，從而讓回應能正確路由回該接口

* ***Redirection***<br>將流量轉發到本地系統的其他端口，而不是轉發到內部網絡中的其他主機

# 確認路由表
```bash
route -n
```

```bash
ip r | grep default
```

# 開啟 Linux 轉發
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

<!-- 允許流量轉發
sudo iptables -A FORWARD -i eth1 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i eth0 -o eth1 -j ACCEPT -->


# 請求轉移至本機其它端口

可使用以下設定轉發端口
```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport [服務端口] -j REDIRECT --to-port [轉發端口]
```

# 請求轉發至其它主機

建立 DNAT:
```bash
sudo iptables -t nat -A PREROUTING -p tcp -d [服務IP] --dport [服務端口] -j DNAT --to-destination [轉發IP]:[轉發端口]
```

回應客戶端(擇一設定)

* 建立 SNAT 回應客戶端:
```bash
sudo iptables -t nat -A POSTROUTING -p tcp -d [轉發IP] --dport [轉發端口] -j SNAT --to-source [服務IP]
```

* 建立 MASQUERADE 回應動態客戶端:
```bash
sudo iptables -t nat -A POSTROUTING -j MASQUERADE
```

# 查詢 NAT 規則

查詢 NAT 設定
```bash
sudo iptables -t nat -S
```

查詢 NAT 規則對應
```bash
sudo iptables -t nat -L -n -v
```

<!-- # 刪除所有 NAT 規則
```bash
sudo iptables -t nat -F
``` -->

# 持久化規則

匯出 iptable 設定
```bash
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
```

編輯 interfaces
```bash
sudo nano /etc/network/interfaces 
```

在結尾加入以下代碼
```bash
up iptables-restore < /etc/iptables.ipv4.nat
```

<!-- sudo tcpdump -i eth0 host 192.168.50.2 and port 5000 -->

{% note warning %}
📜 參考資料
1. [ubuntu iptables](https://manpages.ubuntu.com/manpages/oracular/en/man8/iptables.8.html)
{% endnote %}