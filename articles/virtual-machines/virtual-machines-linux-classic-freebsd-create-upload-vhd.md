<properties
   pageTitle="建立和上傳 FreeBSD VM 圖像 |Microsoft Azure"
   description="瞭解如何建立和上傳虛擬硬碟 (VHD) 包含 FreeBSD 作業系統建立 Azure 虛擬機器"
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
   ms.date="08/29/2016"
   ms.author="kyliel"/>

# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>建立並上傳至 Azure 的 FreeBSD VHD

本文將示範如何建立和上傳虛擬硬碟 (VHD) 包含 FreeBSD 作業系統。 您上傳後，您可以為自己的影像使用建立 Azure 虛擬機器 (VM)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>必要條件
本文假設您有下列項目︰

- **Azure 訂閱**-如果您沒有帳戶，您可以建立一個幾分鐘。 如果您有 MSDN 訂閱，請參閱[Visual Studio 訂閱者的每月 Azure 信用卡。](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 否則，瞭解如何[建立的免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。  

- **PowerShell 的 azure 工具**-Azure PowerShell 模組必須安裝並設定為使用您的 Azure 訂閱。 若要下載的模組，請參閱[Azure 下載](https://azure.microsoft.com/downloads/)。 教學課程，將說明如何安裝和設定以下是可用的模組。 若要上傳 VHD 使用[Azure 下載](https://azure.microsoft.com/downloads/)指令程式。

- **安裝.vhd 檔案中的 FreeBSD 作業系統**-支援 FreeBSD 作業系統必須安裝虛擬的硬碟。 若要建立.vhd 檔案中有多個的工具。 例如，您可以使用虛擬化解決方案，例如 HYPER-V 建立.vhd 檔案，並安裝作業系統。 如需有關如何安裝和使用 HYPER-V 的指示，請參閱[安裝 HYPER-V 並建立虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

> [AZURE.NOTE] Azure 中不支援較新的 VHDX 格式。 您可以轉換成 VHD 格式的磁碟使用 HYPER-V 管理員或 cmdlet[轉換 vhd](https://technet.microsoft.com/library/hh848454.aspx)。 此外，還有[教學課程，瞭解如何使用與 HYPER-V FreeBSD MSDN 上](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx)。

這項工作包括下列五個步驟。

## <a name="step-1-prepare-the-image-for-upload"></a>步驟 1︰ 準備上傳圖像

虛擬在電腦上安裝 FreeBSD 作業系統的位置，請完成下列程序︰

1. 啟用 DHCP。

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart

2. 啟用 SSH。

    預設會啟用 SSH 從光碟安裝後。 如果尚未啟用基於某種原因，或直接使用 FreeBSD VHD，輸入以下資料︰

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart

3. 設定序列主控台。

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. 安裝 sudo。

    根帳戶會停用 Azure 中。 這表示您需要利用 sudo 從未授權的使用者，以執行提高權限的權限的命令。

        # pkg install sudo
;
5. Azure 代理程式的先決條件。

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git

6. 安裝 Azure 代理程式。

    在[github](https://github.com/Azure/WALinuxAgent/releases)能找到 Azure 代理程式的最新版本。 版本 2.0.10 + 正式支援 FreeBSD 10 與 10.1，以及版本 2.1.4 正式支援 FreeBSD 10.2 及更新版本。

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0，我們要使用 2.0.16 作為範例︰

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1，我們要使用 2.1.4 作為範例︰

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

    >[AZURE.IMPORTANT] 安裝 Azure 代理程式後，最好先確認它正在執行︰

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log

7. Deprovision 系統。

    Deprovision 清除，使其更適合重新佈建系統。 最後一個提供的使用者帳戶和相關聯的資料，也會刪除下列命令︰

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    現在您可以關閉您 VM。

## <a name="step-2-create-a-storage-account-in-azure"></a>步驟 2︰ 建立 Azure 中的儲存空間帳戶 ##

您需要 Azure 上傳.vhd 檔案，讓它可以用來建立虛擬機器中的儲存空間帳戶。 若要建立儲存帳戶，您可以使用 Azure 傳統入口網站。

1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。

2. 在命令列中，選取 [**新增**]。

3. 選取 [**資料服務** > **儲存** > **快速建立**。

    ![快速建立儲存的帳戶](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)

4. 填滿的欄位，如下所示︰

    - 在 [ **URL** ] 欄位中，輸入要在儲存帳戶 URL 中使用的子網域名稱。 從 3 24 數字和小寫字母，可以包含項目。 此名稱就會變成內的 URL 用來處理 Azure Blob 儲存體、 Azure 佇列中的儲存空間或 Azure 資料表儲存體資源的訂閱，主機名稱。

    - 在**位置/相關性群組**下拉功能表中，選擇**位置或相關性群組**儲存空間的帳戶。 相關性群組可讓您在相同的資料中心將您的雲端服務，儲存空間。

    - 在 [**複製**] 欄位中，決定使用**地理重複**試驗的儲存空間帳戶。 依預設會開啟地理複寫。 這個選項，將資料複製到第二個位置，免費的好讓您的儲存空間無法移到該位置當主要發生主要位置。 第二個位置會自動獲指派，且無法變更。 如果您需要更多的控制法律要求或組織的原則雲端儲存空間的位置，您可以關閉地理複寫。 不過，請注意，如果您稍後開啟地理複寫，您需要付費複製現有資料至第二個位置的一次性的資料傳輸費用。 沒有地理複寫儲存服務會提供在折扣。 管理地理複寫的儲存空間帳戶的相關的更多詳細資料可以在這裡找到︰[建立、 管理，或刪除儲存的帳戶](../storage-create-storage-account/#replication-options)。

    ![輸入儲存帳戶詳細資料](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)


5. 選取 [**建立儲存帳戶**]。 帳戶現在會出現在**儲存空間**。

    ![儲存帳戶已成功建立](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)

6. 接下來，建立容器上傳的.vhd 檔案。 選取儲存體帳戶名稱，然後選取 [**容器**。

    ![儲存帳戶的詳細資訊](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)

7. 選取 [**建立容器**。

    ![儲存帳戶的詳細資訊](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)

8. 在 [**名稱**] 欄位中，輸入您的容器的名稱。 然後，在**Access**下拉功能表中，選取您想要的何種類型的存取原則。

    ![容器名稱](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] 根據預設，容器是私人且只能存取帳戶擁有者。 若要讓公用讀取權限 blob 在容器中，但無法以容器屬性和中繼資料，請使用 [**公用 Blob**選項。 若要允許容器和二進位大型物件的完整公用讀取權限，使用 [**公用容器**] 選項。

## <a name="step-3-prepare-the-connection-to-azure"></a>步驟 3︰ 準備 Azure 的連線

您可以上傳.vhd 檔案之前，必須先建立您的電腦和 Azure 訂閱之間的安全連線。 若要這麼做，您可以使用的 Azure Active Directory (Azure AD) 方法或認證方法。

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>使用 Azure AD 方法，將.vhd 檔案上傳

1. 開啟 [Azure PowerShell 主控台。

2. 輸入下列命令︰  
    `Add-AzureAccount`

    這個命令會開啟，您可以登入您的公司或學校帳戶登入視窗。

    ![PowerShell 視窗](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure 會驗證並儲存的認證資訊。 然後將其關閉視窗。

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>使用憑證的方法將.vhd 檔案上傳

1. 開啟 [Azure PowerShell 主控台。

2. [類型]:  `Get-AzurePublishSettingsFile`。

3. 在瀏覽器視窗隨即開啟，並提示您下載.publishsettings 檔案。 此檔案包含的資訊與 Azure 訂閱的憑證。

    ![在瀏覽器下載頁面](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. 儲存.publishsettings 檔案。

4. [類型]:  `Import-AzurePublishSettingsFile <PathToFile>`，其中`<PathToFile>`.publishsettings 檔案的完整路徑。

   如需詳細資訊，請參閱[開始使用 Azure cmdlet](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)。

   如需有關安裝和設定 PowerShell 的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

## <a name="step-4-upload-the-vhd-file"></a>步驟 4:.vhd 檔案上傳

當您上傳.vhd 檔案時，您可以將它放您 Blob 儲存體中的任何位置。 以下是一些您上傳檔案時，您會使用的字詞︰
-  **BlobStorageURL**是您在步驟 2 中建立的儲存空間帳戶的 URL。
-  **YourImagesFolder**是 Blob 儲存體中的容器，您要儲存圖像的位置。
- **VHDName**會出現在識別虛擬硬碟 Azure 傳統入口網站的標籤。
- **PathToVHDFile**是完整路徑和.vhd 檔案的名稱。


您在上一個步驟中使用 PowerShell 的 Azure 視窗中，輸入︰

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>步驟 5︰ 建立 VM.vhd 上傳檔案
您.vhd 檔案上傳後，您可以為圖像新增到自訂圖像，與您的訂閱相關聯，並建立的自訂映像的虛擬機器的清單。

1. 您在上一個步驟中使用 PowerShell 的 Azure 視窗中，輸入︰

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    > [AZURE.NOTE]使用 Linux OS 類型。 目前的 PowerShell 的 Azure 版本接受只有 「 Linux 」 或 「 Windows 」 做為參數。

2. 完成上述步驟之後，當您選擇 Azure 傳統入口網站上的 [**圖像**] 索引標籤列新的圖像。  

    ![選擇圖像](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)

3. 從圖庫建立虛擬機器。 現在**我的圖像**下提供此新的圖像。
4. 選取新的圖像。 接下來，幫助提示設定主機名稱、 密碼、 SSH 鍵等等。

    ![自訂圖像](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. 在您完成佈建後，您會看到您在 Azure 中執行的 FreeBSD VM。

    ![Azure FreeBSD 圖像](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)
