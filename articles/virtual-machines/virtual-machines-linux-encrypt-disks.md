<properties
   pageTitle="加密磁碟上 Linux VM |Microsoft Azure"
   description="如何加密磁碟上 Linux VM 使用 Azure CLI 和資源管理員部署模型"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/11/2016"
   ms.author="iainfou"/>

# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli"></a>加密磁碟上使用 Azure CLI Linux VM
增強的虛擬機器 (VM) 的安全性和法規遵循，可以在其餘加密中 Azure 虛擬磁碟。 使用加密金鑰保護 Azure 金鑰保存庫中的加密磁碟。 您控制下列的加密金鑰，並可稽核使用。 本文詳細說明如何加密上使用 Azure CLI 和資源管理員部署模型 Linux VM 的虛擬磁碟。


## <a name="quick-commands"></a>快速命令
若要快速完成工作，下列區段詳細資料基底命令加密上您 VM 的虛擬磁碟。 更詳細的資訊和每個步驟的內容可找到其餘的文件中，[從這裡開始](#overview-of-disk-encryption)。

您需要[最新的 Azure CLI](../xplat-cli-install.md)安裝及登入的資源管理員模式，如下所示︰

```
azure config mode arm
```

在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含`myResourceGroup`， `myKeyVault`，及`myVM`。

首先，讓您 Azure 訂閱中的 Azure 金鑰保存庫提供者，並建立資源群組。 下列範例會建立資源群組名稱`myResourceGroup`中`WestUS`位置︰

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

建立 Azure 的金鑰保存庫。 下列範例會建立名為金鑰保存庫`myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

在您的金鑰保存庫中建立密碼編譯鍵，並啟用磁碟加密。 下列範例會建立名為`myKey`:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

建立用於處理驗證與交換的加密金鑰從金鑰保存庫中的 Azure Active Directory 端點。 `--home-page`和`--identifier-uris`不需要實際舉例來說地址。 最高層級的安全性，用戶端密碼應該使用的而不是密碼。 Azure CLI 目前無法產生用戶端密碼。 用戶端密碼只能產生 Azure 入口網站中。 下列範例會建立具名的 Azure Active Directory 端點`myAADApp`，且使用的密碼`myPassword`。 指定您自己的密碼如下所示︰

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

請注意`applicationId`上述命令輸出所示。 下列步驟中使用此應用程式識別碼︰

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

將現有的 VM 資料磁碟。 下列範例會資料磁碟命名 vm `myVM`:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

檢閱您的金鑰保存庫，以及您建立的鍵的詳細資料。 您需要金鑰保存庫識別碼、 URI 與索引鍵的最後一個步驟的 URL。 下列範例會檢閱詳細資料的命名鍵保存庫`myKeyVault`和名為`myKey`:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

加密磁碟，如下所示，輸入整個自己參數名稱︰

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Azure CLI 不在加密程序提供詳細的錯誤。 如需詳細的疑難排解資訊，請檢閱`/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`。 為之前的命令有多個變數，您可能會不會收到太多可用來指示程序失敗的原因的完整命令範例應，如下所示︰

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

最後，檢視加密的狀態] 以確認現在經過加密虛擬磁碟。 下列範例會檢查是否有的名稱為 VM 狀態`myVM`中`myResourceGroup`資源群組︰

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>磁碟加密概觀
虛擬磁碟上 Linux Vm 加密在使用[dm 窖](https://wikipedia.org/wiki/Dm-crypt)的其餘部分。 有免費的加密中 Azure 虛擬磁碟。 加密金鑰儲存於 Azure 金鑰保存庫使用軟體保護]，或您可以匯入或產生經過認證、 可 FIPS 140-2 第 2 層級標準硬體安全性模組 (Hsm) 您鍵。 保留下列的加密金鑰控制，並可稽核使用。 下列的加密金鑰用來加密並解密附加至您 VM 的虛擬磁碟。 Azure Active Directory 端點提供 Vm 可開啟和關閉時發出這些加密金鑰安全機制。

加密 VM 的程序如下所示︰

1. Azure 鍵保存庫中建立加密金鑰。
2. 設定密碼編譯金鑰，可用來加密磁碟。
3. 若要從 Azure 金鑰保存庫讀取加密金鑰，建立使用適當的權限的 Azure Active Directory 端點。
4. 發佈加密虛擬磁碟，指定的 Azure Active Directory 結束點及適當的加密金鑰的使用命令。
5. Azure Active Directory 端點要求的必要的加密金鑰從 Azure 金鑰保存庫。
6. 使用所提供的密碼編譯索引鍵，來加密虛擬磁碟。


## <a name="supporting-services-and-encryption-process"></a>支援服務及加密的程序
磁碟加密依賴以下的額外元件︰

- **Azure 鍵保存庫**-用來保護加密金鑰和機密用於磁碟加密/解密程序。 
  - 如果有的話，您可以使用現有的 Azure 金鑰保存庫。 您沒有所鍵保存庫加密磁碟。
  - 若要管理的界限及索引鍵的可見度分隔，您可以建立專用的金鑰保存庫。
- **Azure Active Directory** -控點的所需的加密金鑰和驗證要求動作的安全交換。 
  - 您通常可以使用現有的 Azure Active Directory 執行個體，為儲存您的應用程式。 
  - 應用程式是多個鍵保存庫和虛擬機器服務要求，並取得發出適當的加密金鑰端點。 您沒有開發的整合與 Azure Active Directory 的實際應用程式。


## <a name="requirements-and-limitations"></a>需求和限制
支援的案例和需求磁碟加密︰

- 下列 Linux 伺服器 Sku-Ubuntu、 CentOS、 SUSE 和 SUSE Linux Enterprise Server (SLES) 及紅色角色企業 Linux。
- 所有資源 （例如金鑰保存庫、 儲存帳戶和 VM） 都必須在同一個 Azure 地區碼和訂閱。
- 標準 A、 D、 DS、 G 和 GS 系列 Vm。

磁碟加密目前不支援在以下情況︰

- 基本層 Vm。
- 建立使用傳統部署模型 Vm。
- 停用 Linux Vm OS 磁碟加密。
- 更新已加密的 Linux VM 的加密金鑰。


## <a name="create-the-azure-key-vault-and-keys"></a>建立 Azure 金鑰保存庫與索引鍵
若要完成本指南的其餘部分，您需要[最新的 Azure CLI](../xplat-cli-install.md)安裝及登入的資源管理員模式，如下所示︰

```
azure config mode arm
```

整個命令範例，取代所有範例參數您自己的名稱、 位置及關鍵值。 下列範例使用的慣例`myResourceGroup`， `myKeyVault`，`myAADApp`等。

第一個步驟是，建立 Azure 金鑰保存庫來儲存您的加密金鑰。 索引鍵、 機密資料，或是要安全地實作這些應用程式與服務的密碼，可以儲存 azure 金鑰保存庫。 虛擬磁碟加密中，您可以使用金鑰保存庫來儲存加密金鑰用來加密或解密虛擬磁碟。 

啟用 Azure 金鑰保存庫中的提供者 Azure 訂閱，然後建立資源群組]。 下列範例會建立資源群組名稱`myResourceGroup`中`WestUS`位置︰

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

內含加密金鑰與相關聯的計算資源，例如儲存和 VM 本身 Azure 金鑰保存庫必須位於相同的區域。 下列範例會建立名為 Azure 金鑰保存庫`myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

您可以儲存加密金鑰使用本軟體或硬體安全性模型 (HSM) 保護。 使用的是 HSM 需要金鑰保存庫進階版。 有其他成本] 以建立索引鍵保存庫，而非標準儲存軟體保護的按鍵的按鍵保存庫進階版。 若要建立索引鍵保存庫進階版，請在上述步驟新增`--sku Premium`命令。 下列範例會使用軟體保護的金鑰，因為我們所建立的標準的金鑰保存庫。 

