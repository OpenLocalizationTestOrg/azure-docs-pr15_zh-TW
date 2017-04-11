<properties
    pageTitle="角色型存取控制 |Microsoft Azure"
    description="快速入門中存取管理 Azure 入口網站中的 Azure 的角色型存取控制。 使用角色指派指定目錄中的權限。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="get-started-with-access-management-in-the-azure-portal"></a>開始使用 access Azure 入口網站中的管理

安全性導向公司應該著重於提供員工所需的確切的權限。 太多的權限公開攻擊的帳戶。 太少的權限表示員工無法取得有效率地完成工作。 Azure 角色型存取控制 (RBAC) 可協助您解決這個問題，提供 Azure 精密存取管理。

使用 RBAC，您可以將您的小組中的職責，並只量的存取權授與執行工作所需的使用者。 而非授與每個人做好不受限制的權限，您可以在 Azure 訂閱或資源，讓特定的動作。 例如，使用 RBAC，讓一員工管理虛擬機器中的訂閱，而另一個可以管理相同的訂閱中的 SQL 資料庫。

## <a name="basics-of-access-management-in-azure"></a>管理存取 Azure 中的基本概念
每個 Azure 的訂閱是一個 Azure Active Directory (AD) 目錄與相關聯。 使用者、 群組和從該目錄的應用程式可以管理 Azure 訂閱中的資源。 指派這些使用 Azure 入口網站、 Azure 的命令列工具和 Azure 管理 Api 的存取權限。

授與存取權，以將適當的 RBAC 角色指派給使用者、 群組和在特定範圍的應用程式。 訂閱、 [資源] 群組中或單一資源，可以是範圍的角色指派。 在父範圍指派角色也會授與子系其包含的存取權。 例如，具有資源群組的存取權的使用者可以管理其包含網站、 虛擬機器等子網路的所有資源。

![關聯性之間 Azure Active Directory 元素的圖表](./media/role-based-access-control-what-is/rbac_aad.png)

您指派的 RBAC 角色會指出的資源在該範圍內管理使用者、 群組或應用程式。

## <a name="built-in-roles"></a>內建的角色
Azure RBAC 具有套用至所有的資源類型的三個基本功能︰

- **擁有完整存取所有的資源，包括委派給其他人的存取權限的權限。**
- **參與者**可以建立及管理 Azure 資源的所有類型，但無法授與其他人的存取權。
- **閱讀程式**可以檢視現有的 Azure 資源。

Azure RBAC 角色的其餘部分允許特定的 Azure 資源管理。 例如的虛擬機器參與者角色可讓使用者建立及管理虛擬機器。 它不會讓他們存取虛擬網路或虛擬機器連線到子網路。

[RBAC 內建的角色](role-based-access-built-in-roles.md)會列出可用 Azure 中的角色。 它會指定作業以及每個內建的角色授與使用者的範圍。 如果您正在尋找定義自己的角色進一步的控制權，請參閱如何建立[自訂 Azure RBAC 角色](role-based-access-control-custom-roles.md)。

## <a name="resource-hierarchy-and-access-inheritance"></a>資源階層與存取權的繼承
- 只有一個目錄所屬 Azure 中的每個**訂閱**。
- 只有一個訂閱所屬的每個**資源群組**。
- 每個**資源**所屬只有一個資源群組。

在子範圍繼承您在上層範圍授與存取權。 例如︰

- 您在訂閱的範圍內的 Azure AD 群組指派讀取者角色。 該群組的成員可以在訂閱中檢視每個資源群組和資源。
- 您可以指派的參與者角色資源群組範圍的應用程式。 它可以管理中的 [資源] 群組中，但不是其他訂閱中的資源群組的所有類型的資源。

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC 與傳統的訂閱的管理員
傳統訂閱系統管理員和共同管理員擁有完整存取權 Azure 訂閱。 他們可以管理 Azure 資源管理員 Api，或[Azure 傳統的入口網站](https://manage.windowsazure.com)和 Azure 傳統部署模型使用[Azure 入口網站](https://portal.azure.com)的資源。 在 RBAC 模型中，傳統的系統管理員會指派在訂閱的範圍內的擁有者角色。

Azure 入口網站和新的 Azure 資源管理員 Api 支援 Azure RBAC。 使用者和 RBAC 角色指派的應用程式無法使用標準管理入口網站和 Azure 傳統部署模型。

## <a name="authorization-for-management-vs-data-operations"></a>管理與資料作業的授權
Azure RBAC 只會在 Azure 入口網站和 Azure 資源管理員 Api 支援 Azure 資源管理作業。 其無法授權 Azure 資源的資料層級作業。 例如，您可以授權的某人管理儲存空間帳戶，但不是以二進位大型物件或資料表儲存體帳戶內無法。 同樣的是 SQL 資料庫進行管理，但不是內的資料表。

## <a name="next-steps"></a>後續步驟
- 開始使用[角色型存取控制 Azure 入口網站中](role-based-access-control-configure.md)。
- 請參閱[RBAC 內建的角色](role-based-access-built-in-roles.md)
- 定義自己的[自訂 Azure RBAC 角色](role-based-access-control-custom-roles.md)
