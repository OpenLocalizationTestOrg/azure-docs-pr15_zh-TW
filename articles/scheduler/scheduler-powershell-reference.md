<properties
 pageTitle="排程器 PowerShell Cmdlet 參考"
 description="排程器 PowerShell Cmdlet 參考"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-powershell-cmdlets-reference"></a>排程器 PowerShell Cmdlet 參考

下表的每個主要的 cmdlet Azure 排程器中的 [參照] 頁面的連結，並說明。

若要安裝 PowerShell 的 Azure，並將它與 Azure 訂閱關聯，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。 

如需有關[Azure 資源管理員 cmdlet](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx)的詳細資訊，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。

|Cmdlet|Cmdlet 描述|
|---|---|
[停用 AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |停用工作集合。 
[啟用 AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |啟用工作集合。
[取得 AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |取得工作排程器。
[取得 AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |取得工作集合。
[取得 AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |取得工作歷程記錄。
[新 AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |會建立 HTTP 工作。
[新 AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |建立工作集合。
[新 AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |建立服務匯流排佇列工作。
[新 AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |建立服務匯流排主題工作。
[新 AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |建立儲存佇列中工作。 
[移除 AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |移除工作排程器。  
[移除 AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |移除工作集合。 
[設定 AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |修改排程器 HTTP 工作。
[設定 AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |修改工作集合。 
[設定 AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |修改服務匯流排佇列工作。  
[設定 AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |修改服務匯流排主題工作。 
[設定 AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |修改儲存佇列中工作。   

如需詳細資訊，您可以執行任何下列 cmdlet: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>另請參閱


 [什麼是排程器？](scheduler-intro.md)

 [Azure 排程器概念、 詞彙及實體階層](scheduler-concepts-terms.md)

 [開始使用 Azure 入口網站中的排程器](scheduler-get-started-portal.md)

 [方案與帳單方面的 Azure 排程器](scheduler-plans-billing.md)

 [Azure 排程器 REST API 參照](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制與預設值]，出現錯誤碼](scheduler-limits-defaults-errors.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)
