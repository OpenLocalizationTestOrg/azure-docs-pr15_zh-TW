<properties 
    pageTitle="Azure RemoteApp-如何網路頻寬和品質體驗的工作放在一起？ |Microsoft Azure"
    description="瞭解如何在 Azure RemoteApp 網路頻寬會影響您的使用者體驗的品質。"
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

# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>Azure RemoteApp-如何網路頻寬和品質體驗的工作放在一起？

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

當您會看到所需的 Azure RemoteApp[整體的網路頻寬](remoteapp-bandwidth.md)時，請謹記下列因素-動態系統會影響整體使用者經驗的所有的一部分。 

- **可用的網路頻寬和目前的網路狀況**的一組指定時間相同的網路上 （遺失，延遲，抖動） 的參數會影響應用程式串流體驗，這就表示降低的整體使用者經驗。 提供您的網路頻寬是壅塞、 隨機遺失延遲的函數，因為所有這些參數會影響壅塞控制機制，接著控制項傳輸速度，以避免發生衝突。  例如，失真網路或在高延遲的網路會使使用者經驗不正確甚至 1000 MB 頻寬網路上。 遺失與延遲因位於相同的網路和什麼這些使用者正在執行的動作 （例如，觀看影片，下載或上傳大型檔案，列印） 的使用者數目。
- **使用案例**體驗使用者為個別使用者和群組相同的網路上所執行的動作而定。 例如，閱讀一張投影片需要單一圖文框更新。如果使用者 skims 或文字文件的內容在捲動時，它們會需要較高的框架更新秒數字。 在這個案例中伺服器來回的通訊最後會使用多個網路頻寬。 也請考慮非常範例︰ 多個使用者看高畫質視訊 （例如 4 K 解析）、 按住 HD 電話會議、 遊戲 3D 影片，或處理 CAD 系統。 這些都可以讓甚至很高頻寬網路無法使用。
- **螢幕解析度和數字的畫面**-多個網路頻寬是完整更新放大畫面比較小的畫面。 基礎技術很好的編碼方式和傳輸已更新的畫面區域但整個螢幕一段，需要更新。 當使用者會有更高解析度螢幕 （例如 4 K 解析） 時，該更新需要更多的網路頻寬比較低解析度 （例如 1024x768px) 的畫面。 如果您使用多個畫面重新導向，適用於此相同的邏輯。 頻寬必須增加數字的畫面。
- **剪貼簿和裝置重新導向**-這是很明顯的問題，但在許多情況下使用者儲存了大量的資料到剪貼簿中，如果花一些時間取得該資訊，以從遠端桌面用戶端轉移到伺服器。 下游體驗可以受到上游傳送剪貼簿內容的體驗。 用於裝置重新導向-如果掃描器或網路相機產生許多傳送到伺服器，上游需要的資料，或在印表機需要接收大型文件，或本機存放區需要使用應用程式將大型檔案複製雲端中執行，使用者可能會注意到捨棄的畫面或暫時 「 凍結 」 影片因為裝置重新導向所需的資料會提高網路頻寬需要。 

當您評估您的網路頻寬需求時，請務必考慮使用系統這些因素的所有項目。

現在，回到[主網路頻寬文章](remoteapp-bandwidth.md)，或移到測試您的[網路頻寬](remoteapp-bandwidthtests.md)。

## <a name="learn-more"></a>深入瞭解
- [估計 Azure RemoteApp 網路頻寬使用量](remoteapp-bandwidth.md)

- [Azure RemoteApp-測試一些常見的案例與您的網路頻寬使用量](remoteapp-bandwidthtests.md)

- [Azure RemoteApp 網路頻寬-（如果您不能測試自己） 的一般指導方針](remoteapp-bandwidthguidelines.md)