<properties
   pageTitle="從 Azure 資訊安全中心資料使用 Power BI 取得深入見解 |Microsoft Azure"
   description="Azure 安全性中心 Power BI 內容套件可讓您輕鬆找到的安全性警告，建議、 攻擊資源和趨勢，根據您的報表所建立的資料集。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>從 Azure 資訊安全中心資料使用 Power BI 取得深入見解
[Power BI 儀表板](http://aka.ms/azure-security-center-power-bi)的 Azure 資訊安全中心 」 可讓您以視覺化方式呈現、 分析和篩選建議和從任何位置，包括您的行動裝置的安全性警告。 使用 Power BI 儀表板顯示趨勢，攻擊圖樣-資源或來源 IP 位址的檢視安全性警訊與資源的年齡 unaddressed 安全性風險。 

您可以也混合資訊安全中心建議與使用其他資料的安全性警訊感興趣的方法，例如使用 [從[Azure 稽核記錄](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/)和[Azure SQL 資料庫稽核](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/)資料。 都提供 Power BI 儀表板，您也可以匯出至 Excel 資料，以簡單雲端資源的安全性狀態報告。

##<a name="using-azure-security-center-dashboard-to-access-power-bi"></a>若要存取 Power BI 中使用 Azure 資訊安全中心儀表板
您也可以存取 Power BI 報表使用 [Azure 資訊安全中心儀表板。 請依照下列步驟來執行這項工作︰ 

1. **Azure 資訊安全中心**儀表板中，按一下 [ **Power BI 中的瀏覽**] 按鈕。

    ![連線至 Azure 使用 Power BI 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig1-new10.png) 

2. **Power BI 中的探索**刀開啟右側，下列畫面所示︰

    ![連線至 Azure 使用 Power BI 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig1-new2.png)

3. 如果您第一次建立 Power BI 儀表板，您可以在**Power BI 中的探索**刀選擇下列選項之一︰ 

    - **安全性的深入見解儀表板**︰ 如果您想要建立包含安全性狀態、 執行緒和偵測儀表板，請選擇這個選項。 更多一般 DevOps 角色之負責分析其保護狀態，偵測訂閱的通知，這個選項。
    - **原則管理儀表板**︰ 選擇這個選項，如果您想要探索管理及強制執行原則。  更多的中央的 IT 人員更多集中管理一般，這個選項。 他們可以使用此儀表板，取得組織內的可見度和安全性原則一致性的深入見解。
    - 如果您已經在 Power BI 儀表板，請按一下 [**移至您目前的 Power BI 儀表板**]。

4. 此範例中，按一下 [**安全性的深入見解儀表板**選項]。 如果這是第一次，您會建立資訊安全中心當系統提示您安裝的內容套件的 Power BI 儀表板。 下列畫面所示，請按一下 [在**適用 Power BI 內容的套件**] 視窗中的 [**取得**] 按鈕︰

    ![Azure 安全性中心安全性獲得深入見解儀表板](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)

5. [**連線至 Azure 安全性中心安全性深入資訊**] 視窗會顯示。 請確定已**oAuth2**如下所示的**驗證**方法，並按一下 [**登入**] 按鈕。
    
    ![驗證](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)

6. 可能會要求您再次以 Azure 認證進行驗證。 驗證您的儀表板之後將會建立。 建立儀表板之後您會看到有類似的結構的報告下列畫面所示︰

    ![Power BI 儀表板](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)


> [AZURE.NOTE] 排定的重新整理的報表進行每日。 萬一遇到此重新整理失敗時，請閱讀[重新整理的潛在問題，透過 Azure 安全性中心 Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/)，如需有關如何疑難排解。

這裡，您可以看到數安全性警訊與建議，以及 Vm、 Azure SQL 資料庫及監視 Azure 資訊安全中心的網路資源的數字。

Azure 資訊安全中心的連結會將您導向 Azure 入口網站。 圖表讓您更容易以視覺化方式呈現資訊安全性的建議和通知，包括︰

- 資源安全性狀態
- 擱置的建議
- VM 建議
- 一段時間的通知
- 受攻擊的資源
- 受攻擊的 IPs

為每個圖表，後面有其他深入資訊。 選取一個磚，若要查看更多的資訊。 例如**資源安全性狀態**磚會顯示其他詳細資料的相關建議擱置依資源下列畫面所示︰

![建議](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

如果您按一下此圖形的任何行，其他人要呈現灰色，您的焦點僅在您選取的項目。 若要返回儀表板，請按一下 [在此頁面的 [**儀表板**] 選項在左窗格的 [ **Azure 安全性中心**]。

> [AZURE.NOTE] 如果您想要新增額外的欄位，來自訂您的報表，或變更現有的視覺效果，您可以編輯報表。 如需詳細資訊，請閱讀[Power BI 中的 [編輯檢視中的報表與互動](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/)。

**一段時間，攻擊資源的通知**及**攻擊 IPs**並排會有類似的輸出，當您按一下每個。 這是因為報表彙總的所有三個變數的相關資訊，並通話**攻擊的資源**下列畫面所示︰

![攻擊的資源](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

現在您可以將此報表的複本儲存、 列印或使用 [**檔案**] 功能表中可用的選項來發佈到網站上。

![[檔案] 功能表](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>探索 Azure 資訊安全中心資料與 Power BI 服務

連線至 Power BI 中的[Power BI 內容的套件服務](https://msit.powerbi.com/groups/me/getdata/services)，然後執行下列步驟︰

1. 在 [ **Power bi 內容的套件**] 視窗中，您會看到這兩個選項，如下所示。

    ![適用 Power BI 內容的套件](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

    >[AZURE.NOTE] 如果已執行本文的第一個部分，您會看到一個選項，這是 Azure 安全性中心原則管理。

2. 以這個範例中，按一下 [ **Azure 安全性中心原則管理**磚中的 [**取得**]。

3. 在 [**連線至 Azure 安全性中心原則管理**] 視窗中，確認以選取**oAuth2**下**驗證方法**為顯示下拉式清單下方，按一下 [**登入**] 按鈕。

    ![原則管理] 視窗](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)

4. 您會被重新導向至驗證頁面，您應該在其中輸入您用於連線至 Azure 資訊安全中心的認證。 驗證程序完成之後，Power BI 會啟動匯入資料來建立您的報表。 在這段期間您可能會在瀏覽器的右上角看到下列訊息︰

    ![連線至 Azure 使用 Power BI 資訊安全中心](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] 第一次建立儀表板時可能需要更長的時間與平常不同，主要用於您有多個訂閱的案例。 

5. 在此程序完成時，Azure 安全性中心 Power BI 儀表板會載入**原則管理**報表類似以下所示︰

    ![原則管理儀表板](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>另請參閱
在此文件中，您學到如何使用 Power BI Azure 資訊安全中心中的內容。 若要進一步瞭解 Azure 資訊安全中心 」 的資訊，請參閱下列各項︰

- [Azure 安全性中心規劃及作業指南](security-center-planning-and-operations-guide.md)，瞭解如何規劃 Azure 資訊安全中心採用。
- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定在 Azure 資訊安全中心的安全性設定
- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)，瞭解如何管理和回應的安全性警告
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找使用服務的相關的常見問題集
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循
