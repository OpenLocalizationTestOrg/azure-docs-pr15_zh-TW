<properties 
    pageTitle="設定傳送單一位元率即時資料流 NewTek TriCaster 編碼器 |Microsoft Azure" 
    description="本主題說明如何設定 Tricaster 即時編碼器單一位元率資料流傳送有啟用即時編碼的反飛彈系統頻道。" 
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
    ms.author="juliako;cenkd;anilmur"/>

#<a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>若要傳送單一位元率即時資料流使用 NewTek TriCaster encoder

> [AZURE.SELECTOR]
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [即時的元素](media-services-configure-elemental-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

本主題說明如何設定[NewTek TriCaster](http://newtek.com/products/tricaster-40.html)即時編碼器單一位元率資料流傳送有啟用即時編碼的反飛彈系統頻道。 如需詳細資訊，請參閱[使用來執行即時編碼與 Azure 媒體服務啟用的頻道](media-services-manage-live-encoder-enabled-channels.md)。

本教學課程中會顯示如何使用 Azure 媒體服務總管 (AMSE) 工具管理 Azure 媒體服務 （反飛彈系統）。 這項工具只能在 Windows 電腦上執行。 如果您是在 Mac 或 Linux 上，使用 Azure 入口網站建立[頻道](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)及[程式](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program)。

>[AZURE.NOTE]當用 Tricaster 傳送摘要的即時編碼啟用時的反飛彈系統頻道的作用中，有可能視訊/音訊故障您即時事件中如果您是使用 Tricaster，例如快速摘要，之間剪下或從 slates 切換的特定功能。 反飛彈系統小組處理修正這些問題之前，它不是建議使用這些功能。


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

![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. 指定頻道的名稱，[描述] 欄位為選用步驟。 [頻道設定] 底下選取**標準**即時編碼] 選項設定為 [ **RTMP**輸入通訊協定。 您可以將所有其他現狀設定。


請確定已選取 [**啟動新頻道現在**。

3. 按一下 [**建立頻道**]。
![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

>[AZURE.NOTE] 頻道可以花費 20 分鐘，才能開始。


啟動頻道時可以[設定編碼器](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp)。

>[AZURE.IMPORTANT] 請注意計費會在頻道進入好狀態時，會立即開始。 如需詳細資訊，請參閱[頻道的狀態](media-services-manage-live-encoder-enabled-channels.md#states)。

##<a id=configure_tricaster_rtmp></a>設定 NewTek TriCaster encoder

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

1. 建立新的**NewTek TriCaster**專案根據正在使用哪些視訊輸入的來源。 
2. 一次在專案，尋找 [**串流**] 按鈕，然後按一下齒輪圖示，以存取資料流設定功能表旁邊。

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. 當已開啟的功能表時，[連線] 標題下的 [**新增**]。 當系統提示您的連線類型，選取 [ **Adobe Flash**]。

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)

4. 按一下**[確定]**。

5. 現在可以**串流的設定檔**] 底下，按一下下拉式箭號，然後瀏覽至**瀏覽**匯入 FMLE 設定檔。

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)

6. 瀏覽至儲存設定的 FMLE 設定檔。
7. 選取，然後按**[確定]**。

    設定檔上傳後繼續進行下一個步驟。

6. 若要將其指派給 Tricaster **RTMP 端點**取得頻道的輸入的 URL。
    
    瀏覽回 [AMSE 工具]，並查看頻道完成狀態。 一旦狀態已經從 [**開始****執行**，您可以取得輸入的 URL。
      
    頻道執行時，以滑鼠右鍵按一下頻道名稱、 瀏覽下將游標暫留在**將輸入 URL 複製到剪貼簿]** ，然後選取**主要輸入 URL**。  
    
    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)

7. 在 [Tricaster 專案內 [ **Flash 伺服器**] 下的 [**位置**] 欄位中，貼上這項資訊。 也指派**串流識別碼**] 欄位中的資料流名稱。 

    資料流資訊加入至 FMLE 的設定檔，它也可匯入這個區段按一下 [**匯入設定**、 瀏覽至儲存 FMLE 設定檔，按一下**[確定]**。 相關的快速伺服器欄位應填入 FMLE 中的資訊。

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)

9. 完成後，按一下 [在畫面底部的**[確定**]。 準備好將 Tricaster 視訊及音訊輸入時，開始串流到反飛彈系統**串流**按鈕即可。

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

>[AZURE.IMPORTANT]按一下 [**資料流**之前，您**必須**確定頻道好。 
>此外，請確定未使好狀態不超過 > 15 分鐘摘要輸入比重頻道。 

##<a name="test-playback"></a>測試播放
  
1. 瀏覽至 [AMSE 工具]，然後以滑鼠右鍵按一下要測試的頻道。 從功能表中，將游標停留在**播放預覽**，選取**對象 Azure Media Player。**  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

如果資料流出現在播放程式，然後編碼器已經設定妥當連線至反飛彈系統。 

如果收到錯誤，則頻道需要重設，並調整 encoder 設定。 請參閱[疑難排解](media-services-troubleshooting-live-streaming.md)主題指導方針。  

##<a name="create-a-program"></a>建立程式

1. 一旦確認頻道播放，請建立一個程式。 在 AMSE 工具的 [ **Live** ] 索引標籤下程式區域內按一下滑鼠右鍵，然後選取 [**建立新的程式**。  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)

2. 命名程式，如有需要調整**封存視窗長度**（預設值為 4 小時）。 您也可以指定儲存位置，或離開為預設值。  
3. 核取 [**啟動程式現在**方塊。
4. 按一下 [**建立程式**。  
  
    附註︰ 程式建立的時間比頻道建立。    
 
5. 後應用程式，請確認 [，以滑鼠右鍵按一下程式和瀏覽至**播放程式**，然後選取**與 Azure 媒體播放程式**的 [播放]。  
6. 一旦確認，以滑鼠右鍵按一下程式並選取 [**複製到剪貼簿的輸出 URL** （或這項資訊擷取] 功能表的**程式資訊及設定**選項）。 

現在準備好在播放機，內嵌或分佈的對象為高階即時檢視資料流。  


## <a name="troubleshooting"></a>疑難排解

請參閱[疑難排解](media-services-troubleshooting-live-streaming.md)主題指導方針。 


##<a name="next-step"></a>下一步

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
