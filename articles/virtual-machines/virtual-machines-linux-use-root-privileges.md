<properties 
    pageTitle="使用 [Linux 虛擬機器中的 [根權限 |Microsoft Azure" 
    description="瞭解如何使用 Linux 虛擬機器 Azure 中的 [根權限。" 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>


# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>使用 [Linux Azure 虛擬機器上的 [根權限

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

根據預設， `root` Linux 虛擬機器中 Azure 上停用使用者。 使用者可以命令以執行提高權限的權限使用`sudo`] 命令。 不過，體驗可能會有所不同系統的佈。

1. **SSH 索引鍵和密碼或只**虛擬機器佈任一的憑證 (`.CER`檔案) 或 SSH 鍵與密碼，或只是使用者名稱和密碼。 在此情況下`sudo`之前執行命令會提示使用者的密碼。

2. **僅 SSH 金鑰**虛擬機器佈的憑證 (`.cer`， `.pem`，或`.pub`檔案) 或 SSH 鍵，但不是使用密碼。  在此情況下`sudo`**不會**提示使用者的密碼，才能執行命令。


## <a name="ssh-key-and-password-or-password-only"></a>SSH 索引鍵和密碼或只密碼

登入 Linux 虛擬機器使用 SSH 金鑰或密碼驗證]，然後再執行命令使用`sudo`，例如︰

    # sudo <command>
    [sudo] password for azureuser:

在此情況下會提示使用者的密碼。 輸入密碼之後`sudo`會執行與命令`root`權限。

您也可以透過編輯啟用 passwordless sudo`/etc/sudoers.d/waagent`檔案，例如︰

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

這項變更會允許 passwordless sudo 由使用者 「 azureuser 」。

## <a name="ssh-key-only"></a>SSH 只按鍵

登入 Linux 虛擬機器使用 SSH 金鑰驗證]，然後再執行命令使用`sudo`，例如︰

    # sudo <command>

使用者會在此情況下**不**會提示您輸入密碼。 按下後`<enter>`，`sudo`會執行與命令`root`權限。

 
