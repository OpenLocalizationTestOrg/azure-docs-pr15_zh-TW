<properties 
    pageTitle="教學課程︰ 為] 方塊的 Azure Active Directory 整合 |Microsoft Azure" 
    description="瞭解如何使用 Azure Active Directory 中的方塊，來啟用單一登入，自動化佈建和更多 ！" 
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




#<a name="tutorial-azure-active-directory-integration-with-box"></a>教學課程︰ 為] 方塊的 Azure Active Directory 整合


  
本教學課程中的目標是以顯示整合 Azure 和方塊。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   在方塊中測試租用戶
  
完成後本教學課程中，您已指派至] 方塊的 Azure AD 使用者將能夠單一登入應用程式] 方塊中的公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用整合應用程式] 方塊
2.  設定單一登入
3.  設定使用者和群組佈建
4.  將使用者指派

![案例](./media/active-directory-saas-box-tutorial/IC769537.png "案例")



##<a name="enabling-the-application-integration-for-box"></a>啟用整合應用程式] 方塊
  
本節的目標是大綱如何啟用整合應用程式] 方塊。

###<a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>若要啟用方塊的應用程式整合，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-box-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-box-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-box-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**方塊**。

    ![應用程式組件庫](./media/active-directory-saas-box-tutorial/IC701023.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**方塊**，，然後按一下 [新增應用程式**完成**。

    ![方塊](./media/active-directory-saas-box-tutorial/IC701024.png "方塊")



##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD] 方塊。 此程序的一部分，您需要上傳至 Box.com 的中繼資料。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 [Azure 傳統入口網站**] 方塊中**的應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769538.png "設定單一登入")

2.  在**您要如何登入] 方塊中的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769539.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在**方塊的租用戶 URL** ] 文字方塊中輸入您方塊租用戶的 URL (例如︰ https://<mydomainname>。 box.com)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-box-tutorial/IC669826.png "設定應用程式 URL")

4.  **設定單一登入] 方塊中，**在頁面上，下載您的中繼資料，請按一下**下載的中繼資料**，然後在本機電腦上的資料檔案。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC669824.png "設定單一登入")

5.  轉寄到] 方塊的中繼資料檔案支援小組。 支援小組需求設定單一登入您。

6.  選取單一登入設定確認，然後按一下 [**完成**，關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769540.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
本節的目標是大綱如何啟用佈建的 Active Directory 使用者帳戶] 方塊。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1. Azure 傳統入口網站中，在**方塊中**的應用程式整合頁面上，按一下 [開啟 [**設定使用者佈建**] 對話方塊的 [**設定使用者佈建**]。 

    ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769541.png "啟用自動使用者佈建")

2. **啟用佈建到] 方塊中的使用者**] 對話方塊在頁面上，按一下 [**啟用使用者佈建**。 

    ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769544.png "啟用自動使用者佈建")

3. 在 [**到] 方塊的存取權授與登入**] 頁面上，提供必要的認證，然後按一下 [**授權**。 

    ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769546.png "啟用自動使用者佈建")


4. 按一下 [**到] 方塊的 [授與存取權**以授權此項作業，並返回 Azure 傳統入口網站。 

    ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769549.png "啟用自動使用者佈建")


5. **佈建選項**] 頁面上的**物件類型進行佈建**核取方塊，讓您選擇群組物件佈建到] 方塊除了使用者物件。  請參閱 「 指派使用者和群組節 」 下方如需詳細資訊。


6. 若要完成設定，請按一下 [完成] 按鈕。 

    ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769551.png "啟用自動使用者佈建")



##<a name="assigning-a-test-user"></a>指派測試使用者
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-box-perform-the-following-steps"></a>若要將使用者指派至] 方塊中，執行下列步驟︰

1. 在 Azure 傳統入口網站中建立測試帳戶。

2. 在**] 方塊中**的應用程式整合頁面上，按一下 [**指派給使用者**。 

    ![為使用者指派](./media/active-directory-saas-box-tutorial/IC769552.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。 

    ![[是]](./media/active-directory-saas-box-tutorial/IC767830.png "[是]")
  
您現在應該等待 10 分鐘的時間，並確認帳戶已同步處理至] 方塊。

驗證，首先，您可以檢查佈建狀態]，即可在] 方塊中的應用程式整合頁面 Azure 傳統入口網站 D 中的儀表板。

![儀表板](./media/active-directory-saas-box-tutorial/IC769553.png "儀表板")

以相關的狀態，表示順利完成的使用者佈建循環圖︰

![整合狀態](./media/active-directory-saas-box-tutorial/IC769555.png "整合狀態")


在方塊的租用戶，同步處理的使用者都會在**管理主控台**中**管理使用者**。

![整合狀態](./media/active-directory-saas-box-tutorial/IC769556.png "整合狀態")


##<a name="assigning-users-and-groups"></a>指定使用者和群組

**方塊 > 使用者和群組**Azure 傳統入口網站中的索引標籤可讓您指定哪些使用者和群組應授與存取權] 方塊。 工作分派的使用者或群組會發生下列項目︰

* Azure AD 允許 （無論是直接指派或群組成員資格） 指派的使用者驗證] 方塊。 如果使用者未指派，Azure AD 就不會讓他們登入] 方塊中，並會傳回錯誤 Azure AD 登入頁面上。

* 應用程式] 方塊方塊會新增到使用者的[應用程式啟動器](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)。

* 如果已啟用自動佈建，然後指定的使用者或群組會新增至自動提供佈建佇列中。

    * 如果只有使用者物件已設定為會佈建後，然後直接指派的所有使用者都會都放在佈建佇列中，並指定的所有群組的成員的所有使用者將會都都置於佈建佇列。 
    
    * 如果已設定佈建群組物件，然後分派] 群組中的所有物件佈都建到] 方塊中，以及這些群組成員的所有使用者。 群組和使用者的成員資格會保留在寫入到] 方塊。
    
您可以使用**屬性 > 單一登入**索引標籤來設定哪些使用者屬性 （或宣告） 呈現方塊期間 SAML 式驗證]，以及**屬性 > 提供**索引標籤來設定如何使用者及群組屬性流程從 Azure AD 方塊期間佈建作業。 請參閱下列資源，如需詳細資訊。


## <a name="additional-resources"></a>其他資源

* [自訂宣告 SAML 權杖發行](active-directory-saml-claims-customization.md)
* [佈建︰ 自訂屬性對應](active-directory-saas-customizing-attribute-mappings.md)
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)
