<properties
    pageTitle="Azure RemoteApp 範本圖像中有什麼？ |Microsoft Azure"
    description="深入了解 Azure RemoteApp 隨附的範本圖像。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Azure RemoteApp 範本圖像中有什麼？

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 訂閱包含三個範本圖像︰


- Windows Server 2012
- Microsoft Office 365 專業增強版 （必要的 Office 365 訂閱）
- Microsoft Office 2013 專業增強版 （僅限試用版）

> [AZURE.IMPORTANT]Azure RemoteApp 訂閱授與您存取權的圖像，但 Office 365 專業增強版，需要個別訂閱，與實際執行中不能使用的 Office 2013 的軟體。 這表示您可以與您的使用者共用的程式或範本圖像上的應用程式。 例如，如果您建立的集合，使用 Windows Server 2012 R2 圖像時，您可以發佈使用者透過 RemoteApp 存取系統管理中心端點保護。
>
> 查看[RemoteApp 授權的詳細資訊](remoteapp-licensing.md)，如需詳細資訊。 和[使用 Office Azure RemoteApp](remoteapp-o365.md) 」 的 office 授權資訊。

如需詳細資訊，在每個圖像的包含閱讀上。

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 （「 香草圖像 」）
此圖像根據 Microsoft Windows Server 2012 R2 資料中心的作業系統，以及具有下列角色和安裝以符合 Azure RemoteApp 範本圖像的需求的功能︰


- .NET framework 4.5，3.5.1、 3.5
- 桌面體驗
- 筆跡和手寫服務
- 媒體基礎
- 遠端桌面工作階段 host （主機)
- Windows PowerShell 4.0
- Windows PowerShell ise [以系統
- WoW64 支援

此圖像也有安裝下列應用程式︰

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft 系統管理中心 2012年端點保護
- Microsoft Windows Media Player


## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 專業增強版 （所需的訂閱）
Office 365 是最要求的應用程式，我們所建立的 「 自訂 」 的圖像，您可以使用。

此圖像香草圖像的副檔名，而具有的 Microsoft Office 365 專業增強版的 Windows Server 2012 R2 圖像所述的元件除了安裝下列元件︰


- 存取
- Excel
- Lync
- OneNote
- （請注意，同步處理代理程式不支援用於 Azure RemoteApp） 的商務用 OneDrive
- Outlook
- PowerPoint
- Word
- Microsoft Office 校訂工具

Visio Pro 及 Project Pro，也包含圖像。

與下列應用程式，以及︰

- SQL 原生的用戶端
- ODBC 驅動程式
- SQL Server 2005 資料採礦用戶端
- MasterDataServices 用戶端
- Microsoft Publisher
- PowerQuery
- 至 PowerMap


僅適用於 Office 365 專業增強版方案的使用者使用的 Office 365 專業增強版應用程式的完整功能。 如需詳細資訊，在 Office 365 訂閱方案，請參閱[Office 365 服務方案](http://technet.microsoft.com/library/office-365-plan-options.aspx)。 您仍然有疑問嗎？ 查看[Office 365 + RemoteApp](remoteapp-o365.md)資訊。 此外，請參考新增文章︰[如何使用 Office 365 訂閱的 Azure RemoteApp](remoteapp-officesubscription.md)。

請注意，您需要獨立的授權 Office 365 專業增強版、 Visio Pro 和 Project Pro 它們會有自己的授權。

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 專業增強版 （僅限試用版）
免費試用期間，您可以測試與 Office 2013 影像的服務。

此圖像香草圖像的副檔名，而具有的 Microsoft Office 2013 專業增強版的 Windows Server 2012 R2 圖像所述的元件除了安裝下列元件︰


- 存取
- Excel
- Lync
- OneNote
- （請注意，同步處理代理程式不支援用於 Azure RemoteApp） 的商務用 OneDrive
- Outlook
- PowerPoint
- 專案
- Visio
- Word
- Microsoft Office 校訂工具

> [AZURE.IMPORTANT]**法律資訊︰**此圖像不包含的 Microsoft Office 授權，*不能用於生產*。 Office 2013 專業增強版圖像是試用版僅供使用。 如果您想要使用 Office 應用程式中 Azure RemoteApp 生產，您需要使用 Office 365 專業增強版的圖像。 如需授權的 Office 的詳細資訊，請參閱[Azure RemoteApp 使用 Office 365](remoteapp-o365.md)
