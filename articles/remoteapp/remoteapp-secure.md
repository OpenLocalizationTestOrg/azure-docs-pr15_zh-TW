
<properties
    pageTitle="保護應用程式中 Azure RemoteApp 資源 |Microsoft Azure"
    description="瞭解如何鎖定應用程式和 Azure RemoteApp 中的資源"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>安全的應用程式和 Azure RemoteApp 中的資源

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

Azure RemoteApp 提供使用者存取集中管理 Windows 應用程式，可讓您控制哪些使用者可以或無法進行的動作。  當使用者在未受管理的裝置 （例如其個人 Macbook) 從連線，而您想要控制使用者存取或體驗，這是特別有用。

例如，如果您使用 Active Directory 使用者驗證，且您想要複製登出應用程式的資料時，防止您的使用者，您可以設定遠端桌面群組原則封鎖使用者從複製的資料。

另一個範例是如果您想要封鎖的集合中的特定應用程式存取網際網路。 您可以建立的集合建立圖像時，會封鎖存取 Windows 防火牆規則。

## <a name="implementation-options"></a>實作選項

  以下是重要實作選項，可使用個別或一起，視需要︰

1.  如果您 RemoteApp 集合加入網域，您可以強制任何[群組原則](https://technet.microsoft.com/library/cc725828.aspx)（但閒置和中斷連線逾時原則所述[以下](../azure-subscription-service-limits.md)）。
2.  群組原則 （如果您集合沒有加入網域，或在 AD 沒有正確的權限） 或者，您可以設定[本機原則](https://technet.microsoft.com/library/cc775702.aspx)到您的範本圖像。  請注意衝突時，該群組原則王牌本機原則。
3.  某些 OS/應用程式設定無法透過原則，可設定，但可透過使用[RegEdit 工具](./remoteapp-hybridtrouble.md)設定您的範本圖像的登錄機碼。
4.  您可以使用[Windows 防火牆](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish)，若要控制網路存取與電腦執行應用程式的位置。 請確定您不會封鎖的 Url 及此處定義的連接埠。
5.  您可以使用[applocker 允許](https://technet.microsoft.com/library/hh831440.aspx)的應用程式和檔案的使用者可以執行的控制項。 例如，知識的使用者可以找出不是由您發佈，但可用於您用來建立的集合的影像-applocker 允許可以封鎖此應用程式的執行方式。

## <a name="detailed-information"></a>詳細的資訊

- 下列 RDS 原則很實用︰
    - [裝置及資源重新導向](https://technet.microsoft.com/library/ee791794.aspx)
    - [印表機重新導向](https://technet.microsoft.com/library/ee791784.aspx)
    - [設定檔](https://technet.microsoft.com/library/ee791865.aspx)。
- 請注意，設定重新導向透過 RemoteApp PowerShell 模組 （時看到[以下](./remoteapp-redirection.md)） 依賴用戶端電腦強制執行原則，因此如果安全性的主要目標您會想要強制執行原則透過範本圖像本機原則，或透過群組原則。
- [Windows Server 2012 R2 原則](https://technet.microsoft.com/library/hh831791.aspx)。
- [Office 2013 原則](https://technet.microsoft.com/library/cc178969.aspx)（包括[如何自訂 Office 工具列](https://technet.microsoft.com/library/cc179143.aspx)）。
