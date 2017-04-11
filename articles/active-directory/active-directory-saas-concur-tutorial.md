<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Concur |Microsoft Azure" 
    description="瞭解如何使用 Concur 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-concur"></a>教學課程︰ 使用 Concur 的 Azure Active Directory 整合  


本教學課程中的目標是以顯示 Azure 和 Concur 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   在 [Concur 租用戶

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Concur 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-concur-tutorial/IC769766.png "案例")

>[AZURE.NOTE] 透過 SAML 同盟 sso Concur 訂閱的設定不是個別的工作，您必須連絡 Concur 執行。

##<a name="enabling-the-application-integration-for-concur"></a>啟用 Concur 應用程式整合

本節的目標是大綱如何啟用 Concur 的整合應用程式。

###<a name="to-enable-the-application-integration-for-concur-perform-the-following-steps"></a>若要啟用的 Concur 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  從**目錄**清單中，選取要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-concur-tutorial/IC700994.png "應用程式")

4.  若要開啟**應用程式組件庫**，請按一下 [**新增應用程式**]，，，然後按一下 [**新增我的組織使用的應用程式**。

    ![您想要做什麼？](./media/active-directory-saas-concur-tutorial/IC700995.png "您想要做什麼？")

5.  在**搜尋] 方塊**中，輸入**Concur**。

    ![應用程式組件庫](./media/active-directory-saas-concur-tutorial/IC721727.png "應用程式組件庫")

6.  在 [結果] 窗格中，選取**Concur**，，然後按一下 [新增應用程式**完成**。

    ![concur](./media/active-directory-saas-concur-tutorial/IC721728.png "concur")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Concur。

>[AZURE.NOTE] 透過 SAML 同盟 sso Concur 訂閱的設定不是個別的工作，您必須連絡 Concur 執行。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Concur**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-concur-tutorial/IC769767.png "設定單一登入")

2.  在**您要如何登入 Concur 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-concur-tutorial/IC769768.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Concur 登入 URL** ] 文字方塊中，輸入您 concur 租用戶登入的 URL，然後再按 [**下一步**︰ 

    ![設定登入 URL](./media/active-directory-saas-concur-tutorial/IC769769.png "設定登入 URL")

4.  在**設定單一登入 Concur 在**頁面上，執行下列步驟。

    ![設定登入 URL](./media/active-directory-saas-concur-tutorial/IC769770.png "設定登入 URL")

    1.  按一下 [下載中繼資料，然後安全資料檔案給您的電腦]。
    2.  連絡您的租用戶設定 SSO Concur 支援小組。
    3.  選取單一登入設定確認，然後按一下 [**完成**，關閉 [**設定單一登入**] 對話方塊。  

    >[AZURE.NOTE] 透過 SAML 同盟 sso Concur 訂閱的設定不是個別的工作，您必須連絡 Concur 執行。

##<a name="configuring-user-provisioning"></a>設定使用者佈建

本節的目標是大綱如何啟用 Active Directory 使用者帳戶，以便 Concur 佈建。

若要啟用費用服務中，那里應用程式必須是正確的設定，並使用 Web 服務管理員設定檔。 不只是新增至現有的管理員設定檔所使用 T & E 管理功能的 WS 管理員角色。

Concur 顧問或用戶端系統管理員必須建立不同的 Web 服務的系統管理員設定檔和用戶端系統管理員必須使用這個設定檔的 Web 服務管理員函數 （例如啟用應用程式）。 這些設定檔必須保持獨立於用戶端系統管理員的每日 T & E 管理員設定檔 （T & E 管理員設定檔應該不需要指派 WSAdmin 角色）。

當您建立要用於啟用應用程式的設定檔時，輸入使用者設定檔欄位用戶端系統管理員的名稱。 這將會指派擁有權的設定檔。一旦建立設定檔，用戶端必須登入協力廠商應用程式 [Web 服務] 功能表中按一下 [*啟用*] 按鈕，這個設定檔。

基於下列原因，這個動作不應該與他們使用標準 T & E 的管理設定檔。

1.  用戶端必須啟用應用程式後，會出現對話視窗上按一下 「**」 的項目。 按一下確認用戶端是願意協力廠商應用程式存取其資料，因此您或合作夥伴無法按一下 [是] 按鈕。
2.  如果用戶端系統管理員已啟用應用程式的使用 T & E 管理員設定檔離職 （導致被停用的設定檔），使用應用程式啟用另一個使用中的 WS 管理員設定檔與之前，將無法運作的設定檔啟用任何應用程式。 這是為什麼您應該要建立不同的 WS 管理員設定檔。
3.  如果系統管理員離開公司，變更為替代系統管理員的相關 WS 管理員設定檔的名稱視需要而不會影響已啟用的應用程式因為不需要的設定檔停用狀態

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  登入您的**Concur**租用戶。

2.  從 [**管理**] 功能表中，選取 [ **Web 服務**]。

    ![Concur 租用戶](./media/active-directory-saas-concur-tutorial/IC721729.png "Concur 租用戶")

3.  在左邊，從**Web 服務**] 窗格中，選取 [**啟用協力廠商應用程式**。

    ![啟用協力廠商應用程式](./media/active-directory-saas-concur-tutorial/IC721730.png "啟用協力廠商應用程式")

4.  **啟用應用程式**清單中，選取**Azure Active Directory**]，然後按一下**啟用**。

    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  按一下 [ **]**以關閉 [**確認巨集指令**] 對話方塊。

    ![確認 [巨集指令](./media/active-directory-saas-concur-tutorial/IC721732.png "確認 [巨集指令")

6.  在 Azure 傳統的入口網站中，選取**Concur**從應用程式清單，以開啟 [ **Concur** ] 對話方塊的頁面。

7.  若要開啟 [**設定使用者佈建**的對話方塊頁面，按一下 [**設定使用者佈建**]。

8.  輸入使用者名稱和您的 Concur 系統管理員的密碼，然後按一下 [**下一步**。

9.  若要完成設定，請在 [**確認**] 頁面上的按一下 [**完成**] 按鈕。

您現在可以建立測試帳戶、 10 分鐘的時間，請等候並確認帳戶已同步處理至 Concur。
##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-concur-perform-the-following-steps"></a>若要指定 Concur 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Concur**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-concur-tutorial/IC769771.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-concur-tutorial/IC767830.png "[是]")

您現在應該等待 10 分鐘的時間，並確認帳戶已同步處理至 Concur。

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
