<properties
   pageTitle="如何連線至資料來源 |Microsoft Azure"
   description="醒目提示如何連線至資料來源發現 Azure 資料目錄的使用方法文章。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="maroche"/>


# <a name="how-to-connect-to-data-sources"></a>如何連線至資料來源

## <a name="introduction"></a>簡介
**Microsoft Azure 資料目錄**] 是探索的登錄的完全受管理的雲端服務，可作為系統和企業資料來源系統。 換句話說， **Azure 資料目錄**是協助人員探索、 瞭解以及使用資料來源，並協助組織取得更多的值從現有的資料。 這種情況的重點使用的資料-使用者探索資料來源，並瞭解其目的下, 一步是連線到資料來源，以將其資料，可供使用。

## <a name="data-source-locations"></a>資料來源位置
在 [資料來源註冊， **Azure 資料目錄**會收到有關的資料來源的中繼資料。 此中繼資料包括詳細資料的資料來源的位置。 位置的詳細資料，也會因資料來源的資料來源，但它會永遠包含連線所需的資訊。 例如，SQL Server 表格的位置包含的伺服器名稱、 資料庫名稱、 結構描述名稱及表格名稱] 時 SQL Server Reporting Services 報表的位置包含的伺服器名稱及報表的路徑。 其他資料來源類型會有反映的結構及來源系統功能的位置。

## <a name="integrated-client-tools"></a>整合式的用戶端工具
連線至資料來源的最簡單方法是使用 「 中開啟。...」 **Azure 資料目錄**入口網站中的功能表。 此功能表顯示連結到選取的資料資產選項的清單。
使用預設的並排檢視，此功能表就可以使用每個圖磚上。

 ![在 Excel 中開啟 SQL Server 資料表的資料資產] 磚](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

使用 [清單] 檢視，功能表會用於入口網站視窗頂端的 [搜尋] 列。

 ![從 [搜尋] 列中報表管理員開啟 SQL Server Reporting Services 報表](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>支援的用戶端應用程式
使用時] 中開啟。...」 用戶端電腦上必須安裝 Azure 資料目錄入口網站，這是正確的用戶端應用程式中的資料來源的功能表。

| 在應用程式中開啟 | 檔案副檔名 / 通訊協定 | 支援的應用程式版本 |
| --- | --- | --- |
| Excel | .odc | Excel 2010 或更新版本 |
| Excel （上方 1000年） | .odc | Excel 2010 或更新版本 |
| Power Query | .xlsx | 安裝 Excel 2016 或 Excel 2010 或 Excel 2013 與 Power Query for Excel 增益集
| Power BI Desktop | .pbix | Power BI Desktop 年 7 月 2016年或更新版本 |
| SQL Server Data Tools | vsweb: / / | Visual Studio 2013 更新 4 或更新版本的安裝 SQL Server 工具 |
| 報表管理員 | http:// | 請參閱[SQL Server Reporting Services 瀏覽器需求](https://technet.microsoft.com/en-us/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>您的資料，您的工具
在功能表中可用的選項視目前選取的資料資產的類型而定。 當然，並非所有可能的工具將會包含在 「 中開啟。...」 ] 功能表中，但輕鬆仍連線到資料來源使用任何用戶端工具。 當**Azure 資料目錄**入口網站中選取的資料資產時，完成的位置會顯示在 [屬性] 窗格中。

 ![SQL Server 表格的連線資訊](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

連線資訊詳細資料將會與資料來源類型不同資料來源類型，但在入口網站中所包含的資訊可讓您必須連線至任何用戶端工具中的資料來源的所有項目。 使用者可以將複製資料來源發現使用**Azure 資料目錄**，讓他們能夠使用其所選擇的工具中的資料連線詳細的資料。

## <a name="connecting-and-data-source-permissions"></a>連線和資料來源的權限
雖然**Azure 資料目錄**讓您可以找到資料來源，請存取本身的資料會保留的資料來源擁有者或系統管理員控制。 探索**Azure 資料目錄**] 中的資料來源並不會提供使用者任何權限來存取資料來源本身。

若要更容易探索資料來源，但沒有存取其資料的權限的使用者，加註解的資料來源時使用者時，可提供要求存取] 屬性中的資訊。 以下提供 – 包括程序或連絡，以存取資料來源的連結 – 資訊會顯示同時入口網站中的資料來源位置資訊。

 ![要求存取指示所提供的連線資訊](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

##<a name="summary"></a>摘要
註冊**Azure 資料目錄**的資料來源可讓您的資料搜尋結構化及描述性的中繼資料複製到類別目錄服務的資料來源。 使用者資料來源具有已註冊，並發現之後，可以從**Azure 資料目錄**入口網站連線至資料來源] 中開啟。...」 」 功能表或使用他們的資料工具的選項。

## <a name="see-also"></a>另請參閱
- 逐步的詳細資訊，了解如何連線至資料來源的[快速入門 Azure 資料目錄](data-catalog-get-started.md)的教學課程。
