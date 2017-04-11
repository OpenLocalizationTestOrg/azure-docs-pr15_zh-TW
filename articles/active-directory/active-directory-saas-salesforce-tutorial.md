<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Salesforce |Microsoft Azure"
    description="瞭解如何使用 Azure Active Directory 中的 Salesforce，若要啟用單一登入，自動化佈建和更多 ！"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#<a name="tutorial-how-to-integrate-salesforce-with-azure-active-directory"></a>教學課程︰ 如何與 Azure Active Directory 整合 Salesforce

本教學課程中會顯示如何連線到您的 Azure Active Directory 的 Salesforce 環境。 您將學習如何設定單一登入到 Salesforce、 如何啟用使用者的自動化佈建，以及如何指派有權存取 Salesforce 的使用者。

##<a name="prerequisites"></a>必要條件

1. 若要存取透過[Azure 傳統入口網站](https://manage.windowsazure.com)的 Azure Active Directory，您必須先有有效 Azure 訂閱。

2. 您必須擁有有效的租用戶中[Salesforce.com](https://www.salesforce.com/)。

> [AZURE.IMPORTANT] 如果您使用的 Salesforce.com**試用**帳戶，然後您將無法設定自動化的使用者佈建。 試用版的帳戶沒有必要的 API 存取啟用直到購買。
> 
> 您可以使用[免費的開發人員帳戶](https://developer.salesforce.com/signup)，完成本教學課程，就可取得這項限制周圍。

如果您使用的 Salesforce 沙箱環境，請參閱[Salesforce 沙箱整合教學課程](https://go.microsoft.com/fwLink/?LinkID=521879)。

##<a name="video-tutorials"></a>視訊的教學課程

您可以遵循此教學課程中使用下列的視訊。

**視訊的教學課程第一部︰ 如何啟用單一登入**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**視訊教學課程中第二部︰ 如何將使用者佈建的自動化**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1-add-salesforce-to-your-directory"></a>步驟 1︰ 將 Salesforce 新增至您的目錄

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![從左側的功能窗格中選取 [Active Directory]。][0]

2. 從 [**目錄**] 清單中，選取您想要新增到 Salesforce 的目錄。

3. 在頂端的功能表中，按一下 [**應用程式**]。

    ![按一下 [應用程式。][1]

4. 按一下 [**新增**頁面的底部。

    ![按一下 [新增] 以新增新的應用程式]。][2]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![按一下 [新增應用程式，從圖庫。][3]

6. 在**搜尋] 方塊**中，輸入**Salesforce**。 然後選取結果]， **Salesforce** ，然後按一下 [**完成**]，以便新增應用程式。

    ![新增 Salesforce。][4]

7. 您現在應該會看到 [快速入門] 頁面的 [Salesforce:

    ![Azure AD 中的 [Salesforce 的快速入門頁面][5]

##<a name="step-2-enable-single-sign-on"></a>步驟 2︰ 啟用單一登入

1. 您可以設定單一登入之前，您必須先設定及 Salesforce 環境中部署自訂的網域。 瞭解如何執行這項作業，請參閱[設定網域名稱](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US)的相關指示。

2. 在 Salesforce 的快速入門頁面 Azure AD 中，按一下 [**設定單一登入**] 按鈕。

    ![設定單一登入] 按鈕][6]

3. 隨即會開啟一個對話方塊，您會看到詢問 「 您要如何登入 Salesforce 使用者嗎？ 」 畫面 **Azure AD 單一登入**，選取，然後按一下 [**下一步**。

    ![選取 [Azure AD 單一登入][7]

    > [AZURE.NOTE] 若要深入瞭解關於不同單一登入的選項，[按一下這裡](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. 在 [**設定應用程式設定**] 頁面上填寫**登入 URL**中使用下列格式您 Salesforce 網域 URL:
 - 企業帳戶︰`https://<domain>.my.salesforce.com`
 - 開發人員帳戶︰`https://<domain>-dev-ed.my.salesforce.com` 

    ![輸入您的登入 URL][8]

5. 在**設定單一登入 Salesforce 在**頁面上，按一下**下載憑證**]，然後再儲存至本機電腦上的憑證檔案。

    ![下載憑證][9]

6. 開啟新的索引標籤，在瀏覽器，並將 Salesforce 系統管理員帳戶登入。

7. **管理員**功能窗格底下，按一下 [以展開區段相關的**安全性控制項**]。 **單一登入設定，**然後按一下。

    ![按一下 [在安全性控制項] 下的單一登入設定][10]

8. 在**單一登入設定**頁面上，按一下 [**編輯**] 按鈕。

    ![按一下 [編輯] 按鈕][11]

    > [AZURE.NOTE] 如果您是無法啟用單一登入您 Salesforce 帳戶設定，您可能需要連絡 Salesforce 的支援，才能有為您啟用的功能。

9. 選取 [ **SAML 啟用**，然後再按一下 [**儲存**]。

    ![選取 [啟用 SAML][12]

10. 若要設定 SAML 單一登入設定，按一下 [**新增**]。

    ![選取 [啟用 SAML][13]

11. **SAML 單一登入設定編輯**在頁面上，執行下列設定︰

    ![您應該要設定的螢幕擷取畫面][14]

 - 若是 [**名稱**] 欄位中，輸入此設定的易記名稱。 **名稱**會自動填入 [ **API 名稱**] 文字方塊，請提供值。

 - Azure AD，在**發行者 URL**值，複製，然後再貼到 Salesforce 中的 [**簽發者**] 欄位。

 - 在**實體 Id] 文字方塊**中，輸入您使用下列模式的 Salesforce 網域名稱︰
     - 企業帳戶︰`https://<domain>.my.salesforce.com`
     - 開發人員帳戶︰`https://<domain>-dev-ed.my.salesforce.com`

 - 按一下 [**瀏覽]** ，或**選擇檔案**以開啟 [**選擇要上傳的檔案**] 對話方塊，選取您 Salesforce 的憑證，然後按一下 [**開啟**上傳憑證]。

 - **SAML 身分識別類型**] 中，選取 [**判斷提示包含使用者的 salesforce.com 使用者名稱**]。

 - **SAML 身分識別的位置**，請選取 [**身分識別位於主旨陳述式的 NameIdentifier 項目**

 - Azure AD，在**遠端登入 URL**值，複製，然後再貼到 Salesforce 中的 [**身分識別提供者登入 URL** ] 欄位。

 - **服務提供者發起的租用戶要求繫結**、 選取**HTTP 重新導向**。

 - 最後，按一下 [**儲存**] 以套用您 SAML 單一登入的設定。

12. 在左側的導覽] 窗格的 [Salesforce 中，按一下**網域管理**] 以展開相關的區段中，，然後按一下 [**我的網域**。

    ![按一下 [在我的網域][15]

13. **驗證設定**] 區段中，往下捲動，然後按一下 [**編輯**] 按鈕。

    ![按一下 [編輯] 按鈕][16]

14. 在**驗證服務**] 區段中，選取 SAML SSO 設定的易記的名稱，然後按一下 [**儲存**。

    ![選取您的 SSO 設定][17]

    > [AZURE.NOTE] 如果已選取多個驗證服務，然後當使用者嘗試啟動單一登入您的 Salesforce 環境，他們會會提示您選取想要登入哪一種驗證服務。 如果您不想使用此選項，您應該**保留所有其他驗證服務取消核取**。

15. Azure AD 中, 選取單一登入設定確認核取方塊，以啟用您上傳到 Salesforce 的憑證。 然後按一下 [**下一步**。

    ![核取確認核取方塊][18]

16. 在對話方塊的最後一頁，輸入電子郵件地址如果您想要收到的錯誤及警告與維護此單一登入設定相關的電子郵件通知。 

    ![輸入您的電子郵件地址。][19]

17. 按一下 [**完成**，關閉對話方塊。 若要測試您的設定，請參閱標題[指派使用者 Salesforce](#step-4-assign-users-to-salesforce)以下的章節。

##<a name="step-3-enable-automated-user-provisioning"></a>步驟 3︰ 啟用自動化的使用者佈建

1. 在 [Salesforce 的 [Azure AD 快速入門] 頁面中，按一下 [**設定使用者佈建**] 按鈕。

    ![按一下 [設定使用者佈建] 按鈕][20]

2. 在 [**設定使用者佈建**] 對話方塊中，輸入您的 Salesforce 管理員的使用者名稱和密碼。

    ![輸入您的管理員的使用者名稱和密碼][21]

    > [AZURE.NOTE] 如果您正在設定生產環境，最佳作法是專為此步驟，Salesforce 中建立新的管理員帳戶。 此帳戶必須 Salesforce 中指派給**系統管理員**設定檔。

3. 若要取得您 Salesforce 安全性權杖，開啟新的索引標籤，並登入到相同的 Salesforce 管理員帳戶。 在頁面的右上角，您的名稱，按一下，然後按一下 [**我的設定**]。

    ![您的名稱，然後按一下 [我的設定][22]

4. 在左側的功能窗格中，在 [**個人**以展開相關的區段中，按一下，然後按一下**重設我的安全性憑證**。

    ![您的名稱，然後按一下 [我的設定][23]

5. 在 [**重設我的安全性憑證**] 頁面上，按一下 [**重設安全性 Token** ] 按鈕。

    ![讀取的警告。][24]

6. 核取此管理員帳戶相關聯的電子郵件收件匣]。 含有新的安全性憑證 Salesforce.com 的電子郵件的外觀。

7. 複製權杖，移至您 Azure AD] 視窗中，並貼到 [**使用者安全性 Token** ] 欄位。 然後按一下 [**下一步**。

    ![安全性權杖中貼上][25]

8. 在 [確認] 頁面上，您可以選擇在發生佈建錯誤時收到的電子郵件通知。 按一下 [**完成**，關閉對話方塊。

    ![輸入您的電子郵件地址收到通知][26]

##<a name="step-4-assign-users-to-salesforce"></a>步驟 4︰ 將使用者指派至 Salesforce

1. 若要測試您的設定，請先在目錄中建立新的測試帳戶。

2. 在 [Salesforce 快速入門] 頁面上，按一下 [**指派的使用者**] 按鈕。

    ![按一下 [指派使用者][27]

3. 選取您測試使用者，並按一下畫面底部的 [**指派**] 按鈕︰

 - 如果您未啟用 [自動化的使用者佈建，，然後您會看到下列提示確認︰

        ![Confirm the assignment.][28]

 - 如果您已啟用自動化佈建的使用者，您會看到定義使用者應有何種類型的 Salesforce 設定檔提示。 新提供的使用者應該會出現 Salesforce 環境中，請稍候幾分鐘。

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] 如果您在 Salesforce**開發人員**環境提供，您就必須有限的每個設定檔可用的授權。 因此，最好將使用者佈建**Chatter 免費的使用者**設定檔，其中有 4,999 可用的授權。

4. 若要測試您的單一登入設定，開啟[https://myapps.microsoft.com](https://myapps.microsoft.com/)，在 [存取面板登入以測試帳戶，然後按一下上**Salesforce**。

##<a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [教學課程如何整合 SaaS 應用程式的清單](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png
