<properties
    pageTitle="什麼是 Azure MFA 應用程式密碼？"
    description="此頁面可協助使用者瞭解應用程式密碼是什麼，以及使用的使用方面 Azure MFA。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>



# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>什麼是 Azure 多重因素驗證應用程式密碼？

某些非瀏覽器的相關應用程式，例如 Apple 原生的電子郵件用戶端使用 Exchange Active Sync，目前不支援多重因素驗證。 每位使用者已啟用多重因素驗證。 這表示，如果使用者已啟用多因素驗證他們嘗試使用非瀏覽器應用程式，他們會無法執行此作業。 應用程式密碼 」 可讓這個情況發生。

>[AZURE.NOTE] Office 2013 用戶端的現代化驗證
>
> Office 2013 用戶端 (包括 Outlook) 現在支援新的驗證通訊協定，並可啟用支援多重因素驗證。  這表示，啟用之後，應用程式密碼並不需要使用與 Office 2013 用戶端。  如需詳細資訊，請參閱[Office 2013 的現代化驗證公用預覽宣告](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

## <a name="how-to-use-app-passwords"></a>如何使用應用程式密碼

以下是如何使用應用程式密碼記住的一些事項。

- 實際的密碼會自動產生，並不會提供的使用者。 這是因為自動產生的密碼，難猜測攻擊，更加安全。
- 目前沒有 40 每位使用者的密碼的限制。 如果您嘗試建立您已達到限制之後，系統會提示您刪除其中一個您現有的應用程式密碼以建立一個新。
- 建議的每個裝置並不每個應用程式建立應用程式密碼。 例如，您可以建立一個應用程式密碼膝上型電腦，並針對您的膝上型電腦上的應用程式的所有使用該應用程式密碼。
- 您可以在應用程式密碼第一次您登入。  如果您需要額外的文件時，您可以建立它們。

![設定](./media/multi-factor-authentication-end-user-app-passwords/app.png)

應用程式密碼之後，您會使用這個取代這些非瀏覽器應用程式與原始密碼。  因此，例如，如果您在手機上使用多重因素驗證與 Apple 原生的電子郵件用戶端。  [密碼] 使用應用程式，使其可以略過多因素驗證繼續處理。

## <a name="creating-and-deleting-app-passwords"></a>建立及刪除應用程式密碼
在您初始的登入您可以使用應用程式密碼，您可以使用。  此外您也可以建立及更新版本上刪除應用程式密碼。  如何執行此動作，取決於您如何使用多重因素驗證。  選擇最適用於您。

使用多重因素適的方式|描述
:------------- | :------------- |
[使用 Office 365 使用](#creating-and-deleting-app-passwords-with-office-365)|  這表示您會想要建立透過 Office 365 入口網站應用程式密碼。
[我不知道](#creating-and-deleting-app-passwords-with-myapps-portal)|這表示您會想要建立[https://myapps.microsoft.com](https://myapps.microsoft.com)透過應用程式密碼
[使用與 Microsoft Azure](#create-app-passwords-in-the-azure-portal)| 這表示您會想要建立透過 Azure 入口網站應用程式密碼。

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>建立及刪除 Office 365 應用程式密碼

如果您是搭配 Office 365，您會想要建立及刪除透過 Office 365 入口網站應用程式密碼使用多重因素驗證。

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>若要在 Office 365 入口網站中建立應用程式密碼
--------------------------------------------------------------------------------

1. 登入[Office 365 入口網站](https://login.microsoftonline.com/)。
2. 在右上角選取 [小工具然後選擇 [Office 365 設定。
3. 按一下 [其他安全性驗證。
4. 在右側，按一下 [連結指出**更新我的電話號碼用於帳戶安全性。**
![設定](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. 這會將您帶往 [可讓您變更您的設定] 頁面。
![雲端](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. 頂端額外的安全性驗證旁按一下**應用程式密碼。**
7. 按一下 [**建立**]。
![雲端](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. 輸入應用程式密碼的名稱，然後按一下 [**下一步**]。
![建立應用程式密碼](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. 將應用程式密碼複製到剪貼簿，並將它貼到您的應用程式。
![建立應用程式密碼](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>若要刪除使用 Office 365 入口網站應用程式密碼
--------------------------------------------------------------------------------


1. 登入[Office 365 入口網站](https://login.microsoftonline.com/)。
2. 在右上角選取 [小工具然後選擇 [Office 365 設定。
3. 按一下 [其他安全性驗證。
4. 在右側，按一下 [連結指出**更新我的電話號碼用於帳戶安全性。**
![設定](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. 這會將您帶往 [可讓您變更您的設定] 頁面。
![雲端](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. 頂端額外的安全性驗證旁按一下**應用程式密碼。**
7. 按一下您想要刪除的應用程式密碼] 旁的 [**刪除**]。
![刪除應用程式密碼](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. 按一下**[是]**確認刪除。
![確認刪除](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. 一旦刪除應用程式密碼可以按一下 [**關閉]**。
![關閉](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>建立及刪除 Myapps 入口網站應用程式密碼。
如果您不確定您如何使用多重因素驗證，然後您可以隨時建立及刪除透過 [myapps] 入口網站應用程式密碼。

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>若要建立使用 [Myapps 入口網站應用程式密碼

1. 登入[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 在頂端，選取 [設定檔。
3. 選取額外的安全性驗證。
![雲端](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 這會將您帶往 [可讓您變更您的設定] 頁面。
![設定](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. 頂端額外的安全性驗證旁按一下**應用程式密碼。**
6. 按一下 [**建立**]。
![建立應用程式密碼](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. 輸入應用程式密碼的名稱，然後按一下 [**下一步**]。
![建立應用程式密碼](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. 將應用程式密碼複製到剪貼簿，並將它貼到您的應用程式。
![建立應用程式密碼](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>若要刪除使用 [Myapps 入口網站應用程式密碼

1. 登入[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 在頂端，選取 [設定檔。
3. 選取額外的安全性驗證。
![雲端](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 這會將您帶往 [可讓您變更您的設定] 頁面。
![設定](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. 頂端額外的安全性驗證旁按一下**應用程式密碼。**
6. 按一下您想要刪除的應用程式密碼] 旁的 [**刪除**]。
![刪除應用程式密碼](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. 按一下**[是]**確認刪除。
![確認刪除](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. 一旦刪除應用程式密碼可以按一下 [**關閉]**。
![關閉](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="create-app-passwords-in-the-azure-portal"></a>Azure 入口網站中建立應用程式密碼

如果您使用的多重因素驗證和 Azure 您會想要建立透過 Azure 入口網站應用程式密碼。

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Azure 入口網站中建立應用程式的密碼

1. 登入 Azure 管理入口網站。
2. 在上方，以滑鼠右鍵按一下您的使用者名稱，然後選取其他安全性驗證。
3. 在 [proofup] 頁面頂端，選取 [應用程式密碼
4. 按一下 [**建立**]。
5. 輸入應用程式密碼的名稱，然後按一下 [**下一步**
6. 將應用程式密碼複製到剪貼簿，並將它貼到您的應用程式。
![雲端](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>若要刪除 Azure 入口網站中的應用程式密碼

1. 登入 Azure 管理入口網站。
2. 在上方，以滑鼠右鍵按一下您的使用者名稱，然後選取其他安全性驗證。
3. 頂端額外的安全性驗證旁按一下**應用程式密碼。**
4. 按一下您想要刪除的應用程式密碼] 旁的 [**刪除**]。
5. 按一下**[是]**確認刪除。
6. 一旦刪除應用程式密碼可以按一下 [**關閉]**。
![關閉](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)
