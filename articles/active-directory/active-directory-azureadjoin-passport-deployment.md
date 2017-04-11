<properties
    pageTitle="啟用 Microsoft Windows Hello 商務用您的組織中 |Microsoft Azure"
    description="若要啟用 Microsoft 密碼，您組織中部署指示。"
    services="active-directory"
    documentationCenter=""
    keywords="設定 Microsoft 密碼，Microsoft Windows Hello 商務部署"
    authors="markusvi"
    manager="femila"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="markvi"/>


# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Microsoft Windows Hello 企業版中啟用您的組織

後[連線 Windows 10 與 Azure Active Directory 網域的裝置](active-directory-azureadjoin-devices-group-policy.md)，請執行下列動作來啟用 Microsoft Windows Hello 商務用您的組織中︰

1. 部署系統管理中心組態管理員  

2. 設定原則設定

3. 設定憑證設定檔  




## <a name="deploy-system-center-configuration-manager"></a>部署系統管理中心組態管理員 

若要部署使用者根據憑證 Windows Hello 商務機碼，您需要下列項目︰

- **系統管理中心設定管理員目前分支**-您必須安裝 1606年或更高的版本。 如需詳細資訊，請參閱[文件的系統管理中心設定管理員](https://technet.microsoft.com/library/mt346023.aspx)和[系統管理中心設定 Manager 團隊部落格](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)。
- **公用基礎結構 (PKI)** -Microsoft Windows Hello 商務用使用啟用使用者的憑證，您必須擁有 PKI 中的位置。 如果您沒有帳戶，或您不想要用於使用者憑證，您可以部署新的網域控制站含有 Windows Server 2016 建立 10551 （或更好） 安裝。 若要[安裝在現有的網域中的複本網域控制站](https://technet.microsoft.com/library/jj574134.aspx)，或安裝[新的 Active Directory 樹，如果您要建立新的環境](https://technet.microsoft.com/library/jj574166)，請遵循的步驟。 （ISOs 可供下載[Signiant 媒體交換](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)上）。


## <a name="configure-policy-settings"></a>設定原則設定

若要設定 Microsoft Windows Hello 商務原則設定，您有兩個選項︰

- 在 Active Directory 中的群組原則 
- 系統管理中心組態管理員 


在 Active Directory 中的使用群組原則會設定 Microsoft Windows Hello 商務原則設定的建議的方式。 



使用系統管理中心設定管理員是慣用的方法，也使用時將其部署憑證。 此案例中︰

- 可確保較新的部署案例與相容性
- 需要 Windows 10 版 1607年或更佳的用戶端。

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>設定 Microsoft Windows Hello 商務用透過 Active Directory 中的群組原則

 
**步驟**︰

1.  開啟伺服器管理員]，然後瀏覽至 [**工具** > **群組原則管理**。
2.  從群組原則管理] 中，瀏覽至對應到您要啟用 Azure AD 加入的網域的 [網域] 節點。
3.  以滑鼠右鍵按一下**群組原則物件**，然後選取 [**新增**]。 授與您的群組原則物件的名稱，例如啟用 Windows Hello 商務用。 按一下**[確定]**。
4.  將新的群組原則物件，以滑鼠右鍵按一下，然後選取 [**編輯**]。
5.  瀏覽至 [**電腦設定** > **原則** > **系統管理範本** > **Windows 元件** > **Windows 認識商務用**。
6.  以滑鼠右鍵按一下**啟用 Windows Hello 商務用**，然後按一下**編輯**。
7.  選取 [**啟用**] 選項按鈕，然後按一下 [**套用]**。 按一下**[確定]**。
8.  您現在可以連結群組原則物件至您所選擇的位置。 若要啟用此網域的 Windows 10 裝置，您組織中的所有的原則，連結到網域的群組原則。 例如︰
 - 特定都位於 Windows 10 網域的電腦的 Active Directory 中組織單位 (OU)
 - 為 Windows 10 網域，就會自動註冊 Azure AD 的特定安全性群組


### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>設定 Windows Hello 使用系統管理中心設定管理員中的商務用


**步驟**︰


1. 開啟**系統管理中心組態管理員**]，然後瀏覽至**資產與規範 > 法規遵循設定 > 公司資源存取 > Windows Hello 為公司設定檔**。

    ![設定 Windows 認識商務用](./media/active-directory-azureadjoin-passport-deployment/01.png)


2. 在工具列在頂端，按一下 [**建立 Windows Hello 公司設定檔**。

    ![設定 Windows 認識商務用](./media/active-directory-azureadjoin-passport-deployment/02.png)

2. 在 [**一般**] 對話方塊中，執行下列步驟︰

    ![設定 Windows 認識商務用](./media/active-directory-azureadjoin-passport-deployment/03.png)

    。 在 [**名稱**] 文字方塊中，輸入您的設定檔，例如，**我的 WHfB 設定檔**的名稱。

    b。 按一下 [**下一步**]。


2. 在**支援的平台**] 對話方塊中，選取 [平台會使用這個 Windows Hello 公司設定檔，佈建後，然後按 [**下一步**]。

    ![設定 Windows 認識商務用](./media/active-directory-azureadjoin-passport-deployment/04.png)


2. 在 [**設定**] 對話方塊中，執行下列步驟︰

    ![設定 Windows 認識商務用](./media/active-directory-azureadjoin-passport-deployment/05.png)

    。 為**設定 Windows Hello 商務用**，請選取 [**啟用**]。

    b。 **使用受信任的平台模組 (TPM)**，請選取**所需**。 

    c。 **驗證方法**，選取 [**憑證**]。

    d。 按一下 [**下一步**]。



2. 在 [**摘要**] 對話方塊中，按一下 [**下一步**]。

2. 在 [**完成**] 對話方塊中，按一下 [**關閉**]。


2. 在頂端上工具列中，按一下 [**部署**]。

    ![設定 Windows 認識商務用](./media/active-directory-azureadjoin-passport-deployment/06.png)



## <a name="configure-the-certificate-profile"></a>設定憑證設定檔 

如果您使用內部部署驗證憑證驗證，您需要設定及部署憑證設定檔。 這項工作要求您設定 NDES 伺服器及憑證註冊點網站角色在系統管理中心設定管理員中。 如需詳細資訊，請參閱[的憑證設定檔組態管理員中的必要條件](https://technet.microsoft.com/library/dn261205.aspx)。

1. 開啟**系統管理中心組態管理員**]，然後瀏覽至**資產與規範 > 法規遵循設定 > 公司資源存取 > 的憑證設定檔**。


2. 選取具有智慧卡登入延伸的金鑰使用 (EKU) 的範本。

在**SCEP 註冊**頁面上的憑證設定檔，您需要選擇 [**安裝]，否則工時密碼失敗**為**金鑰儲存提供者**。



## <a name="next-steps"></a>後續步驟
* [企業版的 Windows 10︰ 裝置用於工作的方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [不需要密碼透過 Microsoft 密碼驗證身分識別](active-directory-azureadjoin-passport.md)
* [瞭解有關使用案例的 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [加入網域的裝置連接到 Windows 10 體驗 Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD 加入](active-directory-azureadjoin-setup.md)
