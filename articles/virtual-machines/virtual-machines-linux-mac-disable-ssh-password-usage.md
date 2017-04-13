<properties
    pageTitle="停用您 Linux VM SSH 密碼設定 SSHD |Microsoft Azure"
    description="保護您的 Linux VM 上 Azure SSH 為停用密碼登入。"
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
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="v-livech"/>

# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>設定 SSHD 來停用您 Linux VM SSH 密碼

本文著重於鎖定您 Linux VM 的登入安全性的方式。  一旦 SSH 連接埠 22 隨即會開啟至世界 bot 開頭嘗試猜測密碼登入。  我們會本文中已停用透過 SSH 的密碼登入。  完全移除能夠使用密碼我們此類型的暴力保護 Linux VM。  我們將會設定為僅允許透過 SSH 公用與私人鍵，登入到目前為止最安全的方式來登入 Linux Linux SSHD 的好處是。  可能的組合，它會需要猜測私密金鑰是以，因此阻礙重覆 bot 從偶數終究嘗試暴力 SSH 鍵。


## <a name="goals"></a>目標

- 設定不允許 SSHD:
  - 密碼登入
  - 根使用者登入
  - 挑戰回應驗證
- 設定為允許 SSHD:
  - 僅限 SSH 金鑰登入
- 重新啟動 SSHD 仍登入
- 測試新 SSHD 設定

## <a name="introduction"></a>簡介

[定義 SSH](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD 是 SSH 伺服器 Linux VM 執行。  SSH 是在 MacBook 或 Linux 工作站執行命令介面從用戶端。  SSH 也是用來保護及加密工作站和 Linux VM 之間的通訊協定。

本文是非常重要的一個登入您的 Linux vm 開啟如整個逐步。  因此我們會開啟兩個終端機和 SSH Linux vm 這兩個它們。  我們會使用第一個終端機 SSHDs 設定檔案進行變更，並重新啟動 SSHD 服務。  我們將使用的第二個終端機服務必須重新啟動後測試這些變更。  因此我們會停用 SSH 密碼及依賴嚴格 SSH 鍵，如果 SSH 索引鍵不正確，關閉 VM 連線，VM 將會永久鎖定，且沒有人都能登入，需要刪除並重新建立。

## <a name="prerequisites"></a>必要條件

- [Azure 中 Linux Vm Linux 和 Mac 上建立 SSH 鍵](virtual-machines-linux-mac-create-ssh-keys.md)
- Azure 帳戶
  - [免費的試用版註冊](https://azure.microsoft.com/pricing/free-trial/)
  - [Azure 入口網站](http://portal.azure.com)
- Azure 上執行的 Linux VM
- SSH 公用與私人組中`~/.ssh/`
- 中 SSH 公開金鑰`~/.ssh/authorized_keys`Linux VM 上
- VM Sudo 權限
- 連接埠開啟 22

## <a name="quick-commands"></a>快速命令

_只想 TLDR 版本資深的 Linux 系統管理員請從這裡開始。 讓其他人想要的詳細的說明和逐步瞭解略過此區段。_

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## <a name="detailed-walk-through"></a>詳細的逐步瞭解

登入 Linux VM 終端機 1 (T1)。  登入 Linux VM 終端機 2 (T2)。

在 T2 我們編輯 SSHD 設定檔。  

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

從這裡我們將編輯的設定停用密碼並啟用 SSH 金鑰登入。  有許多設定值，您應該研究此檔案，然後變更讓 Linux 與 SSH 視需要安全。

#### <a name="disable-password-logins"></a>停用密碼登入

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>啟用公開金鑰驗證

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>停用根登入

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>停用挑戰回應驗證

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>重新啟動 SSHD

從 T1 命令介面請確認您仍登入。  這是要徑，因此您執行沒有取得遭到鎖定您 VM 如果 SSH 索引鍵不正確，因為密碼會停用。  如果任何的設定不正確，您可以使用 T1 來修正 sshd_config，您仍可進行登入並 SSH 就會保存連線 SSHD 服務期間您 Linux VM 上重新啟動。

將 T2 執行︰

##### <a name="on-the-debian-family"></a>在 Debian 家族

```
username@macbook$ sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>在 RedHat 家族

```
username@macbook$ sudo service sshd restart
```

防止暴力密碼登入您 VM 現在已停用密碼。  只有 SSH 金鑰以允許您必須在登入更快且更安全。
