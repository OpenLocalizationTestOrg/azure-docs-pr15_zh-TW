<properties
    pageTitle="Azure Active Directory 網域服務︰ 管理指南 |Microsoft Azure"
    description="Azure AD 網域服務受管理的網域建立組織單位 (OU)"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Azure AD 網域服務受管理的網域建立組織單位 (OU)
Azure AD 網域服務受管理的網域包含兩個內建的容器分別稱為 「 AADDC 電腦] 和 [AADDC 使用者]。 「 AADDC 電腦 ' 容器具有的電腦加入受管理的網域的所有電腦上的物件。 [AADDC 使用者] 容器包含 Azure AD 租用戶中的使用者和群組]。 有時候，可能需要在部署負載受管理的網域建立服務帳戶。 達到這個目的，您可以建立自訂組織單位 (OU) 上的受管理的網域，並建立該 OU 中的服務帳戶。 本文將示範如何建立 OU 中的受管理的網域。


## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>在 [遠端管理網域的虛擬機器上安裝 AD 系統管理工具
Azure AD 網域服務受管理的網域，可以從遠端使用熟悉的 Active Directory 系統管理工具例如 Active Directory 系統管理管理中心 (ADAC) 或 AD PowerShell 來管理。 租用戶系統管理員並沒有連線到網域控制站的受管理的網域，透過遠端桌面上的權限。 若要管理的受管理的網域，請安裝 AD 系統管理工具] 功能虛擬機器加入受管理的網域。 請參閱文章標題[管理 Azure AD 網域服務管理網域](active-directory-ds-admin-guide-administer-domain.md)的相關指示。

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>在受管理的網域建立組織單位
現在，AD 系統管理工具已安裝在加入虛擬機器的網域，我們可以使用這些工具來建立組織單位的受管理的網域上。 執行下列步驟︰

> [AZURE.NOTE] 只有 「 AAD DC 管理員 」 群組的成員有足夠的權限以建立自訂的 OU。 請確定您屬於此群組的使用者身分執行下列步驟。

1. 從 [開始] 畫面中，按一下 [**系統管理工具**]。 您應該會看到虛擬機器上安裝 AD 系統管理工具。

    ![在伺服器上安裝的系統管理工具](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. 按一下 [ **Active Directory 系統管理中心**]。

    ![Active Directory 系統管理中心](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. 若要檢視的網域，請按一下網域名稱 (例如，「 contoso100.com 」) 的左窗格中。

    ![ADAC-檢視網域](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. 在 [右側]**工作**窗格中，按一下 [**新增**網域名稱節點下。 在此範例中，我們可以按一下 [**新增**下右側的 [**工作**] 窗格的 'contoso100(local)' 節點。

    ![ADAC-新 OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. 您應該會看到建立組織單位的選項。 按一下以啟動 [**建立組織單位**] 對話方塊的**單位**。

6. 在 [**建立組織單位**] 對話方塊中指定新 OU**名稱**。 Ou 中提供的簡短描述。 您也可能 ou 設定**受管理**功能的變數。 若要建立自訂 OU，按一下**[確定]**。

    ![ADAC-建立 OU] 對話方塊](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. 新建立的 OU 現在應該出現在 AD 管理管理中心 (ADAC)。

    ![ADAC-OU 建立](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## <a name="permissionssecurity-for-newly-created-ous"></a>新建立的 Ou 中的權限/安全性。
根據預設，（AAD DC 管理員 」 群組的成員） 建立自訂 OU 授與使用者系統管理員權限 （「 完全控制） 透過 OU。 然後，使用者就可以繼續操作並授與權限給其他使用者或視 ' AAD DC 系統管理員] 群組。 以下螢幕擷取畫面，使用者中所見'bob@domainservicespreview.onmicrosoft.com'建立新的 「 MyCustomOU' 組織單位的人員授與完全控制權。

 ![ADAC-新 OU 安全性](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## <a name="notes-on-administering-custom-ous"></a>在 [管理自訂 Ou 備忘稿
現在，您建立的自訂 OU，您可以繼續，並在此 OU 中建立使用者、 群組、 電腦和服務帳戶。 您無法從 'AAD DC 使用者' OU 移動使用者或群組，到自訂 Ou。

> [AZURE.WARNING] 使用者帳戶、 群組、 服務帳戶及您建立自訂之下的電腦物件無法使用 Azure AD 租用戶。 換句話說，這些物件沒有顯示使用 Azure AD Graph API，或在 Azure AD ui 上。 Azure AD 網域服務受管理網域才可以使用這些物件。


## <a name="related-content"></a>相關的內容

- [管理 Azure AD 網域服務管理的網域](active-directory-ds-admin-guide-administer-domain.md)

- [Active Directory 系統管理中心︰ 快速入門](https://technet.microsoft.com/library/dd560651.aspx)

- [服務帳戶的逐步指南](https://technet.microsoft.com/library/dd548356.aspx)
