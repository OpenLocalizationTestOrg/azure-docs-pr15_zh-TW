<properties
    pageTitle="Azure Active Directory 網域服務︰ 管理上受管理的網域的 DNS |Microsoft Azure"
    description="管理 DNS Azure Active Directory 網域服務受管理的網域"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>管理 DNS Azure AD 網域服務受管理的網域
Azure Active Directory 網域服務中包含提供的受管理的網域的 DNS 解析 DNS （網域名稱解析） 伺服器。 有時候，您可能需要設定 DNS 的受管理的網域上。 您可能需要建立 DNS 記錄的電腦沒有加入網域、 設定的負載平衡器的虛擬 IP 位址或設定外部 DNS 轉寄站。 因此，屬於 AAD DC 管理員 」 群組授與使用者的受管理的網域的 DNS 管理權限。


## <a name="before-you-begin"></a>開始之前
若要執行本文中所列的工作，您需要︰

1. 有效**Azure 訂閱**。

2. **Azure AD 目錄**中的 [同步處理內部部署目錄或雲端專用的目錄。

3. **Azure AD 網域服務**必須啟用 Azure AD 目錄。 如果您尚未這麼做，請遵循[快速入門指南](./active-directory-ds-getting-started.md)所述的所有工作。

4. 您用來管理 Azure AD 網域服務**網域的虛擬機器**受管理的網域。 如果您沒有安裝虛擬機器，請依照下列文章標題[加入受管理的網域 Windows 虛擬機器](./active-directory-ds-admin-guide-join-windows-vm.md)中的所有工作。

5. 您需要**屬於 AAD DC 管理員 」 群組的使用者帳戶**的認證，在您的目錄，來管理您受管理的網域的 DNS。

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>任務 1-從遠端管理受管理的網域的 [DNS 佈建網域的虛擬機器
Azure AD 網域服務受管理的網域，可以從遠端使用熟悉的 Active Directory 系統管理工具例如 Active Directory 系統管理管理中心 (ADAC) 或 AD PowerShell 來管理。 同樣地，可以使用 [DNS 伺服器的系統管理工具，從遠端管理受管理的網域的 DNS。

Azure AD 目錄中的管理員並沒有連線到網域控制站的受管理的網域，透過遠端桌面上的權限。 「 AAD DC 管理員 」 群組的成員可以使用 DNS 伺服器工具加入受管理的網域的 Windows Server 用戶端電腦從遠端受管理的網域管理 DNS。 遠端伺服器管理工具 (RSAT) 選擇性功能，Windows Server 和加入受管理的網域中的用戶端電腦上的一部分，就可以安裝 DNS 伺服器工具。

第一個任務是佈建加入受管理的網域的 Windows Server 虛擬機器。 如需相關指示，請參閱標題為 [[加入至 Azure AD 網域服務管理網域的 Windows Server 虛擬機器](active-directory-ds-admin-guide-join-windows-vm.md)中的文件。


## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>任務 2-虛擬機器上安裝 DNS 伺服器工具
執行下列步驟，以加入網域虛擬機器上安裝 DNS 管理工具。 如需有關[安裝和使用遠端伺服器管理工具](https://technet.microsoft.com/library/hh831501.aspx)的詳細資訊，請參閱 Technet。

1. 瀏覽至 Azure 傳統入口網站中的**虛擬機器**節點。 選取您在任務 1 中建立的虛擬機器，然後按一下視窗底部的命令列上的 [**連線**]。

    ![連線到 Windows 虛擬機器](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. [傳統] 入口網站會提示您開啟或儲存檔案副檔名為 「.rdp 」，這用來連線到虛擬機器。 完成下載後，請按一下 [檔案]。

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

9. 在 [**功能**] 頁面上，按一下以展開 [**遠端伺服器管理工具**節點，然後按一下以展開 [**角色管理工具**節點。 從 [角色管理工具] 清單中選取**DNS 伺服器工具**] 功能。

    ![功能] 頁面](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. 在**確認**頁面上，按一下 [**安裝**虛擬機器上安裝 DNS 伺服器的 [工具] 功能。 在功能安裝完成後成功，請按一下 [**關閉**結束 [**新增角色和功能**] 精靈。

    ![確認頁面](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>任務 3-管理 DNS 的啟動 DNS 管理主控台
現在，安裝 DNS 伺服器工具功能加入虛擬機器的網域，我們可以使用 [DNS 工具來管理上管理網域的 DNS。

> [AZURE.NOTE] 您必須 「 AAD DC 管理員群組的成員]，來管理 DNS 的受管理的網域上。

1. 從 [開始] 畫面中，按一下 [**系統管理工具**]。 您應該會看到**DNS**主控台虛擬機器上安裝。

    ![系統管理工具-DNS 主控台](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. 按一下 [ **DNS** ] 以啟動 DNS 管理主控台]。

3. 在 [ **DNS 伺服器的連線**] 對話方塊中按一下選項標題為**下列的電腦**，並輸入 DNS 網域名稱的受管理的網域 (例如，「 contoso100.com 」)。

    ![DNS 主控台-連線至網域](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. DNS 主控台連線至受管理的網域。

    ![DNS 主控台-管理網域](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. 您現在可以使用 DNS 主控台新增 DNS 項目清單中已啟用 AAD 網域服務虛擬網路的電腦。

> [AZURE.WARNING] 請小心管理使用 DNS 管理工具的受管理網域的 DNS 時。 請確定該您**無法刪除或修改內建使用的網域服務中該網域的 DNS 記錄**。 內建的 DNS 記錄包括網域 DNS 記錄，名稱伺服器記錄，以及用於 DC 位置其他記錄。 如果您修改這些記錄時，網域服務會中斷虛擬網路上。


請參閱[Technet 上的 [DNS 工具文件](https://technet.microsoft.com/library/cc753579.aspx)管理 DNS 的相關詳細資訊。


## <a name="related-content"></a>相關的內容

- [Azure AD 網域服務-快速入門指南](./active-directory-ds-getting-started.md)

- [加入 Azure AD 網域服務受管理網域的 Windows Server 虛擬機器](active-directory-ds-admin-guide-join-windows-vm.md)

- [管理 Azure AD 網域服務管理的網域](active-directory-ds-admin-guide-administer-domain.md)

- [DNS 管理工具](https://technet.microsoft.com/library/cc753579.aspx)
