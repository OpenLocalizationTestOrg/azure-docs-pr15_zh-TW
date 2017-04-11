<properties 
    pageTitle="使用 Azure 媒體服務進行 Widevine 授權的合作夥伴 |Microsoft Azure" 
    description="本文將說明如何使用 Azure 媒體服務 （反飛彈系統） 傳送反飛彈系統與 PlayReady 和 Widevine DRMs 動態加密資料流。 PlayReady 授權來自媒體服務 PlayReady 授權伺服器，Widevine 授權已傳送 castLabs 授權伺服器。" 
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
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>使用 Azure 媒體服務進行 Widevine 授權的合作夥伴

##<a name="overview"></a>概觀

Microsoft Azure 媒體服務可讓您進行 MPEG 虛線受到 Widevine DRM，每個一般加密 (CENC) 規格已加密。

開始使用媒體服務.NET SDK 版本 3.5.2，媒體服務可讓您設定 Widevine 授權範本，並取得 Widevine 授權。 您也可以使用下列反飛彈系統合作夥伴以協助您進行 Widevine 授權︰ [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)， [EZDRM](http://ezdrm.com/)， [castLabs](http://castlabs.com/company/partners/azure/)。

##<a name="castlabs"></a>castLabs

您可以使用[castLabs](http://castlabs.com/company/partners/azure/)進行 Widevine 授權。 如需詳細資訊，請參閱[使用 castLabs 進行 DRM 授權 Azure 媒體服務](media-services-castlabs-integration.md)

##<a name="axinom"></a>Axinom

您可以使用[Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)進行 Widevine 授權。 如需詳細資訊，請參閱[使用 Axinom 進行 DRM 授權 Azure 媒體服務](media-services-axinom-integration.md)


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>另請參閱

[使用 PlayReady 及/或 Widevine 動態一般加密](media-services-protect-with-drm.md)

[Mingfei 的部落格](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

