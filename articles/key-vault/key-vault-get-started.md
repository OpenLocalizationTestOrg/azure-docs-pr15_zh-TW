<properties
    pageTitle="開始使用 Azure 金鑰保存庫 |Microsoft Azure"
    description="使用此教學課程，可協助您開始使用 Azure 金鑰保存庫來儲存和管理加密金鑰和 Azure 中的機密 Azure 中建立強化的容器。"
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>

# <a name="get-started-with-azure-key-vault"></a>Azure 鍵保存庫快速入門 #
Azure 鍵保存庫大部分地區都提供。 如需詳細資訊，請參閱[金鑰保存庫價格頁面](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="introduction"></a>簡介  
使用本教學課程中，以協助您開始使用 Azure 金鑰保存庫來儲存和管理加密金鑰和 Azure 中的機密 Azure 中建立強化的容器 （保存庫）。 會引導您完成建立保存庫包含鍵或密碼，您可以使用 Azure 應用程式使用 PowerShell 的 Azure 的程序。 接著會說明如何應用程式，可以使用該金鑰或密碼。

**估計完成時間︰** 20 分鐘

>[AZURE.NOTE]  本教學課程中不包含如何撰寫 Azure 應用程式的其中一個步驟包括，也就是如何授權應用程式使用金鑰或私人金鑰保存庫中的指示。
>
>本教學課程中使用 PowerShell 的 Azure。 跨平台命令列介面指示，請參閱[本教學課程中相同](key-vault-manage-with-cli.md)。

Azure 鍵保存庫相關的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)

## <a name="prerequisites"></a>必要條件

若要完成此教學課程中，您必須具備下列項目︰

