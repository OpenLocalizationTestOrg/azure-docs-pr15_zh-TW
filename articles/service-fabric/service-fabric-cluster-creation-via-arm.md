
<properties
   pageTitle="建立使用 Azure 資源管理員安全服務布料的轉印圖樣叢集 |Microsoft Azure"
   description="本文將說明如何設定 [安全的服務布料的轉印圖樣叢集 Azure 用戶端驗證使用 Azure 資源管理員、 Azure 金鑰保存庫和 Azure Active Directory (AAD) 中。"
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>建立使用 Azure 資源管理員 Azure 服務布料的轉印圖樣叢集

> [AZURE.SELECTOR]
- [Azure 資源管理員](service-fabric-cluster-creation-via-arm.md)
- [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)

這是會引導您完成設定安全 Azure 服務布料的轉印圖樣叢集 Azure 使用 Azure 資源管理員中的步驟的逐步指南。 本指南逐步引導您進行下列步驟︰

 - 設定金鑰保存庫管理叢集和應用程式的安全性的按鍵。
 - 建立安全的叢集 Azure 與 Azure 資源管理員中。
 - 驗證叢集管理使用者與 Azure Active Directory (AAD)。

安全叢集是叢集管理作業，可防止未經授權的存取包括部署、 升級，以及刪除應用程式、 服務和其包含的資料。 不安全叢集是叢集任何人都可以隨時連線至，並執行管理作業。 雖然您可以建立不安全叢集，建議**建立安全叢集**。 不安全叢集**無法稍後保護**-必須建立新的叢集。

概念也適用於建立安全叢集，叢集是 Linux 叢集或 Windows 叢集。 其他資訊及協助指令碼建立安全 Linux 叢集，請參閱 <<c0>建立安全叢集 linux

## <a name="log-in-to-azure"></a>登入 Azure
本指南使用[PowerShell 的 Azure][azure-powershell]。 時開始新的 PowerShell 工作階段，請登入您的 Azure 帳戶，然後選取您的訂閱，然後再執行 Azure 的命令。

登入您的 azure 帳戶︰

```powershell
Login-AzureRmAccount
```

選取您的訂閱︰

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>設定金鑰保存庫

本節將引導建立索引鍵保存庫中 Azure 服務布料的轉印圖樣叢集和服務布料的轉印圖樣應用程式。 索引鍵保存庫上的完整指南，請參閱[金鑰保存庫快速入門指南][key-vault-get-started]。

服務布料的轉印圖樣使用安全叢集，提供應用程式的安全性功能的 X.509 憑證。 Azure 鍵保存庫用來管理服務布料的轉印圖樣叢集 Azure 中的憑證。 Azure 中部署叢集時，負責建立服務布料的轉印圖樣叢集 Azure 資源提供者從金鑰保存庫擷取憑證及叢集 Vm 上加以安裝。

下圖說明鍵保存庫、 服務布料的轉印圖樣叢集，與使用時，它會建立一個叢集儲存在金鑰保存庫中的憑證 Azure 資源提供者之間的關係︰

