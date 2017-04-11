<properties 
    pageTitle="教學課程︰ 使用 SAP HANA 雲端平台的 Azure Active Directory 整合 |Microsoft Azure" 
    description="瞭解如何使用 SAP HANA 雲端平台與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>教學課程︰ 使用 SAP HANA 雲端平台的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 SAP HANA 雲端平台的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   SAP HANA 雲端平台帳戶
  
完成後本教學課程中，您指派給 SAP HANA 雲端平台的 Azure AD 使用者將能夠[存取面板簡介](active-directory-saas-access-panel-introduction.md)在應用程式中的單一登。

>[AZURE.IMPORTANT]您需要將您自己的應用程式部署或訂閱您 SAP HANA 雲端平台的帳戶上的應用程式，若要測試單一登入。 在本教學課程中，在帳戶部署應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 SAP HANA 雲端平台的應用程式整合
2.  設定單一登入
3.  將角色指派給使用者
4.  將使用者指派

![案例](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "案例")
##<a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>啟用 SAP HANA 雲端平台的應用程式整合
  
本節的目標是大綱如何啟用 SAP HANA 雲端平台的應用程式整合。

###<a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>若要啟用 SAP HANA 雲端平台的應用程式整合，請執行下列步驟︰

1.  在 Azure 管理入口網站，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**SAP HANA 雲端平台**。

    ![應用程式組件庫](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**SAP HANA 雲端平台**，然後按一下要新增應用程式**完成**。

    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD SAP HANA 雲端平台。  
此程序的一部分，您會需要上傳至您的 SAP HANA 雲端平台租用戶的底數 64 編碼的憑證。  
如果您不熟悉這個程序，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **SAP HANA 雲端平台**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "設定單一登入")

2.  在**您要如何登入 SAP HANA 雲端平台的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "設定單一登入")

3.  在不同的網頁瀏覽器視窗中，登入 SAP HANA 雲端的平台終極在 https://account。\<橫向主機\>.ondemand.com/cockpit (例如︰ *https://account.hanatrial.ondemand.com/cockpit*)。

4.  按一下 [**信任**] 索引標籤。

    ![信任](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "信任")

5.  在信任管理] 區段中，執行下列步驟︰

    ![取得中繼資料](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "取得中繼資料")

    1.  按一下 [**本機的服務提供者**] 索引標籤。
    2.  若要下載 SAP HANA 雲端平台的中繼資料檔案，按一下 [**取得中繼資料**]。

6.  在 Azure 作用中的傳統入口網站，在**設定應用程式 URL**頁面上，執行下列步驟，然後再按 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "設定應用程式 URL")

    1.  在 [**登入 URL** ] 文字方塊中，輸入您的使用者用來登入您的**SAP HANA 雲端平台**應用程式的 URL。 這是資源的帳戶特定 URL 的 SAP HANA 雲端平台應用程式中受保護。 URL 根據下列模式︰ *https://\<applicationName\>\<accountName\>。\<橫向主機\>.ondemand.com/\<路徑\_至\_保護\_資源\>*(例如︰ *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]這是您需要要驗證的使用者的 SAP HANA 雲端平台應用程式中的 URL。

    2.  開啟下載的 SAP HANA 雲端平台的中繼資料檔案，然後找到 [ **ns3:AssertionConsumerService**標籤。
    3.  複製屬性的值**的位置**，然後再貼到 [ **SAP HANA 雲端平台回覆 URL** ] 文字方塊。

7.  在**設定單一登入 SAP HANA 雲端平台**頁面上，以下載您的中繼資料，請按一下**下載中繼資料**]，然後儲存您的電腦上的檔案。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "設定單一登入")

8.  在 SAP HANA 雲端的平台終極，在 [**本機的服務提供者**] 區段中，執行下列步驟︰

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "信任管理")

    1.  按一下 [**編輯**]。
    2.  為**設定類型**，選取 [**自訂**]。
    3.  **本機提供者的名稱**，以保留預設值。
    4.  若要產生**登入索引鍵**和**簽章憑證**金鑰，請按一下 [**產生金鑰**。
    5.  為**主要傳播**，選取 [**停用**]。
    6.  為**強制驗證**] 下，選取 [**停用**]。
    7.  按一下 [**儲存**]。

