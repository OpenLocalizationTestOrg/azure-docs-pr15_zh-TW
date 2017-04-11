<properties
   pageTitle="Azure 資料目錄的常見問題集 |Microsoft Azure"
   description="常見問題集的疑問 Azure 資料目錄，包括的資料來源探索、 註解，以及管理功能的詳細資訊。"
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
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure 資料目錄的常見問題集

這份文件與 Microsoft **Azure 資料目錄**服務相關的常見問題提供解答。

## <a name="q-what-is-azure-data-catalog"></a>問︰ 什麼是 Azure 資料目錄？

答︰ Microsoft Azure 資料目錄是探索的登錄的完全受管理的服務裝載於 Microsoft Azure 雲端，可作為系統和企業資料來源系統。 Azure 資料目錄提供探索、 瞭解以及使用資料來源的任何使用者 – 從分析師資料科學家開發人員 – 註冊，以啟用的功能。

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>問︰ 哪些客戶挑戰會 Azure 資料目錄解決嗎？

Azure 資料目錄位址允許使用者探索並瞭解企業資料來源的資料來源探索及 「 深資料 」 的挑戰。

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>問︰ 誰是 Azure 資料目錄的目標對象？

Azure 資料目錄功能提供技術與技術的使用者，包括︰

- 資料開發人員、 BI 及分析專業人員︰ 誰負責產生其他人使用的資料和分析內容
-   資料監管︰ 那些人有資料、 其代表的意義與適用於如何與哪的知識
- 資料消費者︰ 那些人必須能夠輕鬆地探索，了解並連線到資料所需的工作使用其所選擇的工具
- 中央 IT︰ 誰需要進行下的數百個資料來源的可探索的商務使用者，並且需要以移至使用資料的方式，以及由誰監督

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>問︰ 什麼是 Azure 資料目錄地區可用性？

Azure 資料目錄服務位於目前提供下列資料中心︰

- 西美制]。
- 設定適用於美國
- 西歐
- 北美歐洲
- 澳大利亞東亞
- 東南亞

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>問︰ 什麼是 Azure 資料目錄] 中的資料資產數目限制？

免費版的 Azure 資料目錄僅限於 5000 已註冊的資料資產。

標準版的 Azure 資料目錄支援多達 100000 已註冊的資料資產。

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>問︰ 什麼是支援的資料來源與資產類型？

請參閱[資料目錄 DSR](data-catalog-dsr.md)目前支援的資料來源清單。

## <a name="q-how-do-i-request-support-for-another-data-source"></a>問︰ 如何要求其他資料來源的支援？

能中[Azure 資料目錄論壇，在](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)提交要求及其他的意見反應。

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>問︰ 如何開始使用 Azure 資料目錄？

若要開始最好是以開始[使用資料目錄](data-catalog-get-started.md)的指示執行。 這篇文章的端對端概觀服務中的功能。

## <a name="q-how-do-i-register-my-data"></a>問︰ 如何註冊我的資料？

若要註冊 Azure 資料目錄] 中的資料，啟動 Azure 資料目錄註冊工具從 Azure 資料目錄入口網站的 「 發佈 」 區域。 Azure 資料目錄發佈應用程式中，使用您用來存取 Azure 資料目錄] 入口網站，相同的認證登入，然後選取資料來源與您想要註冊的特定資產。

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>問︰ 哪些內容已登錄的資料資產的擷取？

特定屬性會與資料來源不同資料來源，但一般 Azure 資料目錄發佈服務會擷取下列資訊︰

- 資產名稱
- 資產類型
- 資產描述
- 屬性/資料行名稱
- 屬性/資料行資料類型
- 屬性/資料行描述

> [AZURE.IMPORTANT] 註冊 Azure 資料目錄的資料資產不移動或複製到雲端的資料。 註冊從資料來源的資產會資產的中繼資料複製到 Azure，但資料仍會保留在現有的資料來源位置。 此規則唯一的例外是如果使用者選擇上傳預覽記錄或資料設定檔時註冊資產。 當包括預覽，進位到 20 的記錄會以複製從每個資產，而另存為快照 Azure 資料目錄] 中的儲存。 當包含資料的設定檔，將計算彙總的資訊 （例如資料表、 每一個資料行，百分比 null 值和最小值、 最大值和平均的值的資料行的大小），並包含在目錄中儲存的中繼資料。

<br/>

