<properties
    pageTitle="教學課程︰ 使用 8 x 8 虛擬 Office 的 Azure Active Directory 整合 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active 目錄及 8 x 8 虛擬辦公室之間。"
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>教學課程︰ 使用 8 x 8 虛擬 Office 的 Azure Active Directory 整合

本教學課程中的目標是以顯示您要與 Azure Active Directory (Azure AD) 整合 8 x 8 虛擬 Office 的方式。

整合 8 x 8 虛擬 Office Azure AD 為您提供下列優點︰

- 您可以控制可存取 8 x 8 虛擬 office Azure AD
- 您可以使用其 Azure AD 帳戶啟用自動取得登入 （單一登入） 8 x 8 虛擬 office 使用者
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合 8 x 8 虛擬 office，您需要下列項目︰

- Azure AD 訂閱
- 8 x 8 虛擬 Office 單一登啟用的訂閱


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Microsoft Azure AD 單一登入。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 8 x 8 虛擬 Office
2. 設定及測試 Microsoft Azure AD 單一登入


## <a name="adding-8x8-virtual-office-from-the-gallery"></a>從圖庫新增 8 x 8 虛擬 Office
若要設定 8 x 8 虛擬 Office 整合 Azure AD，必須將 8 x 8 虛擬 Office 從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 8 x 8 虛擬 Office，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。
    
    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**8 x 8 虛擬 Office**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. 在 [結果] 窗格中，選取 [ **8 x 8 虛擬 Office**，，然後按一下**完成**新增應用程式。

    ![在圖庫中選取應用程式](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>設定及測試 Microsoft Azure AD 單一登入
本節的目標是以顯示您如何設定和測試 Microsoft Azure AD 單一登入與 8 x 8 虛擬 Office 根據測試使用者稱為 「 許承恩 」。

單一登入的工作，Azure AD 需要知道什麼對應使用者在中 8 x 8 虛擬 office 中 Azure AD 使用者。 換句話說，連結和之間的關聯 Azure AD 使用者 8 x 8 中相關的使用者虛擬 Office 必須建立。

所指派**的使用者名稱**的值為 8 x 8 虛擬 Office**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定及測試 Microsoft Azure AD 單一登入 8 x 8 虛擬 office，必須完成下列建置組塊︰

1. **[設定 Microsoft Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Microsoft Azure AD 單一登入與許承恩測試。
3. **[建立 8 x 8 虛擬 Office 測試使用者](#creating-a-8x8-virtual-office-test-user)**-8 x 8 虛擬 office 的已連結至她的 Azure AD 表示有許承恩的對應。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Microsoft Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>設定 Microsoft Azure AD 單一登入

在此區段中，您將 Microsoft Azure AD 單一登入 [傳統] 入口網站中啟用並 8 x 8 虛擬 Office 應用程式中設定單一登入。

**若要使用 8 x 8 虛擬 Office 設定 Microsoft Azure AD 單一登入，請執行下列步驟︰**

1. 在 [傳統] 入口網站， **8 x 8 虛擬 Office**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。
     
    ![設定單一登入][6] 

2. **如何將您登入 8 x 8 虛擬 Office 的使用者**] 頁面上選取**Microsoft Azure AD 單一登入**，然後再按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)

    。 在 [**回覆 URL** ] 文字方塊中輸入︰`https://sso.8x8.com/saml2`

    b。 按一下 [**下一步**

4. 在**單一登入 8 x 8 虛擬 office 設定**] 頁面上，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。

5. 登入至 8 x 8 虛擬 Office 租用戶系統管理員身分。
6. 選取應用程式面板的**虛擬 Office 帳戶管理員**。

    ![設定應用程式方](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

7. 選取 [**企業**管理，然後按一下 [**登入**] 按鈕的帳戶]。

    ![設定應用程式方](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

8. 按一下 [功能表] 清單中的 [**帳戶**] 索引標籤。

    ![設定應用程式方](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

9. 在 [帳戶] 清單中，按一下 [**單一登入**]。

    ![設定應用程式方](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

10. 在 [驗證方法中選取 [ **Signle 登入**，然後按一下 [ **SAML**。

    ![設定應用程式方](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

11. 將從 Azure AD **SAML SSO URL**、**單一歌唱查看服務的 URL**及**發行者 URL**複製到**登入 URL**、**登出 URL**和 8 x 8 虛擬 Office 中的**發行者的 URL** 。 

    ![設定應用程式方](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    ![設定應用程式方](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)

12. 按一下 [上傳您從 Azure AD 下載憑證的**瀏覽器**] 按鈕。

13. 按一下 [**儲存**] 按鈕。

14. 在 [傳統] 入口網站，選取單一登入設定確認，然後按 [**下一步**。

    ![Azure AD 單一登入][10]

15. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![Azure AD 單一登入][11]



### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩傳統入口網站中建立一個測試使用者。
    
![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-a-8x8-virtual-office-test-user"></a>建立 8 x 8 虛擬 Office 測試使用者

本節的目標是建立稱為許承恩 8 x 8 虛擬 Office 中的使用者。 8 x 8 虛擬 Office 支援只時間佈建，這是預設啟用。

沒有動作項目為您在此區段中。 在嘗試存取 8 x 8 虛擬 Office，如果不存在，將會建立新的使用者。 

> [AZURE.NOTE] 若要手動建立的使用者，您需要連絡 8 x 8 虛擬 Office 支援小組。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 8 x 8 虛擬 Office。
    
![為使用者指派][200]

**若要指定許承恩 8 x 8 虛擬 office，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201]

2. 在應用程式清單中，選取 [ **8 x 8 虛擬 Office**。

    ![設定單一登入](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)

3. 在頂端的功能表，按一下 [**使用者**]。
    
    ![為使用者指派][203]

4. 在 [使用者] 清單中，選取**許承恩**。

5. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是以測試您 Microsoft Azure AD 單一登入設定使用 Access 面板。

當您按一下 [Access] 面板中的 [8 x 8 虛擬 Office] 磚時，您應該取得自動登入 8 x 8 虛擬 Office 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png
