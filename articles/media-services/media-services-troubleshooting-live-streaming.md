<properties 
    pageTitle="疑難排解指南即時資料流 |Microsoft Azure" 
    description="本主題提供建議如何即時串流問題進行疑難排解。" 
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
    ms.date="10/12/2016"  
    ms.author="juliako"/>

#<a name="troubleshooting-guide-for-live-streaming"></a>即時資料流疑難排解指南

本主題提供建議如何一些即時串流問題進行疑難排解。

## <a name="issues-related-to-on-premises-encoders"></a>內部部署編碼器的相關問題 

本節提供建議設定為啟用即時編碼的反飛彈系統頻道傳送單一位元率資料流的內部部署編碼器的相關問題的疑難排解。

###<a name="problem-would-like-to-see-logs"></a>問題︰ 想要查看記錄 

- **潛在問題**︰ 無法尋找 encoder 記錄的可能有幫助偵錯問題。
    
    - **Telestream Wirecast**︰ 您通常可以找到記錄下 C:\Users\{username} \AppData\Roaming\Wirecast\ 
    - **元素 Live**︰ 您可以找到管理入口網站上具有記錄檔的連結。 按一下 [**統計資料**，然後**記錄**]。 在**記錄檔]**頁面上，您會看到的所有 LiveEvent 項目; 記錄清單選取符合您目前的工作階段。 
    - **Flash 媒體 Live Encoder**︰ 您可以找到**記錄目錄...**瀏覽至 [**編碼記錄檔**] 索引標籤。
    
###<a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>問題︰ 沒有任何的輸出漸進的資料流選項

- **潛在問題**︰ 使用 encoder 不會自動非交錯。 

    **疑難排解步驟**︰ 尋找在 encoder 介面就能取消交錯訊號選項。 一次去交錯已啟用，再次檢查有漸進的輸出設定]。 
 
###<a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>問題︰ 嘗試幾個 encoder 輸出設定和仍然無法連線。 

- **潛在問題**︰ Azure 編碼頻道未正確重設。 

    **疑難排解步驟**︰ 請確定編碼器已不再發送至反飛彈系統、 停止以及重設頻道。 一次執行，請嘗試連線您 encoder 以新的設定。 如果此仍無法修正問題，請嘗試建立新頻道的完全，有時候數個失敗次數之後頻道可以損毀。  

- **潛在問題**︰ GOP 大小] 或 [索引鍵的框架設定不是最佳化。 

    **疑難排解步驟**︰ 建議 GOP 大小或畫面間隔是 2 的秒數。 某些編碼器計算圖文框，在這項設定，有些則會使用秒。 例如︰ 輸出 30 每秒畫面格時, GOP 大小會 60 框架，相當於 2 秒鐘。  
     
- **潛在問題**︰ 關閉的連接埠會封鎖資料流。 

    **疑難排解步驟**︰ 當串流透過 RTMP、 時檢查以確認 1935年和 1936年輸出的連接埠是開啟的防火牆或 proxy 設定。 在使用 RTP 串流時，確認已開啟輸出的連接埠 2010年。 


###<a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>問題︰ 當設定串流 encoder RTP 通訊協定，就會無法輸入主機名稱的位置。 

- **潛在問題**︰ 數 RTP 編碼器不允許主機名稱和需要取得 IP 位址。  

    **疑難排解步驟**︰ 若要尋找的 IP 位址，開啟 [任何電腦上的命令提示字元。 在 Windows 中，開啟 [執行] 啟動器 （WIN + R），然後輸入 [cmd] 以開啟。  

    一旦開啟命令提示字元中，輸入 「 偵測 （ping） [反飛彈系統主機名稱]]。 

    可省略從 Azure 內嵌 URL，以在下列範例中醒目提示的連接埠號碼衍生主機名稱︰ 

    rtp://test2-amstest009.rtp.channel.mediaservices.windows.net:2010 / 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###<a name="problem-unable-to-playback-the-published-stream"></a>問題︰ 無法播放已發佈的資料流。
 
- **潛在問題**︰ 執行，任何串流端點，或有不串流單位 （[刻度] 單位）。 

    **疑難排解步驟**︰ 瀏覽至 AMSE 工具中的 「 串流結束點] 索引標籤，然後確認執行一個串流單位串流結束點。 
    


>[AZURE.NOTE] 如果之後您仍然無法順利串流的疑難排解步驟，送出使用 [Azure 入口網站的支援票證。

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
