<properties 
    pageTitle="選取使用者名稱的 Linux |Microsoft Azure" 
    description="瞭解如何將 Azure 中選取 Linux 虛擬機器中的使用者名稱。" 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



#<a name="selecting-user-names-for-linux-on-azure"></a>選取上 Azure Linux 的使用者名稱#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

當您佈建 Linux 上 Azure 虛擬機器時，您必須指定稍後可用來登入 VM 非根使用者的名稱。 您可以選擇新的使用者名稱，或如果佈建透過 Azure 傳統入口網站，您就可以接受預設名稱 「 azureuser 」。

在大多數情況下此使用者不存在於基底圖像，然後才會提供程序。 如果使用者存在於基底 VM 圖像，然後 Azure Linux 代理程式只要設定密碼及/或根據您指定在建立 VM 資訊該使用者的 SSH 鍵。

**不過**，Linux 定義一組不應使用的使用者名稱。 提供的程序會**失敗，**如果您嘗試佈建 Linux VM 使用定義為 UID 0-99 與使用者現有系統使用者。 典型的範例是`root`使用者，有 UID 0。

 - 另請參閱︰ [Linux 標準基底-使用者識別碼範圍](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

以下是 CentOS 和 Ubuntu 您應避免使用佈建 Linux 上 Azure 虛擬機器時常見的內建系統使用者清單。 這份清單，只是範例，您的通訊群組，以確保您選擇的使用者名稱不會與現有系統使用者衝突的文件，請參閱。


## <a name="centos"></a>CentOS
- abrt
- adm
- 音訊
- 回收筒
- 光碟機
- cgred
- 精靈
- dbus
- ] 撥出
- dip
- 磁碟
- 磁碟
- ftp
- ftp
- 運動會
- gopher
- haldaemon
- 停止
- kmem
- 鎖定
- lp
- 郵件
- 男人
- 記憶體
- nfsnobody
- 沒有人
- ntp
- 運算子
- oprofile
- postdrop
- 後置
- qpidd
- 根
- rpc
- rpcuser
- saslauth
- 關閉
- slocate
- sshd
- stapdev
- stapusr
- 同步處理
- 系統
- 膠帶貼上
- 測試
- tcpdump
- tty
- 使用者
- utempter
- utmp
- uucp
- vcsa
- 視訊
- 滾輪


## <a name="ubuntu"></a>Ubuntu
- adm
- 管理員
- 音訊
- 備份
- 回收筒
- 光碟機
- crontab
- 精靈
- ] 撥出
- dip
- 磁碟
- 傳真
- 磁碟
- 將
- 運動會
- gnats
- irc
- kmem
- 橫向
- libuuid
- 清單
- lp
- 郵件
- 男人
- messagebus
- mlocate
- netdev
- 新聞
- 沒有人
- nogroup
- 運算子
- plugdev
- proxy
- 根
- sasl
- 陰影
- src
- ssh
- sshd
- 員工
- sudo
- 同步處理
- 系統
- 系統
- 膠帶貼上
- tty
- 使用者
- utmp
- uucp
- 視訊
- 語音
- whoopsie
- www 資料

 