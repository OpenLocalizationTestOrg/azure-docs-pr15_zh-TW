<properties
   pageTitle="Microsoft Power BI 內嵌-連線到資料來源"
   description="Power BI 內嵌、 連線至資料來源"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="connect-to-a-data-source"></a>連線至資料來源

與**Power BI 內嵌**，您可以內嵌到您自己的應用程式的報表。 當您將您的應用程式中內嵌 Power BI 報表時，報表連線至基礎資料**匯入**的資料，或**直接連接**到資料來源使用**不同**的複本。

以下是使用**匯入**和**DirectQuery**之間的差異。

|匯入 | 不同
|---|---
|資料表資料行*和資料*匯入或複製到報表的資料集。 若要查看基礎資料的變更，您必須重新整理]，或匯入，請完成、 目前資料集一次。|*資料表和資料行*，僅匯入或複製到報表的資料集。 您隨時都可以檢視最新的資料。
使用 Power BI 內嵌，您可以使用不同的雲端資料來源但不是內部部署資料來源這一次。

## <a name="benefits-of-using-directquery"></a>使用不同的優點

使用**不同**時，有兩個主要的好處︰

   -    **不同**可讓您建立視覺效果在大型資料集，其中，否則會可行，第一個匯入的所有資料。
   -    基礎資料變更可能需要重新整理的資料，並針對某些報表，以顯示目前的資料需要可以要求大型的資料傳輸，進行重新匯入的資料不可行。 相反地， **DirectQuery**報表一律使用目前的資料。

## <a name="limitations-of-directquery"></a>不同的限制

   有使用**不同**的一些限制︰

   -    所有資料表必須都來自單一資料庫。
   -    過於複雜查詢時，會發生錯誤。 若要修正錯誤，因此很複雜，必須重構查詢。 如果 quuery 必須複雜，您必須匯入資料︰ 除了使用**不同**。
   -    關聯性篩選僅限於單一的方向，而不是兩個方向。
   -    您無法變更資料行的資料類型。
   -    根據預設，限制會放在量值中允許的 DAX 運算式。 請參閱[DirectQuery 和量值](#measures)。

<a name="measures"/>
## <a name="directquery-and-measures"></a>不同和量值

若要確保查詢傳送至基礎資料來源具有可接受的效能，限制所加上量值。 何時使用**Power BI Desktop**，進階使用者可以選擇以選擇略過這項限制**檔案 > 選項和設定 > 選項**。 在 [**選項**] 對話方塊中選擇**不同**，然後選取 [**允許不受限制的量值在以 DirectQuery 模式**] 選項。 選取該選項，就能使用的是有效的量值的任何 DAX 運算式。 使用者必須知道;不過，某些運算式的執行很好匯入資料時可能會導致非常緩慢查詢時在以**DirectQuery**模式的後端來源。 

## <a name="see-also"></a>另請參閱
- [開始使用 Microsoft Power BI 內嵌](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
