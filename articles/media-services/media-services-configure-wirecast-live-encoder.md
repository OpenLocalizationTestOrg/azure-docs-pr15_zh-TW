<properties 
    pageTitle="設定傳送單一位元率即時資料流 Telestream Wirecast 編碼器 |Microsoft Azure" 
    description="本主題說明如何設定 Wirecast 即時編碼器單一位元率資料流傳送有啟用即時編碼的反飛彈系統頻道。 " 
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
    ms.date="10/12/2016"
    ms.author="juliako;cenkdin;anilmur"/>

#<a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>若要傳送單一位元率即時資料流使用 Wirecast encoder

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [即時的元素](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

本主題說明如何設定[Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm)即時編碼器單一位元率資料流傳送有啟用即時編碼的反飛彈系統頻道。  如需詳細資訊，請參閱[使用來執行即時編碼與 Azure 媒體服務啟用的頻道](media-services-manage-live-encoder-enabled-channels.md)。

本教學課程中會顯示如何使用 Azure 媒體服務總管 (AMSE) 工具管理 Azure 媒體服務 （反飛彈系統）。 這項工具只能在 Windows 電腦上執行。 如果您是在 Mac 或 Linux 上，使用 Azure 入口網站建立[頻道](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)及[程式](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program)。


##<a name="prerequisites"></a>必要條件

- [建立 Azure 媒體服務帳戶](media-services-portal-create-account.md)
- 確定執行配置至少有一個串流單位串流結束點。 如需詳細資訊，請參閱[媒體服務帳戶管理串流結束點](media-services-portal-manage-streaming-endpoints.md)
- 安裝最新版的[AMSE](https://github.com/Azure/Azure-Media-Services-Explorer)工具。
- 啟動工具，並連線到您的反飛彈系統帳戶。

##<a name="tips"></a>秘訣

- 可能的話，請使用硬式編碼網際網路連線。
- 建議法則決定頻寬需求時可按兩下串流 bitrates。 雖然這不是必要的需求，就能協助降低網路擁擠的影響。
- 何時使用軟體編碼器，請關閉任何不需要的程式。


## <a name="create-a-channel"></a>建立頻道

1.  在 AMSE 工具中，瀏覽至**Live** ] 索引標籤，然後以滑鼠右鍵按一下 [頻道] 區域中。 選取**建立頻道...** 從功能表。

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. 指定頻道的名稱，[描述] 欄位為選用步驟。 [頻道設定] 底下選取**標準**即時編碼] 選項設定為 [ **RTMP**輸入通訊協定。 您可以將所有其他現狀設定。


請確定已選取 [**啟動新頻道現在**。

3. 按一下 [**建立頻道**]。
![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] 頻道可以花費 20 分鐘，才能開始。

啟動頻道時可以[設定編碼器](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp)。

>[AZURE.IMPORTANT] 請注意計費會在頻道進入好狀態時，會立即開始。 如需詳細資訊，請參閱[頻道的狀態](media-services-manage-live-encoder-enabled-channels.md#states)。

##<a id=configure_wirecast_rtmp></a>設定 Telestream Wirecast encoder

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


###<a name="configuration-steps"></a>設定步驟

1. 開啟 Telestream Wirecast 應用程式在電腦正在使用，而且設定 RTMP 串流。
2. 設定輸出瀏覽至 [**輸出**] 索引標籤，然後選取**輸出設定]**。
    
    請確定**輸出目的地**已設定為**RTMP 伺服器**。
3. 按一下**[確定]**。
4. 在 [設定] 頁面中，設定要**Azure 媒體服務****目標**欄位。
 
    編碼設定檔是預先選取的**Azure H.264 720 p 16:9 (1280 x 720)**。 若要自訂這些設定，向下選取下拉式清單右側的齒輪圖示，然後選擇**新的預設**。

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. 設定 encoder 預設格式]。

    命名預設格式]，再核取下列建議的設定︰

    **視訊**
    
    - Encoder: MainConcept H.264
    - 每秒的圖文框︰ 30
    - 位元率︰ 5000 kbit/sec （可以調整根據網路限制）
    - 設定檔︰ 主要
    - 主要畫面每個︰ 60 圖文框

    **音訊**

    - 目標位元率︰ 192 kbit 秒
    - 範例率︰ 44.100 kHz
     
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. 按下**儲存**。

    [編碼] 欄位現在有可用的選取範圍建立新的設定檔。 

    請確定已選取新的設定檔。

7. 若要將其指派給 Wirecast **RTMP 端點**取得頻道的輸入的 URL。
    
    瀏覽回 [AMSE 工具]，並查看頻道完成狀態。 一旦狀態已經從 [**開始****執行**，您可以取得輸入的 URL。
      
    頻道執行時，以滑鼠右鍵按一下頻道名稱、 瀏覽下將游標暫留在**將輸入 URL 複製到剪貼簿]** ，然後選取**主要輸入 URL**。  
    
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. 在 Wirecast**輸出設定**] 視窗中，在 [輸出] 區段的 [**位址**] 欄位中貼上這項資訊，並指派資料流名稱。 


    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. 選取**[確定]**。

10. 在 [主**Wirecast**畫面上，確認視訊與音訊的輸入的來源準備然後按 [左邊的角落中的 [**資料流**。

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT]按一下 [**資料流**之前，您**必須**確定頻道好。 
>此外，請確定未使好狀態不超過 > 15 分鐘摘要輸入比重頻道。

##<a name="test-playback"></a>測試播放
  
1. 瀏覽至 [AMSE 工具]，然後以滑鼠右鍵按一下要測試的頻道。 從功能表中，將游標停留在**播放預覽**，選取**對象 Azure Media Player。**  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

如果資料流出現在播放程式，然後編碼器已經設定妥當連線至反飛彈系統。 

如果收到錯誤，則頻道需要重設，並調整 encoder 設定。 請參閱[疑難排解](media-services-troubleshooting-live-streaming.md)主題指導方針。  

##<a name="create-a-program"></a>建立程式

1. 一旦確認頻道播放，請建立一個程式。 在 AMSE 工具的 [ **Live** ] 索引標籤下程式區域內按一下滑鼠右鍵，然後選取 [**建立新的程式**。  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

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
