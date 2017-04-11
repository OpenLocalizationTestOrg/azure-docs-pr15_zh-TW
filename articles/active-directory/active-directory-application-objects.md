<properties
pageTitle="Azure Active Directory 應用程式和服務主要物件 |Microsoft Azure"
description="討論的應用程式與服務 Azure Active Directory 中的本金物件之間的關係"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>應用程式和服務 Azure Active Directory 中的本金物件
閱讀關於 Azure Active Directory (AD) 」 應用程式]，並不一定清除完全什麼由作者參照至。 本文的目標是，使其更清楚，以定義 Azure AD 應用程式整合，註冊和同意[多租用戶應用程式](active-directory-dev-glossary.md#multi-tenant-application)的範例的概念性和具體方面。

## <a name="overview"></a>概觀
Azure AD 應用程式是軟體的更廣泛比只一顆棋子。 是概念性的字詞，參照不只應用程式軟體，但其註冊 (又稱︰ 身分識別設定) 使用 Azure AD，讓它參與驗證與授權 「 交談 」 在執行階段。 根據預設，應用程式，可以在[用戶端](active-directory-dev-glossary.md#client-application)角色 （使用資源）、[資源伺服器](active-directory-dev-glossary.md#resource-server)角色 (用戶端的可以明顯 Api)，或甚至兩個函數。 交談通訊協定所定義之[OAuth 2.0 授權授與流程](active-directory-dev-glossary.md#authorization-grant)，以存取/保護資源的資料分別允許用戶端/資源的目標。 現在我們跳更深入的一個層級，請參閱如何 Azure AD 應用程式模型表示的應用程式內部。 

## <a name="application-registration"></a>應用程式註冊
當您註冊應用程式中[Azure 傳統入口網站][AZURE-Classic-Portal]，Azure AD 租用戶中建立兩個物件︰ 應用程式物件和服務的主體物件。

#### <a name="application-object"></a>應用程式物件
Azure AD 應用程式*定義*其一，而只應用程式物件，位於應用程式在哪裡註冊 Azure AD 租用戶中，稱為應用程式的 「 住家 」 租用戶。 應用程式物件提供身分識別相關應用程式的資訊，從其對應的服務主要的物件是在執行階段使用*衍生*的範本。 

您可以將為您的應用程式 （適用於使用過所有租用戶） 的*通用*表示應用程式與服務本金為 （適用於使用特定的租用戶） 的*本機*表示。 Azure AD 圖形[應用程式實體][AAD-Graph-App-Entity]定義的結構描述的應用程式物件。 應用程式物件因此有 1:1 關聯的軟體應用程式，與 1:*n*其對應*n*服務本金個物件之間的關係。

#### <a name="service-principal-object"></a>服務主體物件
服務主體物件定義的原則和權限的應用程式，並提供安全性原則存取在執行階段的資源時，代表應用程式的基礎。 Azure AD Graph [ServicePrincipal 實體][AAD-Graph-Sp-Entity]定義服務主體物件的結構描述。 

服務主體物件被必要的應用程式的使用方式的執行個體必須表示，啟用安全的存取權的使用者帳戶，從該租用戶的資源的每個租用戶中。 單一租用戶應用程式會有一個服務主要 （在其常用的租用戶）。 [Web 應用程式](active-directory-dev-glossary.md#web-client)的多租用戶也會有服務主要位置的系統管理員或從該租用戶的使用者同意，讓它存取他們的資源的每個租用戶中。 追蹤同意，會供未來的授權要求諮詢服務主體物件。 

> [AZURE.NOTE] 您對您的應用程式的物件，進行任何變更也會反映在其應用程式的主租用戶中只 （它在哪裡註冊的租用戶） 的服務主體物件。 多租用戶的應用程式，變更應用程式物件才會反映在任何消費者租用戶的服務主體物件中移除 access 消費者租用戶，並再次授與存取權。

## <a name="example"></a>範例
下圖顯示應用程式的應用程式物件和對應之間的關係服務範例多租用戶的應用程式，稱為**HR 應用程式**的內容中的本金物件。 在這個案例中有三個 Azure AD 租用戶︰ 

- **Adatum**開發**HR 應用程式**的公司所使用的租用戶
- **Contoso** -Contoso 組織，也就是**HR 應用程式**的消費者所使用的租用戶
- **Fabrikam** Fabrikam 組織，其中也會耗費**HR 應用程式**所使用的租用戶

![應用程式物件與服務的主體物件之間的關聯](./media/active-directory-application-objects/application-objects-relationship.png)

在上一個圖表中，步驟 1 是應用程式的主租用戶中建立的應用程式及服務的主體物件的程序。

在 [步驟 2 Contoso 及 Fabrikam 的系統管理員完成同意，服務主體物件建立其公司 Azure AD 租用戶中並指派管理員授與的權限。 也請注意，HR 應用程式可能會設定/允許同意的個別使用的使用者。

步驟 3 中，HR 應用程式 （Contoso 和 Fabrikam） 每個消費者租用戶會有自己的服務主體物件。 每個表示在執行階段，由權限的應用程式的執行個體使用同意個別的系統管理員。

## <a name="next-steps"></a>後續步驟
可透過 Azure AD 圖形 API，存取應用程式的應用程式的物件，表示其 OData[應用程式實體][AAD-Graph-App-Entity]

可透過 Azure AD 圖形 API，存取應用程式的服務主體物件，表示其 OData [ServicePrincipal 實體][AAD-Graph-Sp-Entity]



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com