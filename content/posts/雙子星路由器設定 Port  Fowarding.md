---
title: "雙子星路由器設定 Port  Fowarding"
date: 2023-06-12T18:21:32+08:00
draft: false
toc: false
tags:
  - Port Fowarding
categories:
  - Others
---

需要先連線至內部網路，可以是 wifi 或是有線連接。在內網才能進入雙子星的後台設定

雙子星 192.168.0.1
帳號 admin
密碼 password

進入登入頁面: 192.168.0.1

![Untitled](/雙子星路由器設定/Untitled.png)

選擇防火牆 → Forwarding

圖中為已設定好的資訊，以下重新說明一次

網路配置:

雙子星路由器 → 三台 Deco X25 分別在二三五樓，使用基地台模式(預設為路由器模式)，都接線至四樓的雙子星路由器。

關於基地台模式和路由器模式 [https://www.tp-link.com/tw/support/faq/442/](https://www.tp-link.com/tw/support/faq/442/)

![Untitled](/雙子星路由器設定/Untitled%201.png)

這樣的好處是所有的路由器都是有線，同時還有 Mesh 功能。缺點就是一開始比較麻煩需要請人接線。

首先取得 NAS 目前的內網 IP, 在 Forwarding 設定上會使用到

![截圖 2021-10-29 下午9.38.46.png](/雙子星路由器設定/%E6%88%AA%E5%9C%96_2021-10-29_%E4%B8%8B%E5%8D%889.38.46.png)

![截圖 2021-10-29 下午9.42.33.png](/雙子星路由器設定/%E6%88%AA%E5%9C%96_2021-10-29_%E4%B8%8B%E5%8D%889.42.33.png)

以我的例子來說，是192.168.0.16

接下來進入主題: 設定 forwarding

左邊選單選擇「防火牆」，選則 Forwarding 分頁，並按下新增

![Untitled](/雙子星路由器設定/Untitled%202.png)

所以有幾個需要填入的資訊
名稱、通訊協定、Public Port、Private Port、IP

根據[官網提供的埠號](https://kb.synology.com/zh-tw/SRM/tutorial/What_network_ports_are_used_by_SRM_services)，我需要檔案傳輸功能中的 AFP 類型，埠號是 548

![Untitled](/雙子星路由器設定/Untitled%203.png)

設定頁面中的「常用應用」並沒有我想要的 548 埠，TCP 的選項，所以就留著不選。

名稱可是只自己看的

通訊埠，根據官網資訊，需要選 'TCP'

Public Port 範圍(別的機器可能叫外部連接埠)，這邊是需要填範圍，我兩個都填入 548

Private Port 範圍(別的機器可能叫內部連接埠)，這邊只要一個填，填入 548

IP，就是我們的 NAS 的內部 IP，剛剛已取得，填入 192.168.0.16

補充說明，Private Port 和 Public Port 在沒有特別需求時可以填一樣，

如果有多個 NAS 都需要 548 這個埠號時，可以設定兩個 Forwarding，分別給不同的 Public Port，和機器的 IP，但 Private Port 是一樣的，用不同的對外 Port 達到連線多台 NAS 的功能。

到這裡就設定完了，完成的設定會是這樣:

![Untitled](/雙子星路由器設定/Untitled%204.png)

## 參考

[https://ningselect.com/30752/58/](https://ningselect.com/30752/58/)

[https://macuknow.com/2017/03/18/1485/教學-人在外面也可以用-finder-快速連回家中-nas/](https://macuknow.com/2017/03/18/1485/%E6%95%99%E5%AD%B8-%E4%BA%BA%E5%9C%A8%E5%A4%96%E9%9D%A2%E4%B9%9F%E5%8F%AF%E4%BB%A5%E7%94%A8-finder-%E5%BF%AB%E9%80%9F%E9%80%A3%E5%9B%9E%E5%AE%B6%E4%B8%AD-nas/)
