---
title: 'Angular Live Reload在Ubuntu沒反應'
date: 2020-08-31 22:41:57
categories: Angular
tags: [Ubuntu]
---
# 實作
在Linux上，默認情況下，Listen使用inotify監視目錄中的更改
 
Ubuntu 的inotify預設限制為8192
 
您可以通過執行以下操作獲取目前的inotify文件監視限制：
 
```bash 
 cat /proc/sys/fs/inotify/max_user_watches
```
 
如果此限制不足以監視目錄中的所有文件，則必須增加該限制以使其正常工作
 
```bash
sudo sysctl fs.inotify.max_user_watches=524288 
sudo sysctl -p
```
 
如果想讓自己的限制永久化，請輸入以下命令:
 
```bash
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

# 參考資料
[User limit of inotify watches reached on Ubuntu 16.04](https://askubuntu.com/questions/770374/user-limit-of-inotify-watches-reached-on-ubuntu-16-04)
