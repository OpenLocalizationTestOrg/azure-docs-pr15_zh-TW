<properties
   pageTitle="在 SQL Azure 資料倉庫稽核 |Microsoft Azure"
   description="快速入門中 Azure SQL Data Warehouse 稽核"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="auditing-in-azure-sql-data-warehouse"></a>在 SQL Azure Data Warehouse 稽核

> [AZURE.SELECTOR]
- [稽核](sql-data-warehouse-auditing-overview.md)
- [威脅偵測](sql-data-warehouse-security-threat-detection.md)

SQL Data Warehouse 稽核可讓您記錄中以稽核的事件登入您的 Azure 儲存體帳戶。 稽核，可協助您維護法規遵循、 瞭解資料庫活動和掌握差異和異常可能表示商務問題或假設安全性衝突。 SQL Data Warehouse 稽核也整合了 Microsoft Power BI 的向下切入報告及分析。

稽核工具啟用並協助遵守法規遵循標準，但不保證規範。 如需支援標準法規遵循的 Azure 程式的詳細資訊，請參閱<a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure 信任中心]</a>。

+ [稽核資料庫的基本概念]
+ [將資料庫的稽核設定]
+ [分析稽核記錄檔和報表]

##<a id="subheading-1"></a>Azure SQL 資料倉庫資料庫稽核的基本概念


SQL Data Warehouse 資料庫稽核可讓您︰

- **保留**選取事件的稽核記錄。 您可以定義要稽核的資料庫動作的類別。
- 資料庫活動**報表**。 若要快速開始使用的活動和事件報告，您可以使用預先設定的報表和儀表板。
- **分析**報表。 您可以找到可疑事件、 異常的活動和趨勢。

您可以設定稽核下列類別的事件︰

**純文字 SQL**和**參數化 SQL**其收集的稽核記錄會被歸類為  

- **存取資料**
- **結構描述變更 (語言 DDL)**
- **資料變更 (DML)**
- **帳戶、 角色和權限 (DCL)**
- **預存程序**，**登入**和**交易管理**。

每個事件類別是分別設定稽核**成功**和**失敗**作業。

進一步瞭解的活動和稽核的事件，請參閱<a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">稽核記錄檔格式參照 （文件檔案下載）</a>。

稽核記錄會儲存在您的 Azure 儲存體帳戶。 您可以定義的稽核記錄保留期間。

稽核原則可定義為特定的資料庫或預設伺服器原則。 預設伺服器稽核原則會套用至所有資料庫伺服器上沒有特定覆寫資料庫稽核原則定義。

設定之前向上稽核稽核核取，如果您使用的[舊版用戶端]](sql-data-warehouse-auditing-downlevel-clients.md)。


##<a id="subheading-2"></a>將資料庫的稽核設定

1. 啟動<a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。

2. 瀏覽至 SQL Data Warehouse 資料庫的設定刀 / 您想要稽核的 SQL Server。 在上方，然後設定刀中，在 [**設定**] 按鈕，然後選取**稽核**。

    ![][1]

3. 稽核設定刀中，在第一次取消選取**繼承稽核設定伺服器**] 核取方塊。 這個選項可讓您指定的設定為特定的資料庫。

    ![][2]

4. 接下來，啟用稽核按一下 [**上**] 按鈕。

    ![][3]

5. 在稽核設定刀中，選取 [開啟稽核記錄檔儲存刀**儲存詳細資料**]。 選取要儲存記錄檔的 Azure 儲存體帳戶和保留期限。 **提示︰**使用相同的儲存空間帳戶的所有稽核資料庫充分利用預先設定的報表範本。

    ![][4]

6. 按一下 [**確定**] 按鈕，將儲存詳細資料設定]。


7. 在 [**記錄的事件**，按一下**成功**和**失敗**記錄所有事件，或選擇個別事件類別。


8. 如果您要設定稽核的資料庫，您可能需要變更您的用戶端，以確保正確擷取稽核資料的連線字串。 核取舊版的用戶端連線的[連線字串中的 [修改伺服器 FDQN](sql-data-warehouse-auditing-downlevel-clients.md)主題。

9. 按一下**[確定]**。


##<a id="subheading-3">分析稽核記錄檔和報表</a>

稽核記錄存放區資料表的集合中彙總的**SQLDBAuditLogs**前置詞，在您選擇在安裝期間 Azure 儲存體帳戶。 您可以檢視使用的工具，例如<a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure 儲存檔案總管</a>的記錄檔。

預先設定的儀表板報表範本已作為<a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">可下載的 Excel 試算表</a>，協助您快速分析記錄資料。 若要使用範本，在您的稽核記錄，您需要 2013年或更新版本的 Excel 和 Power Query，您可以下載<a href="http://www.microsoft.com/download/details.aspx?id=39379">以下</a>。

範本中，含有虛構範例資料，您可以直接從 Azure 儲存體帳戶匯入您的稽核記錄設定 Power Query。

如需使用的報表範本的詳細指示，請閱讀<a href="http://go.microsoft.com/fwlink/?LinkId=506731">如何 （文件下載）</a>。

![][5]


##<a id="subheading-4">作法生產環境中的使用方式</a>
本節描述指的是螢幕擷取畫面上方。 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>或<a href= "https://manage.windowsazure.com/" target="_bank">標準 Azure 傳統入口網站</a>可使用。


##<a id="subheading-5"></a>重新產生金鑰儲存空間

在您可能會定期重新整理儲存索引鍵。 當重新整理您的金鑰必須重新儲存的原則。 程序如下:。


1. 在 （上述的設定稽核區段） 的稽核設定刀切換**儲存便捷鍵***主要**次要*和**儲存**。
![][4]
2. 移至儲存設定刀並**重新產生** *Access 的主索引鍵*。

3. 移至稽核設定刀中，切換**儲存便捷鍵**從*第二個**主要*並按下 [**儲存**]。

4. 回到 UI 儲存並**重新產生***次要便捷鍵*（如 [下一步的按鍵重新整理循環圖的準備。

##<a id="subheading-6"></a>自動化
有數種 PowerShell 指令程式可用來設定 Azure SQL 資料庫中的 [稽核。 若要存取稽核 cmdlet 您必須執行 PowerShell Azure 資源管理員模式中。

> [AZURE.NOTE] [Azure 資源管理員](https://msdn.microsoft.com/library/dn654592.aspx)模組目前正在預覽。 它可能會提供的 Azure 模組管理功能。

當您在 Azure 資源管理員模式中，執行`Get-Command *AzureSql*`清單可用的 cmdlet。


<!--Anchors-->
[稽核資料庫的基本概念]: #subheading-1
[將資料庫的稽核設定]: #subheading-2
[分析稽核記錄檔和報表]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
