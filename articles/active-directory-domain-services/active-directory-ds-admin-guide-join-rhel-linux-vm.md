<properties
    pageTitle="Azure Active Directory 網域服務︰ RHEL VM 加入受管理的網域 |Microsoft Azure"
    description="Azure AD 網域服務中加入紅色角色企業 Linux 虛擬機器"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>紅色角色企業 Linux 7 虛擬機器加入受管理的網域
本文將示範如何紅色角色企業 Linux (RHEL) 7 虛擬機器加入至 Azure AD 網域服務受管理的網域。

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>佈建紅色角色企業 Linux 虛擬機器
執行下列步驟，以佈建 RHEL 7 虛擬機器使用 Azure 入口網站。

1. [Azure 入口網站](https://portal.azure.com)登入。

    ![Azure 入口網站的儀表板](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. 在左窗格中按一下 [**新增]** ，然後搜尋列中輸入**紅色角色**下列的螢幕擷取畫面所示。 紅色角色企業 Linux 的項目會出現在搜尋結果。 按一下 [**紅色角色企業 Linux 7.2**]。

    ![在結果中選取 RHEL](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. 在 [**所有項目**] 窗格中的搜尋結果應該列出紅色角色企業 Linux 7.2 圖像。 按一下**紅色角色企業 Linux 7.2**若要檢視的虛擬機器圖像的相關資訊。

    ![在結果中選取 RHEL](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. 在 [**紅色角色企業 Linux 7.2** ] 窗格中，您應該會看到虛擬機器影像的相關詳細資訊。 在 [**選取部署模型**] 下拉式清單中，選取 [**傳統**]。 然後按一下 [**建立**] 按鈕。

    ![檢視圖像詳細資料](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. 在 [**建立 VM** ] 窗格中，輸入新的虛擬機器**主機名稱**。 在 [**使用者名稱**] 欄位和**密碼**，也指定本機系統管理員使用者名稱。 您也可以選擇使用 SSH 鍵來本機系統管理員的使用者進行驗證。 也選取虛擬機器**價格層**。

    ![建立 VM-基本的詳細資料](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. 按一下 [**選擇性的設定**]。 在 [**選擇性的設定**] 窗格中，按一下 [**網路**。

    ![建立 VM-設定虛擬網路](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. 如此會開啟名為 [**網路**] 窗格。 在 [**網路**] 窗格中，按一下以選取應該要部署 Linux VM 虛擬網路的**虛擬網路**。 這會開啟 [**虛擬網路**] 窗格。 在 [**虛擬網路**] 窗格中，選擇 [**使用現有的虛擬網路**] 選項。 然後選取 [虛擬 Azure AD 網域服務有的網路。 在此範例中，選擇 ['MyPreviewVNet' 虛擬網路。

    ![建立 VM-選取虛擬網路](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. 在 [**選擇性的設定**] 窗格中，按一下**[確定**] 按鈕。

    ![建立 VM-選取的虛擬網路](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. 您已準備好要建立虛擬機器。 在 [**建立 VM** ] 窗格中，按一下 [**建立**] 按鈕。

    ![建立 VM-準備](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. 新的虛擬機器根據 RHEL 7.2 圖像的部署應該會啟動。

  ![建立 VM-部署入門](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. 請稍候幾分鐘虛擬機器應該部署成功並可供使用。

  ![建立 VM-部署](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>從遠端連線到新能夠 Linux 虛擬機器
RHEL 7.2 虛擬機器佈 Azure 中。 下一步是從遠端連線至虛擬機器。

**連線到 RHEL 7.2 虛擬機器**追蹤文件[如何登入執行 Linux 虛擬機器](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)中的指示進行。

其餘的步驟假設您使用 PuTTY SSH 用戶端連線至 RHEL 虛擬機器。 如需詳細資訊，請參閱[PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. 開啟 PuTTY 程式。

2. 新建立的 RHEL 虛擬機器中輸入 [**主機名稱**]。 在此範例中，我們的虛擬機器有主機名稱為 「 contoso rhel.cloudapp.net 」。 如果您不確定您 VM 主機名稱，請參閱 Azure 入口網站上的 [VM 儀表板。

    ![PuTTY 連線](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. 虛擬機器使用時建立虛擬機器您指定的本機系統管理員認證登入。 在此範例中，我們會使用本機系統管理員帳戶 」 mahesh 」。

    ![PuTTY 登入](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Linux 虛擬機器上安裝所需的封裝
後連線至虛擬機器下, 一步是安裝套件所需的網域加入虛擬機器上。 執行下列步驟︰

1. **安裝 realmd:**Realmd 套件用於網域加入。 在您 PuTTY terminal 中，輸入以下命令︰

    sudo yum 安裝 realmd

    ![安裝 realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    請稍候幾分鐘 realmd 套件應該安裝虛擬機器上。

    ![安裝 realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **安裝 sssd:**Realmd 套件取決於 sssd 執行網域加入的作業。 在您 PuTTY terminal 中，輸入以下命令︰

    sudo yum 安裝 sssd

    ![安裝 sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    請稍候幾分鐘 sssd 套件應該安裝虛擬機器上。

    ![安裝 realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **安裝 kerberos:**在您 PuTTY terminal 中，輸入以下命令︰

    sudo yum 安裝 krb5 工作站 krb5-程式庫

    ![安裝 kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    請稍候幾分鐘 realmd 套件應該安裝虛擬機器上。

    ![Kerberos 安裝](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>加入受管理的網域中的 Linux 虛擬機器
現在 Linux 虛擬機器上安裝所需的封裝下, 一步就是加入虛擬機器為受管理的網域。

1. 探索 AAD 網域服務管理的網域。 在您 PuTTY terminal 中，輸入以下命令︰

    sudo 領域探索 CONTOSO100.COM

    ![探索領域](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    如果**領域探索**是無法找到您受管理的網域，請確定該網域可從虛擬機器 （嘗試偵測 （ping））。 同時請確定的虛擬機器確實已部署至受管理的網域有相同的虛擬網路。

2. 初始化 kerberos。 在您 PuTTY terminal 中，輸入下列命令。 請確定您指定的所屬的 「 AAD DC 管理員 」 群組的使用者。 只有這些使用者可以將電腦加入受管理的網域。

    kinitbob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    請確定您指定的網域名稱設為小寫，還 kinit 失敗。

3. 加入網域的 [電腦]。 在您 PuTTY terminal 中，輸入下列命令。 指定您在先前的步驟 (「 kinit 」) 中指定的相同的使用者。

    sudo 領域加入-詳細 CONTOSO100.COM U'bob@CONTOSO100.COM'

    ![領域加入](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

您應收到一則訊息 （「 成功註冊電腦領域中 」） 電腦成功加入受管理的網域時。


## <a name="verify-domain-join"></a>驗證網域加入
若要快速驗證是否台機器已經順利加入受管理的網域。 連線至加入 RHEL VM 使用 SSH 和網域的使用者帳戶，然後核取的使用者帳戶已正確解決新網域。

1. 在您 PuTTY terminal 中，輸入下列命令以連線至加入 RHEL 虛擬機器使用 SSH 新網域。 使用受管理的網域所屬的網域帳戶 (例如，'bob@CONTOSO100.COM'在此情況下。)

    ssh-l bob@CONTOSO100.COM contoso rhel.cloudapp.net

2. 在您 PuTTY terminal 中，輸入下列命令，請參閱是否正確初始的主目錄。

    顯示密碼詢問

3. 在您 PuTTY terminal 中，輸入下列命令，請參閱是否正確解析群組成員資格。

    識別碼

這些命令的輸出範例所示︰

![驗證網域加入](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## <a name="troubleshooting-domain-join"></a>疑難排解網域加入
請參閱[疑難排解網域加入](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join)文章。


## <a name="related-content"></a>相關的內容
- [Azure AD 網域服務-快速入門指南](./active-directory-ds-getting-started.md)

- [加入 Azure AD 網域服務受管理網域的 Windows Server 虛擬機器](active-directory-ds-admin-guide-join-windows-vm.md)

- [如何登入執行 Linux 虛擬機器](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)。

- [安裝 Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)

- [紅色的角色企業 Linux 7-Windows 整合指南](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
