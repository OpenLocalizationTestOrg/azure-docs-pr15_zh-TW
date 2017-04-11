<properties 
    pageTitle="授權 Microsoft® 平滑串流用戶端移轉套件" 
    description="進一步瞭解如何以授權此 Microsoft® 平滑串流用戶端移轉套件。" 
    services="media-services" 
    documentationCenter="" 
    authors="xpouyat,vsood" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016"  
    ms.author="xpouyat"/>

#<a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>授權 Microsoft® 平滑串流用戶端移轉套件

##<a name="overview"></a>概觀

Microsoft 平滑串流用戶端移轉套件 (簡稱為**SSPK** ) 是平滑串流的用戶端實作最佳化協助內嵌的裝置製造商、 纜線和行動電信業、 內容的服務提供者、 話筒製造商、 獨立軟體廠商 (Isv)，以及解決方案提供者建立產品及服務串流調整串流內容平滑串流格式。 SSPK 是平滑串流的用戶端可以使用任何裝置和平台的人，當中的裝置與平台獨立實作。 

包含在下面是高層級的架構並 IIS 平滑串流移轉套件] 方塊是由 Microsoft 提供的平滑串流的用戶端實作包含所有的核心邏輯播放平滑串流的內容。 這是然後當中特定裝置的平台的合作夥伴，透過實作適當的介面。 

![SSPK](./media/media-services-sspk/sspk-arch.png)

##<a name="description"></a>描述

SSPK 被授權提供絕佳的商業價值的字詞。 SSPK 授權提供使用產業︰

- C + + 平滑串流移轉套件來源 
  - 實作平滑串流的用戶端功能
  - 新增剖析，heuristics，緩衝等邏輯的格式。
- 播放程式的應用程式 Api 
  - 媒體播放程式應用程式的互動的程式設計介面
- 平台抽象層級 （朋友） 介面 
  - 作業系統 （對話、 通訊端） 互動的程式設計介面
- 硬體抽象層 (HAL) 介面 
  - 程式設計介面互動的硬體 A / V 解碼器 （解碼、 呈現）
- 數位版權管理 (DRM) 介面 
  - 處理 DRM 透過 DRM 抽象層 (DAL) 程式設計介面
  - Microsoft PlayReady 移轉套件隨附分開，但整合透過這個介面。 如需 Microsoft PlayReady 裝置授權的詳細資訊，請按一下[這裡](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl)。
- 實作範例 
  - 將朋友實作 Linux 的範例
  - 範例 HAL 實作 GStreamer

##<a name="licensing-options"></a>授權選項

Microsoft 平滑串流用戶端移轉套件可供使用人均下兩個不同的授權合約︰ 一個用於開發平滑串流用戶端過渡產品，另一個用於發佈平滑串流用戶端最終產品的使用者。
 
- 晶片組製造商、 系統整合，或需要移轉開發過渡產品套件原始程式碼獨立軟體廠商 (Isv)，您應該執行 Microsoft 平滑串流用戶端移轉套件**過渡產品授權**。
- 裝置製造商或 Isv 需要平滑串流用戶端最終產品的通訊群組的權限給使用者，請執行 Microsoft 平滑串流用戶端移轉套件**最終產品的授權**。

###<a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft 平滑串流移轉套件過渡產品授權的用戶端

在此授權，Microsoft 提供平滑串流用戶端移轉 Kit 與開發，並將平滑串流用戶端過渡產品發送給其他平滑串流用戶端移轉套件裝置授權的發佈平滑串流用戶端最終產品的必要的智慧財產權限。

####<a name="fee-structure"></a>費用結構

美國 $50000 一次性授權費用提供存取 「 平滑串流用戶端移轉套件。 

###<a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft 平滑串流用戶端，移轉套件最終產品的授權

在此授權，Microsoft 提供所有必要智慧財產權平滑串流用戶端過渡產品接收其他平滑串流用戶端移轉套件授權的使用者，並發佈公司品牌平滑串流用戶端最終產品的使用者。

####<a name="fee-structure"></a>費用結構

帶有平滑串流用戶端最終產品會提供為免權利模式下︰

- 每次運送的裝置實作 $ 0.10
- 免權利長 $ 50000，每年
- 沒有免前 10000 個裝置實作每年 

##<a name="licensing-procedure-and-sspk-access"></a>授權的程序和 SSPK 存取

請以電子郵件[sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com)所有授權查詢。

以註冊過渡授權的存取[SSPK 分配入口網站](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/)。

過渡和最終 SSPK 使用人均可以送出技術的問題[smoothpk@microsoft.com](mailto:smoothpk@microsoft.com)。

##<a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft 平滑串流中期產品合約授權的用戶端

- 商務解決方案，Inc adroit
- 進階的數位廣播索
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis 技術 ltd
- Alticast Corporation
- Amazon 數位服務國廣兌
- AVC 多媒體軟體，有限公司
- Cavium 國廣兌
- EchoStar 購買 Corporation
- Enseo 國廣兌
- Fluendo S.A.
- HANDAN BroadInfoCom，有限公司
- Infomir GMBH
- Irdeto 美國 Inc.
- 全域服務 BV 自由
- MediaTek Inc.
- MStar 共同，Ltd
- Nintendo，有限公司
- OpenTV 國廣兌
- Saffron 數位限制
- Sichuan Changhong 電子 Co.，Ltd
- SoftAtHome
- Sony Corporation
- Tatung 技術 Inc.
- TCL Technoly Electronics (Huizhou)，有限公司
- Vestel Elektronik Sanayi 我們 Ticaret A.S.
- VisualOn 國廣兌
- ZTE Corporation

##<a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft 平滑串流最終產品合約授權的用戶端

- 進階的數位廣播索
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis 技術 ltd
- Amazon 數位服務國廣兌
- AmTRAN 技術，有限公司
- Arcadyan 技術 Corporation
- ATMACA ELEKTRONİK 舊。 我們 TİC。 A.Ş
- 英國天空廣播限制
- CastPal 技術 Inc.Shenzhen
- Compal Electronics 國廣兌
- Dongguan 數位 AV 技術企業，ltd
- EchoStar 購買 Corporation
- Enseo 國廣兌
- Filmflex 影片限制
- Fluendo S.A.
- Gibson 創新限制
- Haier 資訊 Applicantion S.R.L
- HANDAN BroadInfoCom，有限公司
- Homecast Co.，Ltd
- Hon Hai 精確度產業，有限公司
- Infomir GMBH
- Kaonmedia，有限公司
- KDDI Corporation
- Nintendo，有限公司
- 橘色索
- Saffron 數位限制
- Sagemcom 寬頻 SA
- Shenzhen Coship Electronics CO.，LTD
- Shenzhen Jiuzhou 電子 Co.，Ltd
- Shenzhen Skyworth 數位技術 Co.，Ltd
- Sichuan Changhong 電子，有限公司
- Skardin 工業企業
- 天空 Deutschland Fernsehen GmbH 與 Co.公斤
- SmarDTV S.A.
- SoftAtHome
- Sony Corporation
- 有限的 TCL 海外行銷 （近岸澳門商業）
- Technicolor 傳遞技術 SA
- Tongfang 全域 ltd
- 地區生活產品及服務 Corporation
- 通用媒體 Corporation /Slovakia/ s.r.o.
- VIZIO 國廣兌
- Wistron Corporation
- ZTE Corporation

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
