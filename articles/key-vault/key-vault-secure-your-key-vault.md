<properties
    pageTitle="保護您的主要保存庫 |Microsoft Azure"
    description="管理管理保存庫及索引鍵和密碼的重要保存庫的存取權限。 驗證與授權模型金鑰保存庫及如何保護您的主要保存庫"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/07/2016"
    ms.author="ambapat"/>


# <a name="secure-your-key-vault"></a>保護您的主要保存庫

Azure 鍵保存庫是雲端服務，保護加密金鑰和雲端應用程式 （例如憑證的連接字串、 密碼） 的密碼。 由於此資料機密，而您想要安全存取您的主要保存庫，以便只允許經授權的要徑商務應用程式和使用者可以存取金鑰保存庫。 本文提供的金鑰保存庫存取模型概觀、 說明驗證與授權，並說明如何安全存取您的雲端應用程式以範例金鑰保存庫。

## <a name="overview"></a>概觀

透過兩種不同的介面控制的存取鍵保存庫︰ 管理平面與資料平面。 兩個飛機適當的驗證及授權是必要之前來電 （使用者或應用程式） 可以存取金鑰保存庫。 驗證用來建立的身分識別的來電，而授權決定哪些來電者可以執行的作業。

驗證資料平面和管理平面使用 Azure Active Directory。 如需授權，管理平面使用角色型存取控制 (RBAC) 資料平面使用金鑰保存庫存取原則。

以下是概觀所涵蓋的主題︰

