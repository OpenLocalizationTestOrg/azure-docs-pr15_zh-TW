<properties
    pageTitle="重設使用副檔名為 VMAccess Azure Linux Vm 存取 |Microsoft Azure"
    description="重設使用副檔名為 VMAccess Azure Linux Vm 存取。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension"></a>管理使用者與 SSH，核取或修復磁碟上 Azure Linux Vm 使用 VMAccess 副檔名

本文將示範如何使用核取或修復磁碟、 重設使用者的存取權，管理使用者帳戶，或重設 SSHD 設定 linux Azure VMAcesss 副檔名。 需要，請參閱︰

- Azure 帳戶 ([取得免費的試用版](https://azure.microsoft.com/pricing/free-trial/))。

- 登入的[Azure CLI](../xplat-cli-install.md) `azure login`。

- Azure CLI_必須使用_Azure 資源管理員模式`azure config mode arm`。

## <a name="quick-commands"></a>快速命令

有兩種方式來使用您的 Linux Vm VMAccess:

- 使用 Azure CLI 和必要的參數。
- 使用 JSON 原始檔案 VMAccess 程序，然後執行動作。

對於快速命令] 區段中，我們將使用 Azure CLI`azure vm reset-access`方法。 在下列命令範例中，將包含 「 範例 」 的值從您自己的環境的值。

## <a name="create-a-resource-group-and-linux-vm"></a>建立資源群組與 Linux VM

```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>建立 Debian VM

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-g myResourceGroup \
-l westus \
-y Linux \
-n myVM \
-Q Debian
```

## <a name="reset-root-password"></a>重設根密碼

若要重設根密碼︰

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u root \
-p myNewPassword
```

## <a name="ssh-key-reset"></a>SSH 金鑰重設

若要重設非根使用者 SSH 索引鍵︰

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>建立使用者

若要建立使用者︰

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-p myAdminUserPassword
```

## <a name="remove-a-user"></a>移除使用者

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-R myRemovedUser
```

## <a name="reset-sshd"></a>重設 SSHD

若要重設 SSHD 設定︰

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM
-r
```


## <a name="detailed-walkthrough"></a>詳細逐步解說

### <a name="vmaccess-defined"></a>定義 VMAccess:

在您 Linux VM 磁碟會顯示錯誤。 您可以設法根密碼重設您 Linux VM 或不小心刪除 SSH 私密金鑰。 如果的發生回資料中心的日期，您必須那里磁碟機，然後開啟 [以取得在伺服器主控台 KVM。 Azure VMAccess 副檔名視為可讓您存取重設為 Linux 的存取權，或執行磁碟層級進行的維修作業主控台該 KVM 參數。

詳細逐步解說中，我們要使用的 VMAccess，使用 JSON 原始檔案長的表單。  這些 VMAccess JSON 檔案也可以從 Azure 範本呼叫。

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>若要檢查或修復的 Linux VM 磁碟使用 VMAccess

使用 VMAccess 您可以執行 fsck 下您 Linux VM 磁碟上執行。  您也可以執行磁碟檢查並使用 VMAccess 磁碟修復。

若要檢查，然後修復磁碟會使用這個 VMAccess 指令碼︰

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

執行與 VMAccess 指令碼︰

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>使用 VMAccess Linux 重設使用者的存取權

如果您遺失存取根您 Linux VM 上，您可以在啟動 VMAccess 指令碼重設根密碼。

若要重設根密碼，請使用這個 VMAccess 指令碼︰

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword",   
}
```

執行與 VMAccess 指令碼︰

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

若要重設 SSH 索引鍵的非根使用者，請使用這個 VMAccess 指令碼︰

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM",   
}
```

執行與 VMAccess 指令碼︰

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>使用 VMAccess 管理 Linux 的使用者帳戶

VMAccess 是可以用來管理使用者在您 Linux VM 沒有登入與使用 sudo 或根帳戶 Python 指令碼。

若要建立使用者，請使用這個 VMAccess 指令碼︰

`create_new_user.json`

```json
{
"username":"myNewUser",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
"password":"myNewUserPassword",
}
```

執行與 VMAccess 指令碼︰

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

若要刪除的使用者，請使用這個 VMAccess 指令碼︰

`remove_user.json`

```json
{
"remove_user":"myDeletedUser",
}
```

執行與 VMAccess 指令碼︰

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>使用 VMAccess 重設 SSHD 設定

如果您對 Linux Vm SSHD 設定進行變更，並關閉 SSH 連線，驗證所做的變更之前，您可能會無法 SSH'ing 回。  VMAccess 可以用於重 SSHD 設定設為正確的設定，而不透過 SSH 登入。

若要重設 SSHD 設定，請使用這個 VMAccess 指令碼︰

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

執行與 VMAccess 指令碼︰

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## <a name="next-steps"></a>後續步驟

更新 Linux 使用 Azure VMAccess 擴充功能是一種方法上執行的 Linux VM 進行變更。  您也可以使用雲端初始化和 Azure 範本等工具來修改您的 Linux VM 開機。

[瞭解虛擬機器副檔名和功能](virtual-machines-linux-extensions-features.md)

[撰寫副檔名為 Linux VM Azure 資源管理員範本](virtual-machines-linux-extensions-authoring-templates.md)

[若要在建立自訂 Linux VM 使用雲端初始化](virtual-machines-linux-using-cloud-init.md)
