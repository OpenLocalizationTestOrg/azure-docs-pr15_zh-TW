<properties 
    pageTitle="Azure 媒體服務.NET SDK 建立篩選" 
    description="本主題說明如何建立篩選，讓您的用戶端可以使用這些資料流特定章節資料流。 媒體服務會建立達到此串流選擇性的動態資訊清單。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="07/18/2016"
    ms.author="juliako;cenkdin"/>


#<a name="creating-filters-with-azure-media-services-net-sdk"></a>Azure 媒體服務.NET SDK 建立篩選

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [其餘](media-services-rest-dynamic-manifest.md)

開始使用 2.11 發行，媒體服務可讓您定義您的資產的篩選。 這些篩選有伺服器端規則，讓您的客戶選擇項目，例如︰ 播放只有一段視訊 （而非播放整個視訊），或指定的客戶的裝置可處理 （而非所有轉譯與資產相關聯的） 的音訊和視訊轉譯子集。 您的資產此篩選的方法是透過串流視訊，根據指定的篩選器的客戶的要求時所建立的**動態資訊清單**s。

篩選及動態資訊清單的相關詳細資訊，請參閱[動態資訊清單概觀](media-services-dynamic-manifest-overview.md)。

本主題說明如何使用媒體服務.NET SDK 建立、 更新及刪除篩選。 


注意是否您要更新的篩選，它可能會花 2 分鐘的時間資料流重新整理規則的結束點。 如果內容提供使用這個篩選器 （和快取中 proxy 和 CDN 快取），則更新此篩選會導致播放程式失敗。 建議後更新篩選清除快取。 如果不可行這個選項，請考慮使用不同的篩選。 

##<a name="types-used-to-create-filters"></a>用來建立篩選的類型

建立篩選時，會使用下列類型︰ 

- **IStreamingFilter**。  此類型會根據下列 REST API[篩選](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- **IStreamingAssetFilter**。 此類型會根據下列 REST API [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- **PresentationTimeRange**。 此類型會根據下列 REST API [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- **FilterTrackSelectStatement**和**IFilterTrackPropertyCondition**。 這些類型會根據下列 REST Api [FilterTrackSelect 和 FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)


##<a name="createupdatereaddelete-global-filters"></a>建立/更新/已讀取/刪除全域篩選

下列程式碼會顯示如何建立、 更新，請閱讀，請使用.NET 和刪除資產篩選。
    
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
                
    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
    
    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);
    
    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
    
    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();
    
    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


##<a name="createupdatereaddelete-asset-filters"></a>建立/更新/已讀取/刪除資產篩選

下列程式碼會顯示如何建立、 更新，請閱讀，請使用.NET 和刪除資產篩選。

    
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
    
        
    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());
    
    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);
    
    filter.Update();
    
    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);
    
    // Delete
    filterUpdated.Delete();
    



##<a name="build-streaming-urls-that-use-filters"></a>建立串流使用篩選的 Url

如何發佈及發表您的資產的詳細資訊，請參閱[發表內容至客戶概觀](media-services-deliver-content-overview.md)文件。


下列範例會顯示如何將篩選新增至您串流的 Url。

**MPEG 破折號** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live 串流 (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live 串流 (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**帶有平滑串流**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>另請參閱 

[動態資訊清單概觀](media-services-dynamic-manifest-overview.md)
 

