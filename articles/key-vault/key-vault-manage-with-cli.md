<properties
    pageTitle="管理金鑰保存庫使用 CLI |Microsoft Azure"
    description="使用本教學課程中使用 CLI 自動化鍵保存庫中的一般工作"
    services="key-vault"
    documentationCenter=""
    authors="BrucePerlerMS"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="bruceper"/>

# <a name="manage-key-vault-using-cli"></a>管理使用 CLI 鍵保存庫 #
Azure 鍵保存庫大部分地區都提供。 如需詳細資訊，請參閱[金鑰保存庫價格頁面](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="introduction"></a>簡介  
使用本教學課程中，以協助您開始使用 Azure 金鑰保存庫來儲存和管理加密金鑰和 Azure 中的機密 Azure 中建立強化的容器 （保存庫）。 會引導您完成建立保存庫包含鍵或密碼，您可以使用 Azure 應用程式使用 Azure 跨平台命令列介面的程序。 接著會說明如何應用程式，可以再使用該金鑰或密碼。

**估計完成時間︰** 20 分鐘

>[AZURE.NOTE]  本教學課程中不包含如何撰寫 Azure 應用程式的其中一個步驟包含，其中顯示如何授權應用程式使用金鑰或私人金鑰保存庫中的指示進行。
>
>目前您無法設定 Azure 金鑰保存庫 Azure 入口網站中。 請改用下列跨平台命令列介面指示進行。 或者，如需 PowerShell 的 Azure 指示，請參閱[此相等的教學課程](key-vault-get-started.md)。

Azure 鍵保存庫相關的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)

## <a name="prerequisites"></a>必要條件
若要完成此教學課程中，您必須具備下列項目︰