[使用 Azure Active Directory 驗證](#authentication-using-azure-active-direcrory)本節說明如何來電驗證與 Azure Active Directory 存取透過管理平面與資料平面金鑰保存庫。 

[管理平面與資料平面](#management-plane-and-data-plane)-管理平面與資料平面是用來存取您的主要保存庫的兩個 access 飛機。 每個 access 平面支援特定的作業。 本節將描述存取端點，作業受支援，而且存取控制每個平面所使用的方法。 

[管理平面存取控制](#management-plane-access-control)-我們允許存取管理平面作業使用角色型存取控制此區段中。

[資料平面存取控制](#data-plane-access-control)-本節將說明如何使用金鑰保存庫存取原則控制資料平面存取。

[範例](#example)此範例說明如何設定存取控制您允許三個不同的小組 （小組安全性、 開發人員/運算子和員） 執行開發、 管理和監視 Azure 中的應用程式的特定工作的按鍵保存庫。


## <a name="authentication-using-azure-active-directory"></a>使用 Azure Active Directory 驗證

當您建立關鍵保存庫中的 Azure 訂閱時，它會自動與訂閱的 Azure Active Directory 租用戶相關聯。 所有的來電者 （使用者和應用程式） 必須註冊存取此按鍵保存庫此租用戶中。 應用程式或使用者必須驗證與 Azure Active Directory 存取金鑰保存庫。 適用於管理平面與資料平面存取。 在這兩種情況下，應用程式，可以存取金鑰保存庫中的兩種方法︰

-  **使用者 + 應用程式存取**-這通常是存取金鑰保存庫代表登入使用者的應用程式。 Azure PowerShell、 Azure 入口網站是範例此類型的存取權。 有兩種方式授與存取權的使用者︰ 授與存取權的使用者，讓他們可以從任何應用程式存取金鑰保存庫及其他的方法是使用特定應用程式 （又稱為複合身分識別） 時，只授與使用者存取金鑰保存庫的其中一個方法是。 
-  **應用程式存取**-執行精靈服務，背景工作等的應用程式。應用程式的身分識別授與存取金鑰保存庫。

在這兩種應用程式，應用程式使用任何[支援的驗證方法](../active-directory/active-directory-authentication-scenarios.md)的 Azure Active Directory 驗證，並取得權杖。 使用驗證方法取決於應用程式類型。 然後應用程式會使用此權杖，並將 REST API 邀請傳送到金鑰保存庫。 若管理平面存取要求傳閱透過 Azure 資源管理員結束點。 存取資料平面，鍵直接應用程式與地窖結束點。 在[整個驗證流程](../active-directory/active-directory-protocols-oauth-code.md)，請參閱更多詳細資料。 

資源名稱的應用程式要求權杖是管理平面或資料平面是否存取應用程式而有所不同。 因此資源名稱為 [管理平面或資料平面端點在更新版本的區段中，根據 Azure 環境表所述。

管理及資料平面驗證一個單一機制具有專屬的優點︰

- 組織集中可以控制組織中的所有按鍵保存庫的存取
- 如果使用者離開時，他們立即無法存取在組織中的所有按鍵保存庫
- 組織可以自訂驗證透過 Azure Active Directory （例如，以提高安全性啟用 「 多重因素驗證 」） 中的選項

## <a name="management-plane-and-data-plane"></a>管理平面與資料平面

Azure 鍵保存庫是可透過 Azure 資源管理員部署模型 Azure 服務。 當您建立索引鍵保存庫時，您會收到虛擬容器內的您可以建立索引鍵、 機密、 憑證等其他物件。 然後您可以存取您使用管理平面與資料平面執行特定的作業的金鑰保存庫。 管理平面介面用來管理您主要的保存庫本身，例如建立、 刪除、 更新金鑰保存庫屬性及設定資料平面存取原則。 資料平面介面用來新增、 刪除、 修改及使用索引鍵、 機密資料，並且儲存在您的主要保存庫中的憑證。

管理平面與資料平面介面存取到不同的結束點 （請參閱資料表）。 在資料表中的第二欄說明這些 Azure 的不同環境中的端點 DNS 名稱。 第三欄說明您可以從每個 access 平面執行的作業。 每個 access 平面也會有自己的存取控制機制︰ 管理平面存取控制是使用 Azure Resource Manager Role-Based 存取控制 (RBAC) 進行設定，時的資料平面存取控制使用金鑰保存庫存取原則設定。

| Access 平面 | 存取端點 | 作業 | 存取控制機制|
|--------------|------------------|--------------------|--------|
| 管理平面|**全域管理員︰**<br> management.azure.com:443<br><br> **Azure china （中國):**<br> management.chinacloudapi.cn:443<br><br> **Azure 美國政府︰**<br> management.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> management.microsoftazure.de:443 | 建立/已讀取/更新/刪除重要保存庫 <br> 設定金鑰保存庫的存取原則<br>設定標籤按鍵保存庫 | Azure 資源管理員角色型存取控制 (RBAC) |
| 資料平面 | **全域管理員︰**<br> &lt;保存庫名稱&gt;。 vault.azure.net:443<br><br> **Azure china （中國):**<br> &lt;保存庫名稱&gt;。 vault.azure.cn:443<br><br> **Azure 美國政府︰**<br> &lt;保存庫名稱&gt;。 vault.usgovcloudapi.net:443<br><br> **Azure 德國︰**<br> &lt;保存庫名稱&gt;。 vault.microsoftazure.de:443 | 索引鍵︰ 解密、 加密，UnwrapKey、 WrapKey，驗證、 登入，取得清單、 更新、 建立、 匯入、 刪除，請備份、 還原<br><br> 用密碼︰ 取得，清單中，設定，刪除 | 索引鍵保存庫存取原則|

管理平面存取平面與資料控制項獨立地運作。 例如，如果您想要授與應用程式存取權，以使用中索引鍵保存庫鍵，您只需要授與資料平面存取權限使用金鑰保存庫存取原則，沒有管理平面存取所需的這個應用程式。 相反地，是否您想讓使用者可以讀取保存庫屬性和標籤，但沒有任何存取鍵、 秘密或憑證，您可以授與此使用者，「 讀取 」 權限使用 RBAC 並沒有存取資料平面資格。

## <a name="management-plane-access-control"></a>管理平面存取控制

管理平面組成影響金鑰保存庫本身的作業。 例如，您也可以建立或刪除重要保存庫。 您可以在訂閱中取得保存庫的清單。 您可以擷取金鑰保存庫屬性 （例如 SKU，標籤)，並設定主要保存庫存取原則，以控制使用者與索引鍵和金鑰保存庫中的機密可以存取的應用程式。 管理平面存取控制使用 RBAC。 請參閱透過管理平面前一節中的資料表，才能執行的重要保存庫作業的完整清單。 

### <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)
每個 Azure 的訂閱有 Azure Active Directory。 使用者、 群組和從這個目錄的應用程式可以授與存取權管理 Azure 訂閱中使用 Azure 資源管理員部署模型的資源。 此類型的存取控制被指角色型存取控制 (RBAC)。 若要管理存取權，您可以使用[Azure 入口網站](https://portal.azure.com/)、 [Azure CLI 工具](../xplat-cli-install.md)、 [PowerShell](../powershell-install-configure.md)或[Azure 資源管理員 REST Api](https://msdn.microsoft.com/library/azure/dn906885.aspx)。

Azure 資源管理員模型中，您將關鍵保存庫資源群組，並控制在 access 中建立此按鍵保存庫的管理平面使用 Azure Active Directory。 例如，您可以授與使用者或群組能夠管理特定的資源群組中的關鍵保存庫。

您可以指派適當 RBAC 角色授與存取使用者、 群組和應用程式在特定的範圍。 例如，如果授與存取權的使用者管理金鑰保存庫，請在特定範圍此使用者指派預先定義的角色 」 鍵保存庫參與者 」。 在此情況下訂閱、 [資源] 群組中或只是特定的金鑰保存庫，就會範圍。 在訂閱層級指派角色適用於所有的資源群組與該訂閱中的資源。 在 [資源群組層級指派角色適用於該資源] 群組中的所有資源。 指派資源的特定角色僅適用於該資源。 有數種預先定義的角色 (請參閱[RBAC︰ 內建的角色](../active-directory/role-based-access-built-in-roles.md))，及預先定義的角色不符合您的需求，如果您也可以定義自己的角色。

>[AZURE.IMPORTANT] 請注意，是否使用者有權限 (RBAC) 鍵保存庫管理平面，她可以授予自己的參與者存取資料平面，以設定金鑰保存庫存取原則，這會控制資料平面存取。 因此，建議緊密控制可存取您金鑰保存庫，以確保授權的人員可以存取及管理金鑰保存庫、 鍵、 機密資料，以及憑證 「 參與者 」。

## <a name="data-plane-access-control"></a>資料平面存取控制

索引鍵保存庫資料平面組成影響金鑰保存庫，例如鍵、 機密資料，以及憑證的物件的作業。  包括的鍵作業例如建立、 匯入、 更新、 清單、 備份和還原鍵加密的作業，例如登、 驗證、 加密、 解密、 自動換行，及列名，並設定標記與索引鍵的其他屬性。 同樣的如其包含的密碼，取得，設定清單，請刪除。

設定的金鑰保存庫的存取原則授與資料的平面存取。 使用者、 群組或應用程式必須擁有參與者] 權限 (RBAC) 管理平面的重要的保存庫，可以設定該金鑰保存庫的存取原則。 使用者、 群組或應用程式可以授與存取金鑰保存庫中執行特定鍵或機密資料的作業。 索引鍵保存庫支援金鑰保存庫最多 16 存取原則項目。 建立 Azure Active Directory 安全性群組，並將使用者新增至資料平面權限授予金鑰保存庫的多個使用者的群組。

### <a name="key-vault-access-policies"></a>索引鍵保存庫存取原則

索引鍵保存庫存取原則分別授與權限金鑰、 密碼和憑證。 例如，您可以授與使用者存取權，只按鍵，但沒有權限的機密資料。 不過，權限存取金鑰或機密資料或憑證，就會保存庫層級。 換句話說，金鑰保存庫存取原則不支援物件層級權限。 您可以使用[Azure 入口網站](https://portal.azure.com/)、 [Azure CLI 工具](../xplat-cli-install.md)、 [PowerShell](../powershell-install-configure.md)或[金鑰保存庫管理 REST Api](https://msdn.microsoft.com/library/azure/mt620024.aspx)來設定金鑰保存庫的存取原則。

>[AZURE.IMPORTANT] 請注意金鑰保存庫存取原則套用保存庫層級。 例如時建立及刪除索引鍵的權限授與使用者，她就可以在該金鑰保存庫中執行這些作業所有按鍵。

## <a name="example"></a>範例

例如，假設您開發的憑證用於 SSL，Azure 儲存資料的儲存空間，並使用 RSA 2048 位元金鑰登作業的應用程式。 假設此應用程式執行 VM （或 VM 縮放比例設定）。 您可以使用按鍵保存庫儲存的應用程式機密資料，但金鑰保存庫儲存啟動安裝的應用程式所用來與 Azure Active Directory 驗證憑證。

因此，以下是您所有的索引鍵和儲存在金鑰保存庫中的機密資料摘要。

- **SSL 憑證**-用於 SSL
- **儲存金鑰**-用來儲存帳戶存取
- **RSA 2048 位元索引鍵**-用來登作業
- **啟動安裝憑證**-用來驗證 Azure Active Directory，來存取擷取的儲存空間鍵，並使用 RSA 金鑰進行簽章的金鑰保存庫。

現在讓我們來符合管理、 部署及稽核此應用程式的人員。 在此範例中，我們會使用下列三種角色。

- **安全性小組**-這些通常是從 」 的 CSO （人力資源保全人員） 的 office 」 的 IT 人員或相當於、 負責適當的妥善保存的機密資料，例如 SSL 憑證，RSA 金鑰用於登入資料庫，儲存帳戶金鑰的連接字串。
- **開發人員/運算子**-這些是開發此應用程式，然後將其部署 Azure 中的人員。 一般而言，他們不屬於安全性小組，及因此他們應該無法存取任何機密資料，例如 SSL 憑證，RSA 金鑰，但在部署的應用程式應該存取這些。
- **稽核者**-這通常是一組不同的開發人員和一般的 IT 人員隔離的人員。 其責任是檢閱適當的使用和維護的憑證、 鍵等，並確保資料安全性標準的規範。 

有一個多個角色，超出此應用程式，但相關的方法如下所述的範圍，就會訂閱 （或資源群組） 系統管理員。 訂閱的管理員可設定的安全性小組初始的存取權限。 以下我們假設訂閱系統管理員具有資源群組中的所有資源所需此應用程式 reside 授與存取權安全性小組。

現在我們來看看這個應用程式的內容中的每一個角色執行什麼動作。

- **安全性小組**
    - 建立索引鍵保存庫
    - 開啟鍵地窖記錄
    - 新增索引鍵/機密資料
    - 建立備份損毀修復索引鍵
    - 設定金鑰保存庫存取原則，授與權限給使用者和應用程式，以執行特定的作業
    - 定期就緒按鍵/機密資料
- **開發人員/運算子**
    - 取得要參照及 SSL 憑證 （憑證碼） 儲存鍵 （私人 URI） 並登入安全性小組金鑰 (金鑰 URI)
    - 開發和部署以程式控制方式存取索引鍵和機密應用程式
- **稽核者**
    - 檢閱使用記錄檔，以確認適當的金鑰/私人使用與資料安全性標準的規範

現在讓我們來看看何種重要保存庫的存取權限所需的每一個角色 （與應用程式） 執行其指派的工作。 

| 使用者角色    | 管理平面權限 | 資料平面權限 |
|--------------|------------------------------|------------------------|
|安全性小組|索引鍵保存庫參與者|索引鍵︰ 備份、 建立、 刪除、 取得匯入、 清單、 還原 <br> 機密資料︰ 所有|
|開發人員/運算子| 索引鍵保存庫部署權限，讓他們部署的 Vm 可以從主要的保存庫擷取機密資料 | 無 |
|稽核者| 無 | 索引鍵︰ 清單<br>機密資料︰ 清單|
|應用程式| 無 | 索引鍵︰ 符號<br>機密資料︰ 取得 |

>[AZURE.NOTE] 稽核者需要清單的索引鍵和機密資料的權限，讓他們可以檢查屬性的索引鍵和密碼，不會發出的記錄，例如 [標籤]，啟動與到期日的日期。

除了金鑰保存庫的權限，所有的三個角色也需要其他資源的存取權。 例如，若要能夠部署 Vm （或 Web 應用程式等）。開發人員/運算子也需要 「 參與者 」 存取這些資源類型。 稽核者需要金鑰保存庫記錄的儲存位置的儲存空間帳戶的 「 讀取 」 權限。

本文的焦點會保護您的主要保存庫存取，因為我們只說明屬於本主題的相關部分，並略過有關部署憑證、 等以程式控制方式存取索引鍵和密碼。這些詳細資料已涵蓋在其他位置。 部署至 Vm 金鑰保存庫中儲存憑證涵蓋的[部落格文章](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)，請與[程式碼範例](https://www.microsoft.com/download/details.aspx?id=45343)使用說明如何使用啟動安裝憑證驗證來存取金鑰保存庫 Azure AD。

大部分的存取權限授與使用 Azure 入口網站，但若要授與微調權限，您可能需要使用 PowerShell 的 Azure （或 Azure CLI） 達到所要的結果。 

假設下列 PowerShell 程式碼片段︰

- Azure Active Directory 系統管理員建立安全性群組，代表三種角色，也就是 Contoso 安全性小組，Contoso 應用程式 Devops，Contoso 應用程式的稽核。 系統管理員具有也會將使用者加到其所屬的群組。

- **ContosoAppRG**是資源群組的所有資源的都所在位置。 **contosologstorage**是記錄的儲存位置。 

- 用於金鑰保存庫記錄**contosologstorage**金鑰保存庫**ContosoKeyVault**和儲存帳戶必須使用相同的 Azure 位置


第一次訂閱系統管理員將 「 重要保存庫參與者 」 和 「 使用者存取系統管理員] 角色指派給安全性小組。 這個選項可讓安全性小組管理存取權給其他資源，並管理 ContosoAppRG 上的 [資源] 群組中的關鍵保存庫。

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

以下指令碼安全性小組如何建立索引鍵保存庫記錄，並設定其他角色與應用程式的存取權限設定。 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionToKeys backup,create,delete,get,import,list,restore -PermissionToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devlopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $role

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionToKeys list -PermissionToSecrets list
```

自訂的角色所定義，才可以指派到訂閱建立 ContosoAppRG 資源群組的位置。 如果相同的自訂角色用於其他專案中其他訂閱，它的範圍可讓新增多個訂閱。

「 部署/動作 」 權限開發人員/運算子的自訂的角色指派限定為資源群組。 如此一來只 Vm 建立 「 ContosoAppRG 」 上的 [資源] 群組中，會收到密碼 （SSL 憑證和憑證啟動安裝）。 開發/運算子小組成員建立其他資源] 群組中的任何 Vm 無法取得這些機密，即使他們知道密碼的 Uri。

此範例說明簡單的案例。 實際案例可能較為複雜，您可能需要調整根據您的需求您金鑰保存庫的權限。 例如，在此範例中，我們假設的安全性小組會提供索引鍵和私人參照 （Uri 和憑證碼） 的開發人員/運算子小組必須在其應用程式中的參考。 因此，他們不需要任何資料平面存取權授與開發人員/運算子。 此外，請注意，此範例中，著重於保護您的主要保存庫。 保護[您的 Vm](https://azure.microsoft.com/services/virtual-machines/security/)、[儲存帳戶](../storage/storage-security-guide.md)及其他 Azure 資源太應該指定類似的考量。

>[AZURE.NOTE] 注意︰ 此範例顯示如何金鑰保存庫 access 就會鎖定生產環境中。 開發人員應該有自己的訂閱或 resourcegroup 在他們擁有完整權限管理其保存庫、 Vm 及儲存帳戶他們開發應用程式的位置。


## <a name="resources"></a>資源

-   [Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md)

    本文說明 Azure Active Directory 角色型存取控制和運作方式。

-   [RBAC︰ 內建的角色](../active-directory/role-based-access-built-in-roles.md)

    本文詳細說明所有的內建角色 RBAC 提供。

-   [了解資源管理員部署及傳統的部署](../resource-manager-deployment-model.md)

    本文說明的資源管理員部署及傳統部署模型，並說明使用資源管理員] 及 [資源群組的好處

-    [管理角色型存取控制使用 Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)

     本文說明如何管理與 PowerShell 的 Azure 的角色型存取控制

-   [管理角色型存取控制以 REST API](../active-directory/role-based-access-control-manage-access-rest.md)

    本文說明如何使用 REST API 來管理 RBAC。

-   [Microsoft Azure 從 Ignite 角色型存取控制](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    這是連結至頻道 9 上的視訊從 2015 MS Ignite 會議。 在這個工作階段，他們談論存取 [管理和 Azure 中的報告權限及探索安全存取使用 Azure Active Directory Azure 訂閱的最佳做法。

-   [授權使用 OAuth 2.0 和 Azure Active Directory 的 web 應用程式存取](../active-directory/active-directory-protocols-oauth-code.md)

    本文將說明與 Azure Active Directory 驗證完成 OAuth 2.0 流量。

-   [管理 REST Api 金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    這份文件是以程式設計方式，管理您的主要保存庫 REST Api 的參照，包括設定金鑰保存庫存取原則。

-   [索引鍵保存庫 REST Api](https://msdn.microsoft.com/library/azure/dn903609.aspx)

    連結至金鑰保存庫 REST API 參考文件。

-   [按鍵存取控制](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

    連結至鍵存取控制項參照文件。

-   [私人存取控制](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

    連結至鍵存取控制項參照文件。

-   使用 PowerShell 的[設定](https://msdn.microsoft.com/library/mt603625.aspx)，並[移除](https://msdn.microsoft.com/library/mt619427.aspx)金鑰保存庫存取原則

    若要參照的 PowerShell cmdlet 來管理金鑰保存庫存取原則的文件的連結。

## <a name="next-steps"></a>後續步驟

系統管理員的取得開始教學課程，請參閱[開始使用 Azure 金鑰保存庫](key-vault-get-started.md)。

如需記錄的索引鍵保存庫使用方式的詳細資訊，請參閱[Azure 金鑰保存庫的記錄](key-vault-logging.md)。

如需有關如何使用 Azure 金鑰保存庫中的索引鍵和機密資料的詳細資訊，請參閱[關於索引鍵和密碼](https://msdn.microsoft.com/library/azure/dn903623.aspx)。

如果您有金鑰保存庫的相關問題，請造訪[Azure 保存庫論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
