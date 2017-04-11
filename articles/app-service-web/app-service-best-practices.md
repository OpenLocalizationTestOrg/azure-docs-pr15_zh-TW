<properties
    pageTitle="Azure 應用程式服務的最佳作法"
    description="進一步瞭解最佳作法和疑難排解 Azure 應用程式服務。"
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="best-practices-for-azure-app-service"></a>Azure 應用程式服務的最佳作法

這份文件摘要列出使用[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)的最佳做法。 

## <a name="colocation"></a>共置
當 Azure 撰寫的 web 應用程式等資料庫解決方案的資源位於不同區域效果可以包括下列各項︰

*  增加的延遲時間，資源之間的通訊
*  貨幣費用輸出資料傳輸跨地區如上所述[Azure 價格的頁面](https://azure.microsoft.com/pricing/details/data-transfers)上。

在相同地區共置最適合 Azure 撰寫的 web 應用程式等用來儲存內容或資料的資料庫或儲存帳戶解決方案的資源。 建立您應該確認的資源時仍在相同的 Azure 地區除非您有特定的商業或設計他們不會。 您可以利用[應用程式服務複製功能](app-service-web-app-cloning-portal.md)目前不適用於規劃的進階應用程式服務應用程式，將應用程式服務應用程式移至與您的資料庫相同區域中。   

## <a name="memoryresources"></a>當應用程式取用比預期更多的記憶體
當您會注意到應用程式會耗費更多記憶體比預期透過監控所示，或服務建議考慮[應用程式服務自動修復功能](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)。 自動修復功能選項的其中一個花費記憶體臨界值為基礎的自訂動作。 動作範圍從電子郵件通知另一方面，若要透過在位置降低記憶體傾印調查回收背景工作程序。 您可以設定自動修復，透過 web.config，並透過使用者介面所述，此部落格文章的[應用程式服務支援網站副檔名](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)。   

## <a name="CPUresources"></a>當應用程式取用比預期更多的 CPU
透過監控所示，當您注意到應用程式會耗費更多的 CPU 比預期或體驗重複 CPU 特殊圖文集或縮放，或應用程式服務計劃縮放比例，請考慮服務建議。 如果您的應用程式開發，縮放時的唯一選項，如果您的應用程式沒有狀態、 縮放比例出可讓您更多的彈性和較高的縮放比例可能。 

如需有關 「 開發 」 與 「 狀態 」 應用程式中，您可以觀看這段影片︰[規劃 Microsoft Azure Web app 的可調整端對端多層應用程式](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid)。 如需有關應用程式服務縮放比例和自動縮放選項閱讀︰[小數位數 Web App 中 Azure 應用程式服務](web-sites-scale.md)。  

## <a name="socketresources"></a>通訊端資源耗盡時
耗盡輸出的 TCP 連線的常見原因是使用用戶端程式庫並未實作要重複使用 TCP 連線，或如果較高的層級通訊協定，例如 HTTP-保持不運用。 請檢閱每一個應用程式中您應用程式服務計劃 」，以確保設定或存取您的程式碼，以有效重複使用的輸出連線參照文件庫的文件。 也依照適當建立和發行或清理若要避免遺漏連線的文件庫文件的指導方針。 時可能會減輕這類調查位於進度影響的用戶端程式庫取出多個執行個體縮放比例。  

## <a name="appbackup"></a>當您的應用程式備份開始失敗
應用程式備份失敗的原因的兩個最常見的原因︰ 設定不正確的儲存空間及無效的資料庫設定。 有變更儲存空間或資料庫的資源或變更如何存取這些資源 （例如資料庫備份設定] 中選取更新的憑證） 時，通常會發生這些失敗。 備份通常會執行的排程，並要求的儲存空間 （輸出備份檔案） 和資料庫的存取權 （適用於複製和讀取備份中所包含的內容）。 無法存取這些資源其中一項的結果是一致的備份失敗。 

當備份失敗發生時，請檢閱最新的結果，若要瞭解發生錯誤的類型。 若是儲存 access 失敗次數，請檢閱並更新用於備份設定儲存設定。 如果資料庫存取失敗，請檢閱並更新您的連線字串應用程式設定。然後繼續進行更新備份設定正確包含必要的資料庫。 如需詳細資訊，[備份] 應用程式，請參閱[備份 Azure 應用程式服務中的 web 應用程式](web-sites-backup.md)的文件。

## <a name="nodejs"></a>當新的 Node.js 應用程式部署至 Azure 應用程式服務
Azure 應用程式服務的預設設定 Node.js 應用程式是最適合最常見的應用程式的需求。 如果您 Node.js 應用程式的設定會因個人化的調整，以改善效能，或最佳化的 CPU/記憶體網路資源的資源使用狀況，您可能會檢閱我們的最佳作法和疑難排解步驟。 文件本文將說明您可能需要設定 Node.js 應用程式、 說明各種情況或您的應用程式可能會遇到，並示範如何解決這些問題的 iisnode 設定︰[最佳作法和節點上的應用程式 Azure 應用程式服務疑難排解指南](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)。   