> [AZURE.NOTE] 資料來源，例如 SQL Server Analysis Services 的第一類的 [**描述**] 屬性，Azure 資料目錄發佈應用程式會擷取的屬性值。 SQL Server 關聯式資料庫，但卻沒有最高級的 [**描述**] 屬性，Azure 資料目錄發佈應用程式會從 ms_description 擴充屬性的物件和資料行擷取值。 如需詳細資訊，請參閱 TechNet[使用延伸內容資料庫物件](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)。

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>問︰ 如何應該花時間新註冊 Azure 資料目錄] 中顯示的資產？

使用 Azure 資料目錄註冊資產之後可能會有一段 5 到 10 秒 Azure 資料目錄入口網站中顯示。

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>問︰ 如何加上註解和我的註冊的資料資產的豐富的中繼資料？

提供的中繼資料已註冊的資產的最簡單的方法是選取 [Azure 資料目錄入口網站中的 [資產，然後輸入中繼資料值屬性] 窗格或結構描述] 窗格中選取的物件。

您也可以提供部分中繼資料，例如專家和標籤，請在註冊程序。 提供 Azure 資料目錄發佈服務中的值會套用到所有的資產所註冊的時間。 若要檢視最近註冊物件在入口網站中的其他註解，請選取 Azure 資料目錄發佈應用程式的最後一個畫面上的 [**檢視入口網站**] 按鈕。

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>問︰ 如何刪除我的註冊的資料物件？

您可以從 Azure 資料目錄中刪除物件，在入口網站中，選取該物件，然後按一下 [**刪除**] 按鈕。 這會從 Azure 資料目錄] 移除物件的中繼資料，但不是會影響實際基礎資料來源。

## <a name="q-what-is-an-expert"></a>問︰ 什麼是專家？

或者向專家是具有最新的觀點來看資料物件的相關的人員。 物件可以有多個專家。 或者向專家不需要 「 擁有者 」 的物件。專家就知道如何資料，應使用的人。

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>問︰ 如何共用資訊與 Azure 資料目錄小組如果我會遇到問題？

請使用提報問題的 Azure 資料目錄論壇、 共用的詳細資訊，並提出問題。 您可以在 http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409 找到論壇

##<a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>問︰ Azure 資料目錄運作與感興趣的這個其他資料來源？
我們正在積極 Azure 資料目錄以新增更多資料來源。 如果您想要請參閱支援的請建議它 （或如果已經建議語音您支援） 中的[Azure 資料目錄論壇，在](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)資料來源。

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>問︰ 如何 Azure 資料目錄相關 Power bi 資料目錄的 Office 365？

您可以想像 Azure 資料目錄的 [資料目錄的功用。 Azure 資料目錄提供為資料來源發行及探索類似的功能，但會著重於狀況提出更廣義的案例並不相依於 Office 365。 引進了 Azure 資料目錄會變成推出之後的兩個類別目錄會合併到單一服務。

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>問︰ 哪些權限的使用者需要哪些註冊 Azure 資料目錄資產？

執行 Azure 資料目錄註冊工具的使用者都必須允許讀取中繼資料來源的資料來源的權限。 如果使用者也可以選取要包含預覽，使用者必須也可以讀取所註冊的物件從資料中的權限。

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>問︰ 將 Azure 資料目錄可供以及內部部署？

Azure 資料目錄是可以使用內部部署與雲端資料來源的雲端服務進行混合式資料來源探索解決方案。 目前有無方案 Azure 資料目錄服務會執行內部部署的版本。

##<a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>問︰ 我們可以我們註冊資料來源擷取更多 / 更豐富的中繼資料？

我們正在積極展開 Azure 資料目錄的功能。 如果有其他您想要查看擷取資料來源的註冊期間的中繼資料，請使用建議它 （或投票，如果已經建議） 中[Azure 資料目錄論壇](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)。 在未來我們可讓協力廠商新增新的資料來源類型，透過 API 擴充功能。

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>問︰ 如何讓特定的人員可以探索這些限制已註冊的資料資產，可見的性？

A︰ 在 Azure 資料目錄中，選取的資料資產，然後按一下 [取得擁有權] 按鈕。 Azure 資料目錄] 中的資料資產的擁有者可以變更可見度設定，以允許目錄的所有使用者以探索擁有的資產，或限制特定使用者的可見性。

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>如何更新的資料資產，若要註冊問︰ 資料來源的變更，會反映在目錄中？

A︰ 若要更新目錄] 中已註冊的資料資產的中繼資料，只要重新註冊含有資產的資料來源。 資料來源中的任何變更，例如欄已加入或移除資料表或檢視，將會更新在目錄中，但維護使用者提供的任何註解。

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>問︰ 我回答的問題不以下-我該怎麼辦？

標題上透過[Azure 資料目錄論壇](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)。 那里問的問題] 會尋找他們的方式。
