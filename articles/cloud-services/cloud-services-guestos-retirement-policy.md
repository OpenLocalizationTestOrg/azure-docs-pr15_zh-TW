<properties 
   pageTitle="支援與淘汰網站原則指南的 Azure 來賓 OS |Microsoft Azure" 
   description="提供有關 Microsoft 會支援人員為 regards Azure 來賓 os 雲端服務所使用的資訊。" 
   services="cloud-services" 
   documentationCenter="na" 
   authors="raiye" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="10/24/2016"
   ms.author="raiye"/>

# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure 來賓 OS 支援與淘汰網站原則
此頁面上的資訊與 Azure 來賓作業系統 ([OS 來賓](cloud-services-guestos-update-matrix.md)) 雲端服務工作者和 web 角色 (PaaS)。 它不會套用到虛擬機器 (IaaS)。 

Microsoft 有[支援來賓 OS 原則](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq)已發佈。 您正在讀取的頁面現在會說明如何執行原則。

原則 

1. Microsoft 支援**至少最新的兩個系列的來賓 os**。 家庭已停用，客戶會有更新為較新的支援來賓 OS 系列的正式退休日期的 12 個月。
2. Microsoft 支援**至少最新的兩個版本的受支援的來賓 OS 家族**。 
3. Microsoft 支援在**Azure SDK 的最小最新兩個版本**。 SDK 的版本已停用，客戶必須更新為較新版本的正式退休日期的 12 個月。 

有時候超過兩個系列或版本可能受支援。 正式來賓 OS 支援資訊會出現在的[Azure 來賓作業系統版本和 SDK 相容性矩陣圖](cloud-services-guestos-update-matrix.md)。


## <a name="when-a-guest-os-family-or-version-is-retired"></a>當來賓 OS 家人或版本是已停用 


新的來賓 OS**系列**有時以後正式新版的 Windows 伺服器作業系統的版本。 只要新增來賓 OS 家庭介紹，Microsoft 就會淘汰最舊的來賓 OS 系列。 

新的來賓作業系統**版本**會介紹有關每個月都將最新的 MSRC 更新。 一般每月的更新，由於來賓作業系統版本是以正常方式停用其發行後的 60 天。 這會保留至少兩個來賓 OS 版本，每個家族可供使用。 

### <a name="process-during-a-guest-os-family-retirement"></a>程序期間來賓 OS 家庭淘汰網站 


一旦退休會通知，客戶會有在 12 月 」 轉場效果 」 期間之前的舊版系列正式移除服務。 一種 Microsoft 可能會擴充這個轉換的時間。 [Azure 來賓作業系統版本和 SDK 相容性矩陣](cloud-services-guestos-update-matrix.md)，將會發佈更新。

逐漸退休程序就會開始切換期間將 6 個月。 在這段時間︰

1. Microsoft 會通知退休的客戶。 
2. 較新版 Azure SDK 不支援的已停用的來賓 OS 系列。
3. 新的部署和雲端服務的 redeployments 不允許上已停用家族

Microsoft 會繼續介紹併入 MSRC 最新更新直到轉換期間，稱為 「 到期日 」 的最後一天的新來賓作業系統版本。 此時，仍在執行中的任何雲端服務會在 Azure SLA 不受支援。 Microsoft 具有強制升級、 刪除或該日期之後停止這些服務排除或限制。



### <a name="process-during-a-guest-os-version-retirement"></a>程序期間來賓作業系統版本淘汰網站 
如果客戶設定自動更新其來賓 OS，它們永遠不必擔心處理來賓 OS 版本。 一律使用最新的來賓作業系統版本。

每個月發行來賓 OS 版本。 標準發行的工資率，因為每個版本都有固定為單位的週期。

將為單位的週期的 60 天版本是 「*停用*]。 「 停用 「 表示會從 Azure 傳統入口網站中移除版本。 它也不再可設定從 CSCFG 設定檔。 現有的部署會保留執行，但新的部署和程式碼並設定更新現有的部署不允許。 

在稍後 」、 「 來賓 OS 版本 「*到期*」 和 「 任何安裝仍在執行該版本是強制升級和自動更新來賓 OS 在未來的設定。 到期日會執行批次中，因此一段時間停用從到期而有所不同。 

這些期間可能會更長的時間在進行 Microsoft 的排除或限制，以便於客戶轉場效果。 [Azure 來賓作業系統版本和 SDK 相容性矩陣圖](cloud-services-guestos-update-matrix.md)會傳達的任何變更。



### <a name="notifications-during-retirement"></a>退休期間的通知 

* **家庭淘汰網站** <br>Microsoft 會使用部落格文章和 Azure 傳統入口網站的通知。 客戶仍在使用已停用的來賓 OS 家庭就會收到透過指派的服務的系統管理員的直接通訊 （電子郵件、 入口網站的郵件、 電話）。 所有的變更就會張貼在此頁面和 RSS 摘要列出在此頁面的開頭。 


* **版本淘汰網站** <br>所有的變更就會張貼在此頁面和 RSS 摘要列出在此頁面，包括停用的版本，以及到期日的開頭。 服務管理員會收到電子郵件，如果有部署停用的來賓 OS 版本或系列上執行。 這些電子郵件的時間而有所不同。 通常他們的年齡至少一個月之前停用，不過此時間不是正式 SLA。 


## <a name="frequently-asked-questions"></a>常見問題集

**如何可以減輕移轉的影響？**

您應該使用最新的來賓 OS 系列設計您的雲端服務。 

1. 開始規劃您移轉到較新的家庭最早。 
2. 若要測試您的新系列上執行的雲端服務設定暫時測試部署。 
3. 為 [**自動**設定您的來賓作業系統版本 (osVersion = * [.cscfg](cloud-services-model-and-package.md#cscfg)檔案中)，移轉至新的來賓 OS 版本會自動發生。

**如果我的 web 應用程式需要更深入的整合與 OS 嗎？**

如果您的 web 應用程式架構需要更深入的相依性在基礎的作業系統上，使用平台支援的功能，例如[啟動工作](cloud-services-startup-tasks.md)或其他可能會在未來存在的擴充機制。 或者，您也可以使用[Azure 虛擬機器](https://azure.microsoft.com/documentation/scenarios/virtual-machines/)(IaaS – 服務為基礎結構，) 您身在何處負責維護基礎的作業系統。
 
## <a name="next-steps"></a>後續步驟
檢閱[來賓 OS 發行](cloud-services-guestos-update-matrix.md)的最新。
