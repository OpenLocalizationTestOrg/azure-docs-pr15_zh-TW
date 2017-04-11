<properties
   pageTitle="Azure 資料目錄術語 |Microsoft Azure"
   description="本文提供簡介概念和 Azure 資料目錄的文件中所用的字詞。"
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
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-terminology"></a>Azure 資料目錄詞彙

## <a name="catalog"></a>目錄

Azure 資料目錄是雲端中繼資料存放庫中的資料來源與資料資產註冊。 目錄] 做為結構化資料來源中擷取的中繼資料，以及新增使用者的描述性的中繼資料的中央存放位置。

## <a name="data-source"></a>資料來源

資料來源是系統或容器，可管理的資料資產。 範例包括 SQL Server 資料庫、 Oracle 資料庫、 SQL Server Analysis Services 資料庫 （為表格式或多維度） 和 SQL Server Reporting Services 伺服器。

## <a name="data-asset"></a>資料資產

資料資產是包含在可使用目錄註冊的資料來源的物件。 範例包括 SQL Server 資料表及檢視、 Oracle 資料表和檢視、 SQL Server Analysis Services 量值、 維度及 Kpi，和 SQL Server Reporting Services 報表。

## <a name="data-asset-location"></a>資料資產的位置

目錄] 儲存資料來源的資料資產，可以用來連線到使用用戶端應用程式的來源位置。 格式和位置的詳細資料變化的資料來源類型。 例如，SQL Server 表格可由識別四組件名稱 – 伺服器名稱、 資料庫名稱、 結構描述名稱的物件名稱 – 時 SQL Server Reporting Services 報表可以由其 URL。

## <a name="structural-metadata"></a>結構化的中繼資料

結構化的中繼資料是從一個描述的資料資產結構的資料來源中擷取的中繼資料。 包括資產位置、 其物件名稱與類型，以及其他特定類型的特性。 例如，結構化的中繼資料的資料表及檢視包含名稱和物件的資料行的資料類型。

## <a name="descriptive-metadata"></a>描述性的中繼資料

描述性的中繼資料是描述的資料資產的用途中繼資料。 目錄使用者使用 Azure 資料目錄] 入口網站，通常是新增描述性的中繼資料，但它可以也擷取資料來源的註冊期間。 例如，Azure 資料目錄註冊工具會擷取描述從 SQL Server Analysis Services 和 SQL Server Reporting Services 中的 [描述] 屬性和[ms_description 延伸的內容](https://technet.microsoft.com/library/ms190243.aspx)在 SQL Server 資料庫中，如果這些屬性有填入值。

## <a name="request-access"></a>要求存取權

資料資產的描述性中繼資料可以包含如何要求存取資料來源的資料資產的詳細資訊。 這項資訊會顯示的資料資產位置，並可以包含一或多個下列選項︰

- 使用者或小組負責授與存取資料來源的電子郵件地址。
- 說明程序，使用者必須遵循來存取資料來源的 URL。
- 身分識別和存取管理工具 （例如 Microsoft 身分識別管理員），可以用來存取資料來源的 URL。
- 說明如何使用者可以存取資料來源空閒文字項目。

## <a name="preview"></a>預覽

Azure 資料目錄] 中的預覽是 20 個記錄可以在註冊期間，從資料來源擷取及儲存在目錄中的資料資產中繼資料的快照。 預覽可以協助探索資料資產的使用者，進一步瞭解其功能和目的。 換句話說，看到範例資料可更有價值超過看到資料行名稱與資料類型。
預覽的資料表及檢視，只支援，必須明確選取使用者註冊期間。

## <a name="data-profile"></a>資料設定檔

Azure 資料目錄] 中的資料設定檔是已註冊的資料資產，可從資料來源擷取在註冊期間，並在目錄中的資料資產中繼資料儲存資料表層級和資料行層級中繼資料的快照。 資料設定檔可以協助探索資料資產的使用者，進一步瞭解其功能和目的。 類似於預覽，資料設定檔必須明確選取使用者註冊期間。

> [AZURE.NOTE] 擷取資料設定檔時，可以大型資料表及檢視的成本作業，而且可能會大幅增加登錄的資料來源所需的時間。

## <a name="user-perspective"></a>使用者檢視方塊

Azure 資料目錄] 中，任何使用者可以的已註冊的資料資產提供描述性的中繼資料。 每個使用者會有不同的觀點來看，在資料和使用。 例如，系統管理員負責伺服器可能會提供其服務等級協定 (SLA) 或備份 windows; 的詳細資料資料監管可能會提供給您的商務用的文件的連結程序資料支援;而分析師提供的條款與其他分析師最相關且可以是最有價值需要探索並了解資料給這些使用者的描述。

每個這些檢視方塊原本就是相當重要，而每位使用者可以使用 Azure 資料目錄提供有意義的資訊，而所有使用者可以都使用這項資訊，了解資料和其目的。

## <a name="expert"></a>專家

為使用者已將其識別為最新的 「 專家 」 檢視方塊的資料資產的專家。 任何使用者可以將自己或其他使用者，新增為資產，或者向專家。 正在列為專家不具有某種 Azure 資料目錄;] 中的任何其他權限其允許使用者輕鬆地找到這些最有可能很有用，檢閱資產的描述性中繼資料時的檢視方塊。

## <a name="owner"></a>擁有者

擁有者是具有管理 Azure 資料目錄] 中的資料資產的其他權限的使用者。 使用者可以取得已註冊的資料資產的擁有權，並擁有者可以新增其他使用者共同擁有者。 如需詳細資訊請參閱[如何管理資料資產](data-catalog-how-to-manage.md)  
> [AZURE.NOTE] 擁有權] 和 [管理可只標準版的 Azure 資料目錄] 中。

## <a name="registration"></a>註冊

註冊是從資料來源擷取資料資產中繼資料，並將它複製到 Azure 資料目錄服務的動作。 可以將附註已註冊的資料資產，然後找到。

## <a name="see-also"></a>另請參閱

- [什麼是 Azure 資料目錄？](data-catalog-what-is-data-catalog.md) -本文提供 Azure 資料目錄服務，它所提供的值和支援的案例的概觀。

- [開始使用 Azure 資料目錄](data-catalog-get-started.md)的這篇文章提供說明如何使用資料來源探索 Azure 資料目錄的端對端教學課程。  
