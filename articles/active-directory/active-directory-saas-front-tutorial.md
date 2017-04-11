<properties
    pageTitle="教學課程︰ 使用最上層的 Azure Active Directory 整合 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 與最上層之間。"
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


# <a name="tutorial-azure-active-directory-integration-with-front"></a>教學課程︰ 使用最上層的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何與 Azure Active Directory (Azure AD) 整合最上層。

Azure AD 與整合正面為您提供下列優點︰

- 您可以控制可存取最上層 Azure AD
- 您可以讓使用者能自動取得登入 （單一登入） 的最上層其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合與最上層，您需要下列項目︰

- Azure AD 訂閱
- 最上層單一登啟用的訂閱


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增最上層
2. 設定及測試 Azure AD 單一登入


## <a name="adding-front-from-the-gallery"></a>從圖庫新增最上層
若要設定正面整合 Azure AD，必須將最上層從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增最上層，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。
    
    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。
    
    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**前端**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-front-tutorial/tutorial_front_01.png)

7. 在 [結果] 面板中，選取**前**，，然後按一下要新增應用程式**完成**。

    ![在圖庫中選取應用程式](./media/active-directory-saas-front-tutorial/tutorial_front_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用以測試使用者稱為 「 許承恩 」 的最上層測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應使用者的前方 Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者的前方相關的使用者。

所指派的**使用者名稱**值在 Azure AD 為**使用者名稱**前面的值建立此連結關聯。

若要設定及測試 Azure AD 單一登入與最上層，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
3. **[建立最上層測試使用者](#creating-a-front-test-user)**-將對應的許承恩的前方 Azure AD 表示她的連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

在此區段中，您可以 Azure AD 單一登入 [傳統] 入口網站中啟用並設定單一登入您的最上層應用程式。

**若要設定 Azure AD 單一登入與最上層，請執行下列步驟︰**

1. 在 [傳統] 入口網站，**使用正面與**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。
     
    ![設定單一登入][6] 

2. **如何要登入到最上層的使用者**] 頁面上選取**Azure AD 單一登入**，然後按 [**下一步**。
    
    ![設定單一登入](./media/active-directory-saas-front-tutorial/tutorial_front_03.png)

3. **設定應用程式設定**] 對話方塊在頁面上，如果您想要將應用程式設定**IDP 發起的租用戶模式**中，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-front-tutorial/tutorial_front_04.png)

    。 在 [**識別碼**] 文字方塊中輸入 URL，請使用下列模式︰`https://<company name>.frontapp.com`

    b。 在 [**回覆 URL** ] 文字方塊中，輸入 URL，請使用下列模式︰`https://<company name>.frontapp.com/sso/saml/callback`

    c。 按一下 [**下一步**

4. 如果您想要設定**預存程序發起的租用戶模式**中的應用程式，在 [**設定應用程式設定**] 對話方塊的頁面上，然後按一下**[顯示進階設定 （可省略） 」**的然後輸入的**登入 URL**並按一下 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-front-tutorial/tutorial_front_05.png)

    。 在 [**登入 URL** ] 文字方塊中輸入 URL，請使用下列模式︰`https://<company name>.frontapp.com`

    b。 按一下 [**下一步**

    > [AZURE.NOTE] 請注意，這些不是真正的值。 您必須更新這些值的實際的登入 URL、 識別碼和回覆 URL。 若要取得這些值，您可以參照**步驟 12**如需詳細資訊，或與最上層，透過[support@frontapp.com](emailTo:support@frontapp.com)。

5. 在 [**設定單一登入在最上層**] 頁面中，執行下列步驟，然後按一下 [**下一步**︰

    ![設定單一登入](./media/active-directory-saas-front-tutorial/tutorial_front_06.png)

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。

6. 登入您的最上層租用戶系統管理員身分。

7. 移至 [**設定 （在左側資訊看板底部的齒輪圖示） > 喜好設定**。

    ![設定單一登入應用程式方](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

8. 按一下**單一登入**] 連結。

    ![設定單一登入應用程式方](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

9. 選取**SAML**中的下拉式清單的**單一登入**。

    ![設定單一登入應用程式方](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

10. 在 [**進入點**文字方塊會從 Azure AD 應用程式設定精靈將**單一登入服務 URL**的值。

    ![設定單一登入應用程式方](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

11. 複製下載的憑證檔案的內容，然後將其貼入**簽章憑證**文字方塊。

    ![設定單一登入應用程式方](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

12. 請確認這些 Url 符合您在步驟 3 中的設定。

    ![設定單一登入應用程式方](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

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

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-front-tutorial/create_aaduser_09.png)

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-front-tutorial/create_aaduser_05.png)

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-front-tutorial/create_aaduser_06.png)

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-front-tutorial/create_aaduser_07.png)

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-front-tutorial/create_aaduser_08.png)

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-a-front-test-user"></a>建立最上層測試使用者

本節的目標是以建立使用者稱為許承恩 Front.Please 工時與您最上層的支援小組中的最上層帳戶中新增使用者。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與最上層。
    
![為使用者指派][200]

**若要指定許承恩到最上層，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。
    
    ![為使用者指派][201]

2. 在應用程式清單中，選取 [**前**]。
    
    ![設定單一登入](./media/active-directory-saas-front-tutorial/tutorial_front_50.png)

1. 在頂端的功能表，按一下 [**使用者**]。
    
    ![為使用者指派][203]

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。
    
    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。
 
當您按一下 [Access] 面板中的 [最上層] 方塊時，您應該取得自動登入至最上層應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-front-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-front-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-front-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-front-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-front-tutorial/tutorial_general_205.png
