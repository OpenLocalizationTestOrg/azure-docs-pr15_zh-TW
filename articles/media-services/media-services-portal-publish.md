<properties
    pageTitle="  發佈 Azure 入口網站的內容 |Microsoft Azure"
    description="本教學課程中會引導您發佈 Azure 入口網站與內容的步驟。"
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="juliako"/>

# <a name="publish-content-with-the-azure-portal"></a>發佈 Azure 入口網站的內容

> [AZURE.SELECTOR]
- [入口網站](media-services-portal-publish.md)
- [.NET](media-services-deliver-streaming-content.md)
- [其餘](media-services-rest-deliver-streaming-content.md)

## <a name="overview"></a>概觀

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。 

若要可讓您的使用者可以用於串流或下載內容的 URL，您必須建立定位器 「 發佈 」 您的資產。 定位器提供存取資產中所包含的檔案。 媒體服務支援定位器的兩種︰ 

- 串流 (OnDemandOrigin) 定位器，用於調整串流 （例如，將資料流 MPEG 破折號、 HLS 或平滑串流）。 若要建立串流定位器您資產必須包含.ism 檔案。 
- 漸進 (SA) 定位器，用於傳遞視訊經由漸進下載。


串流 URL 具有下列的格式，您可以使用它來播放平滑串流資產。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

若要建立串流 URL HLS，附加 (格式 = m3u8 aapl) 的 url。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

若要建立串流 URL 的 MPEG 破折號，附加 (格式 = mpd-時間-csf) 的 url。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

SA URL 有下列格式。

    {blob container name}/{asset name}/{file name}/{SAS signature}

如需詳細資訊，請參閱[傳送內容概觀](media-services-deliver-content-overview.md)。

>[AZURE.NOTE] 如果您是使用入口網站建立定位器 2015 年 3 月之前，會建立定位器兩年到期日期。  

若要更新定位器到期日，請使用[其餘](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator )或[.NET](http://go.microsoft.com/fwlink/?LinkID=533259) Api。 請注意，當您更新 SA 定位器的到期日，URL 就會變更。

### <a name="to-use-the-portal-to-publish-an-asset"></a>使用發佈資產的入口網站

若要使用入口網站發佈資產，請執行下列動作︰

1. 在[Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
1. 選取 [**設定** > **資產**。
1. 選取您想要發佈的資產。
1. 按一下 [**發佈**] 按鈕。
1. 選取定位器類型。
2. 按下**新增**。

    ![發佈](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL 會新增至**發佈 Url**的清單。

## <a name="play-content-from-the-portal"></a>播放入口網站中的內容

Azure 入口網站提供內容，您可以使用以測試您的視訊播放程式。

按一下所要的視訊，然後按一下 [**播放**] 按鈕。

![發佈](./media/media-services-portal-vod-get-started/media-services-play.png)

一些事項︰

- 請確定已發行視訊。
- 此**Media player**播放從預設的串流結束點。 如果您想要從非預設串流端點，請按一下 [複製 URL，並使用其他的播放程式。 例如， [Azure 媒體服務播放程式](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
- 必須執行您要從中串流串流結束點。  
- 若要從串流端點串流，您應該新增至少有一個串流單位。 如需詳細資訊，請參閱[本](media-services-portal-scale-streaming-endpoints.md)主題。   

##<a name="next-steps"></a>後續步驟

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