9.  按一下 [**信任的身分識別提供者**] 索引標籤，然後按一下 [**新增受信任的身分識別提供者**。

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "信任管理")

    >[AZURE.NOTE]若要管理信任的身分識別提供者的清單，您需要在本機的服務提供者] 區段中選擇 [自訂設定類型。 預設設定類型，您有非編輯和隱含信任 SAP 識別碼服務。 您不需要任何信任設定。

10. 按一下 [**一般**] 索引標籤，然後按一下 [上傳已下載的中繼資料檔案的 [**瀏覽**。

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "信任管理")

    >[AZURE.NOTE] 之後上傳的中繼資料檔案，會自動填入**單一登入 URL**、**單一登出 URL**及**簽章憑證**的值。

11. 按一下 [**屬性**] 索引標籤。

12. 在 [**屬性**] 索引標籤中，執行下列步驟︰

    ![屬性](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "屬性")

    1.  藉由按一下**Add Assertion-Based 屬性**，新增下列判斷提示型屬性︰

        |判斷提示屬性| 本金屬性|
        |-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname|   名字|--------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname|        [姓氏]|-----------|
        |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|電子郵件|

    >[AZURE.NOTE]屬性的設定而定如何 HCP 上的應用程式開發，亦即預期 SAML 回應，而且在哪一個名稱 （本金屬性），其存取這個屬性中的程式碼的屬性。
    >  
    >。  螢幕擷取畫面中的**預設屬性**只適用於說明之用。 它不需要進行此工作的案例。  
    >
    >b。  名稱和**本金屬性**螢幕擷取畫面所示的值取決於應用程式開發的方式。 有可能應用程式需要不同的對應。

13. Azure 傳統入口網站，在 [**設定單一登入 SAP HANA 雲端平台**對話] 頁面中選取單一登入設定確認，然後按 [**完成**。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "設定單一登入")
  
為選用步驟中，您可以設定判斷提示型群組 Azure Active Directory 身分識別提供者

>[AZURE.NOTE]SAP HANA 雲端平台上使用群組可讓您以動態方式 SAML 2.0 判斷提示中的屬性值取決於您 SAP HANA 雲端平台應用程式中的一或多個角色指派一或多位使用者。 例如，如果判斷提示包含 [屬性]*合約 = 暫時*」，您可能會想所有受影響的使用者，都會新增至 「*暫時*」 群組。 「*暫時*」 群組可能包含一或多個角色，從一或多個應用程式部署在您 SAP HANA 雲端平台的帳戶。
>  
>如果您想要質量指派一或多個角色 SAP HANA 雲端平台帳戶中的應用程式的多個使用者，請使用判斷提示為基礎的群組。 如果您只想要指派給我們建議您直接在 [**授權**] 索引標籤中的 SAP HANA 雲端平台終極分派 (a) 特定角色的單一或小的數字的使用者。

##<a name="assigning-a-role-to-a-user"></a>將角色指派給使用者
  
若要啟用 Azure AD 使用者登入 SAP HANA 雲端平台，您必須為其指派角色 SAP HANA 雲端平台。

###<a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>若要指派角色給使用者，請執行下列步驟︰

1.  登入您的**SAP HANA 雲端平台**終極。

2.  執行下列步驟︰

    ![授權](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "授權")

    1.  按一下 [**授權**]。
    2.  按一下 [**使用者**] 索引標籤。
    3.  在 [**使用者**] 文字方塊中，輸入使用者的電子郵件地址。
    4.  按一下 [**指派**至角色指派給使用者。
    5.  按一下 [**儲存**]。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>若要指定 SAP HANA 雲端平台的使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **SAP HANA 雲端平台**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。