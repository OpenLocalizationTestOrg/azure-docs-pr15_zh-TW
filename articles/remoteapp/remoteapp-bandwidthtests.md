<properties 
    pageTitle="Azure RemoteApp-測試一些常見的案例與您的網路頻寬使用量 |Microsoft Azure"
    description="瞭解如何將常見的使用案例可協助您找出您的網路頻寬需求的 Azure RemoteApp。"
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />
    
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp-測試一些常見的案例與您的網路頻寬使用量

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

如前述[估計 Azure RemoteApp 網路頻寬使用量](remoteapp-bandwidth.md)中，找出您的網路的 Azure RemoteApp 影響執行某些使用方式的最佳方式就會測試。 執行下列測試設定的時間內和測量每個案例所需的頻寬。 如果您有功能，您也可以測量網路封包遺失與網路抖動瞭解將會建立在您的特定環境網路模式。

    
當評估頻寬使用量，請記得變化之間不同的使用者，您公司內的使用方式。 例如，文字和作者通常取用較少的頻寬，比使用視訊的使用者。 為了獲得最佳結果，學習您自己的使用者需求並建立下列情況混合代表最適合您的公司中的使用者。 請記得要[檢閱因素的影響頻寬使用量和使用者經驗](remoteapp-bandwidthexperience.md)-可協助您識別理想的測試。

第一個閱讀測試中，選擇您的混合，然後再執行它們。 您可以使用下表，以協助追蹤效能。

>[AZURE.NOTE] 如果您無法執行您的網路測試，或您沒有時間這麼做，請查看我們的[基本網路頻寬來估算/建議](remoteapp-bandwidthguidelines.md)。 您里程數可能會視情況而定，不過，因此您*可以*執行您自己的測試，如果您。


## <a name="the-usage-tests"></a>使用測試
這些測試執行的不同的時間量，並測試函數/功能會耗用網路頻寬。 請記住，選擇最符合您的公司個別使用者的測試混合。
 
### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>每次複雜的高階主管 PowerPoint-執行 900 1000年秒

使用者會顯示在全螢幕模式中使用 Microsoft Office PowerPoint 45 50 高畫質投影片之間。 圖像、 轉場效果 （含動畫），與貴公司的典型的背景色彩漸層的包含投影片。 使用者應該花至少 20，在每張投影片。
    
這種情況下建立爆發流量，切換至簡報中的下一個投影片的投影片。
    
### <a name="simple-powerpoint---run-for-620-seconds"></a>簡單的 PowerPoint-執行 ~ 620 秒

使用者會簡單的 PowerPoint 檔案提供在全螢幕模式中使用 Microsoft Office PowerPoint 大約 30 的投影片。 投影片更多文字密切比在每次複雜的高階主管 PowerPoint 的案例中，而且有較簡單的背景和圖像 （黑色圖表）。 
    
### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer-執行 ~ 250 秒

使用者瀏覽網頁版中使用 Internet Explorer。 使用者瀏覽和混合文字、 自然圖像和一些圖中捲動。 儲存在本機磁碟機的遠端桌面工作階段主機 （RD 工作階段主機） 伺服器上的網頁。MHT 檔案。 使用者將捲動 Page Down、 向上和向下鍵 Page Up 使用不同的時間間隔捲動每個機碼/類型︰
    
    - 向下-250 按鍵非常 500 ms
    - Page Up-36 按鍵每個 1000 ms
    - 向下-75 按鍵每個 100 ms
    - Page Down-20 按鍵每個 500 ms
    - 最多-120 按鍵每個 300 ms
    
### <a name="pdf-document---simple---run-for-610-seconds"></a>PDF 文件-簡單-執行 ~ 610 秒
使用者讀取和 PDF 文件搜尋各種不同的方式，使用 Adobe Acrobat Reader。 表格、 簡易圖形和文字的多種字型應該包含文件。 文件是 35 40 頁長。 使用者捲動兩個不同的工資率，在向後及轉寄，以四個不同的縮放大小 (符合頁面上，適合寬度，以 100%，和您所選擇的另一個)。 縮放，可確保不同的大小，呈現的文字 （字型）。 捲動已關閉] 頁面上、 Page Down、 向上和向下鍵，使用不同的時間間隔的每個捲軸。

### <a name="pdf-document---mixed---run-for-320-seconds"></a>PDF 文件-混合-執行 ~ 320 秒鐘
使用者讀取和 PDF 文件搜尋各種不同的方式，使用 Adobe Acrobat Reader。 文件包含高品質圖像 （包括相片）、 表格、 簡單圖形和文字的多種字型。 使用者捲動兩個不同的工資率，在向後及轉寄，以四個不同的縮放大小 (符合頁面上，適合寬度，以 100%，和您所選擇的另一個)。 縮放，可確保不同的大小，呈現的文字 （字型）。 捲動已關閉] 頁面上、 Page Down、 向上和向下鍵，使用不同的時間間隔的每個捲軸。

