<properties 
    pageTitle="設定傳送單一位元率即時資料流元素 Live 編碼器 |Microsoft Azure" 
    description="本主題說明如何設定元素 Live 編碼器單一位元率資料流傳送有啟用即時編碼的反飛彈系統頻道。" 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="cenkdin;anilmur;juliako"/>

#<a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>使用元素 Live 編碼器傳送單一位元率即時資料流

> [AZURE.SELECTOR]
- [即時的元素](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

本主題說明如何設定[元素 Live](http://www.elementaltechnologies.com/products/elemental-live)編碼器單一位元率資料流傳送有啟用即時編碼的反飛彈系統頻道。  如需詳細資訊，請參閱[使用來執行即時編碼與 Azure 媒體服務啟用的頻道](media-services-manage-live-encoder-enabled-channels.md)。

本教學課程中會顯示如何使用 Azure 媒體服務總管 (AMSE) 工具管理 Azure 媒體服務 （反飛彈系統）。 這項工具只能在 Windows 電腦上執行。 如果您是在 Mac 或 Linux 上，使用 Azure 入口網站建立[頻道](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)及[程式](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program)。

##<a name="prerequisites"></a>必要條件

- 必須具備使用元素 Live web 介面建立即時事件。
- [建立 Azure 媒體服務帳戶](media-services-portal-create-account.md)
- 確定執行配置至少有一個串流單位串流結束點。 如需詳細資訊，請參閱[媒體服務帳戶管理串流結束點](media-services-portal-manage-streaming-endpoints.md)。
- 安裝最新版的[AMSE](https://github.com/Azure/Azure-Media-Services-Explorer)工具。
- 啟動工具，並連線到您的反飛彈系統帳戶。

##<a name="tips"></a>秘訣

- 可能的話，請使用硬式編碼網際網路連線。
- 建議法則決定頻寬需求時可按兩下串流 bitrates。 雖然這不是必要的需求，就能協助降低網路擁擠的影響。
- 何時使用軟體編碼器，請關閉任何不需要的程式。

## <a name="elemental-live-with-rtp-ingest"></a>使用 RTP 元素 Live 內嵌

本節說明如何設定透過 RTP 傳送單一位元率即時資料流元素 Live encoder。  如需詳細資訊，請參閱[透過 RTP MPEG TS 資料流](media-services-manage-live-encoder-enabled-channels.md#channel)。

### <a name="create-a-channel"></a>建立頻道

1.  在 AMSE 工具中，瀏覽至**Live** ] 索引標籤，然後以滑鼠右鍵按一下 [頻道] 區域中。 選取**建立頻道...** 從功能表。

![元素](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. 指定頻道的名稱，[描述] 欄位為選用步驟。 [頻道設定] 底下選取**標準**即時編碼] 選項中，輸入通訊協定設定為 [ **RTP (MPEG TS)**。 您可以將所有其他現狀設定。


請確定已選取 [**啟動新頻道現在**。

3. 按一下 [**建立頻道**]。
![元素](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE] 頻道可以花費 20 分鐘，才能開始。

啟動頻道時可以[設定編碼器](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp)。

>[AZURE.IMPORTANT] 請注意計費會在頻道進入好狀態時，會立即開始。 如需詳細資訊，請參閱[頻道的狀態](media-services-manage-live-encoder-enabled-channels.md#states)。

###<a id=configure_elemental_rtp></a>設定元素 Live 編碼器 

在本教學課程中會使用下列的輸出設定。 這一節的其他說明更多詳細資料的設定的步驟。 

**影片**︰
 
- 轉碼器︰ H.264 
- 設定檔︰ 高 (層級 4.0) 
- 位元率︰ 5000 kbps 
- 主畫面︰ 2 秒數 （60 秒） 
- 外框工資率︰ 30
 
**音訊**︰

- 轉碼器︰ AAC (LC) 
- 位元率︰ 192 kbps 
- 範例率︰ 秒 44.1 kHz


####<a name="configuration-steps"></a>設定步驟

1. 瀏覽至該**元素 Live** web 介面，並設定為**UDP/TS**串流 encoder。 

2. 一旦建立新的事件，請向下輸出群組捲動，並新增**UDP/TS**輸出群組。 

3. 建立新的輸出選取**新的資料流**，然後按一下 [**新增輸出**。  
    
    ![元素](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
    
    >[AZURE.NOTE] 建議的元素事件具有設定為 「 系統時鐘 」，以協助編碼器串流失敗重新連線時。

4. 現在，輸出建立之後，請按一下 [**新增資料流**]。 現在您可以設定的輸出設定。 
5. 向下捲動 「 資料流 1 」 剛建立的在左側的 [**視訊**] 索引標籤並展開 [**進階**設定] 區段。 

    ![元素](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    雖然元素 Live 各種可用的自訂，建議使用下列設定的快速入門串流到反飛彈系統。 
    
    - 解決方法︰ 1280 x 720 
    - 畫面播放速率︰ 30 
    - GOP 大小︰ 60 圖文框 
    - 交錯模式︰ 漸進 
    - 位元率︰ 5000000 元/s （這可以調整根據網路限制） 
    

    ![元素](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. 取得頻道的輸入的 URL。
    
    瀏覽回 [AMSE 工具]，並查看頻道完成狀態。 一旦狀態已經從 [**開始****執行**，您可以取得輸入的 URL。
      
    頻道執行時，以滑鼠右鍵按一下頻道名稱、 瀏覽下將游標暫留在**將輸入 URL 複製到剪貼簿]** ，然後選取**主要輸入 URL**。  
    
    ![元素](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
    
1. 在 [**主要目的**欄位中的元素，貼上這項資訊。 所有其他設定可以保留預設值。
    
    ![元素](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    額外的重複，重複這些步驟次要輸入 url 為 UDP/TS 串流建立不同的 「 輸出] 索引標籤。
    
7. （如果編輯現有的事件），按一下 [**建立**（如果已建立新的事件） 或**更新**，然後繼續開始 encoder。 

>[AZURE.IMPORTANT]按一下 [在元素 Live web 介面上的 [**開始**之前，您**必須**確定頻道準備好。 
>此外，請確定不離開頻道在準備好狀態不超過 > 15 分鐘事件。

執行資料流 30 秒後，請瀏覽回 AMSE 工具和測試播放。  

###<a name="test-playback"></a>測試播放
  
1. 瀏覽至 [AMSE 工具]，然後以滑鼠右鍵按一下要測試的頻道。 從功能表中，將游標停留在**播放預覽**，選取**對象 Azure Media Player。**  

    ![元素](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

如果資料流出現在播放程式，然後編碼器已經設定妥當連線至反飛彈系統。 

如果收到錯誤，則頻道需要重設，並調整 encoder 設定。 請參閱[疑難排解](media-services-troubleshooting-live-streaming.md)主題指導方針。   

###<a name="create-a-program"></a>建立程式

1. 一旦確認頻道播放，請建立一個程式。 在 AMSE 工具的 [ **Live** ] 索引標籤下程式區域內按一下滑鼠右鍵，然後選取 [**建立新的程式**。  

    ![元素](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. 命名程式，如有需要調整**封存視窗長度**（預設值為 4 小時）。 您也可以指定儲存位置，或離開為預設值。  
3. 核取 [**啟動程式現在**方塊。
4. 按一下 [**建立程式**。  
  
    附註︰ 程式建立的時間比頻道建立。    
 
5. 後應用程式，請確認 [，以滑鼠右鍵按一下程式和瀏覽至**播放程式**，然後選取**與 Azure 媒體播放程式**的 [播放]。  
6. 一旦確認，以滑鼠右鍵按一下程式並選取 [**複製到剪貼簿的輸出 URL** （或這項資訊擷取] 功能表的**程式資訊及設定**選項）。 

現在準備好在播放機，內嵌或分佈的對象為高階即時檢視資料流。  

## <a name="troubleshooting"></a>疑難排解

請參閱[疑難排解](media-services-troubleshooting-live-streaming.md)主題指導方針。 


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
