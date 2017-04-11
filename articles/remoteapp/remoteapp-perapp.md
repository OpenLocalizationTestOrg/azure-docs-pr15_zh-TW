<properties
   pageTitle="Azure RemoteApp 集合 （預覽版本） 中的 [發佈至個別使用者的應用程式 |Microsoft Azure"
   description="瞭解如何發佈至個別使用者的應用程式而不是根據 Azure RemoteApp 中的群組，而定。"
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="piotrci"/>

# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>發佈的個別使用者的應用程式中 Azure RemoteApp 集合 （預覽版本）

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

本文說明如何發佈 Azure RemoteApp 集合中的個別使用者的應用程式。 目前在 「 私人預覽 」，才可使用選取最早採用供評估之用，這是 Azure RemoteApp 中的新功能。

Azure RemoteApp 原本啟用 「 發佈 」 應用程式的只有一個方法︰ 系統管理員會發佈圖像的應用程式，並應集合中的所有使用者看見。

常見的案例是一張圖像中包含許多應用程式，並以減少管理成本部署一個集合。 通常不所有應用程式的相關的所有使用者-系統管理員喜歡發佈至個別使用者的應用程式，讓他們看不到摘要其應用程式中不必要的應用程式。

這是現在 Azure RemoteApp – 在目前的有限的預覽功能。 以下是新功能的簡短摘要︰

1. 將其中一個兩種模式，可以設定集合︰
 
  - 原始 」 集合模式 」，集合中的所有使用者可以在這裡都看見所有已發佈的應用程式。 這是預設的模式。
  - 新 」 應用程式的模式 」，明確的使用者只看到應用程式，指派給他們

2. 在應用程式模式可以只使用 Azure RemoteApp PowerShell cmdlet。

  - 設定為應用程式模式時，無法透過 Azure 入口網站管理集合中的使用者指派。 使用者指派有透過 PowerShell cmdlet 來管理。

3. 使用者只會看到直接發佈的應用程式。 不過，仍可能的使用者，即可直接在系統中存取這些啟動其他可用的應用程式上的圖像。
  - 此功能不會提供安全鎖定的應用程式。此外，而且只會限制摘要應用程式中的可見性。
  - 如果您需要找出從應用程式的使用者，您必須使用不同的集合。

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>如何取得 Azure RemoteApp PowerShell cmdlet

若要預覽的新功能，您必須使用 Azure PowerShell 指令程式。 不目前可能使用 Azure 管理入口網站，以啟用新的應用程式發佈的模式。

首先，請確定您有安裝[Azure PowerShell 模組](../powershell-install-configure.md)。

然後啟動 PowerShell 主控台管理員模式中的，並執行下列 cmdlet:

        Add-AzureAccount

它會提示您輸入 Azure 的使用者名稱和密碼。 登入之後，您可以針對您 Azure 訂閱執行 Azure RemoteApp cmdlet。

## <a name="how-to-check-which-mode-a-collection-is-in"></a>如何檢查是哪一種模式的集合

執行下列 cmdlet:

        Get-AzureRemoteAppCollection <collectionName>

![核取 [集合模式](./media/remoteapp-perapp/araacllelvel.png)

AclLevel 屬性可能會有下列的值︰

- 集合︰ 原始發佈模式。 所有使用者，請都參閱所有已發佈的應用程式。
- 應用程式︰ 新發佈模式。 使用者看到直接發佈的應用程式。

## <a name="how-to-switch-to-application-publishing-mode"></a>如何切換應用程式發佈模式

執行下列 cmdlet:

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

應用程式發佈的狀態會保留︰ 一開始的所有使用者會都看到所有的原始的已發佈應用程式。

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>如何將 [可以檢視特定應用程式使用者清單

執行下列 cmdlet:

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

這會列出所有使用者都可以看到的應用程式。

注意︰ 您可以查看 （上述語法中稱為 「 應用程式別名 」） 的應用程式別名執行取得 AzureRemoteAppProgram CollectionName <collectionName>。

## <a name="how-to-assign-an-application-to-a-user"></a>如何指派給使用者的應用程式

執行下列 cmdlet:

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

使用者將會看到 Azure RemoteApp 用戶端應用程式，並無法連線至該。

## <a name="how-to-remove-an-application-from-a-user"></a>如何從使用者移除應用程式

執行下列 cmdlet:

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>提供意見反應
感謝您的意見反應及預覽功能相關的建議。 請填寫[問卷](http://www.instant.ly/s/FDdrb)，讓我們知道您的想法。

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>您尚未有機會試著 [預覽] 功能？
如果您有不參與預覽尚未，請使用此[問卷](http://www.instant.ly/s/AY83p)要求存取。
