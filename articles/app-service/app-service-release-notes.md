<properties 
   pageTitle="Azure SDK.NET 2.5.1 版本資訊" 
   description="Azure SDK.NET 2.5.1 版本資訊" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/10/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-net-251-release-notes"></a>Azure SDK.NET 2.5.1 版本資訊

這份文件包含版本資訊 Azure SDK 的.NET 2.5.1 放開。 

##<a name="azure-sdk-for-net-251-release-notes"></a>Azure SDK.NET 2.5.1 版本資訊

以下是新功能和.net 2.5.1 Azure sdk 的更新。

- **Web 工具副檔名**相關的新 features\scenarios。 

    - Azure 網站已重新命名為 [Azure 應用程式服務。 如需詳細資訊，請參閱[Azure 應用程式服務與現有的 Azure 服務](app-service-changes-existing-services.md)。
    - 讓客戶發佈 API 應用程式]，為 ASP.NET 專案，然後使用 [新增已新增 azure API 應用程式 （預覽版本） 支援 > Azure API 應用程式的用戶端手勢在 C# 產生基礎結構的部署 API 應用程式的程式碼的專案。 
    - 伺服器總管] 中的 [網站] 節點已被取代，而 Azure 應用程式服務節點，其中包含 [資源群組型群組的 Azure API 應用程式、 行動應用程式，與 Web 應用程式的支援。
    - 已新增 azure Mobile 應用程式 （預覽版本） 支援]，讓客戶可以建立 Mobile 應用程式的新專案，加入行動應用程式控制站、 發佈專案，並從遠端偵錯應用程式。
    - 新增 > Azure API 應用程式的用戶端筆勢現在支援本機 Swagger JSON 檔案，讓網頁 API 開發人員可以使用協力廠商 NuGets 像 Swashbuckle 產生 Swagger 或作者，以手動方式。 如此一來，用戶端開發人員可以使用的程式碼產生功能來 C# 專案中的任何 Swagger 結束點。 
    - Web 應用程式和 API 應用程式發佈對話方塊增強支援 Azure 入口網站的概念資源群組，並選取/建立 Azure 資源群組和應用程式服務方案都可以在 [新的 Web 應用程式及 API 應用程式佈建]。 
    - Azure API 應用程式伺服器總管節點提供連結中 Azure 入口網站，以及其他功能，例如串流記錄，以及遠端偵錯 [深層 API 應用程式] 連結。

    已知的問題和 Azure SDK.NET 2.5.1[此](app-service-release-notes.md#known_issues_2_5_1)下方的章節中的目前限制。


- 在這個版本中啟用新 features\scenarios 相關**HDInsight 工具**Visual Studio 中。 
    - 本機的驗證的登錄區指令碼。 按一下 [若要查看您的指令碼是否有任何錯誤] 工具列中的驗證指令碼] 按鈕。 
    - 改良的偵錯的登錄區工作。 您現在可以存取 Yarn 記錄在 Visual Studio 偵錯登錄區工作。 如果您的應用程式有效能問題，調查 YARN 記錄會提供有用的資訊。
    - （公用預覽版本）關鍵字自動完成及 IntelliSense 支援登錄區。 若要協助您撰寫登錄區指令檔、 Visual Studio HDInsight 工具新增關鍵字自動完成及 IntelliSense 支援登錄區。
    - 大量支援。 您現在可以使用 HDInsight 工具 Visual Studio 開發大量拓撲/Spouts/螺栓 C#。 然後，您可以送出到大量叢集開發的拓撲，並查看拓撲/螺栓/spout 狀態。 您可以使用系統記錄，以及客戶記錄，以疑難排解您大量為拓撲/螺栓/Spouts。 您也可以使用現有的 JAVA 資產中大量 HDInsight 上。
    
    如需詳細資訊，請參閱[開始使用 Visual Studio HDInsight Hadoop 工具](hdinsight-hadoop-visual-studio-tools-get-started.md)。



##<a id="known_issues_2_5_1"></a>Azure SDK.NET 2.5.1 已知問題和限制

- Azure API 應用程式是顯示為行動應用程式的部署目標。 Web 應用程式應該是唯一的目的 Mobile 應用程式，後續發行之前。 
- Azure API 應用程式佈建後，可以成功的結果，但間歇性無法更新 Azure 應用程式服務活動視窗中的進度。 因應措施若要檢查狀態 Azure 入口網站中的新 Azure API 應用程式。 
- 檔案 > 新專案 > API 應用程式 > F5 體驗結果 HTTP 錯誤，因為沒有 default/index.html。 因應措施若要手動瀏覽至 /api/values URL。 
- 有關，出現平面伺服器總管] 圖示。 重新啟動與解決這個問題。 
- 如果發生例外狀況 （例如超出的配額 」 錯誤或重複 Azure API 應用程式的閘道器名稱） 提供的 Web 應用程式或 API 應用程式時，錯誤會顯示原始 JSON 文字。 
- 應用程式的深入見解已在專案建立時，出現間歇性發生的專案建立問題。
- 有時候，產生的 Azure API 應用程式的用戶端程式碼會遺失命名空間，需要手動包含 （或自動匯入透過 Visual Studio 提示） 編譯程式碼。 
- 行動應用程式專案發佈至 web 應用程式，但您必須選擇 [行動應用程式專案需要資料庫後，您建立為 Azure 入口網站中的行動應用程式的網站。 
- [開始] 頁面的行動應用程式使用的字詞，而不是 「 行動應用程式 」 的 「 行動訊息服務 」 
- 行動應用程式專案建立最多可能需要幾分鐘建立。 
- 期間 API 應用程式 （在某些情況下），佈建錯誤是來自回 Azure API 反映的權限無法設定正確，同時 API 應用程式正確佈建且可供使用。 您可以手動設定使用 Azure 入口網站的權限。
- 應用程式的深入見解不支援的 API 應用程式範本和行動應用程式範本。
- API 應用程式專案不能搭配雲端服務專案。
- 只適用於 C# API 應用程式專案範本。
- C# 只支援透過新增 Azure API 應用程式用戶端] 快顯功能表的 API 應用程式使用。

 
