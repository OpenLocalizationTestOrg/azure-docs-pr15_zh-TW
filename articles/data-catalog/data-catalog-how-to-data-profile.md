<properties
    pageTitle="如何將資料的設定檔資料來源"
    description="醒目提示如何註冊 Azure 資料目錄中的資料來源時，包括資料表和資料行層級的資料設定檔，以及如何使用了解資料來源的資料設定檔的使用方法文章。"
    services="data-catalog"
    documentationCenter=""
    authors="spelluru"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/13/2016"
    ms.author="spelluru"/>

# <a name="data-profile-data-sources"></a>資料的設定檔資料來源

## <a name="introduction"></a>簡介

**Microsoft Azure 資料目錄**] 是探索的登錄的完全受管理的雲端服務，可作為系統和企業資料來源系統。 換句話說， **Azure 資料目錄**是協助人員探索、 瞭解以及使用資料來源，並協助組織取得更多的值從現有的資料。 當資料來源註冊使用**Azure 資料目錄**時，請複製它的中繼資料和索引服務，但本文結尾不是那里。

**Azure 資料目錄**的 [**資料剖析**] 功能會檢查支援的資料來源的資料目錄] 中的資料，並會收集統計資料以及該資料的相關資訊。 您可以輕鬆包含您的資料資產的設定檔。 當您註冊的資料資產時，請選擇 [資料來源註冊工具中的 [**包含資料設定檔**]。

## <a name="what-is-data-profiling"></a>什麼是資料分析

資料剖析檢查登錄、 資料來源中的資料，並會收集統計資料以及該資料的相關資訊。 在 [資料來源探索，這些統計資料，也可以協助您判斷要解決商務問題的資料符合。

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

下列資料來源支援的資料分析︰

- （包括 Azure SQL 資料庫和 Azure SQL Data Warehouse） 的 SQL Server 資料表及檢視
- Oracle 資料表和檢視
- Teradata 資料表和檢視
- 登錄區資料表

包括資料設定檔時登錄的資料資產，可協助使用者回答問題資料來源，包括︰

-   可將其用於解決我的商務問題嗎？
-   資料是否符合特定標準或圖樣中？
-   有哪些資料來源的異常？
-   什麼是可能的挑戰的整合此資料到我的應用程式？

> [AZURE.NOTE] 您也可以新增描述如何資料可以整合至應用程式資產的文件。 請參閱[如何將資料來源的文件](data-catalog-how-to-documentation.md)。


<a name="howto"/>
## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>如何註冊的資料來源時包含資料的設定檔

您可以輕鬆要包含的資料來源設定檔。 當您**註冊物件**的面板中的資料來源註冊工具註冊資料來源時，請選擇 [**包含的資料設定檔**]。

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

若要進一步瞭解如何註冊的資料來源，請參閱[如何註冊的資料來源](data-catalog-how-to-register.md)，並[開始使用 Azure 資料目錄](data-catalog-get-started.md)。


## <a name="filtering-on-data-assets-that-include-data-profiles"></a>篩選的資料資產，包含資料的設定檔
若要找出包含資料的設定檔的資料資產，您可以包含`has:tableDataProfiles`或`has:columnsDataProfiles`做為其中一個搜尋文字。

> [AZURE.NOTE] 選取**包含資料設定檔**中的資料來源註冊工具包括資料表和資料行層級設定檔資訊。 不過，資料目錄 API 」 可讓資料資產，註冊只有一個設定的設定檔所包含的資訊。

## <a name="viewing-data-profile-information"></a>檢視資料的設定檔資訊

當您找到為適當的資料來源的設定檔時，您可以檢視資料設定檔詳細資料。 若要檢視資料設定檔，請選取的資料資產，選擇 [**資料設定檔**]，請在 [資料目錄入口網站] 視窗。

![](media\data-catalog-data-profile\data-catalog-view.png)

**Azure 資料目錄**] 中的資料設定檔顯示資料表和資料行的設定檔資訊包括︰

### <a name="object-data-profile"></a>物件資料設定檔

-   資料列數目
-   表格大小
-   上次更新物件

### <a name="column-data-profile"></a>欄資料設定檔

- 資料行資料類型
- 唯一值的數目
- NULL 值的資料列的數字
- 最小值、 最大值、 average 與資料行值的標準差

## <a name="summary"></a>摘要
分析的資料提供統計資料以及已註冊的資料資產，可協助您判斷要解決商務問題的資料符合的相關資訊。 註解，及文件的資料來源，以及資料設定檔可以讓使用者更深入瞭解您的資料。


## <a name="see-also"></a>另請參閱
-   [如何註冊的資料來源](data-catalog-how-to-register.md)
-   [開始使用 Azure 資料目錄](data-catalog-get-started.md)
