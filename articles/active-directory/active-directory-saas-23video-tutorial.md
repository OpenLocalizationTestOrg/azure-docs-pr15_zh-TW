<properties
    pageTitle="教學課程︰ 23 視訊的 Azure Active Directory 整合 |Microsoft Azure"
    description="瞭解如何設定單一登入之間 Azure Active Directory 和 23 的視訊。"
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>教學課程︰ 23 視訊的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何與 Azure Active Directory (Azure AD) 整合 23 的影片。  
整合 23 Azure AD 與影片為您提供下列優點︰ 

- 您可以控制可存取 23 影片 Azure AD 
- 您可以讓使用者能自動取得登入至 23 視訊 （單一登入），其 Azure AD 帳戶

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件 

若要設定 Azure AD 整合 23 的視訊，您需要下列項目︰

- Azure AD 訂閱
- 23 影片單一登入啟用訂閱


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 

 
## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 23 的視訊 
2. 設定及測試 Azure AD 單一登入


## <a name="adding-23-video-from-the-gallery"></a>從圖庫新增 23 的視訊
若要設定 23 影片整合 Azure AD，您需要從庫新增 23 視訊至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 23 的視訊，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**23 的影片**。

    ![應用程式][5]

7. 在 [結果] 窗格中，選取**23 的視訊**，然後按一下**完成**新增應用程式。

    ![應用程式][25]

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和測試 Azure AD 單一登入與 23 根據稱為 「 許承恩 」 的測試使用者的視訊。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 23 的視訊︰ Azure AD 中的使用者。 換句話說，連結和之間的關聯 Azure AD 使用者 23 相關的使用者影片必須建立。  
所指派的**使用者名稱**值在 Azure AD 為 23 視訊**的使用者名稱**的值建立此連結關聯。
 
若要設定並測試 Azure AD 單一登入與 23 的視訊，您需要完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 23 視訊測試使用者](#creating-a-23-video-test-user)**-23 Azure AD 表示她的已連結的視訊中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用和設定單一登入應用程式 23 的影片。

**若要設定 Azure AD 單一登入與 23 的視訊，請執行下列步驟︰**

1. 在 [Azure 傳統入口網站， **23 影片**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][6] 

2. 在**您要如何登入 23 的視訊的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![Azure AD 單一登入][7] 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![Azure AD 單一登入][8] 
 
     。 在 [**回覆 URL** ] 文字方塊中，輸入您的使用者用來登入 23 視訊網站的 URL (例如︰ *https://britta-simon.23Video.com/saml/login*)。

     > [AZURE.NOTE] 使用 SAML 2.0 的 active Directory 整合適用於所有 23 的影片使用者。 請連絡支援部門[support@23company.com](mailto:support@23company.com)如果您需要關聯的中繼資料。

     b。 按一下 [**下一步**]。
 
4. 在**設定單一登入 23 影片在**頁面上，執行下列步驟︰

    ![Azure AD 單一登入][9] 

    。 按一下 [下載憑證，然後儲存您的電腦上的檔案。

    b。 請連絡 23 視訊支援小組透過[support@23company.com](mailto:support@23company.com)，向他們提供下載憑證**發行者 URL**、**單一登入服務的 URL**、**單一 Sign-Out URL**，然後請要求他們設定 SSO 23 影片應用程式。 

    c。 按一下 [**下一步**]。


6. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按 [**下一步**。 

    ![Azure AD 單一登入][10]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
  
    ![Azure AD 單一登入][11]




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)  

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png) 
 
4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)  

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png) 
 
    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。
    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png) 
 
8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png) 
  
    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   

  
 
### <a name="creating-a-23-video-test-user"></a>建立 23 的影片測試使用者

本節的目標是建立稱為許承恩 23 視訊中的使用者。

**若要建立稱為許承恩 23 視訊中的使用者，請執行下列步驟︰**

1. 以系統管理員身分登入您 23 的影片公司網站。

1. 移至 [**設定]**。


2. 在 [**使用者**] 區段中，按一下 [**設定**]。

    ![為使用者指派][400]

3. 按一下 [**新增使用者**]。 

    ![為使用者指派][401]

4. 在 [**邀請某人加入此網站**] 區段中，執行下列步驟︰

    ![為使用者指派][402]

    。 在 [**電子郵件地址**] 文字方塊中輸入許承恩電子郵件地址中 Azure AD。

    b。 按一下 [**新增使用者**。   


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 23 的影片。

![為使用者指派][200] 

**若要指定許承恩 23 的視訊，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **23 的視訊**]。

    ![為使用者指派][202] 

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203] 

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下 [Access] 面板中 23 的 [視訊] 方塊時，您應該取得自動登入 23 影片應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png