![憑證安裝][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>建立資源群組

第一步是專為金鑰保存庫中建立資源群組。 建議您將金鑰保存庫置於自己資源的群組。 這個選項可讓您移除計算和儲存資源群組，包括具有服務布料的轉印圖樣叢集，而不遺失您索引鍵和機密資料的 [資源] 群組。 資源群組含有您鍵保存庫必須和叢集所使用的相同的區域。

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>建立索引鍵保存庫 

建立新的 [資源] 群組中的金鑰保存庫。 索引鍵保存庫**必須啟用以供部署**允許服務布料的轉印圖樣資源提供者，從其和安裝叢集節點上的取得憑證︰

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

如果您有現有的金鑰保存庫，您可以將它啟用以供部署使用 Azure CLI:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>將憑證新增至金鑰保存庫

憑證用於提供驗證及加密保護各式各樣的叢集和其應用程式服務布料的轉印圖樣。 如需有關憑證服務結構中的使用方式的詳細資訊，請參閱[服務布料的轉印圖樣叢集安全性案例][service-fabric-cluster-security]。

### <a name="cluster-and-server-certificate-required"></a>叢集和伺服器的憑證 （必要） 

這個憑證必須安全叢集並防止未經授權的存取。 它提供叢集安全性幾個方法︰
 
 - **叢集驗證︰**驗證叢集同盟節點的通訊。 可以證明這個憑證其身分識別的節點可以加入叢集。
 - **伺服器驗證︰**驗證管理用戶端叢集管理端點，好讓管理用戶端知道交談實數叢集。 這個憑證也提供 SSL HTTPS 管理 API 和服務布料的轉印圖樣總管透過 HTTPS。

若要提供這些目的，憑證必須符合下列需求︰

 - 憑證必須包含私密金鑰。
 - 憑證必須建立索引鍵的 exchange，匯出為個人資訊交換 (.pfx) 檔案。
 - 憑證的主體名稱必須符合用來存取服務布料的轉印圖樣叢集的網域。 需要 SSL 提供叢集的 HTTPS 管理結束點與服務布料的轉印圖樣總管此 matchng。 您無法取得 SSL 憑證從憑證授權單位 (CA)`.cloudapp.azure.com`網域。 您必須取得叢集的自訂網域名稱。 當您從 CA 要求憑證時，憑證的主體名稱必須符合叢集所用的自訂網域名稱。

### <a name="application-certificates-optional"></a>（選擇性） 的應用程式憑證

任何數目的額外的憑證可以安裝在叢集上的應用程式的安全性考量。 建立之前叢集，請考慮需要安裝在節點，例如憑證的應用程式的安全性案例︰

 - 加密和解密的應用程式的設定值
 - 複製期間加密跨節點的資料 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>設定格式化的 Azure 資源提供者使用的憑證

可以新增私人檔案 (.pfx)，並透過金鑰保存庫直接使用。 不過，Azure 資源提供者需要鍵儲存為 64 基本.pfx 包含特殊 JSON 格式編碼字串和私密金鑰密碼。 若要符合這些需求，金鑰必須置於 JSON 字串，然後儲存為金鑰保存庫中的*機密資料*。

若要讓此程序更容易，PowerShell 模組位於[上 GitHub][service-fabric-rp-helpers]。 請遵循下列步驟使用模組︰

  1. 請 repo 的全部內容下載到本機的目錄。 
  2. 匯入的模組 PowerShell 視窗中︰

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
`Invoke-AddCertToKeyVault`這個 PowerShell 模組中的命令會自動將憑證私密金鑰格式化為 JSON 字串，並將它上載至金鑰保存庫。 使用金鑰保存庫中加入叢集憑證和任何其他應用程式的憑證。 重複此步驟的任何其他您想要安裝叢集的憑證。

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

上述字串是設定服務布料的轉印圖樣叢集資源管理員範本所安裝的節點驗證、 管理端點安全性和驗證憑證和任何其他應用程式的安全性功能使用 X.509 憑證的所有按鍵保存庫先決條件。 此時，您現在應該 Azure 中會有下列設定︰

 - 索引鍵保存庫資源群組
   - 索引鍵保存庫
     - 叢集伺服器驗證憑證
     - 應用程式的憑證

## <a name="set-up-azure-active-directory-for-client-authentication"></a>設定用戶端驗證的 Azure Active Directory

AAD 可讓 （亦稱為租用戶） 的組織管理應用程式，可分為應用程式與網路基礎登入 UI 和原生的用戶端的經驗的應用程式的使用者存取權。 在此文件中，我們假設您已經建立之租用戶。 如果不是，請先閱讀[如何取得 Azure Active Directory 租用戶][active-directory-howto-tenant]。

服務布料的轉印圖樣叢集使用者提供管理功能，包括 web[服務布料的轉印圖樣檔案總管]的多個項目指向[service-fabric-visualizing-your-cluster]和[Visual Studio][service-fabric-manage-application-in-visual-studio]。 如此一來，您建立兩個 AAD 應用程式來控制存取叢集、 一個 web 應用程式和一原生應用程式。

若要簡化部分設定 AAD 與服務布料的轉印圖樣叢集所需的步驟，我們會建立一組的 Windows PowerShell 指令碼。

>[AZURE.NOTE] 您必須執行這些步驟*之前*，在指令碼預期叢集名稱及結束點的位置的情況下建立叢集，這些應該計劃的值，不項目，您已經建立。

1. [下載指令碼][sf-aad-ps-script-download]到您的電腦。

2. 以滑鼠右鍵按一下 zip 檔案、 選擇 [**內容**]，然後核取**解除封鎖**核取方塊及套用。

3. 展開的 zip 檔案。

4. 執行`SetupApplications.ps1`，提供 TenantId ClusterName，與 WebApplicationReplyUrl 做為參數。 例如︰

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    您可以藉由執行的 PowerShell 命令 '取得 AzureSubscription' 找到您**TenantId** 」。 這會顯示每個訂閱的**TenantId** 。

    **ClusterName**用來加上字首的指令碼建立 AAD 應用程式。 不需要一樣，只用來讓您可以將 AAD 成品對應到他們搭配使用的服務布料的轉印圖樣叢集更容易符合實際叢集名稱。

    **WebApplicationReplyUrl**是 AAD 傳回給您的使用者完成登入程序後的預設端點。 您應該服務布料的轉印圖樣總管端點設定這個叢集，依預設為︰

    https://&lt;cluster_domain&gt;: 19080/總管

    系統會提示您登入 AAD 租用戶具有系統管理權限的帳戶。 一旦您這麼做，指令碼進行來建立網站和代表服務布料的轉印圖樣叢集的原生應用程式。 如果您查看[Azure 傳統入口網站]中的租用戶的應用程式[azure-classic-portal]，您應該會看到兩個新的項目︰

    - *ClusterName*\_叢集
    - *ClusterName*\_用戶端

    當您在下一節中建立叢集 Azure 資源管理員範本所需 Json 所以讓視窗開啟 PowerShell 指令碼列印。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>建立服務布料的轉印圖樣叢集資源管理員範本

在此區段中，先前的 PowerShell 命令的輸出用於服務布料的轉印圖樣叢集資源管理員範本。

範本範例資源管理員可在[Azure 快速開始範本庫] GitHub][azure-quickstart-templates]。 這些範本可做為起點叢集範本。 

