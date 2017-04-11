<properties
    pageTitle="從行動裝置的服務移轉到應用程式服務的行動應用程式"
    description="瞭解如何輕鬆地移轉您的行動電話服務應用程式至應用程式服務行動應用程式"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="adrianha"/>

# <a name="article-top"></a>將現有的 Azure 行動訊息服務移轉到 Azure 應用程式服務

[開放 Azure 應用程式服務]Azure 行動服務網站可以輕鬆地移轉就地升級利用 Azure 應用程式服務的所有功能。  這份文件說明時從 Azure 行動服務升級您的網站，Azure 應用程式服務發生的事。

## <a name="what-does-migration-do"></a>「 移轉做什麼至您的網站

移轉您的 Azure 行動訊息服務變成[Azure 應用程式服務]應用程式不會影響程式碼您的行動訊息服務。  通知集線器、 SQL 資料連線、 驗證設定、 排程的工作及網域名稱維持不變。  使用您的 Azure 行動訊息服務的行動用戶端繼續正常運作。  傳送至 Azure 應用程式服務之後，移轉重新啟動您的服務。

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>為什麼應該將移轉您的網站

Microsoft 會建議您移轉您的 Azure 行動訊息服務，以利用其功能的 Azure 應用程式服務，包括︰

  *  新主機功能，包括[WebJobs]和[自訂網域名稱]。
  *  連線至您的內部部署資源使用[VNet]除了[混合式連線]。
  *  監視及疑難排解新聖器或[應用程式的深入見解]。
  *  內建 DevOps 可用的工具，包括[暫存之位置]，回復，並在生產測試。
  *  [自動調整大小]、 負載平衡及[效能監視]。

更多優點 Azure 應用程式服務的詳細資訊，請參閱[行動服務與應用程式服務]主題。

## <a name="before-you-begin"></a>開始之前

在開始之前任何主要的工作，在您的網站上，您應該[先備份您的行動訊息服務]的指令碼和 SQL 資料庫。

## <a name="migrating-site"></a>移轉您的網站

移轉程序移轉單一 Azure 區域內的所有網站。

若要移轉您的網站︰

  1.  [Azure 傳統入口網站]登入。
  2.  選取您想要移轉的區域中的行動訊息服務。
  3.  按一下 [**應用程式服務移轉**] 按鈕。

    ![[移轉] 按鈕][0]

  4.  閱讀移轉應用程式服務] 對話方塊。
  5.  提供的方塊中輸入您的行動訊息服務的名稱。  例如，如果 contoso.azure mobile.net 為您的網域名稱，然後輸入_contoso_提供的方塊中。
  6.  按一下 [刻度] 按鈕。

