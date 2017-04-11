<properties
    pageTitle="如何安裝和設定 PowerShell 的 Azure"
    description="瞭解如何安裝和設定 PowerShell 的 Azure。"
    editor="tysonn"
    manager="dongill"
    documentationCenter=""
    services=""
    authors="coreyp-at-msft"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="powershell"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="coreyp"/>

# <a name="how-to-install-and-configure-azure-powershell"></a>如何安裝和設定 PowerShell 的 Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##<a name="what-is-azure-powershell"></a>什麼是 Azure？
Azure PowerShell 是提供 cmdlet 來管理使用 Windows PowerShell 的 Azure 的模組的設定。 您可以使用 cmdlet 來建立、 測試、 部署，並管理解決方案與服務透過 Azure 平台。 在大部分情況下，指令程式可用的相同的工作，例如 [Azure 入口網站，例如建立及設定雲端服務，虛擬機器、 虛擬網路和 web 應用程式。

## <a name="how-versioning-works"></a>版本設定的運作方式

Azure PowerShell 使用語意的版本設定，這表示，如果版本 A > 版本 B，然後 A 有最新的 Api 的版本。 此外，這表示變更主要版本平均值相關變更一或多個 cmdlet。  因此，版本 1.7.0，例如會是重要修正地址的 PowerShell 的 Azure 1.x 版本的相關變更。

如需 PowerShell 的 Azure 中的語意版本設定作法的詳細資訊，請參閱在語意的版本設定規格︰ http://semver.org
 
若要取得最新的 Api，您應該使用版本 2.x。 但如果您有撰寫的指令碼針對版本 1.x，而且您不想吸收重大變更版本 2.x 述 2.x[發行備忘稿](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md)，那麼您應該安裝 1.7.0。

如果已安裝最新版的設定檔模組，就會載入舊版取決於其模組，可能會造成版本不相符。 若要解決這個問題最簡單的方法是從最新的.msi 安裝。 .msi 自動清理較舊版本的模組。
 
###<a name="installing-module-versions-side-by-side"></a>安裝模組版本--並排

版本 2.1.0 （AzureStack 1.2.6 版） 是設計用來安裝的第一個模組版本，並使用 [並排顯示。 因為 PowerShell 的 Azure 使用二進位模組，您必須開啟新的 PowerShell 視窗，並使用**匯入模組**匯入 AzureRM cmdlet 的特定版本︰

    Import-Module AzureRM -RequiredVersion 2.1.0**

與其他 Azure PowerShell 模組版本並列也很簡單 2.1.0 （非 1.2.6) 無法運作之前的版本。 載入舊版使用命令，如上述 Azure PowerShell 模組時, **AzureRM.Profile**模組版本不相容會載入，要求您登入 cmdlet 結果每當您執行指令程式，即使您已登入。

最簡單的方法來解決這是從 WebPI 安裝最新 PowerShell 的 Azure 摘要或.msi – 這樣就會移除舊版從圖庫已安裝的模組。 

請注意，Azure 和 AzureRM 模組相依性，因此如果其中一個更新時，您可以使用兩個模組，您應該更新這兩。 舊版 Azure 模組有相同的問題與載入該舊版 AzureRM 模組的並排顯示模組有。

<a id="Install"></a>
## <a name="step-1-install"></a>步驟 1︰ 安裝

以下是兩種方法可以安裝 PowerShell 的 Azure。 您可以將其從 PowerShell 圖庫或 WebPI 安裝。

###<a name="installing-azure-powershell-from-the-powershell-gallery"></a>從 PowerShell 庫安裝 Azure PowerShell

慣用的方法是使用 PowerShell 庫。 您需要使用 PowerShell 庫 PowerShellGet 模組。 這是可以使用以下︰ [PowerShellGallery.com](https://www.powershellgallery.com/)

安裝 PowerShell 的 Azure 1.3.0 或更大的 PowerShell 圖庫提高權限在 Windows PowerShell 整合式指令碼環境 （ise [以系統） 提示時使用下列命令︰

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

####<a name="more-about-these-commands"></a>深入瞭解這些命令

- **安裝模組 AzureRM**安裝 Azure 資源管理員 cmdlet 的彙總套件模組。 AzureRM 模組而定 
- 針對每個 Azure 資源管理員模組特定版本範圍。 包含的版本範圍可確保安裝 AzureRM 模組使用相同的主要版本時，沒有最新的模組變更，可以包含在內。 當您安裝 AzureRM 模組時，任何 Azure 資源管理員模組，可先前尚未安裝會下載並安裝 PowerShell 圖庫中。 如需有關使用 PowerShell 的 Azure 模組語意版本設定的詳細資訊，請參閱[semver.org](http://semver.org)。 
- **安裝模組 Azure**安裝 Azure 模組。 本單元是從 PowerShell 的 Azure 服務管理模組 0.9.x。 此應有無主要的變更，並可以交換的舊版 Azure 模組。

###<a name="installing-azure-powershell-from-webpi"></a>從 WebPI 安裝 Azure PowerShell

0.9.x 是︰ 從 WebPI 安裝 Azure PowerShell 1.0 和大於都相同。 下載[PowerShell 的 Azure](http://aka.ms/webpi-azps) ，並啟動安裝。 如果您有 PowerShell 的 Azure 0.9.x 安裝、 版本 0.9.x 將會移除升級的一部分。 如果您是從 PowerShell 庫安裝 Azure PowerShell 模組，安裝程式就會自動移除之前安裝，以確保一致的 PowerShell 的 Azure 環境模組。

> [AZURE.NOTE] 如果您先前已安裝 Azure 模組 PowerShell 圖庫中，安裝程式會自動移除它們。 如此可避免混淆，哪些模組版本瞭解您已安裝，以及它們的所在位置。 **%ProgramFiles%\WindowsPowerShell\Modules**會以正常方式安裝 PowerShell 庫模組。 相反地，WebPI 安裝程式安裝中*Azure 模組*%programfiles (x86) %\Microsoft SDKs\Azure\PowerShell\**。如果在安裝期間發生錯誤，您可以手動移除 Azure*您**%ProgramFiles%\WindowsPowerShell\Modules** ] 資料夾中，並嘗試再次安裝中的資料夾。

安裝完成後，您```$env:PSModulePath```設定應該包含包含 Azure PowerShell cmdlet 的目錄。

> [AZURE.NOTE] 使用 PowerShell 的已知問題**$env: PSModulePath**的發生時從 WebPI 安裝。 如果您的電腦需要重新啟動電腦，因為系統更新或其他安裝，可能會導致更新**$env: PSModulePath**不包括安裝 PowerShell 的 Azure 路徑。 如果這種情況，您可能會看到 「 無法辨識的 cmdlet 」 訊息，當您嘗試安裝或升級後，使用 PowerShell 的 Azure cmdlet 時。 如果這種情況，重新啟動電腦應該修正問題。

如果嘗試載入或執行 cmdlet 時，您會收到下列訊息︰

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

要解決此問題，重新啟動電腦，或從 C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ 的 cmdlet 匯入為下列 (其中 XXXX 是 PowerShell 安裝的版本︰

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>步驟 2︰ 開始
在標準的 Windows PowerShell 主控台，或從 PowerShell 整合式指令碼環境 （ise [以系統），您可以執行指令程式。
您用來開啟 [主控台的方式取決於您執行的的 Windows 版本︰

- 至少執行 Windows 8 或 Windows Server 2012，您可以使用內建的搜尋。 從**[開始**] 畫面中，開始輸入 power。 這會傳回範圍的應用程式清單，包括 Windows PowerShell。 若要開啟主控台，請按一下 [應用程式。 （釘選至 [**開始**] 畫面的應用程式，以滑鼠右鍵按一下 [] 圖示。）

- 在電腦上執行的版本早於 Windows 8 或 Windows Server 2012，使用**[開始] 功能表**。 從**[開始**] 功能表中，按一下 [**所有程式**]、 都 [**附屬應用程式**，都按一下 [ **Windows PowerShell** ] 資料夾中，，然後都按一下**Windows PowerShell**。

您也可以執行的**Windows PowerShell ise [以系統**使用功能表項目和鍵盤快速鍵來執行許多您想要在 Windows PowerShell 主控台執行相同的工作。 若要在 Windows PowerShell 主控台 Cmd.exe，或在 [**執行**] 方塊中，請使用 ise [以系統，，輸入， **powershell_ise.exe**。

###<a name="commands-to-help-you-get-started"></a>命令可協助您快速入門

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
    
    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>步驟 3︰ 連線
Cmdlet 會需要您的訂閱，讓他們可以管理您的服務。 如果您還沒有其中一個，您可以購買 Azure 的訂閱。 如需相關指示，請參閱[如何購買 Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795)。

1. 輸入**登入 AzureRmAccount**

2. 輸入電子郵件地址和密碼，您的帳戶相關聯。 Azure 驗證和儲存的認證資訊，並關閉視窗。

--或-

登入您的工作或學校帳戶︰

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] 如果您有多個與您的組織帳戶相關聯的租用戶，指定 TenantId 參數︰

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] 此非互動式的登入方法只適用於公司或學校帳戶。 公司或學校帳戶是由公司或學校，管理，在您的公司或學校的 Azure Active Directory 執行個體中定義的使用者。 如果您沒有公司或學校帳戶，並使用 Microsoft 帳戶登入您的 Azure 訂閱時，您可以輕鬆地建立一個使用下列步驟。

> 1. 登入至[Azure 傳統入口網站](https://manage.windowsazure.com)，並按一下 [ **Active Directory**。

> 2. 如果沒有目錄存在，請選取 [**建立您的目錄**，並提供要求的資訊。

> 3. 選取您的目錄，然後新增使用者]。 這個新使用者可以使用公司或學校帳戶登入。 在建立期間的使用者，您會提供這兩個電子郵件地址的使用者和暫時密碼。 儲存此資訊，請使用以下的步驟 5。

> 4. 從 Azure 傳統的入口網站中，選取 [**設定**]，然後選取**的系統管理員**。 選取 [**新增**]，並以共同的系統管理員身分新增新的使用者。 這個選項可讓公司或學校帳戶，來管理您的 Azure 訂閱。

> 5. 最後，登出 Azure 傳統入口網站，然後再重新登入使用工作或學校帳戶。 如果這是第一次帳戶登入此時，系統會提示您變更密碼。

> 如需有關如何註冊 Microsoft Azure 以公司或學校帳戶的詳細資訊，請參閱[註冊 Microsoft Azure 做為組織](./active-directory/sign-up-organization.md)。

> 如需有關 Azure 中的驗證及訂閱管理的詳細資訊，請參閱[管理帳戶、 訂閱及系統管理角色](http://go.microsoft.com/fwlink/?LinkId=324796)。

### <a name="view-account-and-subscription-details"></a>檢視帳戶與訂閱詳細資料

您可以有多個帳戶與訂閱可供使用 PowerShell 的 Azure。 您可以藉由執行**新增 AzureRmAccount**不只一次新增多個帳戶。

若要顯示可用的 Azure 帳戶，請輸入**取得 AzureAccount**。

若要顯示的 Azure 訂閱，請輸入**取得 AzureRmSubscription**。

##<a id="Help"></a>取得說明##

這些資源特定 cmdlet 提供協助︰


-   您可以在主控台，內使用內建的 [說明] 系統。 **取得說明**指令程式會提供此系統的存取權。 

- 從社群的說明，請嘗試這些常用論壇︰

 - [Azure MSDN 上的論壇]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##<a name="learn-more"></a>深入瞭解


請參閱下列資源，進一步瞭解使用 cmdlet:

如需瞭解如何使用 Windows PowerShell 的基本指示，請參閱[使用 Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939)。

如需 cmdlet 參考資訊，請參閱[Azure Cmdlet 參考](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx)。

若要請指令碼範例，可協助您瞭解如何使用指令碼來管理 Azure 的指示，請參閱[指令碼中心](http://go.microsoft.com/fwlink/p/?LinkId=321940)。