兩種保護模型，都必須授與存取要求加密金鑰 VM 開機解密虛擬磁碟時 Azure 平台。 建立加密金鑰內您鍵保存庫，然後啟用虛擬磁碟加密搭配使用。 下列範例會建立名為`myKey`，讓磁碟加密︰

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式
當虛擬磁碟加密或解密時，您可以使用端點來處理驗證和交換的加密金鑰從金鑰保存庫。 此端點，Azure Active Directory 應用程式，可讓要求適當的加密金鑰代表 VM Azure 的平台。 雖然許多組織具有專用的 Azure Active Directory 的目錄，預設的 Azure Active Directory 執行個體是用於您的訂閱。

當您不需要建立一個完整的 Azure Active Directory 應用程式，`--home-page`和`--identifier-uris`不需要在下列範例中的參數是實際舉例來說地址。 下列範例也會指定密碼為基礎的密碼，而不是從 Azure 入口網站中的產生金鑰。 這次請為無法從 Azure CLI 執行產生金鑰。 

建立您的 Azure Active Directory 應用程式。 下列範例會建立應用程式名稱為`myAADApp`，且使用的密碼`myPassword`。 指定您自己的密碼如下所示︰

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

請記下`applicationId`的輸出中傳回上述命令。 部分其餘的步驟中使用此應用程式識別碼。 接下來，請建立服務主要名稱 (SPN) 應用程式的可存取您的環境。 若要順利加密或解密虛擬磁碟，金鑰保存庫中儲存的加密金鑰權限必須設定為允許讀取索引鍵的 Azure Active Directory 應用程式。 

