<properties
    pageTitle="教學課程︰ 與公司的 Facebook 的 Azure Active Directory 整合 |Microsoft Azure"
    description="瞭解如何將公司設定單一登入之間 Azure Active Directory 或 facebook 的連絡人。"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-facebook-at-work"></a>教學課程︰ 與公司的 Facebook 的 Azure Active Directory 整合

本教學課程中的目標是以顯示您如何將公司 Facebook 整合與 Azure Active Directory (Azure AD)。

Azure AD 與整合作用中的 Facebook 為您提供下列優點︰ 

- 您可以控制可存取 Facebook，公司 Azure AD 
- 您可以自動提供者有權存取公司的 facebook 使用者帳戶
- 您可以讓使用者能自動取得登入 Facebook 作用中 （單一登入） 至其 Azure AD 帳戶
- 您可以管理您的帳戶，在一個中央位置 

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。


## <a name="prerequisites"></a>必要條件 

若要設定 CS 顆星 Azure AD 整合，您需要下列項目︰

- Azure AD 訂閱
- 在使用單一登入 Facebook 啟用

若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。 


## <a name="adding-facebook-at-work-from-the-gallery"></a>從圖庫新增作用中的 Facebook
若要設定的 Facebook 整合到 Azure AD 的工作，您需要將 Facebook 作用中的資料從圖庫新增到受管理的 SaaS 應用程式清單。

**若要在工作中新增 Facebook 從圖庫中，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。 

    ![Active Directory][1]

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。
    
    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中輸入 [**作用中的 Facebook**。

7. 在 [結果] 窗格中，選取 [**作用中的 Facebook**]，然後按一下**完成**新增應用程式。


### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

本節說明如何讓使用者能夠在公司的 facebook 的 Azure Active Directory，使用 SAML 通訊協定所根據的同盟其帳戶驗證方法。

**若要與公司的 Facebook 設定 Azure AD 單一登入，請執行下列步驟︰**

1.  新增後 Facebook 公司 Azure 傳統入口網站中，按一下 [**設定單一登入**]。

2.  在 [**設定應用程式 URL** ] 畫面中，輸入的 URL 會將您的 Facebook 工作應用程式登入的使用者。 這是您的 Facebook 工作租用戶 url (範例︰ https://example.facebook.com/)。 完成之後，請按一下 [**下一步**]。

3.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Facebook 工作公司網站。

4. 依照下列指示使用 Azure AD 身分識別提供者為公司設定 Facebook: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.  一旦完成，請返回至瀏覽器視窗，顯示 [Azure 傳統入口網站，按一下核取方塊，以確認您已完成程序，然後按一下 [**下一步**，**完成**。


## <a name="automatically-provisioning-users-to-facebook-at-work"></a>自動佈建作用中的 Facebook 使用者

Azure AD 支援自動立即同步處理功能的作用中的 facebook 指派使用者的帳戶詳細資料。 此自動同步處理可讓作用中取得資料需要授權的使用者存取，請開始前事先它們的第一次登入嘗試 Facebook。 也就能取消佈建新的作用中的 facebook 使用者時已撤銷中 Azure AD 的存取權。

自動佈建可以設定 Azure 傳統入口網站的視窗中的 [**設定帳戶佈建**] 即可。

如需如何設定自動佈建的詳細資訊，請參閱[https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## <a name="assigning-users-to-facebook-at-work"></a>將使用者指派至作用中的 Facebook

能夠 AAD 使用者都能看到其存取面板上的 [作用中的 Facebook，他們必須被指派內 Azure 傳統入口網站的存取權。

**若要指派的作用中的 facebook 使用者︰**

1.  在開始頁面作用中的 facebook Azure 傳統入口網站中，按一下 [**指派帳戶**。

2.  在 [**顯示**] 功能表中，選取您想要指派的使用者或群組至作用中的 Facebook，然後按一下核取記號按鈕。

3.  在結果清單中，選取您要指派工作的 Facebook 使用者或群組。

4.  在頁尾中，按一下 [**指派**] 按鈕。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png




