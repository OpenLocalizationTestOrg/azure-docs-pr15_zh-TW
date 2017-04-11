<properties
    pageTitle="如何將多個 Azure 區域部署 Azure API 管理服務執行個體"
    description="瞭解如何將多個 Azure 區域部署 Azure API 管理服務執行個體。" 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>如何將多個 Azure 區域部署 Azure API 管理服務執行個體

API 管理支援多區域部署讓 API 發行者的單一 API 管理服務分散所需的 Azure 任何的區域數字。 這可協助降低延遲觀察得到的散布 API 消費者和一個區域離線，也可以改善服務可用性的要求。 

一開始建立 API 管理服務時，它包含單一[單位][]，並在單一的 Azure 區域，而指定為主要區域。 透過 Azure 傳統入口網站，可以輕鬆地新增其他區域。 API 管理閘道器伺服器已部署至每個區域，並且通話流量將會路由至最接近的閘道器。 如果地區離線，流量是自動重新導向至下一個最接近的閘道器。 

> [AZURE.IMPORTANT] 多區域部署只適用於**[進階版][]**層。

## <a name="add-region"> </a>API 管理服務執行個體部署至新的區域

若要開始，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。 會帶您到 API 管理 publisher 入口網站。

![Publisher 入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

瀏覽至 [**縮放比例**] 索引標籤中 Azure 傳統入口網站 API 管理的服務執行個體。 

![縮放比例] 索引標籤][api-management-scale-service]

部署至新的區域，按一下下拉式清單下方的主要區域上，選擇清單中的區域。

![新增地區][api-management-add-region]

選取地區後，選擇新的區域，從 [單位] 下拉式清單的單位數量。

![指定單位][api-management-select-units]

一旦設定所要的區域和單位，請按一下 [**儲存**]。

## <a name="remove-region"> </a>API 管理服務執行個體刪除區域

若要移除的區域中的 API 管理服務執行個體，瀏覽至 [**縮放比例**] 索引標籤中 Azure 傳統入口網站 API 管理的服務執行個體。 

![縮放比例] 索引標籤][api-management-scale-service]

按一下 [若要移除所要的區域右側的**X** ]。  

![移除的區域][api-management-remove-region]

當您要的區域將會移除時，請按一下 [**儲存**]。


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance
[開始使用 Azure API 管理]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[單位]: http://azure.microsoft.com/pricing/details/api-management/
[進階版]: http://azure.microsoft.com/pricing/details/api-management/

