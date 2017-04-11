<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 PerformanceCentre |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 PerformanceCentre 之間。"
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
    ms.date="09/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>教學課程︰ 使用 PerformanceCentre 的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何將整合 PerformanceCentre 與 Azure Active Directory (Azure AD)。  
Azure AD 與整合 PerformanceCentre 為您提供下列優點︰ 

- 您可以控制可存取 PerformanceCentre Azure AD 
- 您可以讓使用者能自動取得登入 PerformanceCentre （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置的 Azure Active Directory 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件 

若要設定 PerformanceCentre Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 PerformanceCentre 單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 

 
## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由三個主要的建置組塊所組成︰

1. 從圖庫新增 PerformanceCentre 
2. 設定及測試 Azure AD 單一登入


## <a name="adding-performancecentre-from-the-gallery"></a>從圖庫新增 PerformanceCentre
若要設定 PerformanceCentre 整合 Azure AD，必須將 PerformanceCentre 從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 PerformanceCentre，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**PerformanceCentre**。
 
    ![應用程式][5]

7. 在 [結果] 窗格中，選取**PerformanceCentre**，，然後按一下要新增應用程式**完成**。

    ![應用程式][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用 PerformanceCentre 根據稱為 「 許承恩 」 的測試使用者測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 PerformanceCentre Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 PerformanceCentre 中相關的使用者。  
所指派**的使用者名稱**的值為 PerformanceCentre**使用者名稱**的值 Azure AD 中建立此連結關聯。
 
若要設定及測試 Azure AD 單一登入與 PerformanceCentre，必須完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 PerformanceCentre 測試使用者](#creating-a-halogen-software-test-user)**-連結至 Azure AD 的表示她的 PerformanceCentre 中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節的目標是 Azure AD 單一登入 Azure AD 傳統入口網站中啟用並 PerformanceCentre 應用程式中設定單一登入。

**若要使用 PerformanceCentre 設定 Azure AD 單一登入，請執行下列步驟︰**

1. Azure AD 傳統入口網站中，在**PerformanceCentre**應用程式整合頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**對話方塊]。

    ![設定單一登入][6] 

2. 在**您要如何登入 PerformanceCentre 的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![Azure AD 單一登入][7] 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![Azure AD 單一登入][8] 
 
     。 在 [**登入 URL** ] 文字方塊中，輸入用來登入您的 PerformanceCentre 網站使用者的 URL (例如︰ *http://companyname.performancecentre.com/saml/SSO*)。
 
     b。 按一下 [**下一步**]。
 
4. 在**設定單一登入 PerformanceCentre 在**頁面上，執行下列步驟︰

    ![Azure AD 單一登入][9] 

    。 按一下**下載的中繼資料**]，然後儲存您的電腦上的檔案。



1. 登入至您的**PerformanceCentre**公司網站管理員。

2. 在左側索引標籤上，按一下 [**設定**]。

    ![Azure AD 單一登入][10]

2. 在左側索引標籤上，按一下 [**其他**]，然後按一下**單一登入**。

    ![Azure AD 單一登入][11]

2. 為**通訊協定**，請選取 [ **SAML**]。

    ![Azure AD 單一登入][12]

2. 在記事本中開啟下載的中繼資料檔案複製內容，將它貼到 [**身分識別提供者中繼資料**] 文字方塊中，再按一下 [**儲存**。

    ![Azure AD 單一登入][13]

2. 確認的值的**實體基礎 URL**及**實體識別碼 URL**正確無誤。

    ![Azure AD 單一登入][14]


6. 在 Azure AD 傳統入口網站中，選取單一登入設定確認，然後再按 [**下一步**。 

    ![Azure AD 單一登入][15]

7. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    ![Azure AD 單一登入][16]




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。  

![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
 
4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰ 

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
 
    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。
    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
 
8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
  
    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   

  
 
### <a name="creating-a-performancecentre-test-user"></a>建立 PerformanceCentre 測試使用者

本節的目標是以建立 PerformanceCentre 中名為許承恩使用者。

**若要建立 PerformanceCentre 中名為許承恩的使用者，請執行下列步驟︰**

1. 以系統管理員身分登入您 PerformanceCentre 公司的網站。

2. 在左側功能表上，按一下**Interrelate**，然後再按一下 [**建立參與者**。

    ![建立使用者][400]

4. 在 [ **Interrelate-建立參與者**] 對話方塊中，執行下列步驟︰

    ![建立使用者][401]

    。 輸入到相關的文字方塊許承恩必要的屬性。
    
    > [AZURE.IMPORTANT] 在 PerformanceCentre 許的使用者名稱屬性必須使用相同的使用者名稱 Azure AD。


    b。 請選取 [**用戶端系統管理員**，以**選擇角色**。 

    c。 按一下 [**儲存**]。   


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 PerformanceCentre。

![為使用者指派][200] 

**若要指定 PerformanceCentre 許承恩，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201]

2. 在應用程式清單中，選取 [ **PerformanceCentre**]。

    ![為使用者指派][202]

1. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203]

1. 在 [使用者] 清單中，選取**許承恩**。

2. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下 [Access] 面板中的 [PerformanceCentre] 方塊時，您應該取得自動登入 PerformanceCentre 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png



