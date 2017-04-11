<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 SciQuest 花主管 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 SciQuest 花主管之間。"
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>教學課程︰ SciQuest 花主管 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何將整合 SciQuest 花主管與 Azure Active Directory (Azure AD)。  
Azure AD 與整合 SciQuest 花主管為您提供下列優點︰ 

- 您可以控制可存取 SciQuest 花主管 Azure AD 
- 您可以使用其 Azure AD 帳戶啟用自動取得登入 SciQuest 花主管 （單一登入） 至使用者
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件 

若要設定 Azure AD 整合與 SciQuest 花主管，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 SciQuest 花主管單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 

 
## <a name="scenario-description"></a>案例說明
本教學課程中的目的是讓您測試環境中測試 Azure AD 單一登入。  
本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 SciQuest 花主管 
2. 設定及測試 Azure AD 單一登入


## <a name="adding-sciquest-spend-director-from-the-gallery"></a>從圖庫新增 SciQuest 花主管
若要設定 SciQuest 花主管整合 Azure AD，您需要從庫新增 SciQuest 花在主管的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 SciQuest 花主管，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**sciQuest 花主管**。

    ![應用程式][5]

7. 在 [結果] 窗格中，選取**SciQuest 花主管**，，然後按一下要新增應用程式**完成**。

    ![應用程式][6]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
本節的目標是以顯示您如何設定和使用根據稱為 「 許承恩 」 的測試使用者的 SciQuest 花主管測試 Azure AD 單一登入。

單一登入搭配使用，Azure AD 需要知道什麼對應中的使用者 SciQuest 花主管 Azure AD 中的使用者。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 SciQuest 花主管中相關的使用者。  
所指派**的使用者名稱**的值為 SciQuest 花在主管**的使用者名稱**的值 Azure AD 中建立此連結關聯。
 
若要設定及測試 Azure AD 單一登入與 SciQuest 花主管，必須完成下列建置組塊︰

1. **[設定 Azure AD 單一單一登入](#configuring-azure-ad-single-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
4. **[建立 SciQuest 花 Director 測試使用者](#creating-a-halogen-software-test-user)**-SciQuest 花在主管的已連結至她的 Azure AD 表示中有許承恩的對應。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-single-sign-on"></a>Azure AD 單一單一登入設定

本節的目標是 Azure AD 單一登入 Azure 傳統入口網站中啟用並 SciQuest 花主管應用程式中設定單一登入。

**若要設定 Azure AD 單一登入 SciQuest 花主管，執行下列步驟︰**

1. 在 Azure 的傳統入口網站， **SciQuest 花主管**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入][8]

2. 在**您要如何登入 SciQuest 花在主管的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![Azure AD 單一登入][9]

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰ 

    ![設定應用程式設定][10]
 
     3.1。 在 [**登入 URL** ] 文字方塊中，輸入您用來使用下列模式 SciQuest 花主管應用程式登入您的使用者的 URL: *https://。*sciquest.com/.**

     3.2 捨位。 在 [**回覆 URL** ] 文字方塊中，輸入您有**登入 URL** ] 文字方塊中輸入的值相同。 

     3.3。 按一下 [**下一步**]。
 
4. 在**設定單一登入 SciQuest 花主管在**頁面上，按一下**下載的中繼資料**]，然後儲存在本機電腦上的中繼資料檔案。

    ![什麼是 Azure AD Connect][11]

5. 若要啟用此驗證方法，使用上述下載的中繼資料支援連絡人 SciQuest。

6. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。 

    ![什麼是 Azure AD Connect][15]

10. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  

    




### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是稱為許承恩 Azure 傳統入口網站中建立的測試使用者。

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![什麼是 Azure AD Connect][100] 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。
3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![什麼是 Azure AD Connect][101] 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。 

    ![什麼是 Azure AD Connect][102] 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰

    ![什麼是 Azure AD Connect][103] 

    。 **輸入的使用者**，選取**您組織中的新使用者**。
  
    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。
  
    c。 按一下 [下一步]。

6.  **使用者設定檔**] 對話方塊在頁面上，執行下列步驟︰ 

    ![什麼是 Azure AD Connect][104] 

    。 在 [**名字**] 文字方塊中輸入**許**。  
  
    b。 在 [**名字姓氏**txtbox，類型，**承恩**。
  
    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。
  
    d。 在 [**角色**] 清單中選取 [**使用者**]。
  
    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![什麼是 Azure AD Connect][105]  

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![什麼是 Azure AD Connect][106]   

    。 寫下的值的**新密碼**。
  
    b。 按一下 [**完成**]。   
  
 
### <a name="creating-a-sciquest-spend-director-test-user"></a>建立 SciQuest 花主管測試使用者

本節的目標是以建立 SciQuest 花主管中名為許承恩使用者。

您需要連絡您 SciQuest 花在主管的支援小組，並向他們提供測試帳戶，才能建立相關的詳細資料。

或者，您也可以利用只時間佈建單一登入的功能支援 SciQuest 花主管。  
如果只時間佈建已啟用，使用者會自動建立 SciQuest 花主管，在單一登入嘗試如果不存在。 這項功能就不需要手動建立單一登入對應使用者。

若要取得只時間佈建啟用，您需要連絡您 SciQuest 花主管支援小組。
  

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

本節的目標是啟用許承恩 Azure 單一登入使用其存取權授與 SciQuest 花主管。

![什麼是 Azure AD Connect][200]

**若要指定許承恩 SciQuest 花主管，請執行下列步驟︰**

1. Azure 傳統的入口網站中，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![什麼是 Azure AD Connect][201]

2. 在應用程式清單中，選取 [ **SciQuest 花主管**]。

    ![什麼是 Azure AD Connect][202]

1. 在頂端的功能表，按一下 [**使用者**]。

    ![什麼是 Azure AD Connect][203]

1. 在 [使用者] 清單中，選取**許承恩**。

    ![什麼是 Azure AD Connect][204]

2. 在底部工具列中，按一下 [**指派**]。

    ![什麼是 Azure AD Connect][205]



### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是測試 Azure AD 單一登入設定使用 Access 面板。  
當您按一下存取面板中的 [SciQuest 花主管] 方塊時，您應該取得自動登入 SciQuest 花主管應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png

