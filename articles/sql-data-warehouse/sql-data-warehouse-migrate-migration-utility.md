<properties
   pageTitle="移轉︰ 倉儲移轉公用程式資料 |Microsoft Azure"
   description="移轉到 SQL Data Warehouse。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="data-warehouse-migration-utility-preview"></a>資料倉庫移轉公用程式 （預覽版本）

> [AZURE.SELECTOR]
- [下載移轉公用程式][]

資料倉庫移轉公用程式是設計用來從 SQL Server 和 Azure SQL 資料庫的結構描述和資料移轉到 Azure SQL Data Warehouse 的工具。 結構描述移轉過程中的工具會自動將對應至目的地對應來源的資料結構描述。 已移轉結構描述之後，工具就會提供移動資料以自動產生的指令碼的選項。

除了結構描述和資料的移轉，這項工具可讓您產生摘要列出不相容性，以防止精簡的移轉的目標和來源例項之間的相容性報告的選項。

## <a name="get-started"></a>快速入門
安裝的必要條件，您必須 BCP 命令列公用程式來執行移轉指令碼和檢視相容性報告的 Office。 在啟動下載的可執行檔之後會提示您接受標準的使用者授權合約，才能將會安裝工具。

此外，若要執行移轉 Utiliy，您會需要一個追蹤您正在尋找要移轉的資料庫的權限︰ 建立資料庫、 變更任何資料庫或檢視任何定義。

### <a name="launching-the-tool-and-connecting"></a>啟動工具] 並連線
啟動 「 桌面 」 圖示會出現文章上的 [工具安裝。 在開啟的工具，系統會提示您可以在其中選擇您的來源與目的地移轉工具初始連線頁面。 目前我們支援 SQL Server 和 Azure SQL 資料庫做為來源與目的地為 SQL Data Warehouse。 選取此之後會會要求您連線到您的來源伺服器，在 [伺服器名稱填滿和驗證，然後按一下 [連線]。

驗證之後, 工具會顯示在您已連線到伺服器的資料庫的清單。 您可以選取您想要移轉的資料庫，然後按一下 [選取移轉] 開始移轉。

## <a name="migration-report"></a>移轉報告
在工具] 中選取核取 [資料庫相容會產生摘要資料庫中的所有物件不相容的報告，要求您移轉。 我們[移轉文件][]中找不存在於 SQL Data Warehouse 中的 SQL Server 功能的一些狀況提出更廣義清單。 在報告產生之後您可以儲存並在 Excel 中開啟報表。

請注意，產生的移轉結構描述，大部分的問題，將識別為 「 物件 」 將調整若要允許即時移轉的資料時。 請檢閱變更，以確定您要套用的結構描述之前先進行其他調整。

## <a name="migrate-schema"></a>移轉結構描述

連線之後，選取移轉架構將會產生所選取的資料表結構描述移轉指令碼。 此指令碼連接埠的資料表，地圖不相容的資料結構類型以更相容的表單，並建立安全性憑證和結構描述，如果這由移轉設定] 中的使用者。 將此程式碼，可以執行的目標 SQL Data Warehouse 執行個體、 儲存至檔案、 複製到剪貼簿，或甚至編輯內嵌採取其他動作前，先。  

如以上所述，當移轉結構描述檢閱移轉變更的工具已以確保您完全瞭解它們。  

## <a name="migrate-data"></a>移轉資料

按一下移轉資料選項，您可以產生 BCP 指令碼會在伺服器上的一般檔案第一次移動您的資料，然後直接在您的 SQL Data Warehouse。 我們建議您移動少量的資料，以及重試做此程序不是內建並的網路連線中斷時，可能會失敗。 若要執行此動作，您需要擁有 BCP 命令列公用程式安裝和資料的結構描述必須已經建立。

上述參數已填寫之後您只需要按一下 [執行的移轉，並會產生一組的兩個封裝至您指定的位置。 移轉來源將資料匯出到一般檔案，才能執行匯出的檔案，才能將資料匯入 SQL Data Warehouse 執行匯入的檔案。

## <a name="next-steps"></a>後續步驟
既然您已移轉一些資料，請查看如何[開發][]。

<!--Image references-->

<!--Article references-->
[移轉文件]: sql-data-warehouse-overview-migrate.md
[開發]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[下載移轉公用程式]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip
