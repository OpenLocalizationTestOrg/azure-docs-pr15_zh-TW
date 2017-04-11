<properties
    pageTitle="Azure 堆疊 Web 應用程式概觀 |Microsoft Azure"
    description="Azure 堆疊的 Web Apps 概觀"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="azure-stack-web-apps-overview"></a>Azure 堆疊 Web 應用程式概觀
    
> [AZURE.NOTE] Azure 堆疊 TP1 部署僅適用於下列資訊。

Azure 堆疊 Web 應用程式是 Azure 應用程式服務內容給 Azure 堆疊的第一個項目。 Azure 堆疊 Web 應用程式安裝程式將會建立的每個五個必要的角色類型執行個體，也會建立在檔案伺服器。 雖然您可以新增多個執行個體的每一種角色類型，請記住，不太多空間，Vm 技術預覽 1 中。 Azure 堆疊 Web 應用程式目前的功能是主要 foundation 管理系統與主機 web 應用程式所需的功能。

![Azure 堆疊應用程式服務 Web 應用程式中 Azure 堆疊入口網站][1]

## <a name="limitations-of-the-technical-preview"></a>技術預覽的限制

有 Azure 堆疊應用程式服務 preview 版本不支援。 不要在這個預覽版本上放置生產負載。 也沒有升級 Azure 堆疊應用程式服務 preview 版本之間。 這些 preview 版本的主要目的是顯示我們提供，並取得意見反應。 

## <a name="what-is-an-app-service-plan"></a>什麼是應用程式服務方案？

Azure 堆疊 Web 應用程式資源提供者使用相同的程式碼 Azure Web 應用程式中的功能 Azure 應用程式服務使用。 如此一來，一些常見的概念是描述。 在 Web 應用程式，web 應用程式的價格容器稱為應用程式服務方案。 它會表示專用的虛擬機器用來存放您的應用程式的設定。 在指定的訂閱，您可以有多個應用程式服務方案。 這也是 Azure 堆疊 Web 應用程式中，則為 true。 

Azure 一些共用及專用的同事。 共用的工作支援高密度 multitenant web 應用程式裝載還有只有一組共用的同事。 只使用一個租用戶，有三種大小專用的伺服器︰ 小型、 中型和大型。 內部部署客戶的需求永遠無法使用這些字詞所述。 Azure 堆疊 Web 應用程式中資源提供者系統管理員可以定義要提供，讓他們擁有多個共用工作者集或組不同的專用的同事根據其唯一虛擬主機需求的工作者層。 使用這些工作者層定義，他們可以定義自己的價格 sku 皆可。

## <a name="portal-features"></a>入口網站的功能

也是如此與後端，Azure 堆疊 Web 應用程式會使用相同的使用者介面 Azure Web 應用程式使用。 某些功能已停用，且不尚未 Azure 堆疊的功能。 這是因為 Azure 特定的期望或服務的這些功能需要尚不提供 Azure 堆疊。 

## <a name="next-steps"></a>後續步驟

- [前 Azure 堆疊 Web Apps 快速入門](azure-stack-webapps-before-you-get-started.md)
- [安裝 Web 應用程式資源提供者](azure-stack-webapps-deploy.md)

您也可以嘗試其他[平台服務 (PaaS) 服務](azure-stack-tools-paas-services.md)，例如[SQL Server 資源提供者](azure-stack-sql-rp-deploy-short.md)和[MySQL 資源提供者](azure-stack-mysql-rp-deploy-short.md)。

<!--Image references-->
[1]: ./media/azure-stack-webapps-overview/AppService_Portal.png