- Microsoft Azure 訂閱。 如果您沒有其中一個，您可以註冊[免費試用版](https://azure.microsoft.com/pricing/free-trial)。
- 命令列介面 0.9.1 版本或更新版本。 若要安裝最新版本，並連線到您 Azure 的訂閱，請參閱[安裝及設定 Azure 跨平台命令列介面](../xplat-cli-install.md)。
- 應用程式，會設定為使用鍵或您在本教學課程中建立的密碼。 從[Microsoft 下載中心](http://www.microsoft.com/download/details.aspx?id=45343)使用範例應用程式。 如需相關指示，請參閱隨附的讀我檔案。

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>取得說明與 Azure 跨平台命令列介面

本教學課程假設您熟悉的命令列介面 （艦隊，終端機，命令提示字元）

--說明或-h 參數可以用來檢視特定命令的說明。 或者，azure 說明 [命令] [選項] 格式也可用來傳回相同的資訊。 例如，下列所有的命令會傳回相同的資訊︰

    azure account set --help

    azure account set -h

    azure help account set

關於所需的命令參數如有疑問，請參閱使用-說明說明、-h 或 azure 說明 [命令]。

您也可以閱讀，瞭解使用 Azure 資源管理員的部分 Azure 跨平台命令列介面中的下列教學課程︰

- [如何安裝和設定 Azure 跨平台命令列介面](../xplat-cli-install.md)
- [使用 Azure 跨平台命令列介面與 Azure 資源管理員](../xplat-cli-azure-resource-manager.md)


## <a name="connect-to-your-subscriptions"></a>連線至您的訂閱

若要登入組織的帳戶，請使用下列命令︰

    azure login -u username -p password

如果您想要互動的方式輸入登入，或

    azure login

>[AZURE.NOTE]  登入方法只適用於組織帳戶。 組織的帳戶是由您的組織管理且定義貴組織的 Azure Active Directory 租用戶中的使用者。


如果您目前沒有組織帳戶，並使用 Microsoft 帳戶登入您的 Azure 訂閱時，您可以輕鬆地建立一個使用下列步驟。

1.  登入[Azure 管理入口網站](https://manage.windowsazure.com/)，並按一下 Active Directory 的登入。
2.  如果沒有目錄存在，請選取 [建立您的目錄，並提供要求的資訊。
3.  選取您的目錄，然後新增使用者]。 這個新使用者是組織的帳戶。 在建立期間的使用者，您會提供這兩個電子郵件地址的使用者和暫時密碼。 在另一個步驟中使用時，請儲存這項資訊。
4.  入口網站中，選取 [設定]，然後選取的系統管理員。 選取 [新增]，並以共同的系統管理員身分新增新的使用者。 這個選項可讓管理 Azure 訂閱的組織帳戶。
5.  最後，登出 Azure 入口網站，然後再重新登入使用新的組織帳戶。 如果這是第一次帳戶登入此時，系統會提示您變更密碼。

如需有關如何使用 Microsoft Azure 中的組織帳戶的詳細資訊，請參閱[註冊 Microsoft Azure 做為組織](../active-directory/sign-up-organization.md)。

如果您有多個訂閱，並想要指定特定的項目，使用於 Azure 金鑰保存庫，請輸入若要查看您的帳戶的訂閱︰

    azure account list

然後，如果要指定要使用的訂閱，請輸入︰

    azure account set <subscription name>

如需更多關於設定 Azure 跨平台命令列介面的詳細資訊，請參閱[如何安裝和設定 Azure 跨平台命令列介面](../xplat-cli-install.md)。


## <a name="switch-to-using-azure-resource-manager"></a>切換成使用 Azure 資源管理員

索引鍵保存庫需要 Azure 資源管理員，因此輸入下列命令以切換到 Azure 資源管理員模式︰

    azure config mode arm

## <a name="create-a-new-resource-group"></a>建立新的資源群組

當使用 Azure 資源管理員，所有相關的資源會建立資源群組內。 我們會在此教學課程中建立新的資源群組 'ContosoResourceGroup'。

    azure group create 'ContosoResourceGroup' 'East Asia'

第一個參數值是資源群組名稱，而第二個參數值是位置。 位置，請使用命令`azure location list`識別如何指定在此範例中的項目至其他位置。 如果您需要詳細資訊，請輸入︰`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>登錄機碼保存庫資源提供者
請確定該金鑰保存庫資源提供者註冊您的訂閱中︰

`azure provider register Microsoft.KeyVault`

這只需要每個訂閱執行一次即可。


## <a name="create-a-key-vault"></a>建立索引鍵保存庫

使用`azure keyvault create`] 命令，建立索引鍵保存庫。 此指令碼有三個必要的參數︰ 資源群組名稱、 金鑰保存庫名稱，以及地理位置。

例如，如果您使用的 ContosoKeyVault 保存庫名稱、 資源群組的名稱 ContosoResourceGroup 及中式地址的位置，請輸入︰

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

這個命令的輸出會顯示您剛剛建立的金鑰保存庫的屬性。 兩個最重要的內容是︰

- **名稱**︰ 在此範例中，這是 ContosoKeyVault。 您會使用其他鍵保存庫 cmdlet 此名稱。
- **vaultUri**︰ 在此範例中，這是 https://contosokeyvault.vault.azure.net。 使用您透過其 REST API 的保存庫應用程式，必須使用這個 URI。

Azure 帳戶立即執行此按鍵保存庫上的任何作業的權限。 尚未，其他人一律是。


## <a name="add-a-key-or-secret-to-the-key-vault"></a>新增索引鍵保存庫鍵或密碼

若要為您建立的軟體保護的金鑰 Azure 金鑰保存庫，請使用`azure key create`命令，然後輸入下列︰

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

不過，如果您有現有的索引鍵.pem 檔案儲存為檔案命名 softkey.pem 您要上傳至 Azure 金鑰保存庫中的本機檔案中，輸入下列命令以匯入的鍵。PEM 檔案]，在金鑰保存庫服務的軟體保護索引鍵︰

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

您現在可以參考的機碼，您建立或上傳 Azure 金鑰保存庫，以利用其 URI。 使用**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**永遠取得目前的版本，並使用**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**取得此特定的版本。

將密碼新增至保存庫，這是一個名為 SQLPassword 的密碼的值的 Pa$ $w0rd 至 Azure 金鑰保存庫，請輸入︰

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

您現在可以參考使用其 URI 加入 Azure 金鑰保存庫，具備密碼。 使用**https://ContosoVault.vault.azure.net/secrets/SQLPassword**永遠取得目前的版本，並使用**https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**取得此特定的版本。

讓我們來檢視鍵或您剛剛建立的密碼︰

- 若要檢視您的金鑰，請輸入︰`azure keyvault key list --vault-name 'ContosoKeyVault'`
- 若要檢視您的密碼，請輸入︰`azure keyvault secret list --vault-name 'ContosoKeyVault'`


## <a name="register-an-application-with-azure-active-directory"></a>Azure Active Directory 中註冊應用程式

開發人員]，在另一部電腦上通常會完成此步驟。 它不是特定 Azure 金鑰保存庫，但會包含在這裡的完整性。


>[AZURE.IMPORTANT] 完成本教學課程與您的帳戶、 保存庫，您會在此步驟中登錄應用程式必須在同一 Azure 目錄。

使用金鑰保存庫應用程式，必須使用從 Azure Active Directory 的權杖進行驗證。 若要這麼做，應用程式的擁有者，必須先在他們的 Azure Active Directory 中登錄應用程式。 註冊結尾應用程式擁有者會取得以下的值︰


- **應用程式識別碼**（也稱為用戶端識別碼） 及**驗證金鑰**（也稱為共用密碼）。 應用程式必須提供兩個值至 Azure Active Directory，取得權杖。 執行此動作的應用程式的設定方式而定的應用程式。 金鑰保存庫範例應用程式的應用程式擁有者，請在 app.config 檔案設定這些值。



註冊 Azure Active Directory 中的應用程式︰

1. Azure 入口網站登入。
2. 在左側，按一下**Active Directory**]，然後選取您要在其中登錄應用程式目錄。 <br> <br> 注意︰ 您必須先選取包含建立您的主要保存庫 Azure 訂閱的相同目錄。 如果您不知道哪一個目錄這是，按一下 [**設定]**，找出的訂閱，與您建立索引鍵的保存庫，並記下目錄中的最後一欄顯示的名稱。

3. 按一下 [**應用程式**]。 如果沒有應用程式已新增至您的目錄，此頁面會顯示只有 [**新增應用程式**] 連結。 按一下連結，或或者，您可以按一下**新增**命令列上。
4.  在 [**新增應用程式**精靈] 在**您想要做什麼？**頁面上，按一下 [**新增組織內部開發的應用程式**。
5.  在 [**告訴我們瞭解您的應用程式**] 頁面中，指定應用程式的名稱，並選取**WEB 應用程式和/或 WEB API** （預設）。 按一下 [下一步] 圖示。
6.  在 [**應用程式屬性**] 頁面中，指定**登開啟 URL** ] 與 [**應用程式識別碼 URI** web 應用程式。 如果您的應用程式未與任何這些值，您可以讓註冊此步驟 （例如，您可以指定 http://test1.contoso.com 這兩個方塊）。 不論這些網站存在; 如果重要的是每個應用程式的應用程式識別碼 URI 是不同的目錄中每個應用程式。 目錄會使用這個字串來找出您的應用程式。
7.  按一下 [完成] 圖示可在精靈中儲存您的變更。
8.  在 [快速入門] 頁面上，按一下 [**設定**]。
9.  向下捲動至 [**索引鍵**] 區段中，選取期間，再按一下 [**儲存**。 頁面重新整理，而且現在會顯示的鍵值。 您必須設定您的應用程式，此鍵值與**用戶端識別碼**值。 （此設定的相關指示會是特定應用程式）。
10. 複製此頁面，您會使用中的下一個步驟來設定您的保存庫的權限的用戶端識別碼值。




## <a name="authorize-the-application-to-use-the-key-or-secret"></a>授權使用金鑰或私人應用程式

若要授權存取鍵或私人保存庫中的應用程式，使用`azure keyvault set-policy`] 命令。

例如，如果您保存庫的名稱為 ContosoKeyVault 及您想要授權的應用程式的用戶端識別碼的 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed，且您想要授權應用程式解密並登入與您保存庫鍵，然後執行下列動作︰

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

>[AZURE.NOTE] 如果您執行的在 Windows 命令提示字元中，您應該單引號括住取代雙引號，，而且也逸出內部雙引號中。 例如: 「 [\"解密\"，\"登\"] 」。

如果您要授權閱讀您保存庫中的機密的同一個應用程式，請執行下列動作︰

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>如果您想要使用硬體安全性模組 (HSM) ##

新增保證，您可以匯入或永遠不會讓 HSM 邊界的硬體安全性模組 (Hsm) 中會產生金鑰。 Hsm 是的 FIPS 140-2 層級 2 驗證。 如果這項需求不適用於您，略過此區段，然後移至[刪除重要保存庫與索引鍵和密碼](#delete-the-key-vault-and-associated-keys-and-secrets)。

若要建立這些 HSM 受保護的按鍵，您必須保存庫訂閱的支援 HSM 受保護的金鑰。

當您建立 keyvault 時，新增 'sku 」 參數︰

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

您可以新增此保存庫軟體保護 （為顯示較舊版本） 的索引鍵和 HSM 受保護的鍵。 若要建立 HSM 保護鍵，設定 「 HSM' 目的地參數︰

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

您可以使用下列命令，從您的電腦上.pem 檔案匯入的金鑰。 這個命令鍵會將匯入 Hsm 鍵保存庫服務︰

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

[下一步] 命令匯入] 將您自己的金鑰 」 (BYOK) 套件。 這個選項可讓您在本機 HSM，產生金鑰，並傳送到 Hsm 鍵保存庫服務中，而不需離開 HSM 邊界的索引鍵︰

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

如何產生此 BYOK 套件的詳細指示，請參閱[如何使用與 Azure 金鑰保存庫 HSM-Protected 鍵](key-vault-hsm-protected-keys.md)。


## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>刪除索引鍵保存庫相關聯的按鍵和密碼

如果您不再需要金鑰保存庫的索引鍵或其包含的密碼，您可以使用 azure keyvault 的 [刪除] 命令來刪除重要保存庫︰

    azure keyvault delete --vault-name 'ContosoKeyVault'

或者，您可以刪除整個 Azure 資源] 群組中，包括主要保存庫包含該群組中的其他資源︰

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>其他 Azure 跨平台命令列介面命令

您可能會很適合用於管理 Azure 金鑰保存庫其他命令。

這個命令可列出表格式顯示的所有按鍵以及選取的內容︰

    azure keyvault key list --vault-name 'ContosoKeyVault'

這個命令會顯示完整的指定索引鍵屬性的清單︰

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

這個命令可列出表格式顯示所有私人的名稱和選取的內容︰

    azure keyvault secret list --vault-name 'ContosoKeyVault'

以下是如何移除特定索引鍵的範例︰

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

以下是如何移除特定的私人的範例︰

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>後續步驟

程式設計參照，請參閱[Azure 金鑰保存庫開發人員指南](key-vault-developers-guide.md)。
