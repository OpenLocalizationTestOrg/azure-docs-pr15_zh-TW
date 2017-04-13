<properties
   pageTitle="在 Azure FreeBSD 簡介 |Microsoft Azure"
   description="瞭解如何使用 FreeBSD 虛擬機器上 Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# <a name="introduction-to-freebsd-on-azure"></a>在 Azure FreeBSD 簡介
本主題提供 Azure 中執行 FreeBSD 虛擬機器中的概觀。

## <a name="overview"></a>概觀
FreeBSD 的 Microsoft Azure 是較進階的電腦的作業系統用於 power 新式伺服器的桌面，並在內嵌平台。 FreeBSD 10.3 圖像由 Microsoft Corporation，且可用於 Azure。 它會根據 FreeBSD 10.3 發行和 Azure [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4)已安裝的 VM 來賓代理程式。 代理程式負責 FreeBSD VM 和作業，例如佈建 （使用者名稱、 密碼、 主機名稱等） 的第一次使用 VM 和啟用的選擇性 VM 擴充功能 Azure 布料的轉印圖樣之間的通訊。
未來版本 FreeBSD 的詳細資訊，先策略是保持目前提供最新版本推出 FreeBSD 發行工程團隊之後，很快就。 即將推出為[FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html)。

## <a name="deploying-a-freebsd-virtual-machine"></a>部署 FreeBSD 虛擬機器
部署 FreeBSD 虛擬機器是乾淨的簡單的程序，使用 [從[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)圖像。

## <a name="vm-extensions-for-freebsd"></a>FreeBSD VM 副檔名
以下是在 FreeBSD 支援的 VM 擴充功能。

### <a name="vmaccess"></a>VMAccess

[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)副檔名可以︰

- 重設原始 sudo 使用者的密碼。
- 建立新的 sudo 使用者指定的密碼。
- 設定公用主機鍵以指定的金鑰。
- 重設公用主機所提供的金鑰期間 VM 佈建如果未提供的主索引鍵。
- 開啟 SSH 連接埠 (22)，並還原 sshd_config reset_ssh 設為 true。
- 移除現有使用者。
- 核取磁碟。
- 修復新增的磁碟。

### <a name="customscript"></a>CustomScript

[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)副檔名可以︰

- 如有提供自訂指令碼從 Azure 儲存體或下載外部公用儲存空間 (例如，GitHub)。
- 執行的項目點指令碼。
- 支援內嵌命令。
- 自動轉換 Windows 樣式命令介面和 Python 指令碼中的新行。
- 自動命令介面和 Python 指令碼中移除 BOM。
- 保護機密 CommandToExecute 的資料。

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>驗證︰ 使用者名稱、 密碼及 SSH 索引鍵
當您要建立 FreeBSD 虛擬機器使用 Azure 入口網站時，您必須提供使用者名稱、 密碼或 SSH 公開金鑰。
部署 FreeBSD 上 Azure 虛擬機器的使用者名稱必須不符合系統帳戶的名稱 (UID < 100) 已經在虛擬機器 （「 根目錄，「 範例）。
目前，只有 RSA SSH 金鑰功能受支援。 多行 SSH 索引鍵的必須使用 「---開始 SSH2 公開金鑰---「 開始和結束以 「---結束 SSH2 公開金鑰---」。

## <a name="obtaining-superuser-privileges"></a>取得 superuser 權限
在 Azure 虛擬機器執行個體部署期間所指定的使用者帳戶是權限的帳戶。 安裝的 sudo 封裝發佈 FreeBSD 圖像。
您透過此使用者帳戶登入之後，您可以使用命令語法為根執行命令。

    # sudo <COMMAND>

您也可以使用 sudo s 取得根命令介面。

## <a name="next-steps"></a>後續步驟
- 移至[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) ] 以建立 FreeBSD VM。
- 如果您想要將您自己 FreeBSD Azure，請參閱[建立和上傳至 Azure FreeBSD VHD](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md)。
