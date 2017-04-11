<properties 
    pageTitle="使用行動互動 REST Api 手動設定驗證"
    description="說明如何手動安裝行動互動 REST api 的驗證" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>使用行動互動 REST Api 手動設定驗證

這是以[驗證與行動互動 REST Api](mobile-engagement-api-authentication.md)附錄文件。 請確定您讀取第一次以取得內容。 這將說明如何設定使用 Azure 入口網站的行動互動 REST api 您驗證的一次性安裝另一種方法。 

>[AZURE.NOTE] 下列指示會根據本[Active Directory 指南](../resource-group-create-service-principal-portal.md)，並針對需要驗證行動互動 Api 自訂。 如果您想要瞭解詳細資料] 下方的步驟，因此請參閱它。 

1. 登入您的 Azure 帳戶透過[傳統入口網站](https://manage.windowsazure.com/)。

2. 從左窗格中選取 [ **Active Directory** ]。

     ![選取 [Active Directory][1]

3. 選擇**預設的 Active Directory**中 Azure 入口網站。 

     ![選擇目錄][2]

    >[AZURE.IMPORTANT] 這種方法可搭配使用您的預設值為您的帳戶的 Active Directory 中工作，並將無法運作，如果您的帳戶中建立 Active Directory 中進行時，才。 

4. 若要檢視應用程式目錄中，按一下**應用程式**。

     ![檢視應用程式][3]

5. 按一下 [**新增**]。 

     ![新增應用程式][4]

6. 按一下 [**新增組織內部開發的應用程式**

     ![新的應用程式][5]

6. 在應用程式的名稱填滿和**WEB 應用程式和/或 WEB API**中選取的應用程式類型，按一下 [下一步] 按鈕。

     ![名稱應用程式][6]

7. 您可以提供任何空的 Url**登開啟 URL** ] 與 [**應用程式識別碼 URI**。 不用於我們的情況，不會驗證本身的 Url。  

     ![應用程式屬性][7]

8. 這結尾，則必須 AAD 應用程式與您先前提供類似下列的名稱。 這是您**AD\_應用程式\_名稱**記下它。  

     ![應用程式名稱][8]

9. 按一下應用程式的名稱，然後按一下 [**設定**。

     ![設定應用程式][9]

10. 記下將做為用戶端識別碼**用戶端\_識別碼**如您的 API 通話。 

     ![設定應用程式][10]

11. 向下**鍵**捲動並新增機碼最好是 2 年 （到期） 工期的任務，按一下 [**儲存**]。 

     ![設定應用程式][11]


12. 立即將複製的值顯示為鍵只會顯示在現在並不會儲存，再將不會顯示。 如果您遺失了，您會有產生新的金鑰。 這是您的 API 來電**CLIENT_SECRET** 。 

     ![設定應用程式][12]

    >[AZURE.IMPORTANT] 此按鍵即將過期，請務必以更新時卻否則您 API 驗證不再運作，讓您指定的期間的結尾。 您也可以刪除並重新建立此鍵，如果您認為已洩漏過。
 
13. 按一下 [**檢視結束點**] 按鈕現在這會開啟 [**應用程式結束點**] 對話方塊。 

    ![][13]

14. 從 [應用程式結束點] 對話方塊中，複製**OAUTH 2.0 TOKEN 結束點**。 

    ![][14]

15. 此結束點就會在 URL 中的 GUID 是您**TENANT_ID** ，因此請記下下列格式︰ 

        https://login.microsoftonline.com/<GUID>/oauth2/token

16. 現在，我們會繼續在此應用程式上設定的權限。 此，您必須開啟[Azure 入口網站](https://portal.azure.com)。 

17. **資源群組**上按一下，然後尋找**行動互動**資源群組]。  

    ![][15]

18. 按一下 [**行動互動**的資源] 群組中，瀏覽至 [**設定**刀以下。 

    ![][16]

19. **使用者**設定刀中按一下，然後按一下 [**新增**]，以新增使用者]。 

    ![][17]

20. 按一下**選取的角色**

    ![][18]

21. 按一下 [**擁有者**

    ![][19]

22. 您的應用程式名稱搜尋**AD\_應用程式\_名稱**在搜尋方塊中。 您不會看到以下預設。 當您找到它時，請選取它，然後按一下刀底部的 [**選取**。 

    ![][20]

23. 在**新增存取**刀，隨即會顯示為**1 的使用者，0 的群組**。 按一下**[確定]**以確認變更此刀上。 

    ![][21]

您現在已完成所需的 AAD 設定，所有設定為呼叫的 Api 您。 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



