<properties
   pageTitle="安裝 SQL Data Warehouse Visual Studio 與 SSDT |Microsoft Azure"
   description="Azure SQL Data Warehouse 安裝 Visual Studio 與 SQL Server 開發 Tools (SSDT)"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>將的 SQL Data Warehouse Visual Studio 2015 及 SSDT

若要開發的 SQL Data Warehouse 應用程式，我們建議使用最新版本的 SQL Server Data Tools (SSDT) 的 Visual Studio 2015。  回溯相容性也支援與 SSDT visual Studio 2013 更新 5年。  

Visual Studio 使用 SSDT 可讓您以視覺方式探索資料表、 檢視、 預存程序及您 SQL Data Warehouse 中的有更多物件，以及執行查詢中使用 SQL Server 物件總管]。

> [AZURE.NOTE] SQL Data Warehouse 還不支援 Visual Studio 資料庫專案。  此功能將會在未來版本中新增。

## <a name="step-1-install-visual-studio-2015"></a>步驟 1︰ 安裝 Visual Studio 2015

請遵循這些連結，下載及安裝 Visual Studio 2015。 如果您已經有 Visual Studio 2013 或 2015 安裝時，您可以直接跳到步驟 2，安裝 SSDT。

1. [下載 Visual Studio 2015][]。
2. 依照[安裝 Visual Studio][]指南 MSDN 上，然後選擇 [預設設定。

## <a name="step-2-install-ssdt"></a>步驟 2︰ 安裝 SSDT

若要安裝的 Visual Studio SSDT 只要核取 SSDT 更新從 Visual Studio 中遵循下列步驟。

1. 在 Visual Studio 中，按一下 [**工具]** / **Extensions 和更新...** / **更新**
2. 選取**產品的更新**，然後尋找**Microsoft SQL Server 更新資料庫工具**

如果找不到更新，您應該已安裝最新版本。  若要確認 SSDT 已安裝，請按一下 [在**協助** / **瞭解 Microsoft Visual Studio**與 SQL Server Data Tools 的 [查詢] 清單中。  最新版的 SSDT 是 14.0.60525.0。  如果 [安裝] 選項無法使用從 Visual Studio，或者您可以造訪下載並安裝 SSDT 手動[SSDT 下載][]頁面。

## <a name="next-steps"></a>後續步驟

現在您已經有 SSDT 的最新版本，您是準備好要[連線][]至您的 SQL Data Warehouse。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[連線]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[下載 Visual Studio 2015]: https://www.visualstudio.com/downloads/
[安裝的 Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT 下載]: https://msdn.microsoft.com/library/mt204009.aspx