- Microsoft Azure 訂閱。 如果您沒有其中一個，您可以註冊[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。
- Azure PowerShell**最低 1.1.0 的版本**。 若要安裝 PowerShell 的 Azure，並將它與 Azure 訂閱關聯，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。 如果您已安裝 PowerShell 的 Azure 並不知道版本，請從 Azure PowerShell 主控台，輸入`(Get-Module azure -ListAvailable).Version`。 如果您有 PowerShell 的 Azure 0.9.1 透過 0.9.8 安裝的版本，您還是可以使用此教學課程與稍微修改一下。 例如，您必須使用`Switch-AzureMode AzureResourceManager`命令及某些 Azure 金鑰保存庫命令已變更。 透過 0.9.8 0.9.1 版本的金鑰保存庫指令程式的清單，請參閱[Azure 金鑰保存庫 Cmdlet](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx)。 
- 應用程式，會設定為使用鍵或您在本教學課程中建立的密碼。 從[Microsoft 下載中心](http://www.microsoft.com/en-us/download/details.aspx?id=45343)使用範例應用程式。 如需相關指示，請參閱隨附的讀我檔案。


本教學課程為設計 PowerShell 的 Azure 初學者，但假設您瞭解基本概念，例如模組、 cmdlet 和工作階段。 如需詳細資訊，請參閱[使用 Windows PowerShell 入門](https://technet.microsoft.com/library/hh857337.aspx)。

若要取得您在本教學課程中看到任何 cmdlet 的詳細的說明，請使用**取得說明**指令程式。

    Get-Help <cmdlet-name> -Detailed

例如，若要取得**登入 AzureRmAccount**指令程式的說明，請輸入︰

    Get-Help Login-AzureRmAccount -Detailed

您也可以閱讀下列的教學課程，瞭解 PowerShell 的 Azure 中的 [與 Azure 資源管理員的部分︰

- [如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)
- [使用 PowerShell 的 Azure 與資源管理員](../powershell-azure-resource-manager.md)


## <a id="connect"></a>連線至您的訂閱 ##

啟動 Azure PowerShell 工作階段，並登入您 Azure 帳戶使用了下列命令︰  

    Login-AzureRmAccount 

請注意，如果您使用的特定的執行個體的 Azure，例如 Azure 政府版使用-環境參數與此命令。 例如︰`Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

在快顯的瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱和密碼。 Azure PowerShell 取得所有的訂閱，此帳戶與預設相關聯，會使用第一個。

如果您有多個訂閱，並想要指定特定的項目，使用於 Azure 金鑰保存庫，請輸入若要查看您的帳戶的訂閱︰

    Get-AzureRmSubscription

然後，如果要指定要使用的訂閱，請輸入︰

    Set-AzureRmContext -SubscriptionId <subscription ID>

如需更多關於設定 PowerShell 的 Azure 的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。


## <a id="resource"></a>建立新的資源群組 ##

當您使用 Azure 資源管理員時，所有相關的資源的資源群組內建立。 我們會建立新的資源群組名稱**ContosoResourceGroup**為此教學課程︰

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>建立索引鍵保存庫 ##

您可以使用 [[新增 AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) cmdlet 來建立索引鍵保存庫。 這個 cmdlet 具有三個必要的參數︰**資源群組的名稱**、**金鑰保存庫名稱**，以及的**地理位置**。

例如，如果您使用的**ContosoKeyVault**保存庫名稱、 資源群組的名稱**ContosoResourceGroup**及**中式地址**的位置，請輸入︰

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

這個 cmdlet 輸出會顯示您剛剛建立的金鑰保存庫的屬性。 兩個最重要的內容是︰

- **保存庫名稱**︰ 在範例中，這是**ContosoKeyVault**。 您會使用其他鍵保存庫 cmdlet 此名稱。
- **保存庫 URI**︰ 範例中，這是 https://contosokeyvault.vault.azure.net/。 使用您透過其 REST API 的保存庫應用程式，必須使用這個 URI。

Azure 帳戶立即執行此按鍵保存庫上的任何作業的權限。 尚未，其他人一律是。

>[AZURE.NOTE]  如果您看到**訂閱未註冊使用命名空間 'Microsoft.KeyVault 」**的錯誤，當您嘗試建立您新的金鑰保存庫，請執行`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`，然後重新執行您新增 AzureRmKeyVault 的命令。 如需詳細資訊，請參閱[Register AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx)。
>

## <a id="add"></a>新增索引鍵保存庫鍵或密碼 ##

若要為您建立的軟體保護的金鑰 Azure 金鑰保存庫，請使用[新增 AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) cmdlet，並輸入以下︰

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

不過，如果您在中有現有的軟體保護索引鍵。PFX 檔案儲存到您的 C:\ 磁碟機，名為您要上傳至 Azure 金鑰保存庫，softkey.pfx 檔案中輸入下列命令以設定密碼的**123**的變數**securepfxpwd** 。PFX 檔案︰

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

然後輸入下列命令以匯入的鍵。PFX 檔案鍵保存庫服務的軟體保護機碼︰

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


您現在可以參考的您建立或使用其 URI 上傳至 Azure 金鑰保存庫，此按鍵。 使用**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**永遠取得目前的版本，並使用**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**取得此特定的版本。  

若要顯示此按鍵 URI，請輸入︰

    $Key.key.kid

將密碼新增至保存庫是一個名為 SQLPassword 的密碼，並以 Azure 金鑰保存庫 Pa$ $w0rd 值，先將值轉換的 Pa$ $w0rd 為安全的字串輸入下列命令︰

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

然後，輸入以下資料︰

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

您現在可以參考使用其 URI 加入 Azure 金鑰保存庫，具備密碼。 使用**https://ContosoVault.vault.azure.net/secrets/SQLPassword**永遠取得目前的版本，並使用**https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**取得此特定的版本。

若要顯示的 URI 這個密碼，請輸入︰

    $secret.Id

讓我們來檢視鍵或您剛剛建立的密碼︰

- 若要檢視您的金鑰，請輸入︰`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- 若要檢視您的密碼，請輸入︰`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

現在您的主要保存庫和鍵或私人可以準備好要使用的應用程式。 您必須授權應用程式的使用方式。  

## <a id="register"></a>Azure Active Directory 中註冊應用程式 ##

開發人員]，在另一部電腦上通常會完成此步驟。 它不是特定 Azure 金鑰保存庫，但會包含以下的完整性。


>[AZURE.IMPORTANT] 完成本教學課程與您的帳戶、 保存庫，您會在此步驟中登錄應用程式必須在同一 Azure 目錄。

使用金鑰保存庫應用程式，必須使用從 Azure Active Directory 的權杖進行驗證。 若要這麼做，應用程式的擁有者，必須先在他們的 Azure Active Directory 中登錄應用程式。 註冊結尾應用程式擁有者會取得以下的值︰


- **應用程式識別碼**（也稱為用戶端識別碼） 及**驗證金鑰**（也稱為共用密碼）。 應用程式必須提供下列兩個值至 Azure Active Directory，取得權杖。 執行此動作的應用程式的設定方式而定的應用程式。 金鑰保存庫範例應用程式的應用程式擁有者，請在 app.config 檔案設定這些值。

註冊 Azure Active Directory 中的應用程式︰

1. Azure 傳統入口網站登入。
2. 在左側，按一下**Active Directory**]，然後選取您要在其中登錄應用程式目錄。 <br> <br> **附註︰**您必須先選取包含建立您的主要保存庫 Azure 訂閱的相同目錄。 如果您不知道哪一個目錄這是，按一下 [**設定]**，找出的訂閱，與您建立索引鍵的保存庫，並記下目錄中的最後一欄顯示的名稱。

3. 按一下 [**應用程式**]。 如果沒有應用程式已新增至您的目錄，此頁面會顯示只有 [**新增應用程式**] 連結。 按一下連結，或或者，您可以按一下 [**新增**命令列上。
4.  在 [**新增應用程式**精靈] 在**您想要做什麼？**頁面上，按一下 [**新增組織內部開發的應用程式**。
5.  在 [**告訴我們瞭解您的應用程式**] 頁面中，指定您的應用程式的名稱，然後選取**WEB 應用程式和/或 WEB API** （預設）。 按一下 [**下一步**] 圖示。
6.  在 [**應用程式屬性**] 頁面中，指定**登開啟 URL** ] 與 [**應用程式識別碼 URI** web 應用程式。 如果您的應用程式未與任何這些值，您可以讓註冊此步驟 （例如，您可以指定 http://test1.contoso.com 這兩個方塊）。 並不重要這些網站存在。 重要的是每個應用程式的應用程式識別碼 URI 是不同的目錄中每個應用程式。 目錄會使用這個字串來找出您的應用程式。
7.  按一下 [儲存變更精靈中的 [**完成**] 圖示。
8.  在 [**快速入門**] 頁面上，按一下 [**設定**]。
9.  向下捲動至 [**索引鍵**] 區段中，選取期間，再按一下 [**儲存**。 頁面重新整理，而且現在會顯示的索引鍵值。 您必須設定您的應用程式，使用此索引鍵值和**用戶端識別碼**值。 （此設定的相關指示是特定應用程式）。
10. 複製此頁面，您會使用中的下一個步驟來設定您的保存庫的權限的用戶端識別碼值。

## <a id="authorize"></a>授權使用金鑰或私人應用程式 ##

若要授權存取鍵或私人保存庫中的應用程式，使用 [設定 AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx)指令程式。

比方說，如果您保存庫的名稱**ContosoKeyVault**和您想要授權的應用程式的用戶端識別碼的 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed，而且您想要授權解密並與您保存庫鍵登入應用程式，請執行下列動作︰


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

如果您要授權閱讀您保存庫中的機密的同一個應用程式，請執行下列動作︰


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>如果您想要使用硬體安全性模組 (HSM) ##

新增保證，您可以匯入或永遠不會讓 HSM 邊界的硬體安全性模組 (Hsm) 中會產生金鑰。 Hsm 是的 FIPS 140-2 層級 2 驗證。 如果這項需求不適用於您，略過此區段，然後移至[刪除重要保存庫與索引鍵和密碼](#delete)。

若要建立這些 HSM 受保護的按鍵，您必須使用[Azure 金鑰保存庫進階版服務層，以支援 HSM 受保護的金鑰](https://azure.microsoft.com/pricing/free-trial/)。 此外，請注意，這項功能不適用於 Azure china （中國）。


當您建立索引鍵保存庫時，新增**-SKU**參數︰


    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



您可以新增此按鍵保存庫軟體保護 （為顯示較舊版本） 的索引鍵和 HSM 受保護的鍵。 若要建立 HSM 保護鍵，將**-目的地**'HSM 」 參數︰

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

您可以使用下列命令，匯入的金鑰。在您的電腦上的 PFX 檔案。 這個命令鍵會將匯入 Hsm 鍵保存庫服務︰

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


[下一步] 命令匯入] 將您自己的金鑰 」 (BYOK) 套件。 這種情況下，可讓您在本機 HSM，產生金鑰，並傳送到 Hsm 鍵保存庫服務中，而不需離開 HSM 邊界的索引鍵︰

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

如何產生此 BYOK 套件的詳細指示，請參閱[如何產生與傳輸 Azure 金鑰保存庫的 HSM 保護索引鍵](key-vault-hsm-protected-keys.md)。

## <a id="delete"></a>刪除索引鍵保存庫相關聯的按鍵和密碼 ##

如果您不再需要金鑰保存庫的索引鍵或其包含的密碼，您可以刪除重要保存庫使用[移除 AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx)指令程式︰

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

或者，您可以刪除整個 Azure 資源] 群組中，包括主要保存庫包含該群組中的其他資源︰

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>其他 Azure PowerShell Cmdlet ##

其他您可能會發現很適合用於管理 Azure 金鑰保存庫的命令︰

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`︰ 這個命令取得表格式顯示的所有按鍵以及選取的內容。
- `$Keys[0]`︰ 這個命令會顯示完整的指定索引鍵的內容清單
- `Get-AzureKeyVaultSecret`︰ 這個命令可列出表格式顯示所有私人的名稱和選取的內容。
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`︰ 範例如何移除特定的金鑰。
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`︰ 範例如何移除特定的密碼。


## <a id="next"></a>後續步驟 ##

在 web 應用程式中使用 Azure 金鑰保存庫的待處理教學課程，請參閱[使用 Azure 金鑰保存庫從 Web 應用程式](key-vault-use-from-web-application.md)。

若要瞭解如何使用您的主要保存庫，請參閱[Azure 金鑰保存庫記錄](key-vault-logging.md)。

如需最新 Azure PowerShell cmdlet Azure 金鑰保存庫的清單，請參閱[Azure 金鑰保存庫 Cmdlet](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx)。 
 

程式設計參照，請參閱[Azure 金鑰保存庫開發人員指南](key-vault-developers-guide.md)。
