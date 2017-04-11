<properties
    pageTitle="Azure 應用程式服務及對現有 Azure 服務的影響"
    description="說明如何新增 Azure 應用程式服務和其功能影響 Azure 中現有的服務。"
    services="app-service"
    documentationCenter=""
    authors="yochay"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="yochayk"/>


# <a name="azure-app-service-and-existing-azure-services"></a>Azure 應用程式服務與現有的 Azure 服務

本文概述現有 Azure 服務所做的變更，變更才有數個 Azure 服務運用到[Azure 應用程式服務](https://azure.microsoft.com/services/app-service/)，新的整合服務的一部分。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>概觀

[Azure 應用程式服務](https://azure.microsoft.com/services/app-service/)是新且唯一的雲端服務，讓開發人員建立 web 及任何平台和任何裝置的行動應用程式。 應用程式服務是設計用來簡化重複的程式碼函數與企業版和 SaaS 系統整合，同時符合您需求的安全性、 可靠性與延展性自動化商務程序的整合式的解決方案。

應用程式服務整合下列現有 Azure 服務-[網站](https://azure.microsoft.com/services/websites/)、[行動電話服務](https://azure.microsoft.com/services/mobile-services/)，以及[Biztalk](https://azure.microsoft.com/services/biztalk-services/)成為單一合併服務時新增功能強大的新功能。  應用程式服務可讓您裝載下列應用程式類型︰

-   Web 應用程式
-   行動應用程式
-   API 應用程式
-   邏輯應用程式

下表說明如何現有 Azure 服務地圖服務應用程式以及內使用的應用程式類型。

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">現有的 Azure 服務</th>
<th align="left", style="width:10%">Azure 應用程式服務</th>
<th align="left", style="width:80%">變更的項目</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure 網站</td>
<td align="left">Web 應用程式</td>
<td align="left"><li>Azure 網站應用程式服務只限嚴格 Web 應用程式中變更網站的名稱。
<p><li>您現有的所有執行個體的網站現在已在應用程式服務的 Web 應用程式。</p>
<p><li>您可以存取您現有的網站，透過<a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure 入口網站</a>，您會在此找到所有您現有的網站在<em>Web 應用程式</em>。</p>
<p><li><em>網頁裝載規劃</em>現在<em>應用程式服務方案</em>。 <em>應用程式服務方案</em>，可以裝載任何應用程式的應用程式服務類型，例如網頁、 行動電話、 邏輯或 API 應用程式。</p>
<p><li>Azure 應用程式服務 Web 應用程式的一般是顯示狀態。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">深入瞭解 Web 應用程式</a>。</p></td>
</tr>
<tr class="even">
<td align="left">Azure 行動服務</td>
<td align="left">行動應用程式</td>
<td align="left"><p><li>行動電話服務繼續會提供獨立版服務，並停留完全受支援。</p>
<p><li>行動應用程式是在應用程式服務整合的行動電話服務等功能的所有應用程式類型。</p>
<p><li>您可以輕鬆<a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">從行動應用程式的行動電話服務</a>移轉。</p>
<p><li>應用程式服務，一部分 Mobile 應用程式取得超出行動服務的新功能，例如整合內部部署與 SaaS 系統臨時位置、 WebJobs、 更有效地縮放比例選項]，及其他功能。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">深入瞭解 Mobile 應用程式</a>。</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API 應用程式</td>
<td align="left">
<p><li>API 應用程式是應用程式服務，讓您輕鬆地建立及使用 Api 在雲端中的新應用程式類型。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">深入瞭解 API 應用程式</a>。</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">邏輯應用程式</td>
<td align="left">
<p><li>邏輯應用程式是應用程式服務，讓您輕鬆地自動化商務程序中的新應用程式類型。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">深入瞭解邏輯應用程式</a>。</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk 服務</td>
<td align="left">BizTalk API 應用程式</td>
<td align="left">
<li><p>Biztalk 繼續會提供獨立版服務，並停留完全受支援。</p>
<li><p>BizTalk 服務的所有功能都整合應用程式服務為 API 應用程式執行企業應用程式整合及 B2B 整合案例的任何應用程式類型服務應用程式中啟用的使用者</p>
<li><p>使用邏輯應用程式現在可以自動化商務程序建立工作流程中使用視覺化設計體驗</p></td>
</tr>
</tbody>
</table>

若要深入瞭解，請造訪[應用程式服務文件](https://azure.microsoft.com/documentation/services/app-service/)。