### <a name="create-the-resource-manager-template"></a>建立資源管理員範本

本指南使用[5 節點的安全叢集][service-fabric-secure-cluster-5-node-1-nodetype-wad]範例範本與範本參數。 下載`azuredeploy.json`和`azuredeploy.parameters.json`到您的電腦並開啟您最愛的文字編輯器中的兩個檔案。

### <a name="add-certificates"></a>新增憑證

憑證新增至叢集資源管理員範本參照包含憑證金鑰鍵保存庫。 建議的這些金鑰保存庫值會放置在資源管理員範本參數檔案保留資源管理員範本檔案可重複使用及部署的特定值的自由。

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>將所有的憑證新增至 VMSS osProfile

必須安裝叢集的每個憑證必須設定 VMSS 資源 (Microsoft.Compute/virtualMachineScaleSets) 的 [osProfile] 區段中。 這會指示資源提供者，若要安裝的 Vm 憑證。 包括叢集憑證，以及任何您打算使用您的應用程式的應用程式的安全性憑證︰

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>設定服務布料的轉印圖樣叢集憑證

叢集驗證憑證也必須設定服務布料的轉印圖樣叢集資源 (Microsoft.ServiceFabric/clusters) 和中服務布料的轉印圖樣副檔名為 VMSS VMSS 資源中。 這個選項可讓將它設定為叢集驗證與管理端點的伺服器驗證使用的服務布料的轉印圖樣資源提供者。

##### <a name="vmss-resource"></a>VMSS 資源︰

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>服務布料的轉印圖樣資源︰

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>插入 AAD 設定

先前建立的 AAD 設定直接插入您資源管理員的範本，但是建議從中擷取到第一次將參數檔案，讓資源管理員範本可重複使用免費的值特定的部署參數的值。

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < 」 設定-arm 」 ></a>設定資源管理員範本參數