建立 SPN 並設定適當的權限，如下所示︰

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>新增虛擬磁碟並檢視加密的狀態
實際加密部分虛擬磁碟，可讓您新增至現有的 VM 的磁碟。 5 Gb 資料將磁碟新增至現有的 VM，如下所示︰

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

目前未加密虛擬磁碟。 請檢閱您 VM 的加密的目前狀態，如下所示︰

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>加密虛擬磁碟
現在加密虛擬磁碟，您將所有的上一個元件︰

1. 指定的 Azure Active Directory 應用程式和密碼。
2. 指定儲存的中繼資料的加密磁碟機碼保存庫。
3. 指定要使用的實際加密及解密加密金鑰。
4. 指定您是否要加密 OS 磁碟、 資料或所有。

可讓您 Azure 金鑰保存庫，以及您建立的您需要金鑰保存庫識別碼，URI，然後重要的最後一個步驟中的 [URL 的鍵檢閱詳細資料︰

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

加密您使用的輸出的虛擬磁碟`azure keyvault show`和`azure keyvault key show`命令，如下所示︰

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

上述命令有多個變數，如下列範例是參照的 [完成] 命令︰

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Azure CLI 不在加密程序提供詳細的錯誤。 如需詳細的疑難排解資訊，請檢閱`/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`要加密的 VM 上。

最後，可讓檢視加密的狀態] 以確認您的虛擬磁碟有現在已加密︰

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>新增其他資料磁碟
一旦您有加密資料磁碟，可以稍後新增其他虛擬磁碟到您 VM，也會將其加密。 當您執行`azure vm enable-disk-encryption`命令、 遞增順序使用版本`--sequence-version`參數。 此順序版本參數可讓您執行的相同 VM。

例如，可讓您 vm 新增第二個虛擬磁碟，如下︰

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

重新執行命令，來加密虛擬磁碟，此時間新增`--sequence-version`參數，以及遞增我們的第一次執行中的值，如下所示︰

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>後續步驟

- 如需有關管理 Azure 金鑰保存庫，包括刪除加密金鑰與保存庫，請參閱[管理金鑰保存庫使用 CLI](../key-vault/key-vault-manage-with-cli.md)。
- 如需有關磁碟加密，例如準備加密的自訂 VM 上傳至 Azure，請參閱[Azure 磁碟加密](../security/azure-security-disk-encryption.md)。