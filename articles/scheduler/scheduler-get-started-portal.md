<properties
 pageTitle="快速入門 Azure 入口網站中的 Azure 排程器 |Microsoft Azure"
 description="快速入門 Azure 入口網站中的 Azure 排程器"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>快速入門 Azure 入口網站中的 Azure 排程器

您可以輕鬆建立 Azure 排程器中的已排程的工作。 在此教學課程中，您將學習如何建立工作。 您也可以學習排程器的監控和管理功能。

## <a name="create-a-job"></a>建立工作

1.  [Azure](https://portal.azure.com/)入口網站登入。  

2.  按一下 [ **+ 新增]** > [搜尋] 方塊中輸入_排程器_> 選取結果中的**排程器**> 按一下 [**建立**]。

     ![][marketplace-create]

3.  現在就讓我們建立直接碰到 http://www.microsoft.com/ GET 要求工作。 在 [**工作排程器**] 畫面中，輸入下列資訊︰

    1.  **名稱︰**`getmicrosoft`  

    2.  **訂閱︰**Azure 訂閱   

    3.  **工作集合︰**選取現有的工作集合，或按一下 [**建立新**> 輸入的名稱。

4.  接下來，請在 [**動作設定**] 中，定義為下列的值︰

    1.  **動作類型︰**` HTTP`  

    2.  **方法︰**`GET`  

    3.  **URL:**` http://www.microsoft.com`  

      ![][action-settings]

5.  最後，讓我們來定義排程。 工作可定義為一次性的工作，但現在就讓我們挑選 [循環排程︰

    1. **週期性**︰`Recurring`

    2. **開始**︰ 今天的日期

    3. **重複發生每個**:`12 Hours`

    4. **結束於**︰ 從今日兩天的日期  

      ![][recurrence-schedule]

6.  按一下 [**建立**]

## <a name="manage-and-monitor-jobs"></a>管理及監視工作

建立工作，它會出現在主要 Azure 儀表板。 按一下 [工作]，然後在新視窗開啟下列索引標籤︰

1.  屬性  

2.  動作設定  

3.  排程  

4.  歷程記錄

5.  使用者

    ![][job-overview]

### <a name="properties"></a>屬性

這些唯讀的屬性描述排程器工作的管理中繼資料。

   ![][job-properties]


### <a name="action-settings"></a>動作設定

按一下在 [**工作**] 畫面的作業，可讓您設定的工作。 這個選項可讓您設定進階的設定]，如果您沒有設定中快速建立精靈。

所有動作類型，您可能會都變更重試原則，以及錯誤的動作。

HTTP 和 HTTPS 工作動作類型，您可能會變更任何允許 HTTP 動詞方法。 您也可以新增、 刪除或變更的標題和基本驗證資訊。

儲存佇列中動作類型，您可能會變更儲存帳戶、 佇列名稱、 SA 權杖與本文。

服務匯流排動作類型，您可能會變更的命名空間、 主題/佇列路徑、 驗證設定、 傳輸類型、 郵件內容及郵件本文。

   ![][job-action-settings]

### <a name="schedule"></a>排程

這個選項可讓您重新設定排程，如果您想要變更您在建立的排程快速建立精靈。

這是商機建立[複雜的排程和工作中的進階循環](scheduler-advanced-complexity.md)

您可能會變更的開始日期和時間、 週期排程和結束的日期和時間 （如果工作週期性。）

   ![][job-schedule]


### <a name="history"></a>歷程記錄

[**歷程記錄**] 索引標籤會顯示所選的指標的每個作業執行系統中的 [選取的工作。 這些計量提供關於您的排程器的健康情況的即時值︰

1.  狀態  

2.  詳細資料  

3.  嘗試再試一次

4.  項目︰ 1st，2nd，3rd，等等。

5.  執行的開始時間  

6.  執行的結束時間

   ![][job-history]

您可以按一下 [執行] 來檢視其**歷程記錄的詳細資料**，包括每次執行的整個回應。 此對話方塊也可讓您複製到剪貼簿的回應。

   ![][job-history-details]

### <a name="users"></a>使用者

Azure 角色型存取控制 (RBAC) 可讓精密存取管理 Azure 排程器。 若要瞭解如何使用 [使用者] 索引標籤，請參閱[Azure Role-Based 存取控制](../active-directory/role-based-access-control-configure.md)


## <a name="see-also"></a>另請參閱

 [什麼是排程器？](scheduler-intro.md)

 [排程器概念、 詞彙及實體階層](scheduler-concepts-terms.md)

 [方案與帳單方面的 Azure 排程器](scheduler-plans-billing.md)

 [如何建立複雜的排程和進階的循環 Azure 排程器](scheduler-advanced-complexity.md)

 [排程器 REST API 參照](https://msdn.microsoft.com/library/mt629143)

 [排程器 PowerShell cmdlet 參考](scheduler-powershell-reference.md)

 [排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [排程器限制與預設值]，出現錯誤碼](scheduler-limits-defaults-errors.md)

 [排程器輸出驗證](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