監視活動監視器] 中的移轉狀態。 您的網站會列為 Azure 傳統入口網站中的 [*移轉*。

  ![移轉活動監視器][1]

每個移轉可能需要 3 到 15 分鐘，每個要移轉的行動訊息服務。  在移轉時，仍然可以使用您的網站。
在移轉程序結尾處必須重新啟動您的網站。  在重新啟動過程中，可能會持續的秒數，則無法使用網站。

## <a name="finalizing-migration"></a>完成移轉

計劃以測試您的網站行動用戶端在結束時移轉程序。  請確定您可以執行所有一般不需變更行動用戶端的用戶端動作。  

### <a name="update-app-service-tier"></a>選取適當的應用程式服務價格層

您有更具彈性價格之後您移轉到 Azure 應用程式服務。

  1.  [Azure 入口網站]登入。
  2.  選取**所有資源**或**應用程式服務**，然後按一下您移轉行動訊息服務的名稱。
  3.  設定刀依預設會開啟。
  4.  在 [設定] 功能表中，按一下 [**應用程式服務方案**。
  5.  按一下 [**價格層**磚。
  6.  按一下磚適合用於您的需求，然後按一下 [**選取**。  您可能需要按一下 [**檢視全部**]，請參閱可用的價格層。

起點，我們建議您以下層︰

| 行動訊息服務價格層 | 應用程式服務價格層級 |
| :-------------------------- | :----------------------- |
| 免費                        | 免費的 F1                  |
| 基本                       | B1 Basic                 |
| 標準                    | S1 標準              |

選擇正確的價格應用程式層有相當大的彈性。  請參閱[應用程式服務價格]的完整詳細資料的新應用程式服務價格。

> [AZURE.TIP]應用程式服務標準層包含許多功能，您可能會想要使用時，access，包括[暫存之位置]，自動備份，並自動調整大小。  當您有時，請查看新功能 ！

### <a name="review-migration-scheduler-jobs"></a>檢閱移轉的排程工作

排程器作業直到 30 分鐘移轉後將無法看到。  排程的工作會繼續在背景中執行。
若要檢視您已排程的工作之後才看得見一次,︰

  1.  [Azure 入口網站]登入。
  2.  選取 [**瀏覽 >**，請在 [_篩選_] 方塊中，輸入**排程**，然後選取 [**排程器集合**。

有限制可用的排程器工作可用移轉後的數字。  檢閱您的使用情況和[Azure 排程器方案]。

### <a name="configure-cors"></a>如有需要請設定 CORS

十字形，資源共用是允許網站存取另一個網域上 Web API 技巧。  如果您使用的 Azure 行動服務與相關聯的網站，您需要將 CORS 設定移轉的一部分。  如果您以獨佔模式從行動裝置存取 Azure 行動服務，然後 CORS 並不需要在某些情況，除了設定。

您移轉的 CORS 設定，可為**MS_CrossDomainWhitelist**應用程式設定。  若要將您的網站移轉應用程式服務 CORS 功能︰

  1.  [Azure 入口網站]登入。
  2.  選取**所有資源**或**應用程式服務**，然後按一下您移轉行動訊息服務的名稱。
  3.  設定刀依預設會開啟。
  4.  API 功能表中，按一下**CORS** 。
  5.  在提供，每個項目後按 Enter 鍵] 方塊中輸入任何允許的來源。
  6.  正確的可容許的來源清單後，按一下 [儲存] 按鈕。

> [AZURE.TIP]使用 Azure 應用程式服務的優點之一是您可以在同一網站上執行您的網站和行動訊息服務。  如需詳細資訊，請參閱[下一步](#next-steps)] 區段。

### <a name="download-publish-profile"></a>下載新發佈的設定檔

移轉至 Azure 應用程式服務時，即會變更發佈網站的設定檔。  如果您想要發佈您的網站在 Visual Studio 中，您需要新的發佈設定檔。  若要下載新發佈的設定檔︰

  1.  [Azure 入口網站]登入。
  2.  選取**所有資源**或**應用程式服務**，然後按一下您移轉行動訊息服務的名稱。
  3.  按一下 [**取得發佈的設定檔**]。

PublishSettings 檔案下載到您的電腦。  通常稱為_站台名稱_。PublishSettings。  發佈設定匯入您的現有專案︰

  1.  開啟 Visual Studio 和 Azure 行動訊息服務專案。
  2.  在**方案總管] 中**以滑鼠右鍵按一下，然後選取 [**發佈...**
  3.  按一下 [**匯入**
  4.  按一下 [**瀏覽]** ，然後選取您下載發佈的設定檔。  按一下**[確定]**
  5.  按一下 [**驗證的連線**，以確保發佈設定工作。
  6.  按一下 [**發佈**] 發佈您的網站]。


## <a name="working-with-your-site"></a>移轉您的網站後使用

開始使用您在[Azure 入口網站]移轉後的新應用程式服務。  以下是一些您用來執行[Azure 傳統入口網站]，與他們的應用程式服務相等值中的特定作業上的筆記。

### <a name="publishing-your-site"></a>下載並發佈您的移轉的網站

您的網站透過給或 ftp，並可以使用各種不同的機制，包括 WebDeploy、 TFS、 水星、 GitHub，以及 FTP 那些。  部署認證一併移轉您的網站的其餘部分。  如果您並未設定您的部署認證，或您不記得它們，您就可以將其重設︰

  1. [Azure 入口網站]登入。
  2. 選取**所有資源**或**應用程式服務**，然後按一下您移轉行動訊息服務的名稱。
  3. 設定刀依預設會開啟。
  4. 按一下 [**部署認證**，在 [發佈] 功能表。
  5. 提供的方塊中輸入新的部署認證，然後按一下 [儲存] 按鈕。

您可以使用這些認證複製，就可以給網站，或從 GitHub、 TFS 或水星設定自動部署。  如需詳細資訊，請參閱[Azure 應用程式服務部署文件]。

### <a name="appsettings"></a>應用程式設定

移轉的行動訊息服務的大部分的設定，可透過應用程式設定。  您可以從[Azure 入口網站]取得應用程式設定的清單。
若要檢視或變更您的應用程式設定︰

  1. [Azure 入口網站]登入。
  2. 選取**所有資源**或**應用程式服務**，然後按一下您移轉行動訊息服務的名稱。
  3. 設定刀依預設會開啟。
  4. 按一下 [一般] 功能表中的 [**應用程式設定**]。
  5. 向下捲動至 [應用程式設定] 區段，找到您的應用程式設定。
  6. 按一下 [應用程式設定來編輯值的值。  按一下 [儲存值的 [**儲存**]。

您可以一次更新多個應用程式設定。

> [AZURE.TIP]有兩個應用程式設定，以相同的值。  例如，您可能會看到_ApplicationKey_和_MS\_ApplicationKey_。  同時更新這兩種應用程式設定。

### <a name="authentication"></a>驗證

所有驗證的設定，都可在網站移轉應用程式設定。  若要更新您的驗證設定，您必須變更適當的應用程式設定。  下表顯示適當的應用程式設定驗證提供者︰

| 提供者          | 用戶端識別碼                 | 用戶端密碼                | 其他設定             |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Microsoft 帳戶 | **MS\_MicrosoftClientID**  | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook          | **MS\_FacebookAppID**      | **MS\_FacebookAppSecret**     |                            |
| Twitter           | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** |                            |
| Google            | **MS\_GoogleClientID**     | **MS\_GoogleClientSecret**    |                            |
| Azure AD          | **MS\_AadClientID**        |                              | **MS\_AadTenants**          |

附註︰ **MS\_AadTenants**會儲存成逗點分隔的租用戶網域清單 （行動服務入口網站中的 「 允許租用戶 」 欄位）。

> [AZURE.WARNING] **在 [設定] 功能表中，不使用的驗證機制**
>
> Azure 應用程式服務，提供個別 「 無程式碼 」 驗證與授權的系統下_驗證 / 授權_設定] 功能表，然後在 [設定] 功能表底下 （已遭取代） 的 [_行動電話驗證_] 選項。  這些選項是移轉的 Azure 行動訊息服務與不相容。  您可以利用 Azure 應用程式服務驗證的 [[升級您的網站](app-service-mobile-net-upgrading-from-mobile-services.md)。

### <a name="easytables"></a>資料

Azure 入口網站中，已由_簡單的表格_取代行動服務中的 [_資料_] 索引標籤。  若要存取簡單的表格︰

  1. [Azure 入口網站]登入。
  2. 選取**所有資源**或**應用程式服務**，然後按一下您移轉行動訊息服務的名稱。
  3. 設定刀依預設會開啟。
  4. 在行動裝置] 功能表中按一下 [**簡單的表格**]。

您可以新增表格，按一下 [**新增**] 按鈕，或按一下資料表名稱來存取您現有的資料表。  有各種作業，您可以從這個刀，包括︰

* 變更資料表的權限
* 編輯操作的指令碼
* 管理資料表結構描述
* 刪除表格
* 清除表格內容
* 刪除表格的特定的列

### <a name="easyapis"></a>API

在行動電話服務中的 [ _API_ ] 索引標籤具有 Azure 入口網站中_輕鬆 Api_被取代。  若要存取輕鬆 Api:

  1. [Azure 入口網站]登入。
  2. 選取**所有資源**或**應用程式服務**，然後按一下您移轉行動訊息服務的名稱。
  3. 設定刀依預設會開啟。
  4. 在行動裝置] 功能表中按一下**簡單的 Api** 。

您已移轉的 Api 已列在刀。  您也可以從這個刀新增 API。  若要管理特定的 API，按一下 [API]。
從新的刀，您可以調整權限，並 api 編輯的指令碼。

### <a name="on-demand-jobs"></a>排程工作

所有的排程器作業，可透過 [排程工作集合] 區段。  若要存取您的排程器作業︰

  1. [Azure 入口網站]登入。
  2. 選取 [**瀏覽 >**，請在 [_篩選_] 方塊中，輸入**排程**，然後選取 [**排程器集合**。
  3. 選取您的網站中的工作集合。  名為_站台名稱_的工作。
  4. 按一下 [**設定**]。
  5. 按一下 [管理下的 [**工作排程器**]。

已排程的工作會列出您指定移轉前的頻率。  視需要工作已停用。  若要執行隨選工作︰

  1. 選取您想要執行的工作。
  2. 如有需要，請按一下 [啟用工作的 [**啟用**]。
  3. 按一下 [**設定**]，然後**排程**。
  4. 選取週期**一次**，然後按一下 [**儲存**]

您指定的工作位於`App_Data/config/scripts/scheduler post-migration`。  我們建議您將所有視工作都轉換成[WebJobs]或[函數]。  撰寫新的排程器作業[WebJobs]或[函數]。

### <a name="notification-hubs"></a>通知集線器

行動服務會使用通知集線器推播通知。  下列應用程式設定用來移轉後，連結至您的行動訊息服務的通知中心︰

| 應用程式設定                    | 描述                              |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace**             | 通知中心命名空間           |
| **MS\_NotificationHubName**             | 通知中心名稱                |
| **MS\_NotificationHubConnectionString** | 通知中心連線字串   |
| **MS\_NamespaceName**                   | MS_PushEntityNamespace 別名      |

您的通知中心是透過[Azure 入口網站]管理。  請注意的通知中心名稱 （您可以找到這使用應用程式設定）︰

  1. [Azure 入口網站]登入。
  2. 選取 [**瀏覽**>，然後選取 [**通知集線器**
  3. 按一下 [與行動訊息服務相關的通知中心名稱]。

> [AZURE.NOTE]如果您的通知中心 」 混合 」 類型，它不可見。  「 混合 」 輸入集線器利用通知集線器和舊版服務匯流排功能的通知。  在繼續之前的 [[轉換混合命名空間]]。  轉換完成後，您的通知中心會出現在[Azure 入口網站]。

如需詳細資訊，檢閱[通知集線器]文件。

> [AZURE.TIP][Azure 入口網站]中的通知集線器管理功能是仍在預覽中。  [Azure 傳統入口網站]，仍然可以管理您的所有通知集線器。

### <a name="legacy-push"></a>舊版的推入設定

如果您設定推入通知集線器上簡介之前您行動訊息服務上時，您使用的_舊版的推入_。  如果您使用的推入看不到您的設定中所列的通知中心，則可能您使用的_舊版的推入_。  此功能被移轉所有其他的功能。  不過，我們建議您升級至通知集線器，只要在移轉完成後。

過渡，（與主要例外狀況的 APN 憑證） 的所有舊版的推入設定可在應用程式設定。  取代適當的檔案系統上更新 APN 憑證。

### <a name="app-settings"></a>其他應用程式設定

下列其他應用程式設定為移轉，從您的行動訊息服務與*設定*] 下提供 > *應用程式設定*︰

| 應用程式設定              | 描述                             |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName**         | 您的應用程式的名稱                    |
| **MS\_MobileServiceDomainSuffix** | 網域前置字元。 例如︰ 存在 azure mobile.net |
| **MS\_ApplicationKey**            | 您的應用程式鍵                    |
| **MS\_主要金鑰**                 | 應用程式的主索引鍵                     |

應用程式鍵和主索引鍵是從您的原始行動訊息服務與應用程式鍵。  特別是應用程式鍵傳送行動用戶端驗證的行動裝置的 api 使用。

### <a name="cliequivalents"></a>命令列的對應項目

_Azure 行動_命令不再可用來管理您 Azure 行動服務的網站。  不過，許多功能已被取代_azure 的網站_] 命令。  若要尋找常用命令對應項目使用下表︰

| _Azure Mobile_] 命令                     | 對等的_Azure 網站_] 命令            |
| :----------------------------------------- | :----------------------------------------- |
| 行動裝置的位置                           | 網站位置] 清單                         |
| 行動裝置的清單                                | 網站清單                                  |
| 行動裝置的顯示_名稱_                         | 網站顯示_名稱_                           |
| 行動重新啟動_名稱_                      | 網站重新啟動_名稱_                        |
| 行動重新部署的機會_名稱_                     | 網站部署重新部署的機會_commitId_ _名稱_ |
| 行動鍵設定_名稱__類型_] 的_值_       | 網站 appsetting 刪除_機碼__名稱_ <br/> 網站 appsetting 新增_鍵_=_值_的_名稱_ |
| 行動裝置設定清單_名稱_                  | 網站 appsetting 清單_名稱_                |
| 行動 config 取得_名稱__索引鍵_             | 網站 appsetting 顯示_機碼__名稱_          |
| 設定行動裝置設定_名稱__索引鍵_             | 網站 appsetting 刪除_機碼__名稱_ <br/> 網站 appsetting 新增_鍵_=_值_的_名稱_ |
| 行動裝置的網域清單_名稱_                  | 網站的網域清單_名稱_                    |
| 行動裝置的網域新增_網域_的_名稱_          | 網站的網域新增_的網域__名稱_            |
| 行動裝置的網域刪除_名稱_                | 網站網域刪除_網域__名稱_         |
| 行動裝置的刻度的顯示_名稱_                   | 網站顯示_名稱_                           |
| 行動裝置的縮放比例變更_名稱_                 | 網站比例模式的_模式__名稱_ <br /> 網站比例執行個體的_執行個體__名稱_ |
| 行動 appsetting 清單_名稱_              | 網站 appsetting 清單_名稱_                |
| 行動 appsetting 新增_名稱__鍵_] 的_值_ | 網站 appsetting 新增_鍵_=_值_的_名稱_   |
| 行動 appsetting 刪除_名稱__索引鍵_      | 網站 appsetting 刪除_機碼__名稱_        |
| 行動 appsetting 顯示_名稱__索引鍵_        | 網站 appsetting 刪除_機碼__名稱_        |

藉由更新適當的應用程式設定更新驗證或推入通知設定。
編輯檔案，並發佈您的網站，透過 ftp 或給]。

### <a name="diagnostics"></a>診斷和記錄

診斷記錄會以正常方式停用在 Azure 應用程式服務。  若要啟用診斷記錄︰

  1. [Azure 入口網站]登入。
  2. 選取**所有資源**或**應用程式服務**，然後按一下您移轉行動訊息服務的名稱。
  3. 設定刀依預設會開啟。
  4. 選取 [功能] 功能表下的 [**診斷記錄**]。
  5. 按一下 [**在**下列記錄檔︰**的應用程式記錄 （系統）**、**詳細的錯誤訊息**，以及**失敗要求追蹤**
  6. 按一下 [**檔案系統**的網頁伺服器記錄
  7. 按一下 [**儲存**]

若要檢視的記錄︰

  1. [Azure 入口網站]登入。
  2. 選取**所有資源**或**應用程式服務**，然後按一下您移轉行動訊息服務的名稱。
  3. 按一下 [**工具**] 按鈕
  4. 選取 [OBSERVE] 功能表下的**記錄資料流**。

記錄會顯示在視窗中，如下圖所產生。  您也可以下載供日後使用您的部署認證的分析記錄。 如需詳細資訊，請參閱[記錄]文件。

## <a name="known-issues"></a>已知的問題

### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>刪除移轉行動應用程式複本會使網站中斷

如果您複製使用 PowerShell 的 Azure 您移轉行動訊息服務，然後刪除複製，則會移除您生產服務的 DNS 項目。  您的網站不會從網際網路存取。  

解決方法︰ 如果您要複製您的網站，請執行此作業透過入口網站。

### <a name="changing-webconfig-does-not-work"></a>變更 Web.config 無法運作

如果您有 ASP.NET 網站時，就會變成`Web.config`執行不會套用檔案。  Azure 應用程式服務會建立適合`Web.config`啟動支援的行動電話服務執行階段期間的檔案。  若要覆寫特定設定 （例如自訂標題），可以使用 XML 轉換檔案。  建立檔案中稱為`applicationHost.xdt`-此檔案必須最後`D:\home\site`目錄 Azure 服務。  上傳`applicationHost.xdt`透過自訂部署指令碼檔案，或直接使用 Kudu。  下列範例顯示的範例文件︰

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

如需詳細資訊，請參閱在 GitHub 的[XDT 轉換範例]文件。

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>移轉的行動服務無法新增至流量管理員

當您建立流量管理員設定檔時，您無法直接選擇移轉的行動訊息服務的設定檔。  使用 「 外部端點 」。  只可以透過 PowerShell 加入外部端點。  如需詳細資訊，請參閱[流量管理員教學課程](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/)。

## <a name="next-steps"></a>後續步驟

現在，您的應用程式至應用程式服務移轉，有更多的功能，您可以使用︰

  * 部署[暫存之位置]可讓您變更您的網站的階段，並執行 A / B 測試。
  * [WebJobs]提供點播排程工作的替代方案。
  * 您可以[持續部署]您的網站連結 GitHub、 TFS 或水星您的網站。
  * 您可以使用[應用程式的深入見解]監控您的網站。
  * 網站與行動 API 從相同的程式碼服務。

### <a name="upgrading-your-site"></a>升級您的行動電話服務網站 Azure 行動應用程式 sdk 的變更

  * 針對 Node.js 為基礎的伺服器專案，新的[行動應用程式 Node.js SDK]會提供幾項新功能。 比方說，您可以立即執行本機開發和偵錯、 使用上方 0.10，任何 Node.js 版本和自訂與任何 Express.js 介軟體。

  * 。網路伺服器專案，新的[行動應用程式 SDK NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)有更多的彈性 NuGet 相依性。  這些套件支援新的應用程式服務驗證]，然後撰寫任何 ASP.NET 專案。 若要進一步瞭解如何升級，請參閱[升級現有的.NET 行動服務應用程式服務](app-service-mobile-net-upgrading-from-mobile-services.md)。

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[應用程式服務價格]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[應用程式的深入見解]: ../application-insights/app-insights-overview.md
[自動調整大小]: ../app-service-web/web-sites-scale.md
[Azure 應用程式服務]: ../app-service/app-service-value-prop-what-is.md
[Azure 服務應用程式部署文件]: ../app-service-web/web-sites-deploy.md
[Azure 傳統入口網站]: https://manage.windowsazure.com
[Azure 入口網站]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Azure 排程器計劃]: ../scheduler/scheduler-plans-billing.md
[持續部署]: ../app-service-web/app-service-continuous-deployment.md
[轉換混合命名空間]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[自訂網域名稱]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[Azure 應用程式服務的一般的顯示狀態]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[混合式連線]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[記錄]: ../app-service-web/web-sites-enable-diagnostic-log.md
[行動應用程式 Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[行動裝置與應用程式服務]: app-service-mobile-value-prop-migration-from-mobile-services.md
[通知集線器]: ../notification-hubs/notification-hubs-push-notification-overview.md
[監控效能]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[備份您的行動訊息服務]: ../mobile-services/mobile-services-disaster-recovery.md
[暫存的位置]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[XDT 轉換範例]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[函數]: ../azure-functions/functions-overview.md
