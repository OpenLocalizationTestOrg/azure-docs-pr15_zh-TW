<properties
   pageTitle="請注意來賓 OS 系列 1 退休 |Microsoft Azure"
   description="提供當有 Azure 來賓 OS 系列 1 退休改變，以及如何判斷是否受到影響的相關資訊"
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



# <a name="guest-os-family-1-retirement-notice"></a>來賓 OS 系列 1 退休通知

第一次在 2013 年 6 月 1 日宣告 OS 系列 1 的退休。

**2014 年 9 月 2 日**Azure 虛擬作業系統 (來賓 OS) 系列 1.x，根據於 Windows Server 2008 作業系統，正式已停用。 錯誤訊息通知您已停用來賓 OS 系列 1 部署新的服務或升級現有的服務系列 1 的所有嘗試會都失敗。

**2014 年 11 月 3 日**延伸的支援來賓 OS 系列 1 結束，完全已停用。 仍在家人 1 上的所有服務會受到都影響。 在任何時間，我們可能會停止這些服務。 不的保證服務會繼續執行，除非您手動升級他們自己有。

如果您有其他問題，請造訪[雲端服務論壇](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc)或[與 Azure 支援人員連絡](https://azure.microsoft.com/support/options/)。




## <a name="are-you-affected"></a>您會受到影響？

雲端服務會受到影響，如果適用於下列其中一項動作︰

1. 您有值為 「 osFamily = 「 1 」 明確檔案中所指定 ServiceConfiguration.cscfg 雲端服務。
2. 您沒有 osFamily 明確雲端服務，以指定 ServiceConfiguration.cscfg 檔案中的值。 目前，系統會在此情況下使用 「 1 」 的預設值。
3. Azure 傳統入口網站會列出您來賓作業系統系列值為 「 Windows Server 2008]。

若要尋找您的雲端服務執行的哪個 OS 系列，您可以執行下列指令碼 PowerShell 的 Azure，但您必須[設定 PowerShell 的 Azure](../powershell-install-configure.md)第一次。 在 [指令碼的其他詳細資料，請參閱[Azure 來賓 OS 系列 1 結束的生活︰ 2014 年 6 月](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx)。 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

雲端服務會影響 OS 系列 1 退休 osFamily 中的資料行指令碼輸出空白或包含 「 1 」。

## <a name="recommendations-if-you-are-affected"></a>如果您受影響的建議

我們建議您移轉到其中一個支援來賓 OS 系列的雲端服務的角色︰

**來賓 OS 家庭 4.x** -Windows Server 2012 R2 *（建議使用）*

1. 請確定您的應用程式使用.NET framework 4.0、 4.5 或 4.5.1 SDK 2.1 或更新版本。
2. 設定 「 4 」 osFamily 屬性中 ServiceConfiguration.cscfg 檔案，並重新部署您的雲端服務。


**來賓 OS 家庭 3.x** -Windows Server 2012

1. 請確定您的應用程式使用.NET framework 4.0 或 4.5 SDK 1.8 或更新版本。
2. "3"osFamily 屬性集 ServiceConfiguration.cscfg 檔案，並重新部署您的雲端服務。


**來賓 OS 家庭 2.x** -Windows Server 2008 R2

1. 請確定您的應用程式會使用 SDK 1.3 及.NET framework 3.5 或 4.0。
2. 「 2 」 osFamily 屬性集 ServiceConfiguration.cscfg 檔案，並重新部署您的雲端服務。


## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>2014 年 11 月 3 日結束延伸的支援來賓 OS 系列 1 的工作流程
已不再支援雲端服務上來賓 OS 系列 1。 請關閉 [越快越若要避免服務中斷系列 1 移轉。  

## <a name="next-steps"></a>後續步驟
檢閱[來賓 OS 發行](cloud-services-guestos-update-matrix.md)的最新。
