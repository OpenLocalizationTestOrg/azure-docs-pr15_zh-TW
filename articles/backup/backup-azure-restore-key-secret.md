<properties
    pageTitle="加密 Vm 使用 Azure 備份還原金鑰保存庫索引鍵和私人 |Microsoft Azure"
    description="瞭解如何還原金鑰保存庫索引鍵和使用 PowerShell 的 Azure 備份中的密碼"
    services="backup"
    documentationCenter=""
    authors="JPallavi"
    manager="vijayts"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="JPallavi" />

# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>加密 Vm 使用 Azure 備份還原金鑰保存庫索引鍵和密碼
本文討論如何使用 Azure VM 備份執行還原的加密 Azure Vm，如果您的識別碼和密碼不存在於金鑰保存庫。 如果您想要還原 vm 維護金鑰 （金鑰加密金鑰） 和密碼 （BitLocker 加密金鑰） 的一份複本，也可以使用下列步驟執行。

## <a name="pre-requisites"></a>必要條件

1. **備份加密 Vm** -加密 Azure Vm 已備份使用 Azure 備份。 如需如何備份加密的 Azure Vm 詳細資訊，請參閱[管理備份與還原使用 PowerShell 的 Azure Vm 的](backup-azure-vms-automation.md)文章。

2. **設定 Azure 金鑰保存庫**– 按鍵的按鍵以及機密資料需要還原保存庫，請確定已存在。 如需詳細資訊金鑰保存庫管理，請參閱[快速入門 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)的文件。

## <a name="setup-recovery-services-vault"></a>設定復原服務保存庫 
使用下列步驟登入 PowerShell，並設定復原服務保存庫內容

### <a name="log-in-to-azure-powershell"></a>登入 Azure PowerShell 

使用下列 cmdlet 的 Azure 帳戶登入

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>設定復原服務保存庫內容

後登入，使用下列 cmdlet，以取得您可用的訂閱清單

```
PS C:\> Get-AzureRmSubscription
```

選取資源可供您的訂閱

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

設定使用修復服務保存庫備份已啟用加密 Vm 保存庫內容

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>取得復原點 

選取 [保存庫代表加密 Azure 虛擬機器中的 [容器

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

使用此容器，請返回設定相對應的虛擬機器中的項目

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

復原點的陣列，取得在變數資源點數選取備份的項目

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>還原加密的虛擬機器
若要還原加密的 VM，其索引鍵和私人使用下列步驟。

### <a name="restore-key"></a>還原機碼

陣列 $rp 上方，以相反順序排序的最新的復原點，在索引 0 的時間。 例如︰ $rp [0] 選取的最新的復原點。

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [AZURE.NOTE]
順利執行這個指令程式後，blob 檔案會產生中指定的資料夾在電腦上執行的位置。 此 blob 檔案代表金鑰加密金鑰加密的格式。

使用下列 cmdlet 的主要保存庫，還原金鑰上一步]。 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>還原密碼

從上方取得復原點還原私人資料

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [AZURE.NOTE]
文字之前 vault.azure.net 代表原始金鑰保存庫的名稱。 機密資料後的文字 / 代表私人的名稱。 

從上述執行 cmdlet 的輸出取得的私人的名稱和值，以防您想要使用相同的私人名稱。 在其他情況下，$secretname 下列應該會更新以使用新的私人名稱。 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

設定密碼、 標籤，以避免 VM 需要以及還原。 標記 DiskEncryptionKeyFileName，值應該包含您要使用的密碼的名稱。 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [AZURE.NOTE]
值 DiskEncryptionKeyFileName 是同私人取得上方的名稱。 可從金鑰保存庫取得 DiskEncryptionKeyEncryptionKeyURL 值，還原回按鍵，使用[取得 AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) cmdlet   

設定私人上一步] 鍵保存庫

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>還原虛擬機器
上述的 PowerShell 指令程式可協助您將還原索引鍵和私人返回鍵保存庫中，如果您已備份加密 VM 使用 Azure VM 備份。 之後還原，請參閱[管理備份與還原使用 PowerShell 的 Azure Vm](backup-azure-vms-automation.md)還原加密的 Vm 的文件。
