<properties
   pageTitle="邏輯應用程式內部部署資料閘道器連線 |Microsoft Azure"
   description="如何建立連線到內部部署資料閘道器，從邏輯應用程式的相關資訊。"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>連線到內部部署資料閘道邏輯應用程式

支援的邏輯應用程式連接器可讓您設定的內部部署資料閘道器透過存取內部部署資料的連線。  下列步驟會引導您完成安裝與設定的內部部署資料的閘道器與邏輯應用程式搭配使用。

## <a name="prerequisites"></a>必要條件

* 必須使用公司或學校電子郵件地址，與您的帳戶 （基礎的 Azure Active Directory 帳戶） 的內部部署資料閘道 Azure 中
    * 如果您使用的 Microsoft 帳戶 (例如@outlook.com,@live.com)您可用來建立公司或學校電子郵件地址，按照[這些步驟](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)Azure 帳戶

> [AZURE.WARNING] 沒有限制目前的內部部署使用已註冊 Power BI 的帳戶時，才會完成閘道器安裝。  同時請註冊任何帳戶都使用 「 Power BI 免費 」 順利完成安裝作業。

* 必須在內部部署資料閘道器[在本機電腦上安裝](app-service-logic-gateway-install.md)。
* 閘道器必須有已不快 （[宣告種情況建立步驟 2] 下方](#2-create-an-azure-on-premises-data-gateway-resource)） 的另一個 Azure 內部部署資料閘道器-安裝只會有一個閘道資源相關聯。

## <a name="installing-and-configuring-the-connection"></a>安裝及設定連線

### <a name="1-install-the-on-premises-data-gateway"></a>1.安裝內部部署資料閘道器

[本文中](app-service-logic-gateway-install.md)找安裝內部部署資料閘道器的資訊。  必須先在內部部署電腦上安裝閘道器，您可以繼續使用其餘的步驟。

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2.建立 Azure 內部部署資料閘道器資源

安裝後，您必須建立 Azure 訂閱與內部部署資料閘道器的關聯。

1. 登入 Azure 使用相同的工作或學校電子郵件地址所使用的閘道器安裝期間
1. 按一下 [**新增**資源] 按鈕
1. 搜尋並選取 [**內部部署資料閘道器**
1. 完成您的帳戶-包括選取適當的**安裝名稱**與建立關聯的閘道器的資訊

    ![內部部署資料閘道器的連線][1]
1. 按一下 [**建立**] 按鈕，以建立資源

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3.在設計工具中建立邏輯應用程式連線

現在，您 Azure 的訂閱是與相關聯的內部部署資料閘道執行個體，您可以建立的連線，從邏輯應用程式中。

1. 開啟邏輯應用程式並選擇支援 （如本文撰寫時，SQL Server） 的內部部署連線的連接器
1. 選取 [**透過內部部署資料閘道器的連線**] 核取方塊

    ![邏輯應用程式的設計工具閘道器建立][2]
1. 選取要將連接並完成所需的任何其他連線資訊的**閘道器**
1. 按一下 [**建立**]，建立連線

使用邏輯應用程式中應現在會成功設定連線。  

## <a name="next-steps"></a>後續步驟
- [常見的範例與邏輯應用程式的案例](app-service-logic-examples-and-scenarios.md)
- [企業整合功能](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG