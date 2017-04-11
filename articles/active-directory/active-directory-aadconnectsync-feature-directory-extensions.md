<properties
   pageTitle="Azure AD Connect 同步處理︰ 目錄副檔名 |Microsoft Azure"
   description="本主題說明 Azure AD Connect 的目錄擴充功能。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同步處理︰ 目錄副檔名
目錄擴充功能可讓您將內部部署的 Active Directory 中使用您自己的屬性 Azure AD 架構。 此功能可讓您建立 LOB 應用程式，使用您繼續管理內部部署的屬性。 透過[Azure AD 圖表目錄副檔名](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)或[Microsoft Graph](https://graph.microsoft.io/)，可以使用這些屬性。 您可以看到可用分別使用[Azure AD 圖形總管](https://graphexplorer.cloudapp.net)] 及 [ [Microsoft Graph 檔案總管](https://graphexplorer2.azurewebsites.net/)的屬性。

目前沒有 Office 365 工作負載會使用這些屬性。

您設定您想要安裝精靈中的自訂設定路徑中同步處理的其他屬性。
![結構描述副檔名精靈](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)安裝會顯示下列屬性，也就是有效的對象︰

- 使用者和群組的物件類型
- 單一值的屬性︰ 字串、 布林值、 整數、 二進位
- 多重值的屬性︰ 字串、 二進位

從 Azure AD Connect 安裝期間建立快取讀取之屬性的清單。 如果您有擴充與其他屬性的 Active Directory 結構描述，[必須重新整理結構描述](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema)之前這些新屬性會顯示。

物件可以有最多 100 個目錄擴充屬性。 最大的長度為 250 個字元。 如果屬性值是更長的時間，它會取至整數，同步處理引擎。

在安裝期間的 Azure AD Connect，則這些屬性可位置登錄應用程式。 您可以看到 Azure 入口網站中的此應用程式。  
![結構描述副檔名應用程式](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

這些屬性現在可透過圖形︰  
![圖形](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

屬性會加上延伸\_{AppClientId}\_。 AppClientId Azure AD 目錄中有相同值的所有屬性。

## <a name="next-steps"></a>後續步驟
進一步瞭解[Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)設定。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
