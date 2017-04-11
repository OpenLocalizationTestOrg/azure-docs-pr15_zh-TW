<properties
   pageTitle="Azure Active Directory 報告︰ 快速入門 |Microsoft Azure"
   description="列出 Azure Active Directory 報告中各種可用的報表"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="getting-started-with-azure-active-directory-reporting"></a>快速入門 Azure [Active Directory 報告

## <a name="what-it-is"></a>這是什麼

Azure Active Directory (Azure AD) 包含安全性、 活動和稽核報告，為您的目錄。 以下是包含報表的清單︰

### <a name="security-reports"></a>安全性報告

- 簽署增益集不明來源
- 多個失敗之後登增益集。
- 從多個地區登增益集。
- 來自可疑活動的 IP 位址登增益集。
- 不規則的登入活動
- 從可能受感染的裝置登增益集。
- 含異常登入活動的使用者

### <a name="activity-reports"></a>活動報表

- 應用程式使用︰ 摘要
- 應用程式使用︰ 詳細
- 應用程式儀表板
- 帳戶佈建錯誤
- 個別使用者的裝置
- 個別使用者活動
- 群組活動報表
- 密碼重設註冊活動報表
- 重設密碼的活動

### <a name="audit-reports"></a>稽核報告

- 目錄稽核報告

> [AZURE.TIP] Azure AD 報表上的多個文件，請參閱[檢視您存取及使用方式的報表](active-directory-view-access-usage-reports.md)。



## <a name="how-it-works"></a>運作方式


### <a name="reporting-pipeline"></a>報表的管線

報表的管線包含三個主要步驟。 每次使用者登入，或進行驗證時，會發生下列情況︰

- 首先，使用者驗證 （成功或失敗），而結果會儲存在 Azure Active Directory 服務資料庫。
- 定期間隔，所有的最近登集的處理。 現在，我們的安全性和異常活動演算法會搜尋所有最近登集可疑的活動。
- 處理後，報表撰寫、 快取，以及在 Azure 傳統入口網站提供服務。

### <a name="report-generation-times"></a>報表產生器時間

因為 proxy 大量，並且登入處理 Azure AD 平台的集，處理最近登集有，平均，一個小時舊。 在少見的情況下，可能需要長達 8 小時處理最新的正負號增益集。

您可以檢查在每個報表頂端的說明文字，就可找到的最近處理過登入。

![在每個報表頂端的說明文字](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP] Azure AD 報表上的多個文件，請參閱[檢視您存取及使用方式的報表](active-directory-view-access-usage-reports.md)。



## <a name="getting-started"></a>快速入門


### <a name="sign-into-the-azure-classic-portal"></a>登入 Azure 傳統入口網站

首先，您必須登入， [Azure 傳統入口網站](https://manage.windowsazure.com)以全域管理員或法規遵循管理員。 您也必須 Azure 訂閱服務系統管理員或共同管理員，或使用 「 存取 Azure AD 」 Azure 訂閱。

### <a name="navigate-to-reports"></a>瀏覽至報表

若要檢視的報表，瀏覽至您的目錄頂端的 [報表] 索引標籤。

如果這是您第一次檢視報表，您會需要同意] 對話方塊中，您可以檢視報表之前。 這是為了確保其接受檢視此資料，可能會被視為私人資訊在某些國家/地區中的組織中的管理員。

![對話方塊](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>瀏覽每個報表

瀏覽至每個報表，以查看所收集的資料及處理簽署增益集。 您可以找到[所有報表清單以下](active-directory-reporting-guide.md)。

![所有報表](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>以 csv 格式下載報表

每個報表可以下載另存為 CSV （逗號分隔值） 檔案。 您可以使用 [在 Excel、 中或協力廠商程式]，進一步分析資料的分析這些檔案。

若要下載 CSV 任何報表，請瀏覽至報表，按一下 「 下載 」 下方。

![[下載] 按鈕](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP] Azure AD 報表上的多個文件，請參閱[檢視您存取及使用方式的報表](active-directory-view-access-usage-reports.md)。





## <a name="next-steps"></a>後續步驟

### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>自訂通知異常登活動

瀏覽至您的目錄中的 「 設定 」] 索引標籤。

捲動到 [通知] 區段。

啟用或停用 「 電子郵件通知的異常登增益集 」 一節。

![[通知] 區段](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Azure AD 報告 API 與整合

請參閱[快速入門報告 API](active-directory-reporting-api-getting-started.md)。

### <a name="engage-multi-factor-authentication-on-users"></a>使用者加入多重因素驗證

選取 [在報表中的 [使用者]。

按一下畫面底部的 「 啟用 MFA 」 按鈕。

![在畫面底部的 [多重因素驗證] 按鈕](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP] Azure AD 報表上的多個文件，請參閱[檢視您存取及使用方式的報表](active-directory-view-access-usage-reports.md)。




## <a name="learn-more"></a>深入瞭解


### <a name="audit-events"></a>稽核的事件

瞭解什麼事件稽核目錄中[Azure Active Directory 報告稽核事件](active-directory-reporting-audit-events.md)。

### <a name="api-integration"></a>API 整合

請參閱[快速入門報告 API](active-directory-reporting-api-getting-started.md)及[API 參照文件](https://msdn.microsoft.com/library/azure/mt126081.aspx)。

### <a name="get-in-touch"></a>連絡

電子郵件[aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com)的意見反應 」、 「 說明 」 或 「 您可能會有任何問題。

> [AZURE.TIP] Azure AD 報表上的多個文件，請參閱[檢視您存取及使用方式的報表](active-directory-view-access-usage-reports.md)。
