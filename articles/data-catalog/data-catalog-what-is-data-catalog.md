<properties
   pageTitle="什麼是 Azure 資料目錄？ |Microsoft Azure"
   description="本文提供 Microsoft Azure 資料目錄，包括其功能和設計來解決問題的概觀。 資料目錄提供探索、 瞭解以及使用資料來源的任何使用者 – 從分析師資料科學家開發人員 – 註冊，以啟用的功能。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="what-is-azure-data-catalog"></a>什麼是 Azure 資料目錄？

Azure 資料目錄是完全受管理的雲端服務，讓使用者能探索所需的資料來源，並了解這些尋找時協助組織取得更多的值從現有的投資的資料來源。 資料目錄提供了解，任何使用者 – 從開發人員 – 探索、 資料科學家分析師啟用的功能，與使用資料來源。 資料目錄包含 crowdsourcing 模型的中繼資料] 及 [註釋]，並允許所有使用者參與知識來建立的社群和文化特性的資料。

## <a name="discovery-challenges-for-data-consumers"></a>探索資料消費者用的挑戰

一般而言，探索企業資料來源已根據成員發揮善意知識庫有機程序。 這樣的挑戰許多公司想要從其資訊資產取得的大部分的值。

-   使用者並不知道他們進攻為另一個程序; 除非，存在於資料來源不有任何資料來源在哪裡註冊的中央位置。
-   除非使用者知道資料來源的位置，他無法連線至資料使用的用戶端應用程式。資料消耗體驗要求使用者知道的連接字串或路徑。
-   除非使用者知道的資料來源的文件的位置，他無法瞭解用途的資料。資料來源文件在不同的位置，並不同體驗到消耗。
-   如果使用者有問題的相關資訊資產，他必須找出專家或小組負責資料，並加入這些專家離線。沒有資料和其使用專家檢視方塊與明確的連線。
-  除非使用者了解要求存取資料來源的程序，探索資料來源與文件仍不會啟用他存取他所需要的資料。

## <a name="discovery-challenges-for-data-producers"></a>探索挑戰的資料生產者

當資料消費者面對這些挑戰時，使用者負責產生和維護的資訊資產面對自己的挑戰。

-   註解使用描述性的中繼資料的資料來源，通常會遺失的項目。用戶端應用程式通常會忽略儲存資料來源中的描述。
-   建立資料來源的文件，通常會遺失的項目。保留與資料來源的同步處理的文件進行中的責任，而通常觀察得到為最新的使用者仍缺乏信任的文件中。
- 限制存取資料來源，並確保資料消費者知道如何要求存取是進行中的挑戰。

建立和維護資料來源文件是複雜且費時。 讓所有人都能使用的資料來源的現成可用的文件的挑戰，通常會更是。

結合時，這些挑戰會呈現的有效障礙公司要鼓勵升階的使用和瞭解企業資料的對象。

## <a name="azure-data-catalog-can-help"></a>Azure 資料目錄可協助

資料目錄的設計，以解決這些問題並啟用企業從其現有的資訊資產取得大部分的值。 資料目錄可協助藉由資料來源易於搜尋，且容易理解的使用者需要所管理的資料。

資料目錄提供的雲端的服務的資料來源登錄。 資料仍會保留在現有的位置，但中繼資料的複本會新增至資料目錄，以及資料來源位置的參考。 此中繼資料也編製索引，讓每個資料來源透過搜尋，輕鬆地探索並探索它的使用者容易理解。

當已註冊的資料來源時，它的中繼資料可以然後會 enriched，執行註冊的使用者或企業中其他使用者。 任何使用者可以加上註釋的資料來源所提供的描述、 標記或其他中繼資料，例如文件和要求資料來源存取的程序。 此描述性的中繼資料補充結構中繼資料 （例如資料行名稱與資料類型） 登錄的資料來源的資料。

探索和了解資料來源與使用已註冊來源的主要目的。 企業使用者需要資料的努力 （這可能是商務智慧功能、 應用程式開發、 資料科學或任何其他任務需要正確的資料的位置） 時，可以使用資料目錄探索體驗快速尋找符合需求的資料，瞭解評估其適合用途，並使用這些資料所選擇的其工具中開啟資料來源的資料。 同時，資料目錄 」 可讓使用者目錄，由標記、 文件，或註解的資料來源，已註冊，以及註冊新的資料來源的可以然後會發現，了解並耗用的目錄使用者社群參與。

![資料目錄功能](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="get-started-with-data-catalog"></a>開始使用資料目錄

若要開始使用資料目錄現今，請造訪[www.azuredatacatalog.com](https://www.azuredatacatalog.com)。

取得入門的指南有[以下](data-catalog-get-started.md)。

## <a name="learn-more-about-data-catalog"></a>深入瞭解資料目錄

若要深入瞭解資料目錄的功能，請參閱︰

* [如何註冊的資料來源](data-catalog-how-to-register.md)
* [探索資料來源的方式](data-catalog-how-to-discover.md)
* [若要加上註釋資料來源的方式](data-catalog-how-to-annotate.md)
* [如何將文件的資料來源](data-catalog-how-to-documentation.md)
* [如何連線至資料來源](data-catalog-how-to-connect.md)
* [如何使用大型資料](data-catalog-how-to-big-data.md)
* [如何管理資料資產](data-catalog-how-to-manage.md)
* [如何設定商務用詞彙](data-catalog-how-to-business-glossary.md)
* [常見問題集](data-catalog-frequently-asked-questions.md)
