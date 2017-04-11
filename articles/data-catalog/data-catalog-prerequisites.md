<properties
   pageTitle="Azure 資料目錄先決條件 |Microsoft Azure"
   description="Azure 資料目錄的先決條件-您要開始使用 Azure 資料目錄。"
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

# <a name="azure-data-catalog-prerequisites"></a>Azure 資料目錄的先決條件

## <a name="what-do-i-need-to-get-started-with-azure-data-catalog"></a>我需要什麼快速入門 Azure 資料目錄？

有您要負責設定**Azure 資料目錄**之前的一些事項。 別擔心-不會花費長 ！

## <a name="azure-subscription"></a>Azure 訂閱
若要設定 Azure 資料目錄，您必須是擁有者或 Azure 訂閱的共同擁有者。

Azure 訂閱可協助您組織雲端服務資源，例如 Azure 資料目錄的存取權。 也有助於您控制資源使用狀況報告方式付款，並支付。 每個訂閱可讓不同帳單與付款設定，您可以安裝不同的訂閱和部門、 project、 地區的 office，等不同的方案。 每個雲端服務屬於訂閱，而您必須具備設定 Azure 資料目錄之前先訂閱。 若要深入瞭解，請參閱[管理帳戶、 訂閱及系統管理角色](../active-directory/active-directory-assign-admin-roles.md)。

## <a name="azure-active-directory"></a>Azure Active Directory
若要設定 Azure 資料目錄，您必須登入的 Azure Active Directory 使用者帳戶。

Azure Active Directory (Azure AD) 讓您輕鬆管理身分識別與存取權，同時在內部部署與雲端企業的。 使用者可以使用單一的公司或學校帳戶的單一登入至任何雲端和內部部署 web 應用程式。 Azure 資料目錄使用 Azure AD 驗證登入。 若要深入瞭解，請參閱[什麼是 Azure Active Directory](../active-directory/active-directory-whatis.md)。

> [AZURE.NOTE] [Azure 入口網站](http://portal.azure.com/)可讓使用者登入使用個人 Microsoft 帳戶或 Azure Active Directory 工作或學校帳戶。 若要設定 Azure 資料目錄使用 Azure 入口網站，或使用[資料目錄入口網站](http://www.azuredatacatalog.com)您必須登入的 Azure Active Directory 帳戶，而不是以個人帳戶。

## <a name="active-directory-policy-configuration"></a>Active Directory 原則設定

在某些情況下，使用者可能會遇到的情況下，他們可以登入 Azure 資料目錄入口網站，但它們，嘗試登入所遇到錯誤訊息，使其無法登入的資料來源註冊工具。 僅與公司網路，或只在公司網路以外的使用者連線時可能會發生時，可能會發生此問題的行為。

資料來源註冊工具使用表單驗證驗證 Active Directory 對使用者的登入。 成功的登入表單驗證中必須啟用全域驗證原則 Active Directory 系統管理員。

全域驗證原則可讓內部和外部網路的連線，分別啟用的驗證方法，如下所示。 如果表單驗證不會啟用的使用者連線網路時，可能會發生登入錯誤。

 ![Active Directory 全域驗證原則](./media/data-catalog-prerequisites/global-auth-policy.png)

如需詳細資訊，請參閱[設定驗證原則](https://technet.microsoft.com/library/dn486781.aspx)。
