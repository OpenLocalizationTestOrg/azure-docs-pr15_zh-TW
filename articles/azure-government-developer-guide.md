<properties 
    pageTitle="Azure 政府開發人員指南" 
    description="此提供功能及指引的比較開發 Azure 政府版的應用程式" 
    services="" 
    cloud="gov"
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>


#  <a name="microsoft-azure-government-developer-guide"></a>Microsoft Azure 政府開發人員指南 

<p> Microsoft Azure 政府版是實際和 Microsoft Azure 網路隔離執行個體。  此開發人員指南將提供詳細資料的差異該應用程式開發人員和系統管理員必須互動，並使用 Azure 這些不同區域。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>本主題中


+ [概觀](#Overview)
+ [適用於開發人員指南](#Guidance)
+ [目前可在 Microsoft Azure 政府版中的功能](#Features)
+ [端點對應](#Endpoint)
+ [後續步驟](#next)


## <a name="Overview"></a>概觀

Microsoft Azure 政府版是處理安全性和法規遵循的需求美國聯邦單位，州的本機政府和其解決方案提供者的 Microsoft Azure 服務的個別執行個體。 Azure 政府版提供實體網路隔離適用於美國政府部署，並提供過濾的美國人員。 

Microsoft 提供的工具來建立並部署 Microsoft 的全域 Microsoft Azure 服務 （「 全域服務 」） 與 Microsoft Azure 政府版服務的雲端應用程式。

建立及部署 Azure 政府版服務，而不是全域的服務，開發人員的應用程式時必須知道這兩項服務的主要差異。  安裝及設定其程式設計環境，周圍的特別設定端點，撰寫應用程式，並部署為 Azure 政府服務。

這份文件中的資訊會摘要列出的差異，並補充[Azure 政府](http://www.azure.com/gov "Azure 政府")網站與[Microsoft Azure 技術文件庫]MSDN 上的(http://msdn.microsoft.com/cloud-app-development-msdn "MSDN")上提供的資訊。 正式的資訊也會有許多其他位置中例如 [Microsoft Azure 信任中心] (https://azure.microsoft.com/support/trust-center/ 「 Microsoft Azure 信任中心 」 /)， [Azure 文件中心](https://azure.microsoft.com/documentation/)和 [Azure 部落格] (https://azure.microsoft.com/blog/ 」 Azure 部落格 「 /)。 

此內容適用於合作夥伴與開發人員部署到 Microsoft Azure 政府版。



## <a name="Guidance"></a>適用於開發人員指南
由於大部分的技術目前使用的內容假設全域的服務，而不是針對 Microsoft Azure 政府版的正在開發應用程式，請務必，以確保開發人員已經知道開發裝載 Azure 政府版中的應用程式的主要差異。

- 首先，有服務和功能差異，這表示全域服務中的特定區域中的某些功能可能無法 Azure 政府版中使用。

- 第二，都有提供 Azure 政府版的功能，有設定差異全域的服務。  因此，您應該檢閱您的程式碼範例、 設定和以確保您可以建立並執行 Azure 政府 Cloud Services 環境中的步驟。


## <a name="Features"></a>目前可在 Microsoft Azure 政府版中的功能
Azure 政府版目前具有在美國 GOV 稍後] 與 [適用於美國 GOV 維吉尼亞州的區域內的可用下列服務︰

- 虛擬機器
- 雲端服務
- 儲存空間
- Active Directory
- 排程器
- 虛擬網路
- SQL 資料庫
- Azure 檔案
- 媒體服務
- 流量管理員
- 服務匯流排
- StorSimple
- Redis 快取
- Azure 的備份
- 自動化
- ExpressRoute
- 等。

其他服務可用以及會持續新增更多服務。  最新的服務清單，請參閱可用的每個地區和其服務將會醒目提示[區域頁面](https://azure.microsoft.com/regions/#services)。  

目前，美國 GOV 稍後和美國 GOV 維吉尼亞州是支援 Azure 政府版的資料中心。  請參閱區域頁面上方目前的資料中心和可用的服務。

## <a name="Endpoint"></a>端點對應

請使用下表以引導您將公開的 Microsoft Azure 及 SQL 資料庫端點對應至 Azure 政府特定結束點。


服務類型|Azure 公用|Azure 政府版
---|---|---
管理入口網站|manage.windowsazure.com|manage.windowsazure.us
一般|*。 windows.net|*。 usgovcloudapi.net
核心|*。 core.windows.net|*。 core.usgovcloudapi.net
計算|*。 cloudapp.net|*。 usgovcloudapp.net
Blob 儲存體|*。 blob.core.windows.net|   *。 blob.core.usgovcloudapi.net
佇列中的儲存空間|*。 queue.core.windows.net|*。 queue.core.usgovcloudapi.net
資料表儲存體|*。 table.core.windows.net|*。 table.core.usgovcloudapi.net
服務管理|management.core.windows.net|management.core.usgovcloudapi.net
SQL 資料庫|*。 database.windows.net|*。 database.usgovcloudapi.net
ARM 負載平衡端點|https://management.windows.net|https://management.usgovcloudapi.net  

* 透過 Azure AD ARM 驗證，請參閱[驗證 Azure 資源管理員要求](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>後續步驟

如果您想要進一步瞭解更多和 Azure 政府請運用下面的連結的部分。

- **[註冊試用版](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[取得並存取 Azure 政府版](http://azure.com/gov)**

- **[Azure 政府版概觀](/azure-government-overview)**

- **[Azure 政府部落格](http://blogs.msdn.com/b/azuregov/)**

- **[Azure 法規遵循](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
