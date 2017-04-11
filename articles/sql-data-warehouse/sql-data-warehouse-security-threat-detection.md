<properties
   pageTitle="開始使用 SQL 資料倉庫威脅偵測"
   description="如何開始使用威脅偵測"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# <a name="get-started-with-threat-detection"></a>快速入門威脅偵測

> [AZURE.SELECTOR]
- [稽核](sql-data-warehouse-auditing-overview.md)
- [威脅偵測](sql-data-warehouse-security-threat-detection.md)

## <a name="overview"></a>概觀

威脅偵測偵測指出資料庫的潛在安全性威脅異常資料庫活動。 威脅偵測預覽中，而支援的 SQL Data Warehouse。

威脅偵測提供新的圖層的安全性，可讓客戶偵測和回應潛在威脅發生異常活動上提供的安全性警告。 使用者可以瀏覽可疑來判斷它們會從存取、 違反或利用資料倉庫中的資料嘗試使用 [ [Azure SQL 資料倉庫稽核](sql-data-warehouse-auditing-overview.md)的事件。
威脅偵測讓簡單資料倉庫，而不需要使用安全性專家或管理監控系統的進階的安全性的地址潛在威脅。

例如，威脅偵測偵測指出可能的 SQL 注入嘗試某些異常資料庫活動。 SQL 插入是一種常見攻擊的資料導向的應用程式，在網際網路上 Web 應用程式的安全性問題。 攻擊利用應用程式弱點插入惡意的 SQL 陳述式的破壞或修改資料庫中的資料的應用程式項目欄位。


## <a name="set-up-threat-detection-for-your-database"></a>設定資料庫的威脅偵測

1. 啟動 Azure [https://portal.azure.com](https://portal.azure.com)在入口網站。

2. 瀏覽至您想要監視 SQL Data Warehouse 的設定刀。 在 [設定刀中，選取**稽核及威脅偵測**。

    ![功能窗格][1]

3. **稽核與威脅偵測**設定刀中開啟 [**開啟**稽核，它將會顯示威脅偵測設定]。

    ![功能窗格][2]

4. 開啟**開啟**威脅偵測。

5. 設定電子郵件安全性警訊偵測的異常資料倉庫活動時，所收到的清單。

6. 若要儲存新的或更新稽核及威脅偵測原則**稽核及威脅偵測**設定刀中，按一下 [**儲存**]。

    ![功能窗格][3]


## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>探索當可疑的事件偵測時的異常資料倉庫活動

1. 您會收到電子郵件通知時偵測的異常資料庫活動。 <br/>
電子郵件會提供包括性質的異常活動、 資料庫名稱、 伺服器名稱及事件時間可疑的安全性事件資訊。 此外，它會提供可能的原因，並建議調查並降低資料庫的潛在威脅的動作。<br/>

    ![功能窗格][4]

2. 在電子郵件，按一下 [ **Azure SQL 稽核記錄檔**的連結，啟動 Azure 傳統入口網站，並顯示相關的稽核記錄可疑事件的時間。

    ![功能窗格][5]

3. 按一下 [檢視更多詳細資料，在 SQL 陳述式，例如可疑的資料庫活動的稽核記錄失敗的原因與用戶端 IP。

    ![功能窗格][6]

4. 在 [稽核記錄刀中，按一下 [開啟預先設定的 [**在 Excel 中開啟**excel 範本匯入及執行深入分析的稽核記錄可疑事件的時間。<br/>
**附註︰**在 Excel 2010 或更新版本，是必要的 Power Query] 和 [**快速合併**] 設定

    ![功能窗格][7]

5. 若要設定 [**快速合併**] 設定中的 [ **POWER QUERY**功能區索引標籤中，選取 [**選項**]，以顯示 [選項] 對話方塊。 選取 [隱私權] 區段，然後選擇 [第二個選項的 [略過隱私權層級和可能會改善效能]:

    ![功能窗格][8]

6. 若要載入 SQL 稽核記錄，請確定在設定] 索引標籤已正確設定，然後選取 [資料] 功能區並按一下 [全部重新整理] 按鈕的參數。

    ![功能窗格][9]

7. 結果會出現在這可讓您更深入分析的未偵測到的異常活動的**SQL 稽核記錄**工作表，並降低應用程式中的安全性事件的影響。


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