### <a name="flash-video-playback---run-for-180-seconds"></a>Flash 視訊的播放-執行 ~ 180 秒
使用者檢視 Adobe Flash 編碼的視訊內嵌在網頁上。 網頁會儲存於本機硬碟 RD 工作階段主機伺服器。 在 Internet Explorer 中播放視訊外掛程式內嵌播放程式。

這種情況資料列傳回檢視 rtf 內容網頁包含多媒體的使用者。 大部分的資料應該透過 VOBR bo。

### <a name="word-remote-typing---run-for-1800-seconds"></a>Word [遠端輸入-執行 ~ 1800年秒
使用者將透過 RDP 工作階段的文件。 按鍵會傳送到 RDP 工作階段的用戶端 Microsoft word 文件遠端工作階段中執行。 輸入利率為每個 250 ms （總 7050 字元） 的一個字元。 

這是最常見的案例的知識工作者。 這種情況下測試使用者，輸入新的工作處理器的回應。 這種情況下會區分頻寬使用量的偶數小型變更項目。

## <a name="tracking-the-test-results"></a>追蹤測試結果

您可以使用下表評估您的環境中的案例。 提供的資料是只針對圖例-可能會從您的觀察非常不同。 

為求，我們會假設使用相同的 1920 x 1080 像素螢幕解析度測試所有的情況，並且 TCP 傳輸網路上的延遲 （延遲） 下方 200 ms 和網路抖動在 120 ms + 標示關於 1%。

有關的資料表︰
- **平均體驗**包含使用者生產力，影響不大，但不會排除偶爾發生的視訊或音訊故障的網路頻寬。 系統會能夠快速復原利用動態邏輯。 若要確保使用者體驗的品質網路頻寬來估算嘗試。
 - **Noticeable 問題 （符號點）**包含的網路頻寬使用者可能會發現重大問題員工的體驗，及他們的生產力會受到影響可以測量的時期的位置。 此時 RDP 演算法致力，並且不保證的使用者體驗的品質，因為沒有足夠的網路頻寬。
 - **建議使用**包含建議好或很好的體驗的網路頻寬。 則通常一個步驟高於**平均體驗**的對應資料行中的值。
 - **備忘稿**包含觀察和註解。
 
| 測試                  | 平均體驗 | 注意問題 （符號點） | 建議的網路頻寬 | 備忘稿                                                              |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| 每次複雜的高階主管 PPT | 10 MB/s             | 1 MB/s                           | > 10 MB/s、 100 MB/s 慣用    | 在 1 MB/s 多動畫都會遺失                                   |
| 簡單的 PPT            | 5 MB/s              | 256 KB/s                         | 10 MB/s                        | 256 KB/s 在投影片載入明顯的延遲                   |
| Internet Explorer     | 10 MB/s             | 1 MB/s                           | > 10 MB/s、 100 MB/s 慣用    | 在 1 MB/s web 影片模糊而不穩定、 快速捲動有問題 |
| 簡單的 PDF            | 1 MB/s              | 256 KB/s                         | 5 MB/s                         | 花在 256 KB/s 若要將頁面載入時                       |
| 混合的 PDF             | 1 MB/s             | 256 KB/s                         | 5 MB/s                         | 256 KB/s 頁面發生大量的載入時間    |
| Flash 視訊播放  | 10 MB/s             | 1 MB/s                           | > 10 MB/s、 100 MB/s 慣用    | 在 1 MB/s 視訊有紋理而會遭到捨棄一些圖文框           |
| Word [遠端輸入    | 256 KB/s            | 128 KB/s                         | 1 MB/s                         | 256 KB/s，使用者可能會發現按鍵之間的時間             |

若要評估每位使用者的網路頻寬，建立上述情況混合和對應的比例的必要的網路頻寬。 挑選所需的案例的最高編號。 使用者幾乎所有使用了解單獨使用系統，請考慮一些保留同時處理相同的網路的使用者。
     
## <a name="learn-more"></a>深入瞭解
- [估計 Azure RemoteApp 網路頻寬使用量](remoteapp-bandwidth.md)

- [Azure RemoteApp-如何網路頻寬和品質體驗的工作放在一起？](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp 網路頻寬-（如果您不能測試自己） 的一般指導方針](remoteapp-bandwidthguidelines.md)