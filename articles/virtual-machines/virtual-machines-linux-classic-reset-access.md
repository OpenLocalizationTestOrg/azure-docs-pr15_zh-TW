<properties
        pageTitle="重設 Linux VM 密碼和 CLI SSH 金鑰 |Microsoft Azure"
        description="如何重設 Linux VM 密碼或 SSH 鍵、 修正 SSH 設定，並檢查磁碟一致性使用副檔名為 VMAccess Azure 命令列介面 (CLI)"
        services="virtual-machines-linux"
        documentationCenter=""
        authors="cynthn"
        manager="timlt"
        editor=""
        tags="azure-service-management"/>

<tags
        ms.service="virtual-machines-linux"
        ms.workload="infrastructure-services"
        ms.tgt_pltfrm="vm-linux"
        ms.devlang="na"
        ms.topic="article"
        ms.date="06/14/2016"
        ms.author="cynthn"/>

# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>如何重設密碼 Linux VM 或 SSH 鍵、 修正 SSH 設定，並核取 [使用副檔名為 VMAccess 磁碟一致性


如果您無法連線至 Linux 上 Azure 虛擬機器由於忘記的密碼，不正確的安全命令介面 (SSH) 鍵，或問題 SSH 設定，使用副檔名為 VMAccessForLinux Azure CLI 來重設密碼或 SSH 鍵，修正 SSH 設定，並檢查磁碟一致性。 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)。

Azure CLI 中，您使用 [ **azure vm 副檔名設定**] 命令從命令列介面 （艦隊，終端機，命令提示字元） 存取命令。 執行**azure 說明 vm 副檔名設定**詳細的延伸使用方式。

使用 Azure CLI 中，您可以執行下列工作︰

+ [重設密碼](#pwresetcli)
+ [重設 SSH 索引鍵](#sshkeyresetcli)
+ [重設密碼和 SSH 索引鍵](#resetbothcli)
+ [建立新的 sudo 使用者帳戶](#createnewsudocli)
+ [重設 SSH 設定](#sshconfigresetcli)
+ [刪除使用者](#deletecli)
+ [顯示 VMAccess 延伸的狀態](#statuscli)
+ [檢查新增的磁碟的一致性](#checkdisk)
+ [修復您 Linux VM 中新增的磁碟](#repairdisk)


## <a name="prerequisites"></a>必要條件

您將需要執行下列動作︰

- 您會使用您的帳戶相關聯的 Azure 資源需要[安裝 Azure CLI](../xplat-cli-install.md)並[連線到您的訂閱](../xplat-cli-connect.md)。
- 在命令提示字元輸入下列設定的傳統部署模型的正確模式︰
        
        azure config mode asm
        
- 如果您想要重設任一個會有新的密碼或 SSH 鍵的設定。 如果您想要重設 SSH 設定，則不需要這些。


## <a name="pwresetcli"></a>重設密碼

1. 建立這些行命名 PrivateConf.json 的檔案。 取代括弧和 & #60; 預留位置 「 & #62;使用您自己的資訊的值。

        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }

2. 執行此命令，替代的虛擬機器與 #60; vm 名稱與 #62; 的名稱。

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>重設 SSH 索引鍵

1. 建立命名 PrivateConf.json 與這些內容的檔案。 取代括弧和 & #60; 預留位置 「 & #62;使用您自己的資訊的值。

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }

2. 執行此命令，替代的虛擬機器與 #60; vm 名稱與 #62; 的名稱。

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>重設密碼] 和 [SSH 索引鍵

1. 建立命名 PrivateConf.json 與這些內容的檔案。 取代括弧和 & #60; 預留位置 「 & #62;使用您自己的資訊的值。

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }

2. 執行此命令，替代的虛擬機器與 #60; vm 名稱與 #62; 的名稱。

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>建立新的 sudo 使用者帳戶

如果您忘記您的使用者名稱，您可以使用 VMAccess 建立一個新 sudo 授權。 在此情況下，現有的使用者名稱和密碼將不會修改。

若要建立新的 sudo 使用者密碼存取，請使用指令碼[重設密碼](#pwresetcli)並指定新的使用者名稱。

若要建立新的 sudo 使用者 SSH 按鍵存取，請使用指令碼[重設 SSH 鍵](#sshkeyresetcli)並指定新的使用者名稱。

您也可以使用[重設密碼和 SSH 鍵](#resetbothcli)，以建立新使用者的密碼] 和 [SSH 按鍵存取。

## <a name="sshconfigresetcli"></a>重設 SSH 設定

如果不想要的狀態是 SSH 設定，您可能也無法存取 VM。 您可以使用副檔名為 VMAccess 要重設為預設狀態的設定。 若要這麼做，您只需要設定為 「 True 」 的 「 reset_ssh 」 鍵。 副檔名為會重新啟動 SSH 伺服器、 開啟您的 VM SSH 連接埠和重 SSH 設定設為預設值。 將不會變更使用者帳戶 （名稱、 密碼或 SSH 鍵）。

> [AZURE.NOTE] 取得重設的 SSH 設定檔案位於 /etc/ssh/sshd_config。

1. 建立命名 PrivateConf.json 搭配這個內容的檔案。

        {
        "reset_ssh":"True"
        }

2. 執行此命令，替代的虛擬機器與 #60; vm 名稱與 #62; 的名稱。 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>刪除使用者

如果您想要刪除使用者帳戶登入的 vm 直接沒有，您可以使用這個指令碼。

1. 建立與此內容，以取代 [要移除的使用者名稱與 #60; usernametoremove 與 #62; 命名 PrivateConf.json 的檔案。 

        {
        "remove_user":"<usernametoremove>"
        }

2. 執行此命令，替代的虛擬機器與 #60; vm 名稱與 #62; 的名稱。 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>顯示 VMAccess 延伸的狀態

若要顯示的副檔名為 VMAccess 狀態，請執行此命令。

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a>< 名稱 = 'checkdisk' <</a>檢查新增的磁碟的一致性

若要執行 fsck Linux 虛擬機器中的所有磁碟上，您將需要執行下列動作︰

1. 建立命名 PublicConf.json 搭配這個內容的檔案。 核取磁碟會帶您要檢查連接到您的虛擬機器磁碟布林值。 

        {   
        "check_disk": "true"
        }

2. 執行此命令，可執行，替代的虛擬機器與 #60; vm 名稱與 #62; 的名稱。

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>修復磁碟 

要修復的磁碟未裝載或有裝載設定錯誤，請使用 [VMAccess 延伸重設您 Linux 虛擬機器上的裝載設定。 取代磁碟與 #60; yourdisk 與 #62; 的名稱。

1. 建立命名 PublicConf.json 搭配這個內容的檔案。 

        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }

2. 執行此命令，可執行，替代的虛擬機器與 #60; vm 名稱與 #62; 的名稱。

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## <a name="next-steps"></a>後續步驟

* 如果您要使用 PowerShell 的 Azure cmdlet 或 Azure 資源管理員範本來重設密碼或 SSH 鍵時，修正 SSH 設定，並檢查磁碟一致性，請參閱[GitHub VMAccess 副檔名相關文件](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)。 

* 您也可以使用[Azure 入口網站](https://portal.azure.com)重設密碼或 Linux VM SSH 索引鍵部署傳統部署模型中。 您目前無法使用入口網站執行以下 Linux vm 部署資源管理員部署模型中。

* 有關使用 Azure 虛擬機器 VM 擴充功能的詳細資訊，請參閱[關於虛擬機器 extensions 和功能](virtual-machines-linux-extensions-features.md)。
