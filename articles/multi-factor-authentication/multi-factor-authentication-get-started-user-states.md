<properties 
    pageTitle="Microsoft Azure 多重因素驗證使用者狀態"
    description="瞭解在 Azure MFA 使用者狀態。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="user-states-in-azure-multi-factor-authentication"></a>在 [Azure 多重因素驗證的使用者狀態

Azure 多重因素驗證的使用者帳戶有下列三個不同的狀態︰

狀態 | 描述 |受影響的非瀏覽器應用程式| 備忘稿
:-------------: | :-------------: |:-------------: |:-------------: |
停用 | 未註冊多重因素驗證中的新使用者預設狀態。|無|使用者不使用多重因素驗證。
啟用 |多重因素驗證中註冊的使用者。|[否]。  它們繼續運作，除非已完成註冊程序。|使用者已啟用，但尚未完成註冊程序。 系統會提示完成程序，在下一個登入。
強制執行|使用者註冊，並已完成的註冊程序使用多重因素驗證。|[是]。  應用程式要求應用程式的密碼。 | 使用者可能或未完成註冊。 如果已經完成註冊程序，他們會使用多重因素驗證。 否則，使用者就會提示您完成程序，在下一個登入。

## <a name="changing-a-user-state"></a>變更使用者狀態
根據已為 MFA 的設定，並確定使用者是否已完成程序使用者狀態會變更。  當您開啟 MFA 使用者時，請停用狀態會變更的使用者啟用。  使用者，其狀態已變更為啟用，登入並完成程序之後，他們的狀態會變更強制。  

### <a name="to-view-a-users-state"></a>若要檢視使用者的狀態
--------------------------------------------------------------------------------
1.  以系統管理員身分登入**Azure 傳統入口網站**。
2.  在左側，按一下 [ **Active Directory**。
3.  **目錄**下的 [您想要啟用使用者的目錄。
![按一下 [目錄]](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  在頂端，按一下 [**使用者**]。
5.  在頁面底部，按一下 [**管理多重因素驗證**]。
![按一下 [目錄]](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  這會開啟新的瀏覽器索引標籤。  您可以檢視使用者狀態。
![按一下 [目錄]](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###<a name="to-change-the-state-from-disabled-to-enabled"></a>若要變更的狀態，停用啟用
1.  以系統管理員身分登入**Azure 傳統入口網站**。
2.  在左側，按一下 [ **Active Directory**。
3.  **目錄**下的 [您想要啟用使用者的目錄。
![按一下 [目錄]](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  在頂端，按一下 [**使用者**]。
5.  在頁面底部，按一下 [**管理多重因素驗證**]。
![按一下 [目錄]](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  這會開啟新的瀏覽器索引標籤。  尋找您想要啟用多因素驗證的使用者。 您可能需要變更 [頂端] 檢視。 確保您的狀態是 [**停用。**
![啟用使用者](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  置於**核取**其名稱旁的方塊。
7.  在右側，按一下 [**啟用**]。
![啟用使用者](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  按一下 [**啟用多重因素驗證**。
![啟用使用者](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  您應該注意到使用者的狀態從**停用**變更為**啟用**。
![讓使用者](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  您已啟用您的使用者後，建議這些通知透過電子郵件。  它應該也通知他們如何使用其非瀏覽器應用程式以避免被鎖定。

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>若要從啟用/強制執行的狀態變更為 [停用
1.  以系統管理員身分登入**Azure 傳統入口網站**。
2.  在左側，按一下 [ **Active Directory**。
3.  **目錄**下的 [您想要啟用使用者的目錄。
![按一下 [目錄]](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  在頂端，按一下 [**使用者**]。
5.  在頁面底部，按一下 [**管理多重因素驗證**]。
![按一下 [目錄]](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  這會開啟新的瀏覽器索引標籤。  尋找您要停用的使用者。 您可能需要變更 [頂端] 檢視。 確保您的狀態是 [**啟用**任一或**強制。**
7.  置於**核取**其名稱旁的方塊。
7.  在右側，按一下 [**停用**。
![停用使用者](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  系統會提示您確認此。  按一下**[是]**。
![停用使用者](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  您應該會看到已順利完成。  按一下 [**關閉。**
![停用使用者](./media/multi-factor-authentication-get-started-user-states/userstate4.png)
