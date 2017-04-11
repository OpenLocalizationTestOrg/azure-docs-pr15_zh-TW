<properties
    pageTitle="縮放比例媒體處理概觀 |Microsoft Azure"
    description="本主題是 Azure 媒體服務使用的縮放比例媒體處理的概觀。"
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
    ms.date="08/29/2016"
    ms.author="juliako"/>


# <a name="scaling-media-processing-overview"></a>縮放比例媒體處理概觀

此頁面可讓概略瞭解如何以及為何要不按比例縮放媒體處理。 

## <a name="overview"></a>概觀

媒體服務帳戶會保留單位類型，決定處理媒體處理工作的速度與相關聯。 您可以選擇下列保留的單位類型之間︰ **S1**、 **S2**或**S3**。 例如，相同的編碼工作執行速度當您使用**S2**保留單位類型比較**S1**類型。 如需詳細資訊，請參閱[保留單位類型](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)。

除了指定保留的單位類型，您可以指定保留單位提供您的帳戶。 能夠保留單位的數字會決定指定帳戶可以同時處理的媒體任務數量。 例如，如果您的帳戶有五個保留的單位，然後將的長度不同時執行五個媒體工作為有要處理的工作。 剩餘任務等待佇列中，將會取得挑選正在執行的工作完成時，依序處理。 如果帳戶並沒有提供任何保留的單位，然後工作挑選循序。 在此情況下，等待和之間的時間完成一項工作的下一個開始而定的資源可用性系統中。

## <a name="choosing-between-different-reserved-unit-types"></a>選擇不同的保留的單位類型

下表可協助您選擇不同的編碼速度時做出決策。 也提供幾個事先情況下，並提供可供您下載的視訊，您可以在其執行您自己的測試 SA Url:

案例|**S1**|**S2**|**S3**|
----------|------------|----------|------------
預定的使用案例| 單一位元率編碼。 <br/>在 SD 或解決方案，以下的檔案不時間機密、 低成本。|單一位元率，多個位元率編碼。<br/>SD] 與 [HD 編碼標準的使用方式。 |單一位元率，多個位元率編碼。<br/>完整的 HD 及 4 K 解析度影片。 時間機密、 更快速地作業編碼。 
基準|[輸入檔案︰ 5 分鐘長 640x360p 29.97 在框架/第二個](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)。<br/><br/>編碼方式的單一位元率 MP4 檔案，請在相同的解析度，大約需要 11 分鐘。|[輸入的檔案︰ 5 分鐘長 1280x720p 29.97 在框架/第二個](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>使用 「 H264 單一位元率 720p 「 編碼預設會花費約 5 分鐘。<br/><br/>編碼方式和 「 H264 多個位元率 720p 「 預設大約需要 11.5 分鐘。|[輸入檔案︰ 5 分鐘長 1920x1080p 29.97 在框架/第二個](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)。 <br/><br/>使用 「 H264 單一位元率 1080p 「 編碼預設會花費 2.7 分鐘。<br/><br/>編碼方式和 「 H264 多個位元率 1080p 「 預設大約需要 5.7 分鐘。

##<a name="considerations"></a>考量

>[AZURE.IMPORTANT] 檢閱本節所述的考量。  

- 保留的單位工作平行處理所有的媒體處理作業，包括工作使用 Azure 媒體索引編製索引作業。  不過，不同於編碼，編製索引作業不可以處理速度更快的速度更快保留單位。

- 如果使用共用的資料庫，也就是任何保留的單位，然後編碼任務沒有相同的效能與 S1 RUs 一樣。 不過，就是執行沒有上限，任務可以花在佇列狀態，並在任何時候，最多一個工作的時間。

- 下列資料中心不提供**S2**保留單位類型︰ 巴西南部、 印度西、 印度中央及印度南部。

- 下列資料中心不提供**S3**保留單位類型︰ 巴西南部，印度西部印度中央。

- 最多 24 小時制期間所指定的單位用於計算的成本。


##<a name="quotas-and-limitations"></a>配額和限制

配額和限制，以及如何開啟支援票證的相關資訊，請參閱[配額和限制](media-services-quotas-and-limitations.md)。

##<a name="next-step"></a>下一步

獲得擁有下列其中一種技術縮放比例的媒體處理任務︰ 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [入口網站](media-services-portal-scale-media-processing.md)
- [其餘](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