最後，使用 [按鍵保存庫] 及 [AAD PowerShell 命令的輸出值填入參數檔案︰

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
此時，您現在應該下列動作︰

 - 索引鍵保存庫資源群組
    - 索引鍵保存庫
    - 叢集伺服器驗證憑證
    - 資料加密憑證
 - Azure Active Directory 租用戶 
    - AAD 應用程式的 web 式管理和服務布料的轉印圖樣總管
    - 原生的用戶端管理 AAD 應用程式
    - 指派角色的使用者 
 - 服務布料的轉印圖樣叢集資源管理員範本
    - 透過金鑰保存庫設定的憑證
    - 設定的 azure Active Directory 

下圖說明鍵保存庫及 AAD 設定配合您的資源管理員範本的位置。

![資源管理員相依性地圖][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>建立叢集

您已準備好要建立使用[雲端世界部署]叢集[resource-group-template-deploy]。

#### <a name="test-it"></a>測試

您可以使用下列 PowerShell 命令，使用參數來測試您的資源管理員範本︰

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>將其部署

如果通過資源管理員範本測試，使用下列 PowerShell 命令參數檔案部署資源管理員範本︰

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>指派給角色的使用者

一旦您建立的應用程式來代表叢集，必須先將您的使用者指派給支援服務布料的轉印圖樣的角色︰ 唯讀與管理員]。 您可以使用[Azure 傳統入口網站][azure-classic-portal]。

1. 瀏覽至您的租用戶，然後選擇 [應用程式。
2. 選擇 [web 應用程式，同時具有名稱等`myTestCluster_Cluster`。
3. 按一下 [使用者] 索引標籤。
4. 選擇 [使用者指派及按一下畫面底部的 [**指派**] 按鈕。

    ![將使用者指派給角色] 按鈕][assign-users-to-roles-button]

5. 選取要指派給使用者的角色。

    ![指派給角色的使用者][assign-users-to-roles-dialog]

>[AZURE.NOTE] 如需有關角色服務布料的轉印圖樣的詳細資訊，請參閱[適用於服務布料的轉印圖樣用戶端的角色型存取控制](service-fabric-cluster-security-roles.md)。

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>建立安全叢集 linux

若要輕鬆地處理程序，協助指令碼已提供[以下](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux)。 使用這個協助指令碼，其假設您已經安裝，Azure CLI，其位於您的路徑。 確認指令碼具有權限，以執行來執行`chmod +x cert_helper.py`之後下載。 第一個步驟是登入您的 Azure 帳戶使用與 CLI `azure login` ] 命令。 登入您 Azure 帳戶之後, 使用協助程式您 CA 簽署的憑證，下列命令顯示為︰

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

這個命令傳回輸出下列三個字串︰ 

1. SourceVaultID，也就是它為您建立新的 KeyVault ResourceGroup 的識別碼。 

2. 用來存取憑證 CertificateUrl。

3. CertificateThumbprint，用於驗證。


下列範例會示範如何使用命令︰

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
執行上述命令可讓您的三個字串，如下所示︰

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 憑證的主體名稱必須符合用來存取服務布料的轉印圖樣叢集的網域。 這是必要的叢集的 HTTPS 管理結束點和服務布料的轉印圖樣 Explorer 提供 SSL。 您無法取得 SSL 憑證從憑證授權單位 (CA)`.cloudapp.azure.com`網域。 您必須取得叢集的自訂網域名稱。 當您從 CA 要求憑證憑證的主體名稱必須符合叢集所用的自訂網域名稱。

這些是所述[設定資源管理員範本參數](#configure-arm)，建立安全服務布料的轉印圖樣叢集 （不含 AAD) 所需的項目。 您可以連線至安全叢集透過在[驗證叢集的用戶端存取](service-fabric-connect-to-secure-cluster.md)的指示進行。 Linux 預覽叢集不支援 AAD 驗證。 您可以指派系統管理] 及 [用戶端的角色[指派給使用者的角色](#assign-roles)] 區段中所述。 指定時 Linux 預覽叢集的系統管理] 及 [用戶端的角色，您必須提供憑證憑證碼驗證 （而不是主體名稱，因為沒有鏈結驗證或撤銷正在執行這個預覽版本）。


如果您想要使用自我簽署的憑證以進行測試，您可以使用相同的指令碼來產生自我簽署的憑證和上傳至 KeyVault，提供標幟`ss`，而非提供的憑證路徑和憑證的名稱。 例如，請參閱建立及上傳自我簽署的憑證的下列命令︰

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

這個命令會傳回相同的三個字串，SourceVault、 CertificateUrl 和 CertificateThumbprint，這用來建立安全 Linux 叢集，以及放置自我簽署的憑證的位置。 您必須連線至叢集自我簽署的憑證。  您可以連線至安全叢集透過在[驗證叢集的用戶端存取](service-fabric-connect-to-secure-cluster.md)的指示進行。 憑證的主體名稱必須符合用來存取服務布料的轉印圖樣叢集的網域。 這是必要的叢集的 HTTPS 管理結束點和服務布料的轉印圖樣 Explorer 提供 SSL。 您無法取得 SSL 憑證從憑證授權單位 (CA)`.cloudapp.azure.com`網域。 您必須取得叢集的自訂網域名稱。 當您從 CA 要求憑證憑證的主體名稱必須符合叢集所用的自訂網域名稱。

可以在入口網站填滿協助指令碼提供的參數，[建立叢集 Azure 入口網站中](service-fabric-cluster-creation-via-portal.md#create-cluster-portal)的一節所述。

## <a name="next-steps"></a>後續步驟

此時，您必須具備 Azure Active Directory 提供管理驗證的安全叢集。 [連線到您叢集](service-fabric-connect-to-secure-cluster.md)下一步]，並瞭解如何[管理應用程式的密碼](service-fabric-application-secret-management.md)。

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Azure Active Directory 設定用戶端驗證的疑難排解

如果您遇到問題時 Azure Active Directory 設定用戶端驗證時，請檢閱下列 suggestings 可能的解決方案。

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>選取憑證服務布料的轉印圖樣總管提示

#### <a name="problem"></a>問題

之後順利在 AAD 登入頁面的服務布料的轉印圖樣檔案總管中的登入，在瀏覽器會傳回至首頁，但提示選取憑證] 對話方塊。

![SFX 選取憑證] 對話方塊][sfx-select-certificate-dialog]

#### <a name="reason"></a>原因

使用者未指派 AAD 叢集應用程式中的角色。 因此 AAD 驗證失敗服務布料的轉印圖樣叢集上。 服務布料的轉印圖樣總管退回憑證驗證。

#### <a name="solution"></a>解決方案

請依照指示設定 AAD 並指派使用者的角色。 此外，建議 」 使用者工作分派所需至 ACCESS 應用程式] 以開啟`SetupApplications.ps1`會。

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>使用 PowerShell 失敗，錯誤連線︰ 指定的認證無效

