 （備份保存庫<properties
   pageTitle = 」 Azure Backup limits table"
   描述 = 「 說明系統限制 Azure Backup."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags
   ms.service="backup"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/05/2015"
   ms.author="trinadhk";"jimpark"; "aashishr" />


下列限制會套用至 Azure 備份。

| 限制識別碼 | 預設的限制 |
|---|---|
|可以針對每個保存庫註冊的伺服器/機器數量|Windows 伺服器/用戶端/SCDPM 50 <br/> IaaS Vm 為 200|
|Azure 保存庫儲存空間中儲存的資料的資料來源的大小|54400 GB 最大值<sup>1</sup>|
|您可以在每個 Azure 訂閱中建立的備份保存庫的數字|25 （備份保存庫） <br/> 25 修復服務地窖每個地區|
|每日排程備份的次數|每日的 Windows 伺服器/用戶端 3 <br/> 每日的 SCDPM 2 <br/> 一次 IaaS Vm 一天|
|附加至 Azure 虛擬機器備份資料磁碟|16|

- <sup>1</sup>54400 GB 的限制並不適用於 IaaS VM 備份。

