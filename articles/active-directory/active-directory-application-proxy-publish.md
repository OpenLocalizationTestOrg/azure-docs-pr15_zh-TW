<properties
    pageTitle="發佈應用程式搭配 Azure AD 應用程式 Proxy |Microsoft Azure"
    description="發佈至雲端與 Azure AD 應用程式 Proxy 的內部部署應用程式。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-using-azure-ad-application-proxy"></a>發佈應用程式使用 Azure AD 應用程式 Proxy

Azure AD 應用程式 Proxy 可協助您發佈網際網路上存取的內部部署應用程式支援遠端工作人員。 此時，您應該已經[啟用 Azure 傳統入口網站中的應用程式 Proxy](active-directory-application-proxy-enable.md)。 本文會引導您發佈的應用程式，在您的區域網路上執行，並提供安全遠端存取從網路以外的步驟。 完成這份文件之後，您就能將應用程式設定個人化的資訊的安全性需求。

> [AZURE.NOTE] 應用程式 Proxy 是您升級至 Premium 或的 Azure Active Directory 基本版時，才可使用的功能。 如需詳細資訊，請參閱[Azure Active Directory 版本](active-directory-editions.md)。

## <a name="publish-an-app-using-the-wizard"></a>發佈應用程式使用精靈

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com/)以系統管理員身分登入。
2. 移至 [Active Directory，然後選取您啟用應用程式 Proxy 的目錄。

    ![Active Directory-圖示](./media/active-directory-application-proxy-publish/ad_icon.png)

3. 按一下 [**應用程式**] 索引標籤，然後按一下 [在畫面底部的 [**新增**] 按鈕

    ![新增應用程式](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. 選取 [**發佈應用程式，將會從您的網路外部存取**]。

    ![發佈的應用程式，將會從您的網路外部存取](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. 提供您的應用程式的下列資訊︰

    - **名稱**︰ 應用程式的好記的名稱。 它必須是您的目錄內的唯一項目。
    - **內部 URL**︰ 應用程式 Proxy 連接器用來存取您私人的網路內應用程式的地址。 您可以發佈，其餘的伺服器時解除發佈的後端伺服器上提供特定的路徑。 在如此一來，您可以發佈相同的伺服器上的不同網站，並為每個自己的名稱及存取規則。

        > [AZURE.TIP] 如果您發佈的路徑，請確定其包含所有必要的圖像、 指令碼和應用程式的樣式表。 例如，如果您的應用程式位於 https://yourapp/app，並使用位於 https://yourapp/media 的圖像，然後您應該發佈 https://yourapp/ 的路徑。

    - **預先驗證方法**︰ 如何應用程式 Proxy 驗證使用者之前讓他們存取您的應用程式。 從下拉式功能表中選擇其中一個選項。

        - Azure Active Directory︰ 應用程式 Proxy 會重新導向登入 Azure AD，驗證他們的目錄和應用程式的權限的使用者。
        - 傳遞︰ 使用者沒有存取應用程式驗證。

    ![應用程式屬性](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. 若要完成精靈中，按一下 [在畫面底部的核取記號]。 應用程式現在定義在 Azure AD。


## <a name="assign-users-and-groups-to-the-application"></a>指派至應用程式的使用者和群組

若要讓使用者能存取您已發佈的應用程式，您需要為其分派個別或群組中。 （記得要指定給自己存取，也）。需要每位使用者都有授權的 Azure 基本或更高。 您可以授權指派給個別或群組。 如需詳細資訊，請參閱[指派使用者至應用程式](active-directory-applications-guiding-developers-assigning-users.md)。 

需要預先驗證的應用程式，這是使用應用程式的權限。 不需要預先驗證的應用程式，使用者可以仍被指派到應用程式，讓它會出現在其應用程式] 清單中，例如 [MyApps。

1. 完成之後新增應用程式精靈，您會看到 [快速入門] 頁面應用程式。 若要管理誰有權存取應用程式，請選取 [**使用者和群組]**。

    ![應用程式 Proxy 快速入門指派使用者的螢幕擷取畫面](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. 搜尋您的目錄中的特定群組，或顯示您所有的使用者。 若要顯示搜尋結果，請按一下核取記號。

    ![搜尋群組或使用者的螢幕擷取畫面](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. 選取 [每一個使用者或群組中您想要指派給此應用程式，然後按一下 [**指派**]。 系統要求您確認此動作。

> [AZURE.NOTE] 整合式 Windows 驗證應用程式，您可以從您內部部署的 Active Directory 指定只有使用者和群組已同步處理。 使用 Microsoft 帳戶和來賓的身分登入的使用者無法指派的應用程式與 Azure Active Directory 應用程式 Proxy 發佈。 請確定您屬於相同的網域為您要發佈的應用程式的認證登入的使用者。

## <a name="test-your-published-application"></a>測試您發佈的應用程式

一旦您發佈您的應用程式，您可以進行測試出瀏覽至您發佈的 URL。 請確定您可以存取，正確呈現和一切運作正常。 如果您遇到問題或收到錯誤訊息，請嘗試[疑難排解指南](active-directory-application-proxy-troubleshoot.md)。

## <a name="configure-your-application"></a>設定您的應用程式

您可以修改已發佈的應用程式，或設定進階設定] 頁面上的選項。 在此頁面上，您可以變更名稱或上傳標誌，以自訂您的應用程式。 您也可以管理存取規則，例如預先驗證方法或多重因素驗證。

![進階的組態](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


發佈應用程式後使用 Azure Active Directory 應用程式 Proxy，其顯示在應用程式清單中 Azure AD，，您那里管理。

如果您停用應用程式 Proxy 服務，您已發佈的應用程式之後，他們已不再可從您的私人網路外部存取。 這並不會刪除應用程式。

若要檢視應用程式，並請確認該存取，請按兩下應用程式的名稱。 如果已停用應用程式 Proxy 服務應用程式無法使用，在螢幕頂端出現的警告訊息。

若要刪除的應用程式清單中選取應用程式，再按一下 [**刪除**。

## <a name="next-steps"></a>後續步驟

- [發佈應用程式使用您自己的網域名稱](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件的存取](active-directory-application-proxy-conditional-access.md)
- [使用 [宣告應用程式](active-directory-application-proxy-claims-aware-apps.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
