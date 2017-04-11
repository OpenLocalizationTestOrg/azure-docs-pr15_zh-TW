<properties 
   pageTitle="StorSimple 8000 更新 0.2 的版本資訊 |Microsoft Azure"
   description="將說明的新功能和修正、 開啟的問題及可用的因應措施年 1 月 2015 Microsoft Azure StorSimple 版本 （更新 0.2）。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />


# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>StorSimple 8000 數列更新 0.2 發行備忘稿-2015 年 1 月

## <a name="overview"></a>概觀

以下版本說明識別關鍵 Microsoft Azure StorSimple 2015 年 1 月版本開啟問題。 他們也包含這個版本中包含 StorSimple 軟體和韌體更新的清單。 StorSimple 8000 數列發行版本進行 2014 年 7 月推出之後，這是第二個版本。
  
此更新不會從年 10 月更新中變更實體裝置軟體版本。 它繼續版本 6.3.9600.17312。 在這個版本中變更所使用的虛擬裝置圖像影像。 因此，所有新虛擬裝置後 1/20/2015年會顯示為 6.3.9600.17361 版本所建立。  

請檢閱在 2015 年 1 月更新的版本資訊中所包含的下列資訊。

> [AZURE.IMPORTANT]  
>
>- 此更新不是透過 Windows Update，而且無法安裝其他更新等。 即使您已套用更新使用 Azure 傳統入口網站，您的裝置將不會收到此更新。 虛擬建立 2015 年 1 月 20 日之後的裝置只會套用此更新。 
> 
>- StorSimple 的年 1 月版本不包含 StorSimple 實體裝置的任何更新。 您還是可以將任何可用的 Windows 更新至虛擬裝置，包括最近安全性修正方式，但您將不會看到 StorSimple 實體裝置版中的變更。

## <a name="whats-new-in-the-january-release"></a>年 1 月發行新功能

此更新包含相關的區虛擬裝置上離線修正。 （請參閱[這個版本中修正問題](#issues-fixed-in-the-january-release)）。  

更新不包含新功能。  

## <a name="issues-fixed-in-the-january-release"></a>固定在年 1 月發行的問題

下表說明此更新中所修正問題。

|[否]。|功能|問題|適用於實體裝置|適用於虛擬裝置 
|---|-------|-----|--------------------------|-------------------------
|1|離線區|當高雲端延遲保存幾分鐘的時間時，StorSimple 虛擬裝置區離線主機上。 此 [修正] 會增加雲端延遲，藉此最小化會造成離線主機上區的情況的臨界值。|無|[是]  

## <a name="known-issues-in-the-january-release"></a>年 1 月發行的已知的問題

下表提供此版本的已知問題的摘要。
 
|[否]。|功能|問題|註解/因應措施|適用於實體裝置|適用於虛擬裝置 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1| 工廠重設|  在某些情況下，當您執行工廠重設 StorSimple 裝置可能無法解決問題，並顯示這則訊息︰**重設為原廠正在進行 （階段 8）。** 會發生這種情況如果 cmdlet 正在進行中時，按 CTRL + C。| 不 CTRL + C 之後按起始廠重設。 如果您已在此狀態，請連絡 Microsoft 支援服務的下一個步驟。|[是]|無|
|2|磁碟仲裁| 少數情況下，如果大部分的磁碟 8600 裝置的 EBOD 圍繞符號會中斷連線，導致沒有磁碟仲裁，然後儲存集區會是離線。 即使磁碟重新連線時，它會保持離線。|您必須重新啟動裝置。 如果問題持續發生，請連絡 Microsoft 支援服務的下一個步驟。|[是] |無
|3|雲端快照失敗|雲端快照少數情況下，可能失敗**最大值備份上限**的錯誤。 這是如果超過 255 線上複製，在同一個裝置上，從同一原始音量已經被刪除。||[是]|[是]
|4|不正確的控制器識別碼|執行控制器替代方案時，控制站 0 可能會顯示為控制站 1。 控制站取代時從對等節點，載入圖像控制器識別碼可以顯示一開始為等控制站的識別碼。  少數情況下，此行為也可能會出現後重新啟動系統。|使用者不不需要任何動作。 控制站取代完成後，這種情況下會自行解決。|[是]|無 
|5| 監控圖表的裝置|在 StorSimple 管理員服務，裝置監控圖表無法運作時是基本或裝置的 proxy 伺服器設定中啟用 NTLM 驗證。|修改網頁 proxy 設定的裝置註冊 StorSimple 管理員服務，以便驗證設定為 [無]。 若要這麼做，請執行 Windows PowerShell 的 StorSimple 設定 HcsWebProxy 指令程式。|[是]|[是]
|6| 儲存帳戶|若要刪除的儲存空間帳戶使用儲存服務是不受支援的案例。 這會導致無法擷取使用者資料在其中一種情況。|| [是] |  [是]
|7|裝置容錯移轉| 不支援在不同的目標裝置從相同的來源裝置音量容器的多個錯誤後移轉。| 從單一無裝置容錯移轉到多個裝置，就能在第一個裝置上無法大量容器會遺失資料擁有權。 後這類移轉後，會顯示這些大量容器，或將其行為，當您在 Azure 傳統入口網站檢視中。|[是]|無
|8| 安裝|期間 StorSimple 介面卡的 SharePoint 安裝，您需要提供裝置 IP 中順利完成安裝]。||[是]|無
|9| 網頁 proxy|如果您的 web proxy 設定為指定的通訊協定 HTTPS，然後會影響您的裝置-服務通訊，並將離線使用的裝置。 在 [程序，使用您的裝置上的大量資源，也會產生支援套件。|請確定網址 proxy 設為指定的通訊協定 HTTP。 詳細資訊請參閱如何[設定 web proxy，您的裝置](storsimple-configure-web-proxy.md)。|[是] |無
|10|網頁 proxy|  如果您設定，並啟用註冊裝置上的網頁 proxy，您會需要重新啟動您的裝置上使用中的控制站。|| [是] |無
|11|高雲端延遲和高 I/O 工作量|當您 StorSimple 裝置遇到更高的雲端延遲 （順序 （秒）） 與高 I/O 工作負載的組合時，裝置區進入降級狀態和 I/o 可能失敗的 「 裝置尚未準備 」 錯誤。|您必須以手動方式重新啟動裝置控制器或執行裝置容錯移轉修復這種情況。|[是]|無

## <a name="physical-device-updates-in-the-january-release"></a>實體裝置中年 1 月的更新版本

此更新不包含 StorSimple 裝置的任何其他變更。

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>循序附加 SCSI (SA) 控制器和韌體年 1 月的更新版本

此版本不包含任何更新的循序附加 SCSI (SA) 控制站或韌體。 驅動程式更新時年 10 月，2014年發行。 

## <a name="virtual-device-updates-in-the-january-release"></a>虛擬裝置中年 1 月的更新版本

在這個版本包含虛擬裝置的更新的影像。 建立 2015 年 1 月 20 日之後的所有虛擬裝置會顯示為 6.3.9600.17361 的軟體版本。

 
