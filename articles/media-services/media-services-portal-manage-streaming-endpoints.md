<properties 
    pageTitle="管理 Azure 入口網站的串流端點 |Microsoft Azure" 
    description="本主題說明如何管理串流端點 Azure 入口網站。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    writer="juliako" 
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


#<a name="manage-streaming-endpoints-with-the-azure-portal"></a>管理串流端點 Azure 入口網站

## <a name="overview"></a>概觀

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。 

Microsoft Azure 媒體服務**串流結束點**代表可以發表內容，直接用戶端播放程式的應用程式，或以內容傳遞網路 (CDN) 進一步分配串流服務。 媒體服務也會提供 Azure CDN 的緊密整合。 即時資料流或隨選資產媒體服務帳戶中的視訊，可使用 StreamingEndpoint 服務的輸出資料流。

此外，您可以控制串流端點服務處理成長的頻寬需求來調整串流單位的容量。 建議您為生產環境中的應用程式配置一或多個刻度] 單位。 串流單位提供給您可以購買 200 Mbps 量的專用的出口容量和其他功能，包括︰[動態包裝](media-services-dynamic-packaging-overview.md)、 CDN 整合，以及進階的設定。

>[AZURE.NOTE]您只被付費您串流端點時的執行狀態。

本主題提供主要功能所提供的串流結束點的概觀。 本主題也會說明如何使用 Azure 入口網站管理資料流結束點。 如需如何不按比例縮放的串流端點，請參閱[本](media-services-portal-scale-streaming-endpoints.md)主題資訊。

## <a name="start-managing-streaming-endpoints"></a>開始管理串流端點

若要管理您的帳戶串流端點，請執行下列動作。

1. 在[Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 在 [**設定**] 視窗中，選取 [**串流結束點**]。

    ![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##<a name="adddelete-a-streaming-endpoint"></a>新增/移除串流的端點

新增/移除串流結束點，使用 [Azure 入口網站，請執行下列動作︰

1. 若要新增的串流端點，按一下 [ **+ 端點**頂端的頁面]。 
2. 若要刪除串流結束點，按下 [**刪除**] 按鈕。 

    您無法刪除預設串流結束點。
2. 按一下 [**開始**] 按鈕，即可開始串流結束點]。

    ![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

根據預設，您可以讓兩個串流結束點。 如果您需要更多要求，請參閱[配額和限制](media-services-quotas-and-limitations.md)。
    
##<a id="configure_streaming_endpoints"></a>串流端點的設定

串流結束點，可讓您必須至少 1 刻度] 單位時，設定下列內容︰ 

- 存取控制
- 快取控制項
- 跨網站存取原則

如需這些屬性的詳細資訊，請參閱[StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)。

您可以設定串流結束點，執行下列動作︰

1. 選取您想要設定的串流端點。
1. 按一下 [**設定**]。
  
以下欄位的簡短描述。

![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. 最大快取原則︰ 用來設定 served 透過此串流端點的資產的快取為單位的週期。 如果沒有值設定，則會使用預設值。 也可以直接在 Azure 儲存體中定義的預設值。 如果 Azure cdn 到底啟用串流端點，您應該未設定快取原則值小於 600 秒。  

2. 允許的 IP 位址︰ 用來指定要連線到已發佈的串流端點允許的 IP 位址。 如果未指定的 IP 位址，任何 IP 位址能連線。 為單一的 IP 位址 (例如，「 10.0.0.1 」)、 IP 範圍使用的 IP 位址和 CIDR 子網路遮罩 (例如，「 10.0.0.1/22 」) 或使用的 IP 位址和虛線小數位數的子網路遮罩的 IP 範圍時，可以指定 IP 位址 (例如，「 10.0.0.1(255.255.255.0)')。

3. Akamai 簽章頁首驗證設定︰ 用來指定簽章頁首驗證要求從 Akamai 伺服器的設定方式。 到期日是以 utc 表示。



##<a id="enable_cdn"></a>啟用 Azure CDN 整合

您可以指定啟用 Azure CDN 整合串流端點 （其預設為停用。）

若要設定為 true 的 Azure CDN 整合︰

- 串流端點必須至少有一個串流單位。 如果您要設為 0 的刻度] 單位之後，您必須先停用 CDN 整合。 依預設您建立新的資料流時也會自動設定端點一個串流單位。

- 停止狀態必須是串流結束點。 Cdn 到底取得啟用之後，您就可以開始串流結束點。 

可能需要最 Azure CDN 整合，以取得啟用 90 最小值。  需要兩個小時的時間，在所有 CDN 會顯示在 [作用中變更。

在所有 Azure 資料中心中啟用 CDN 整合︰ 美國西部、 美國東部、 北歐洲、 西歐、 日本西、 日本東亞、 東南亞括住及中式地址。

一旦啟用，**存取控制**設定會停用。

![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] **從 Verizon Azure CDN**實作 Azure CDN azure 媒體服務整合。  如果您想要**從 Akamai Azure CDN**用於 Azure 媒體服務，您必須[以手動方式設定端點](../cdn/cdn-create-new-endpoint.md)。  如需有關 Azure CDN 功能的詳細資訊，請參閱[CDN 概觀](../cdn/cdn-overview.md)。

###<a name="additional-considerations"></a>其他考量

- Cdn 到底是啟用串流端點，用戶端無法直接從來源要求的內容。 如果您需要測試您的內容，包含或不含 CDN 的功能，您可以建立未啟用的 CDN 的另一個串流端點。
- 將資料流端點主機名稱會保持不變啟用 CDN 之後。 您不需要對您的媒體服務工作流程進行任何變更之後 CDN 已啟用。 例如，如果您串流端點主機名稱是 strasbourg.streaming.mediaservices.windows.net，啟用 CDN 之後，就會使用完全相同的主機名稱。
- 為新的資料流端點，您可以啟用 CDN 只要藉由建立新的結束點。為現有的資料流端點，必須先停止端點，然後啟用 [cdn 到底。
 

如需詳細資訊，請參閱[Azure CDN （內容傳遞網路） 宣佈使用 Azure 媒體服務整合](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/)。


##<a name="next-steps"></a>後續步驟

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
