<properties
    pageTitle="設定 WinRM access 的虛擬機器中 Azure 資源管理員 |Microsoft Azure"
    description="如何設定用於 Azure 資源管理員虛擬機器 WinRM 存取"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="singhkay"/>

# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>設定 [虛擬機器 Azure 資源管理員] 中的 [WinRM 存取權限

## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM 中 Azure 服務管理與 Azure 資源管理員

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型

* 如 Azure 資源管理員的概觀資訊，請參閱這篇[文章](../azure-resource-manager/resource-group-overview.md)
* Azure 服務管理和 Azure 資源管理員之間的差異，請參閱這篇[文章](../resource-manager-deployment-model.md)

在設定 WinRM 設定兩個堆疊之間的主要差異是 VM 上取得安裝的憑證。 Azure 資源管理員堆疊中, 憑證建立模型做為索引鍵保存庫資源提供者管理的資源。 因此，使用者必須提供自己的憑證，然後上傳至金鑰保存庫之前 VM 中使用。

以下是您需要設定 WinRM 連線 VM 採取的步驟

1. 建立索引鍵保存庫
2. 建立自我簽署的憑證
3. 上傳至金鑰保存庫的自我簽署的憑證
4. 取得您自我簽署憑證金鑰保存庫中的 URL
5. 建立 VM 時參照您自我簽署的憑證的 URL

## <a name="step-1-create-a-key-vault"></a>步驟 1︰ 建立索引鍵保存庫

您可以使用下列命令以建立索引鍵保存庫

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>步驟 2︰ 建立自我簽署的憑證
您可以建立自我簽署的憑證使用這個 PowerShell 指令碼

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>步驟 3︰ 將您的自我簽署的憑證上傳到金鑰保存庫

上傳之前憑證以在步驟 1 建立的金鑰保存庫，就必須轉換成 Microsoft.Compute 資源提供者能夠理解的格式。 下列 PowerShell 指令碼可讓您執行此動作

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>步驟 4︰ 取得您自我簽署憑證金鑰保存庫中的 URL

Microsoft.Compute 資源提供者必須鍵保存庫內私人 URL 時佈建 VM。 這可讓 Microsoft.Compute 資源提供者，若要下載密碼並 VM 上建立相同的憑證。

>[AZURE.NOTE]包含的版本，也必須私人的 URL。 範例 URL 看起來像 https://contosovault.vault.azure.net:443/機密/contososecret/01h9db0df2cd4300a20ence585a6s7ve 下方


#### <a name="templates"></a>範本

您可以取得中使用範本的 URL 連結下方的程式碼

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell

您可以取得這個 URL 使用下列 PowerShell 命令

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>步驟 5︰ 建立 VM 時參照您自我簽署的憑證的 URL

#### <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

建立範本透過 VM，時憑證取得參照 [機密] 區段及下的 [winRM] 區段中︰

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

以上的範例範本，請參閱以下[201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

您可以在[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)找到此範本的原始碼

#### <a name="powershell"></a>PowerShell

    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>步驟 6︰ 連線到 VM
您可以連線至您將需要確認的 VM 之前 WinRM 遠端管理設定您的電腦。 以系統管理員身分開始 PowerShell，並執行以下命令，以確定您正在設定。

    Enable-PSRemoting -Force

>[AZURE.NOTE] 您可能需要確認 WinRM 服務正在執行如果以上沒有作用。 您可以執行的使用`Get-Service WinRM`

一旦完成安裝後，您可以連線至 VM 使用下方] 命令

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate