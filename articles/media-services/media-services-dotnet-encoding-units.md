<properties 
    pageTitle="如何新增編碼單位" 
    description="瞭解如何新增.net 編碼單位的方式"  
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016"
    ms.author="juliako;milangada;gtrifonov"/>


#<a name="how-to-scale-encoding-with-net-sdk"></a>如何調整.NET SDK 編碼方式

> [AZURE.SELECTOR]
- [入口網站](media-services-portal-scale-media-processing.md )
- [.NET](media-services-dotnet-encoding-units.md)
- [其餘](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>概觀

>[AZURE.IMPORTANT] 請確定檢閱[概觀](media-services-scale-media-processing-overview.md)主題，以取得詳細資訊縮放處理主題的媒體。
 
若要保留的單位類型和數字編碼保留的單位使用.NET SDK 的變更，請執行下列動作︰

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
    
    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();
    
    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##<a name="opening-a-support-ticket"></a>開啟支援票證

預設的每一個媒體服務帳戶可以容納最多 25 編碼及 5 視串流保留單位。 您可以在開啟的支援票證要求較高的限制。

###<a name="open-a-support-ticket"></a>開啟支援票證

若要開啟支援票證執行下列動作︰

1. 按一下 [[取得支援](https://manage.windowsazure.com/?getsupport=true)]。 如果您不登入，系統會提示您輸入您的認證。

1. 選取您的訂閱。

1. 支援類型] 下，選取 [技術]。

1. 按一下 「 建立票證 」。

1. 選取 「 Azure 媒體服務 「 產品清單中下一個頁面上呈現。

1. 選取 「 問題類型 」 適合您的問題。

1. 按一下 [繼續]。

1. 依照指示進行下一個頁面上，然後輸入您的問題的詳細資料。

1. 按一下 [送出以開啟票證。



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
