<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 NetSuite |Microsoft Azure"
    description="瞭解如何使用 NetSuite 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！"
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

#<a name="tutorial-how-to-integrate-netsuite-with-azure-active-directory"></a>教學課程︰ 如何與 Azure Active Directory 整合 NetSuite

本教學課程中會顯示如何將 NetSuite 環境連接到您 Azure Active Directory (Azure AD)。 您將學習如何設定單一登入至 NetSuite、 如何啟用使用者的自動化佈建，以及如何指派有權存取 NetSuite 的使用者。 

##<a name="prerequisites"></a>必要條件

1. 若要存取透過[Azure 傳統入口網站](https://manage.windowsazure.com)的 Azure Active Directory，您必須先有有效 Azure 訂閱。

2. 您必須[NetSuite](http://www.netsuite.com/portal/home.shtml)訂閱的管理員存取權。 您可以使用免費的試用帳戶任何服務。

##<a name="step-1-add-netsuite-to-your-directory"></a>步驟 1︰ 新增 NetSuite 至您的目錄

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![從左側的功能窗格中選取 [Active Directory]。][0]

2. 從 [**目錄**] 清單中，選取您想要新增至 NetSuite 的目錄。

3. 在頂端的功能表中，按一下 [**應用程式**]。

    ![按一下 [應用程式。][1]

4. 按一下 [**新增**頁面的底部。

    ![按一下 [新增] 以新增新的應用程式]。][2]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![按一下 [新增應用程式，從圖庫。][3]

6. 在**搜尋] 方塊**中，輸入**NetSuite**。 然後選取結果]， **NetSuite** ，然後按一下 [**完成**]，以便新增應用程式。

    ![新增 NetSuite。][4]

7. 您現在應該會看到 [快速入門] 頁面的 NetSuite:

    ![Azure AD 中的 [NetSuite 的快速入門頁面][5]

##<a name="step-2-enable-single-sign-on"></a>步驟 2︰ 啟用單一登入

1. 在 Azure AD，在 NetSuite，[快速入門] 頁面上按一下 [**設定單一登入**] 按鈕。

    ![設定單一登入] 按鈕][6]

2. 隨即會開啟一個對話方塊，您會看到詢問 「 您要如何登入 NetSuite 使用者嗎？ 」 畫面 **Azure AD 單一登入**，選取，然後按一下 [**下一步**。

    ![選取 [Azure AD 單一登入][7]

    > [AZURE.NOTE] 若要深入瞭解關於不同單一登入的選項，[按一下這裡](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. 在 [**設定應用程式設定**] 頁面的 [**回覆 URL** ] 欄位中，輸入您使用下列格式的 NetSuite 租用戶 URL:
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![輸入您的租用戶 URL][8]

4. 在**設定單一登入 NetSuite 在**頁面上，按一下**下載的中繼資料**，請然後再儲存至本機電腦上的憑證檔案。

    ![下載中繼資料。][9]

5. 在瀏覽器中開啟新的索引標籤，然後到 Netsuite 公司網站以系統管理員身分登入。

6. 在頂端的頁面] 工具列中按一下 [**設定**]，然後按一下 [**安裝管理員**。

    ![移至 [設定管理員][10]

7. 從 [**設定工作**清單中，選取 [**整合**]。

    ![移至 [整合][11]

8. 在 [**管理驗證**] 區段中，按一下**SAML 單一登入**。

    ![移至 [SAML 單一登入][12]

9. 在 [ **SAML 設定**] 頁面上執行下列步驟︰

    - Azure Active Directory 中的**遠端登入 URL**值複製並貼到 NetSuite 的**身分識別提供者登入頁面**欄位。

        ![[SAML 設定] 頁面中。][13]

    - 在 [NetSuite，選取**主要驗證方法**。

    - 針對標示為 [ **SAMLV2 身分識別提供者中繼資料**的欄位，選取 [**上傳 IDP 中繼資料檔**]。 然後按一下 [上傳的中繼資料下載的檔案，您在步驟 4 中的 [**瀏覽**]。

        ![上傳中繼資料][16]

    - 按一下 [**送出**]。

9. Azure AD 中, 選取單一登入設定確認核取方塊，以啟用您上傳到 NetSuite 的憑證。 然後按一下 [**下一步**。

    ![核取確認核取方塊][14]

10. 在對話方塊的最後一頁，輸入電子郵件地址如果您想要收到的錯誤及警告與維護此單一登入設定相關的電子郵件通知。 

    ![輸入您的電子郵件地址。][15]

11. 按一下 [**完成**，關閉對話方塊。 接下來，按一下頁面頂端的**屬性**。

    ![按一下 [屬性]。][17]

12. 按一下 [**新增使用者屬性**]。

    ![在上的按一下 [新增使用者屬性。][18]

13. [**屬性名稱**] 欄位中輸入`account`。 **屬性值**欄位中，輸入您的 NetSuite 帳戶識別碼。 如何尋找您的帳戶識別碼指示會包含下列︰

    ![新增您的 NetSuite 帳戶識別碼。][19]

    - 在 NetSuite，按一下**設定**從頂端導覽功能表。
    - 然後按一下 [在左側的導覽功能表的 [**設定工作**] 區段底下，選取 [**整合**] 區段中，，按一下 [ **Web 服務喜好設定**。
    - 複製您 NetSuite 帳戶識別碼，並 Azure AD 中將其貼到**屬性值**欄位。

        ![取得您的帳戶識別碼][20]

14. 在 Azure AD，按一下 [**完成**，完成新增 SAML 屬性]。 在 [底部] 功能表上按一下**套用變更**。

    ![儲存變更。][21]

15. 使用者可以執行單一登入到 NetSuite 之前，必須先指派 NetSuite 中適當的權限。 請遵循下列指示來指派這些權限。

    - 在頂端瀏覽] 功能表中，按一下 [**設定**]，然後按一下 [**安裝管理員**。

        ![移至 [設定管理員][10]

    - 在左側的導覽功能表中，選取**使用者/角色**，然後按一下 [**管理角色**。

        ![移至管理角色][22]

    - 按一下**新的角色**。

    - 輸入您新的角色的**名稱**，然後選取 [**單一登入僅**核取方塊。

        ![命名新的角色。][23]

    - 按一下 [**儲存**]。

    - 在頂端的功能表，按一下 [**權限**。 然後按一下 [**設定**]。

        ![移至 [權限][24]

    - **設定設定 SAM 單一登入**，選取，然後再按一下 [**新增**]。

    - 按一下 [**儲存**]。

    - 在頂端瀏覽] 功能表中，按一下 [**設定**]，然後按一下 [**安裝管理員**。

        ![移至 [設定管理員][10]

    - 在左側的導覽功能表中，選取**使用者/角色**，然後按一下 [**管理使用者**。

        ![移至管理使用者][25]

    - 選取 [測試使用者。 然後按一下 [**編輯**]。

        ![移至管理使用者][26]

    - 在 [角色] 對話方塊中，選取您所建立，然後按一下 [**新增**的角色。

        ![移至管理使用者][27]

    - 按一下 [**儲存**]。

19. 若要測試您的設定，請參閱標題[指派使用者 NetSuite](#step-4-assign-users-to-netsuite)以下的章節。

##<a name="step-3-enable-automated-user-provisioning"></a>步驟 3︰ 啟用自動化的使用者佈建

> [AZURE.NOTE] 根據預設，您能夠的使用者將會新增至 NetSuite 環境的根下層。

1. Azure Active Directory 在 NetSuite，[快速入門] 頁面上按一下中**設定使用者佈建**。

    ![設定使用者佈建][28]

2. 在 [開啟] 對話方塊的 NetSuite，輸入您的管理員認證，然後按一下 [**下一步**]。

    ![輸入您的 NetSuite 管理員認證。][29]

3. 在 [確認] 頁面上輸入您的電子郵件地址收到的佈建失敗通知。

    ![確認。][30]

4. 按一下 [**完成**，關閉對話方塊。

##<a name="step-4-assign-users-to-netsuite"></a>步驟 4︰ 將使用者指派至 NetSuite

1. 若要測試您的設定，開始建立新的測試帳戶目錄中。

2. 在 [NetSuite 快速入門] 頁面上，按一下 [**指派的使用者**] 按鈕。

    ![按一下 [指派使用者][31]

3. 選取您測試使用者，並按一下畫面底部的 [**指派**] 按鈕︰

 - 如果您未啟用 [自動化的使用者佈建，，然後您會看到下列提示確認︰

        ![Confirm the assignment.][32]

 - 如果您已啟用自動化佈建的使用者，您會看到提示定義何種類型的角色的使用者應有 NetSuite 中。 新提供的使用者應該會出現 NetSuite 環境中，請稍候幾分鐘。

4. 測試您的單一登入設定，請開啟 [存取面板中位於[https://myapps.microsoft.com](https://myapps.microsoft.com/)、 登入以測試帳戶，然後按一下**NetSuite**上。

##<a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [教學課程如何整合 SaaS 應用程式的清單](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png
