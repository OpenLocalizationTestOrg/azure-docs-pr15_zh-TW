<properties
    pageTitle="Azure Active Directory 網域服務︰ 管理受管理的網域 |Microsoft Azure"
    description="管理 Azure Active Directory 網域服務受管理的網域"
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

# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>管理的 Azure Active Directory 網域服務受管理的網域
本文將說明如何管理的 Azure Active Directory (AD) 網域服務受管理的網域。


## <a name="before-you-begin"></a>開始之前
若要執行本文中所列的工作，您需要︰

1. 有效**Azure 訂閱**。

2. **Azure AD 目錄**中的 [同步處理內部部署目錄或雲端專用的目錄。

3. **Azure AD 網域服務**必須啟用 Azure AD 目錄。 如果您尚未這麼做，請遵循[快速入門指南](./active-directory-ds-getting-started.md)所述的所有工作。

4. 您用來管理 Azure AD 網域服務**網域的虛擬機器**受管理的網域。 如果您沒有安裝虛擬機器，請依照下列文章標題[加入受管理的網域 Windows 虛擬機器](./active-directory-ds-admin-guide-join-windows-vm.md)中的所有工作。

5. 您需要**屬於 AAD DC 管理員 」 群組的使用者帳戶**的認證，在您的目錄，來管理您受管理的網域。

<br>


## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>管理工作，您可以執行的受管理的網域
「 AAD DC 管理員 」 群組的成員權限的受管理的網域上，來執行下列工作︰

- 加入受管理的網域中的電腦。

- 在受管理的網域設定 「 AADDC 電腦 」 和 「 AADDC 使用者 ' 容器的內建 GPO。

- 管理 DNS 的受管理的網域上。

- 建立及管理自訂組織單位 (Ou) 的受管理的網域上。

- 取得至電腦的系統管理存取加入受管理的網域。


## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>您沒有在 [管理網域的系統管理員權限
網域是由 Microsoft，包括例如修補、 監控及執行備份的活動管理。 因此，網域已鎖定，而且您不需要的權限的網域上執行某些管理任務。 您無法執行的工作的部分範例如下。

- 您不會授與受管理的網域的網域管理員或企業系統管理員權的限。

- 您無法將架構的受管理的網域。

- 您無法連線至受管理的網域使用遠端桌面網域控制站。

- 您無法將網域新增至受管理的網域。


## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>任務 1-佈建網域的 Windows Server 虛擬機器遠端管理受管理的網域
Azure AD 網域服務受管理的網域，可以使用熟悉的 Active Directory 系統管理工具例如 Active Directory 系統管理管理中心 (ADAC) 或 AD PowerShell 來管理。 租用戶系統管理員並沒有連線到網域控制站的受管理的網域，透過遠端桌面上的權限。 因此，AAD DC 管理員 」 群組的成員可以管理使用 AD 系統管理工具加入受管理的網域的 Windows Server 用戶端電腦從遠端受管理的網域。 遠端伺服器管理工具 (RSAT) 選擇性功能，Windows Server 和加入受管理的網域中的用戶端電腦上時，就可以安裝 AD 系統管理工具。

第一步是設定 Windows Server 虛擬機器加入受管理的網域。 如需相關指示，請參閱標題為 [[加入至 Azure AD 網域服務管理網域的 Windows Server 虛擬機器](active-directory-ds-admin-guide-join-windows-vm.md)中的文件。

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>從用戶端電腦 (例如，Windows 10)，從遠端管理受管理的網域
此文件使用 Windows Server 虛擬機器中的指示來管理 AAD DS 受管理的網域。 不過，您也可以選擇使用 Windows 用戶端 (例如，Windows 10) 虛擬機器執行此作業。

您可以在 Windows 用戶端虛擬機器上的 [[安裝遠端伺服器管理工具 (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)依循 TechNet 上的指示。


## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>任務 2-虛擬機器上安裝 Active Directory 系統管理工具
執行下列步驟，以加入網域虛擬機器上安裝 Active Directory 系統管理工具。 [安裝和使用遠端伺服器管理工具的詳細資訊](https://technet.microsoft.com/library/hh831501.aspx)，請參閱 Technet。

1. 瀏覽至 Azure 傳統入口網站中的**虛擬機器**節點。 選取您在任務 1 中建立的虛擬機器，然後按一下視窗底部的命令列上的 [**連線**]。

    ![連線到 Windows 虛擬機器](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. [傳統] 入口網站會提示您開啟或儲存檔案副檔名為 「.rdp 」，這用來連線到虛擬機器。 按一下以完成下載後，請開啟檔案。

3. 在 [登入提示時，使用屬於 AAD DC 管理員 」 群組的使用者認證。 例如，我們使用'bob@domainservicespreview.onmicrosoft.com'我們的大小寫。

4. 從 [開始] 畫面中，開啟 [**伺服器管理員**]。 在 [伺服器管理員] 視窗的中央窗格中，按一下 [**新增角色和功能**]。

    ![虛擬機器上啟動伺服器管理員](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. 在**您開始前****新增角色和功能] 精靈**頁面，按一下 [**下一步**]。

    ![在您開始頁面之前](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. 在 [**安裝類型**] 頁面離開核取 [**角色或功能的安裝**選項，然後按 [**下一步**]。

    ![安裝類型] 頁面](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. 在 [**選擇伺服器**] 頁面選取目前的虛擬機器 server 集區，然後按一下 [**下一步**。

    ![伺服器選擇頁面](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. 在 [**伺服器角色**] 頁面上，按一下 [**下一步**]。 我們跳過此頁面，因為我們不會在伺服器上安裝任何角色。

9. 在 [**功能**] 頁面上，按一下以展開 [**遠端伺服器管理工具**節點，然後按一下以展開 [**角色管理工具**節點。 從 [角色管理工具] 清單中選取**AD DS 和 AD LDS 工具**功能。

    ![功能] 頁面](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. 在**確認**頁面上，按一下 [安裝 AD 與 AD LDS 工具] 功能虛擬機器上的 [**安裝**]。 在功能安裝完成後成功，請按一下 [**關閉**結束 [**新增角色和功能**] 精靈。

    ![確認頁面](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>任務 3-連線至及探索受管理的網域
現在，AD 系統管理工具已安裝在加入虛擬機器的網域，我們可以使用這些工具來探索及管理受管理的網域。

> [AZURE.NOTE] 您必須 「 AAD DC 管理員群組的成員]，來管理受管理的網域。

1. 從 [開始] 畫面中，按一下 [**系統管理工具**]。 您應該會看到虛擬機器上安裝 AD 系統管理工具。

    ![在伺服器上安裝的系統管理工具](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. 按一下 [ **Active Directory 系統管理中心**]。

    ![Active Directory 系統管理中心](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. 若要瀏覽網域按一下網域名稱 (例如，「 contoso100.com 」) 的左窗格中。 請注意兩個容器分別稱為 「 AADDC 電腦] 和 [AADDC 使用者]。

    ![ADAC-檢視網域](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. 按一下 [容器稱為**AADDC 使用者**查看 [所有使用者和群組屬於受管理的網域。 您應該會看到使用者帳戶和群組從 Azure AD 租用顯示戶設定容器中。 請注意，在此範例中，稱為 「 bob' 使用者和群組，稱為 「 AAD DC 管理員 」 的使用者帳戶，可在此容器中。

    ![ADAC-網域使用者](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. 按一下 [容器稱為**AADDC 電腦**看到此受管理的網域的電腦。 您應該會看到的項目目前的虛擬機器加入網域。 電腦帳戶加入 Azure AD 網域服務受管理網域的所有電腦會儲存在這個 'AADDC 電腦' 容器。

    ![ADAC-加入電腦的網域](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>相關的內容

- [Azure AD 網域服務-快速入門指南](./active-directory-ds-getting-started.md)

- [加入 Azure AD 網域服務受管理網域的 Windows Server 虛擬機器](active-directory-ds-admin-guide-join-windows-vm.md)

- [部署遠端伺服器管理工具](https://technet.microsoft.com/library/hh831501.aspx)
