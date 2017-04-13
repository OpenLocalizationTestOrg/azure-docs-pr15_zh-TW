<properties
    pageTitle="建立 SSH 鍵 Linux 和 Mac |Microsoft Azure"
    description="產生和 Linux 和 Mac 上使用 SSH 索引鍵的資源管理員和 Azure 上的傳統部署模型。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="v-livech"/>

# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Azure 中 Linux Vm Linux 和 Mac 上建立 SSH 鍵

SSH 還是您可以建立上預設的 [驗證]，使用 SSH 鍵不需要密碼以登入的 Azure 虛擬機器。  密碼可以猜到，並開啟您的 Vm 最連續暴力嘗試猜測您的密碼。 Azure 從範本建立的 Vm 或`azure-cli`可以包含 SSH 公開金鑰部署中，移除文章部署設定的一部分。  如果您連線到 Linux VM 從 Windows，請參閱[這份文件。](virtual-machines-linux-ssh-from-windows.md)

需要，請參閱︰

- Azure 帳戶 ([取得免費的試用版](https://azure.microsoft.com/pricing/free-trial/))。

- [Azure CLI](../xplat-cli-install.md)登入`azure login`

- Azure CLI_必須使用_Azure 資源管理員模式`azure config mode arm`

## <a name="quick-commands"></a>快速命令

在下列命令，範例換成您自己的選擇。

SSH 鍵會保留在預設`.ssh`目錄。  

```bash
cd ~/.ssh/
```

如果您沒有`~/.ssh`目錄`ssh-keygen`] 命令將會建立您以正確的權限。

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

輸入將會儲存檔案的名稱`~/.ssh/`目錄︰

```bash
id_rsa
```

輸入 id_rsa 複雜密碼︰

```bash
correct horse battery staple
```

現在有`id_rsa`和`id_rsa.pub`中的 SSH 金鑰組`~/.ssh`目錄。

```bash
ls -al ~/.ssh
```

新增新建立的關鍵`ssh-agent`Linux 和 （也新增至 OSX 鑰匙圈） 在 Mac 上︰

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

複製 SSH 公開金鑰 Linux 伺服器︰

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

測試使用，而不密碼登入︰

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>詳細逐步解說

使用 SSH 公開及私密金鑰索引鍵是登入您的 Linux 伺服器最簡單的方法。 [公開金鑰加密](https://en.wikipedia.org/wiki/Public-key_cryptography)提供登入您的 Linux 或 BSD VM Azure 中比可惡意強制更容易的密碼更安全的方式。 與任何; 共用您的公開金鑰但是只有 （或您的本機安全性基礎結構） 擁有您私人的金鑰。  SSH 私密金鑰應有[非常安全密碼](https://www.xkcd.com/936/)(來源︰[xkcd.com](https://xkcd.com)) 來保護它。  此密碼就是存取私密金鑰 SSH 而**不是**使用者帳戶密碼。  當您新增至您 SSH 索引鍵的密碼時，它會使私密金鑰沒有用不解除鎖定密碼加密私密金鑰。  如果攻擊偷走私密金鑰該鍵沒有密碼，他們能夠使用該私密金鑰登入任何有公開金鑰的伺服器。  如果私密金鑰是密碼保護，無法使用該攻擊，提供額外的安全性層級上 Azure 基礎結構。

本文會建立*ssh rsa*格式化鍵檔案，針對部署資源管理員上所建議。  傳統和資源管理員部署[入口網站](https://portal.azure.com)上需要*ssh rsa*鍵。

## <a name="create-the-ssh-keys"></a>建立 SSH 索引鍵

Azure 需要至少 2048年位元，ssh rsa 格式化公開及私密金鑰。 若要建立索引鍵使用`ssh-keygen`，其中詢問一系列的問題，然後將 [私密金鑰和對應的公開金鑰。 公開金鑰 Azure VM 建立時，會複製到`~/.ssh/authorized_keys`。  SSH 鍵`~/.ssh/authorized_keys`用於是以符合對應的私密金鑰 SSH 登入連線的用戶端的挑戰。

## <a name="using-ssh-keygen"></a>使用 ssh-keygen

這個命令會建立密碼保護 （加密） SSH 還是使用 2048年位元 RSA，它註解，以便於識別它。  

藉由變更目錄、 開始，讓所有您 ssh 索引鍵的建立的目錄。

```bash
cd ~/.ssh
```

如果您沒有`~/.ssh`目錄`ssh-keygen`] 命令將會建立您以正確的權限。

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

_說明] 命令_

`ssh-keygen`= 用來建立索引鍵的程式

`-t rsa`= 類型的金鑰來建立的是 [RSA 格式](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`= 位元的金鑰

`-C "myusername@myserver"`= 輕鬆地將其識別公開金鑰檔案結尾加上註解。  一般電子郵件會用來作為註解，但您可以使用上述程序最適合您的基礎結構。

### <a name="using-pem-keys"></a>使用 PEM 鍵

如果您使用傳統部署模型 (Azure 傳統入口網站或 Azure 服務管理 CLI `asm`)，您可能需要使用 PEM 格式化 SSH 鍵，以存取您的 Linux Vm。  以下說明如何建立 PEM 鍵現有 SSH 公開金鑰與現有的 x509 憑證。

若要建立 PEM 格式化現有 SSH 公開金鑰金鑰︰

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Ssh-keygen 的範例

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

儲存主要的檔案︰

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

這份文件組名稱。  有的名稱為**id_rsa**組是預設值，有些工具，因此無法其中一個建議您先預期**id_rsa**私密金鑰檔案名稱。 目錄`~/.ssh/`是 SSH 組和 SSH 設定檔的預設位置。

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
列出`~/.ssh`目錄。 `ssh-keygen`建立`~/.ssh`目錄，如果它不存在，也會設定正確的擁有權和檔案模式。

密碼︰

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`密碼稱為 「 複雜密碼。 」  這是*強烈*建議您組加上密碼。 不使用密碼保護的金鑰組中，具有私密金鑰檔任何人都可以登入有公開金鑰任何伺服器使用。 新增密碼提供更多的保護，以防其他人可以存取您私人的金鑰檔案，指定您用來驗證您的時間，若要變更索引鍵。

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>若要儲存您私密金鑰密碼使用 ssh 代理程式

若要避免輸入與每個 SSH 登入您私密金鑰檔案的密碼，您可以使用`ssh-agent`快取私密金鑰檔案密碼。 當您叫用如果您使用的 Mac，OSX 鑰匙圈安全地儲存私人金鑰密碼`ssh-agent`。

首先，請確認`ssh-agent`正在執行

```bash
eval "$(ssh-agent -s)"
```

現在新增至私密金鑰`ssh-agent`使用命令`ssh-add`。

```bash
ssh-add ~/.ssh/id_rsa
```

私密金鑰密碼就會儲存在`ssh-agent`。

## <a name="create-and-configure-an-ssh-config-file"></a>建立及設定 SSH 設定檔

建立及設定建議最佳作法是`~/.ssh/config`若要加快檔案記錄集和最佳化您 SSH 用戶端的行為。

下列範例顯示標準設定。

### <a name="create-the-file"></a>建立檔案

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>編輯加入新的 SSH 組態檔案︰

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>範例`~/.ssh/config`檔案︰

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User myusername
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

此 SSH 設定可讓您以啟用各有它自己專屬的重要對每個伺服器的節。 預設設定 (`Host *`) 都不符合任何的設定檔中設定較高的特定主機任何主機。


### <a name="config-file-explained"></a>說明的設定檔

`Host`= 終端機上呼叫主機名稱。  `ssh fedora22`告訴`SSH`設定區塊中使用的值標示為 [`Host fedora22`附註︰ 這個可供您使用邏輯，且不代表實際的任何伺服器主機名稱的任何標籤。

`Hostname 102.160.203.241`= 的 IP 位址或 DNS 所存取的伺服器名稱。

`User myusername`= 遠端使用者来使用的帳戶登入伺服器。

`PubKeyAuthentication yes`= 會 SSH 告訴您想要使用 SSH 鍵來登入。

`IdentityFile /home/myusername/.ssh/id_id_rsa`= SSH 私密金鑰及公開金鑰用來進行驗證。


## <a name="ssh-into-linux-without-a-password"></a>將 Linux 不使用密碼 SSH

現在，您必須 SSH 金鑰並設定的 SSH 設定檔案，也可以快速及安全地您 Linux vm 登入。 第一次您登入伺服器，使用 SSH 鍵在命令提示您的複雜密碼的重要的檔案。

```bash
ssh fedora22
```

### <a name="command-explained"></a>說明] 命令

當`ssh fedora22`執行 SSH 先找出並載入任何設定`Host fedora22`區塊，然後按一下 [載入所有剩餘的設定，最後一個區塊，從`Host *`。

## <a name="next-steps"></a>後續步驟

接下來是建立使用新的 SSH 公開金鑰 Azure Linux Vm。  Azure Vm SSH 公開金鑰登入以建立的是較較 Vm 預設登入方法密碼建立的。  建立使用 SSH azure Vm 是預設設定的密碼已停用，避免惡意強制猜測嘗試。

- [建立安全 Linux VM 使用 Azure 的範本](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [建立安全 Linux VM 使用 Azure 入口網站](virtual-machines-linux-quick-create-portal.md)
- [建立使用 Azure CLI 安全 Linux VM](virtual-machines-linux-quick-create-cli.md)
