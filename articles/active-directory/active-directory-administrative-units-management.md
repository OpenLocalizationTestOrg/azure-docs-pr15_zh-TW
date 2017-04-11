<properties
   pageTitle="Azure Active Directory 中的系統管理單位管理"
   description="使用微調委派的 Azure Active Directory 中的權限管理的單位"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Azure AD-公用預覽中的系統管理單位管理

本文將說明系統管理單位 – 新的 Azure Active Directory 容器，可以使用委派系統管理權限透過使用者和使用者的子集套用原則的子集合的資源。 Azure Active Directory 系統管理單位啟用中央的系統管理員委派地區的系統管理員的權限或細微層級設定原則。

這是在組織中以獨立的間隔數，例如大型大學組成許多自主學校 （學校商業、 工程學校等等） 會從彼此獨立的實用。 這類部門會有自己 IT 系統管理員控制存取權與管理使用者，特別是針對其除法設定原則。 中央的系統管理員想来能夠授與這些分區在他們的特定區域中的使用者的管理員權限。 更明確地說，使用此範例中，中央的系統管理員可以、 舉例來說，建立系統管理單位為特定的學校 （商務學校） 並填入只有商務學校的使用者。 然後中央的系統管理員可以將商務學校 IT 人員新增至範圍的角色，也就是說，授與商務學校的系統管理員權限的 IT 人員僅透過商務學校的系統管理單位。

> [AZURE.IMPORTANT]
> 您可以建立並啟用 Azure Active Directory 進階版時，才使用系統管理員的單位。 如需詳細資訊，請參閱[快速入門 Azure AD 進階版](active-directory-get-started-premium.md)。

從中央系統管理員的觀點來看，管理單位會為目錄物件，您可以建立及填入資源。 **在這個版本中，這些資源可以是只有使用者。** 建立並填入之後，系統管理單位可以用於當做範圍限制只在資源管理單元中所包含的授與權限。

## <a name="managing-administrative-units"></a>管理系統管理單位

在這個預覽版本中，您可以建立並管理系統管理單位使用 Azure Active Directory 模組的 Windows PowerShell cmdlet。

如需詳細資訊的軟體需求及安裝的 Azure AD 模組，以及管理系統管理單位，包括語法、 參數描述與範例，Azure AD 模組 cmdlet 的詳細資訊請參閱[管理使用 Windows PowerShell 的 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。


## <a name="next-steps"></a>後續步驟
[Azure Active Directory 版本](active-directory-editions.md)
