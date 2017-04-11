<properties 
    pageTitle="管理使用 PowerShell 的 Azure 媒體服務帳戶" 
    description="瞭解如何管理 Azure 媒體服務帳戶使用 PowerShell cmdlet。" 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"
    ms.author="juliako"/>


#<a name="manage-azure-media-services-accounts-with-powershell"></a>管理使用 PowerShell 的 Azure 媒體服務帳戶

> [AZURE.SELECTOR]
- [入口網站](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [其餘](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] 若要建立 Azure 媒體服務帳戶，您必須擁有 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 免費試用版</a>。

##<a name="overview"></a>概觀 

本文將列出 Azure 資源管理員架構中的 Azure 媒體服務 （反飛彈系統） Azure PowerShell cmdlet。 Cmdlet 存在於**Microsoft.Azure.Commands.Media**命名空間。

## <a name="versions"></a>版本

**ApiVersion**: 「 2015年-10-01 」
               

## <a name="new-azurermmediaservice"></a>新 AzureRmMediaService

建立媒體服務。

### <a name="syntax"></a>語法

設定參數︰ StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

設定參數︰ StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>參數

**-ResourceGroupName&lt;字串&gt;**

指定此媒體服務所屬的 [資源] 群組的名稱。

別名 | 無
---|---
需要？   |  true
位置嗎？   |  0
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？  |false

**-AccountName&lt;字串&gt;**

指定媒體服務的名稱。

別名 |名稱
---|---
需要？ |true
位置嗎？ |1
預設值 |無
接受管線輸入嗎？ |false
接受萬用字元嗎？ |false

**位置&lt;字串&gt;**

指定資源之位置的媒體服務。

別名 |無
---|---
需要？ |true
位置嗎？ |2
預設值  |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？ |false

**-StorageAccountId&lt;字串&gt;**

指定與媒體服務相關的主要儲存帳戶。

- 儲存建立新帳戶 （和資源管理員 API） 只支援。

- 儲存帳戶必須存在，而且有與媒體服務的同一個位置。

別名 |無
---|---
需要？ |true
位置嗎？ |3
預設值  |無
接受管線輸入嗎？ |true(ByPropertyName)
設定參數的名稱 |StorageAccountIdParamSet
接受萬用字元嗎？|false

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

指定與媒體服務相關的儲存空間帳戶。

- 儲存建立新帳戶 （和資源管理員 API） 只支援。

- 儲存帳戶必須存在，而且有與媒體服務的同一個位置。

- 只有一個儲存帳戶可指定為主要。

別名 |無
---|---
需要？  |true
位置嗎？  |3
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
設定參數的名稱 |StorageAccountsParamSet
接受萬用字元嗎？ |false

**-標籤&lt;Hashtable&gt;**

指定雜湊標籤與媒體服務相關聯的資料表。

- 範例︰@{"tag1"="value1";"tag2"=:value2"}

別名 |無
---|---
需要？  |false
位置嗎？  |名為
預設值 |無
接受管線輸入嗎？ |false
接受萬用字元嗎？ |false

**&lt;CommandParameters&gt;**

這個指令程式支援的常見的參數:-偵錯，-ErrorAction、-ErrorVariable、-InformationAction-InformationVariable、-OutVariable，-OutBuffer-PipelineVariable、-詳細資訊，-WarningAction，與-WarningVariable。

### <a name="inputs"></a>輸入

輸入的類型是您可以管道 cmdlet 的物件的類型。

### <a name="outputs"></a>輸出

輸出類型是指令程式會發出的物件的類型。

## <a name="set-azurermmediaservice"></a>設定 AzureRmMediaService

更新媒體服務。

### <a name="syntax"></a>語法

    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>參數

**-ResourceGroupName&lt;字串&gt;**

指定此媒體服務所屬的 [資源] 群組的名稱。

別名 |無
---|---
需要？  |true
位置嗎？  |0
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？ |false

**-AccountName&lt;字串&gt;**

指定媒體服務的名稱。

別名 |名稱
---|---
需要？ |為 true
位置嗎？ |1
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？ |False

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

指定與媒體服務相關的儲存空間帳戶。

- 儲存建立新帳戶 （和資源管理員 API） 只支援。

- 儲存帳戶必須存在，而且有與媒體服務的同一個位置。

- 只有一個儲存帳戶可指定為主要。

別名 |無
---|---
需要？ |false
位置嗎？ |名為
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
設定參數的名稱 |StorageAccountsParamSet
接受萬用字元嗎？ |false

**-標籤&lt;Hashtable&gt;**

指定雜湊資料表與此媒體服務相關聯的標籤。

- 與媒體服務相關聯的標籤所取代客戶所指定的值。

別名 |無
---|---
需要？ |False
位置嗎？  |名為
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？ |false

**&lt;CommandParameters&gt;**

這個指令程式支援的常見的參數:-偵錯，-ErrorAction、-ErrorVariable、-InformationAction-InformationVariable、-OutVariable，-OutBuffer-PipelineVariable、-詳細資訊，-WarningAction，與-WarningVariable。

### <a name="inputs"></a>輸入

輸入的類型是您可以管道 cmdlet 的物件的類型。

### <a name="outputs"></a>輸出

輸出類型是指令程式會發出的物件的類型。

## <a name="remove-azurermmediaservice"></a>移除 AzureRmMediaService

移除媒體服務。

### <a name="syntax"></a>語法

    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>參數

**-ResourceGroupName&lt;字串&gt;**

指定此媒體服務所屬的 [資源] 群組的名稱。

別名 |無
---|---
需要？ |true
位置嗎？ |0
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？ |false

**-AccountName&lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
需要？ |true
位置嗎？ |2
預設值 |無
接受管線輸入嗎？  |true(ByPropertyName)
接受萬用字元嗎？ |False

**&lt;CommandParameters&gt;**

這個指令程式支援的常見的參數:-偵錯，-ErrorAction、-ErrorVariable、-InformationAction-InformationVariable、-OutVariable，-OutBuffer-PipelineVariable、-詳細資訊，-WarningAction，與-WarningVariable。

### <a name="inputs"></a>輸入

輸入的類型是您可以管道 cmdlet 的物件的類型。

### <a name="outputs"></a>輸出

輸出類型是指令程式會發出的物件的類型。

## <a name="get-azurermmediaservice"></a>取得 AzureRmMediaService

取得資源群組中的所有媒體服務或媒體服務指定名稱。

### <a name="syntax"></a>語法

ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>] 

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>參數

**-ResourceGroupName&lt;字串&gt;**

指定此媒體服務所屬的 [資源] 群組的名稱。

別名 |無
---|---
需要？ |true
位置嗎？  |0
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
設定參數的名稱 |ResourceGroupParameterSet AccountNameParameterSet
接受萬用字元嗎？   false

**-AccountName&lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
需要？ |true
位置嗎？  |1
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
設定參數的名稱  |AccountNameParameterSet
接受萬用字元嗎？ |false

**&lt;CommandParameters&gt;**

這個指令程式支援的常見的參數:-偵錯，-ErrorAction、-ErrorVariable、-InformationAction-InformationVariable、-OutVariable，-OutBuffer-PipelineVariable、-詳細資訊，-WarningAction，與-WarningVariable。

### <a name="inputs"></a>輸入

輸入的類型是您可以管道 cmdlet 的物件的類型。

### <a name="outputs"></a>輸出

輸出類型是指令程式會發出的物件的類型。

## <a name="get-azurermmediaservicekeys"></a>取得 AzureRmMediaServiceKeys

取得媒體服務的索引鍵。

### <a name="syntax"></a>語法

    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>參數

**-ResourceGroupName&lt;字串&gt;**

指定此媒體服務所屬的 [資源] 群組的名稱。

別名 |無
---|---
需要？ |true
位置嗎？  |0
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？ |false

**-AccountName&lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
需要？ |true
位置嗎？ |1
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？ |false

**&lt;CommandParameters&gt;**

這個指令程式支援的常見的參數:-偵錯，-ErrorAction、-ErrorVariable、-InformationAction-InformationVariable、-OutVariable，-OutBuffer-PipelineVariable、-詳細資訊，-WarningAction，與-WarningVariable。

### <a name="inputs"></a>輸入

輸入的類型是您可以管道 cmdlet 的物件的類型。

### <a name="outputs"></a>輸出

輸出類型是指令程式會發出的物件的類型。

## <a name="set-azurermmediaservicekey"></a>設定 AzureRmMediaServiceKey

重新產生媒體服務的主要或次要鍵。

### <a name="syntax"></a>語法

    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>參數

**-ResourceGroupName&lt;字串&gt;**

指定此媒體服務所屬的 [資源] 群組的名稱。

別名 |無
---|---
需要？  |true
位置嗎？  |0
預設值 |無
接受管線輸入嗎？  |true(ByPropertyName)
接受萬用字元嗎？ |false

**-AccountName&lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
需要？ |true
位置嗎？  |1
預設值 |無
接受管線輸入嗎？   |true(ByPropertyName)
接受萬用字元嗎？ |false

**-KeyType &lt;KeyType&gt;**

指定索引鍵的媒體服務類型。

- 主要或次要

別名 |無
---|---
需要？  |true
位置嗎？  |2
預設值 |無
接受管線輸入嗎？ |false
接受萬用字元嗎？ |false

**&lt;CommandParameters&gt;**

這個指令程式支援的常見的參數:-偵錯，-ErrorAction、-ErrorVariable、-InformationAction-InformationVariable、-OutVariable，-OutBuffer-PipelineVariable、-詳細資訊，-WarningAction，與-WarningVariable。

### <a name="inputs"></a>輸入

輸入的類型是您可以管道 cmdlet 的物件的類型。

### <a name="outputs"></a>輸出

輸出類型是指令程式會發出的物件的類型。

## <a name="sync-azurermmediaservicestoragekeys"></a>同步處理 AzureRmMediaServiceStorageKeys

同步處理儲存帳戶金鑰儲存帳戶相關聯的媒體服務。

### <a name="syntax"></a>語法

    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>參數

**-ResourceGroupName&lt;字串&gt;**

指定此媒體服務所屬的 [資源] 群組的名稱。

別名 |無
---|---
需要？ |true
位置嗎？ |0
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？ |false

**-AccountName&lt;字串&gt;**

指定媒體服務的名稱。

別名 |無
---|---
需要？ |true
位置嗎？ |1
預設值 |無
接受管線輸入嗎？ |true(ByPropertyName)
接受萬用字元嗎？ |false

**-StorageAccountId&lt;字串&gt;**

指定與媒體服務相關的儲存空間帳戶。

別名 |識別碼
---|---
需要？ |true
位置嗎？  |2
預設值 |無
接受管線輸入嗎？ |      true(ByPropertyName)
接受萬用字元嗎？ |false

**&lt;CommandParameters&gt;**

這個指令程式支援的常見的參數:-偵錯，-ErrorAction、-ErrorVariable、-InformationAction-InformationVariable、-OutVariable，-OutBuffer-PipelineVariable、-詳細資訊，-WarningAction，與-WarningVariable。

### <a name="inputs"></a>輸入

輸入的類型是您可以管道 cmdlet 的物件的類型。

### <a name="outputs"></a>輸出

輸出類型是指令程式會發出的物件的類型。

## <a name="next-step"></a>下一步 

請參閱學習路徑的媒體服務。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