#### <a name="problem"></a>問題

何時使用 PowerShell 連線到使用 「 AzureActiveDirectory 」 的安全模式叢集、 AAD 登入頁面上的順利登入後, 連線錯誤而失敗︰ 指定的認證無效顯示。

#### <a name="solution"></a>解決方案

與上面相同。

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>服務布料的轉印圖樣檔案總管中傳回登入失敗︰ AADSTS50011

#### <a name="problem"></a>問題

之後 AAD 登入服務布料的轉印圖樣檔案總管中的頁面上的 [登入頁面會傳回登失敗-AADSTS50011︰ 回覆地址&lt;url&gt;不相符的回覆地址，設定應用程式︰ &lt;guid&gt;。 

![不符合 SFX 回覆地址][sfx-reply-address-not-match]

#### <a name="reason"></a>原因

代表服務布料的轉印圖樣總管嘗試驗證 AAD cluster(web) 應用程式的情況下，為要求的一部分會提供重新導向傳回 URL。 但沒有列出 AAD 應用程式回覆 URL] 清單中。

#### <a name="solution"></a>解決方案

將服務布料的轉印圖樣總管] 的 url 新增 cluster(web) 應用程式中的 [設定] 索引標籤中的 [回覆 URL]，或取代清單中的項目。 然後儲存。

![Web 應用程式回覆 url][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>可重複使用多重叢集相同的 AAD 租用的戶嗎？

#### <a name="answer"></a>Answer （回應)

[是]。 但請記住，若要新增服務布料的轉印圖樣總管的 URL，否則服務布料的轉印圖樣 Explorer 無法運作 cluster(web) 應用程式。

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>為什麼我仍需要伺服器憑證 AAD 啟用時？

#### <a name="answer"></a>Answer （回應)

FabricClient 和 FabricGateway 執行相互驗證。 若 AAD 驗證 AAD 整合會提供伺服器和伺服器憑證的用戶端身分識別用來確認伺服器身分識別。 如需有關憑證服務布料的轉印圖樣上的運作方式的詳細資訊，請檢查[X.509 憑證和服務布料的轉印圖樣][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png