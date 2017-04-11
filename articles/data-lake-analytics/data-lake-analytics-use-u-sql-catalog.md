<properties
   pageTitle="介紹 Azure 資料湖分析 U SQL 目錄 |Azure"
   description="介紹 Azure 資料湖分析 U SQL 目錄"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="use-u-sql-catalog"></a>使用 U SQL 目錄

U SQL 目錄用來讓他們可以共用 U SQL 指令碼的結構資料和程式碼。 目錄] 可讓最高的效能可能 Azure 資料湖中的資料。

每個 Azure 資料湖分析帳戶有與其相關聯的一個 U SQL 目錄。 您無法刪除 U SQL 目錄。 目前無法資料湖存放帳戶之間共用 U SQL 目錄。

每個 U SQL 目錄的內容資料庫，稱為 [**母片**。 您無法刪除主要資料庫。  每個 U SQL 目錄可以包含多個其他的資料庫。

U SQL 資料庫包含︰

- 組件 – 共用.NET 之間 U SQL 指令碼的程式碼。
- 資料表值函式 – 共用之間 U SQL 指令碼的 U SQL 程式碼。
- 資料表 – U SQL 指令碼之間共用資料。
- 結構描述-共用 U SQL 指令碼之間的資料表結構描述。

## <a name="manage-catalogs"></a>管理目錄
每個 Azure 資料湖分析帳戶有與其相關聯的預設 Azure 資料湖存放帳戶。 此資料湖存放帳戶被指為預設資料湖存放帳戶。 U SQL 目錄會儲存於預設資料湖存放帳戶 /catalog 資料夾下。 不要刪除任何 /catalog 資料夾中的檔案。

### <a name="use-azure-portal"></a>使用 Azure 入口網站

請參閱[管理資料湖分析使用入口網站](data-lake-analytics-manage-use-portal.md#view-u-sql-catalog)


### <a name="use-data-lake-tools-for-visual-studio"></a>使用 Visual Studio 資料湖工具。

您可以使用 Visual Studio 資料湖工具來管理目錄。  如需工具的詳細資訊，請參閱[使用資料湖工具 Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)。

**若要管理目錄**

1. 開啟 Visual Studio 中，並連接至 azure。 如需相關指示，請參閱[連線至 Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure)。
1. 按下**CTRL + ALT + S**開啟**伺服器總管**]。
2. 從**伺服器總管**] 中，展開**Azure**、 展開**資料湖分析**、 展開資料湖分析帳戶、 展開**資料庫**，然後再展開**母片**。



    - 若要新增為新資料庫的**資料庫**，以滑鼠右鍵按一下，再按一下 [**建立資料庫**。
    - 若要將新的組件**的組件**，以滑鼠右鍵按一下，再按一下 [**註冊的組件**。
    - 若要新增新的結構描述**結構描述**，以滑鼠右鍵按一下，再按一下 [建立結構描述 * *。
    - 若要新增新的資料表，以滑鼠右鍵按一下**資料表**，然後按一下 「 「 建立表格 * *。
    - 若要新增新的資料表值函式，請參閱[開發 U SQL 使用者定義資料湖分析工作的運算子](data-lake-analytics-u-sql-develop-user-defined-operators.md)。


![瀏覽 U SQL Visual Studio 目錄](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## <a name="see-also"></a>另請參閱

- 快速入門
    - [開始使用資料湖分析使用 Azure 入口網站](data-lake-analytics-get-started-portal.md)
    - [使用 PowerShell 的 Azure 資料湖分析快速入門](data-lake-analytics-get-started-powershell.md)
    - [使用 Azure.NET SDK 資料湖分析快速入門](data-lake-analytics-get-started-net-sdk.md)
    - [開發 U SQL 指令碼的 Visual Studio 中使用資料湖工具](data-lake-analytics-data-lake-tools-get-started.md)
    - [開始使用 Azure 資料湖分析 U SQL 語言](data-lake-analytics-u-sql-get-started.md)

- U SQL 與開發
    - [開始使用 Azure 資料湖分析 U SQL 語言](data-lake-analytics-u-sql-get-started.md)
    - [Azure 資料湖分析工作使用 U SQL 視窗函數](data-lake-analytics-use-window-functions.md)
    - [開發 U SQL 資料湖分析工作的使用者定義的運算子](data-lake-analytics-u-sql-develop-user-defined-operators.md)

- 管理
    - [管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)
    - [管理使用 Azure PowerShell 的 Azure 資料湖狀況分析](data-lake-analytics-manage-use-powershell.md)
    - [監控和疑難排解 Azure 資料湖分析工作使用 Azure 入口網站](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- 端對端教學課程
    - [使用 Azure 資料湖分析互動式教學課程](data-lake-analytics-use-interactive-tutorials.md)
    - [分析網站記錄使用 Azure 資料湖狀況分析](data-lake-analytics-analyze-weblogs.md)
