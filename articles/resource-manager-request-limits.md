<properties
   pageTitle="Azure 資源管理員要求限制 |Microsoft Azure"
   description="說明如何使用節流與 Azure 資源管理員邀請達到訂閱限制。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="tomfitz"/>

# <a name="throttling-resource-manager-requests"></a>節流資源管理員的要求

為每個訂閱和租用戶，資源管理員限制讀取每小時 15000 要求，並要求寫入 1200 每小時。 如果您的應用程式或指令碼達到這些限制，您需要節流您的要求。 本主題說明如何判斷剩餘的要求您有之前的限制，以及當您已達到限制如何回應。

當您到達限制時，您會收到 HTTP 狀態碼**429 太多要求**。

要求的數量範圍限定為您的訂閱或您的租用戶。 如果您有多個，同時在您的訂閱，進行要求的應用程式要求從這些應用程式就會加入在一起，以決定剩餘要求的數目。

訂閱範圍要求會傳送您的訂閱識別碼，例如擷取資源 involve 群組在您的訂閱中的項目。 範圍的租用戶要求不會包含您的訂閱識別碼，例如擷取有效 Azure 的位置。

## <a name="remaining-requests"></a>剩餘的要求

您可以藉由檢查回應標頭決定剩餘要求的數目。 每個邀請中包含的剩餘已讀取和寫入要求數的值。 下表說明您可以檢查這些值的回應標頭︰

| 回應標頭 | 描述 |
| --------------- | ----------- |
| x-ms-ratelimit-remaining-subscription-reads | 訂閱範圍讀取剩餘 |
| x-ms-ratelimit-remaining-subscription-writes | 範圍的訂閱將剩餘 |
| x-ms-ratelimit-remaining-tenant-reads | 範圍的租用戶讀取剩餘 |
| x-ms-ratelimit-remaining-tenant-writes | 範圍的租用戶寫入剩餘 |
| x-ms-ratelimit-remaining-subscription-resource-requests | 訂閱範圍剩餘的資源類型要求。<br /><br />如果服務已覆寫預設的限制，則只會傳回此標題值。 資源管理員會將此值，而非訂閱讀取或寫入。 |
| x-ms-ratelimit-remaining-subscription-resource-entities-read | 訂閱範圍剩餘的資源類型集合要求。<br /><br />如果服務已覆寫預設的限制，則只會傳回此標題值。 此值會提供剩餘集合要求 （清單資源） 的數目。 |
| x-ms-ratelimit-remaining-tenant-resource-requests | 租用戶範圍剩餘的資源類型要求。<br /><br />這個標題只加在租用戶層級，要求的只有當服務已覆寫 [預設限制。 資源管理員會將此值，而不是租用戶讀取或寫入。 |
| x-ms-ratelimit-remaining-tenant-resource-entities-read | 租用戶範圍剩餘的資源類型集合要求。<br /><br />這個標題只加在租用戶層級，要求的只有當服務已覆寫 [預設限制。 |

## <a name="retrieving-the-header-values"></a>擷取的標頭值

擷取這些標頭中的值程式碼或指令碼和並無不同擷取任何頁首的值。 

例如， **C#**，所擷取的標頭值**HttpWebResponse**物件命名**回應**下列程式碼︰

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

在**PowerShell**中，您可以擷取的標頭值從叫用 WebRequest 作業。

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
    
或者，如果想要查看的偵錯時，其餘的要求，您可以提供**-偵錯**參數，在您的**PowerShell**指令程式。

    Get-AzureRmResourceGroup -Debug
    
傳回的許多的資訊，包括下列回應值︰

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

**Azure CLI**，在您要使用更多詳細資訊] 選項擷取的標頭值。

    azure group list -vv --json

傳回的許多的資訊，包括下列物件︰

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>等待傳送下一個要求之前

當您到達要求限制時，資源管理員會傳回**429** HTTP 狀態碼和**重試之後**標頭中的值。 **重試之後**值的下一個要求在傳送前指定應用程式應該等待的秒數 （或就寢） 的數目。 如果之前已過重試值，您就會傳送要求，不會處理您的要求，並傳回新的重試] 值。
