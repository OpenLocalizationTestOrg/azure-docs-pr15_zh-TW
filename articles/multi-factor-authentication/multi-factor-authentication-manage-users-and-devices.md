<properties 
    pageTitle="Azure 多重因素驗證報表"
    description="以下說明如何變更使用者設定，例如強制再次執行證明程序的使用者。"
    documentationCenter=""
    services="multi-factor-authentication"
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

# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>管理使用者設定與 Azure 雲端中的多因素驗證

身為管理員，您可以管理下列使用者和裝置設定。  

- [需要所選的使用者，以再次提供連絡人的方法](#require-selected-users-to-provide-contact-methods-again)
- [刪除現有的應用程式密碼的使用者](#delete-users-existing-app-passwords)
- [還原 MFA 使用者的所有已擱置的裝置上](#restore-mfa-on-all-suspended-devices-for-a-user)






如果在電腦或裝置遺失或竊取或您需要移除的使用者存取權，這會很有幫助。


## <a name="require-selected-users-to-provide-contact-methods-again"></a>需要所選的使用者，以再次提供連絡人的方法

此設定會強制使用者，以完成註冊程序，當他登入。 請注意，非瀏覽器應用程式會繼續如果使用者擁有應用程式密碼，才能運作。  您可以刪除使用者應用程式密碼也選取 [**刪除所有現有的應用程式密碼產生的所選的使用者**。

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>如何要求使用者再次提供連絡人的方法




1. 登入 Azure 傳統入口網站。
2. 在左側，按一下 [Active Directory。
3. 目錄下的 [目錄要必須再次提供其連絡方式的使用者。
4. 在頂端，按一下 [使用者]。
5. 在頁面底部，按一下 [管理多重因素驗證 這會開啟 [多重因素驗證] 頁面。
6. 尋找您想要管理，並在名稱旁的方塊中的使用者。 您可能需要變更 [頂端] 檢視。
7. 這會顯示在右側的 [**管理使用者設定**] 連結。 按一下它。
8. **需要所選的使用者提供連絡人方法一次**存回的位置。
![提供連絡人的方法](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. 按一下 [儲存]。
11. 按一下 [關閉]

## <a name="delete-users-existing-app-passwords"></a>刪除現有的應用程式密碼的使用者

刪除的所有使用者所建立的應用程式密碼。 這些應用程式密碼與相關聯的非瀏覽器應用程式會停止運作，除非已建立新的應用程式密碼。

### <a name="how-to-delete-users-existing-app-passwords"></a>如何刪除現有的應用程式密碼的使用者

1. 登入 Azure 傳統入口網站。
2. 在左側，按一下 [Active Directory。
3. 目錄下的 [您想要刪除的應用程式密碼的使用者的目錄。
4. 在頂端，按一下 [使用者]。
5. 在頁面底部，按一下 [管理多重因素驗證 這會開啟 [多重因素驗證] 頁面。
6. 尋找您想要管理，並在名稱旁的方塊中的使用者。 您可能需要變更 [頂端] 檢視。
7. 這會顯示在右側的 [**管理使用者設定**] 連結。 按一下它。
8. 將核取中**刪除所有現有的應用程式密碼產生的所選的使用者**。
![刪除應用程式密碼](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. 按一下 [儲存]。
10. 按一下 [關閉]。

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>還原 MFA 使用者的所有記憶裝置上

系統管理員可以還原使用者的裝置與瀏覽器上的多重因素驗證。 這麼一來，這會移除記住 MFA 所有使用者的裝置與瀏覽器與一位使用者都必須在下次登入時使用 MFA。

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>如何在使用者的所有已擱置的裝置上還原 MFA

1. 登入 Azure 傳統入口網站。
2. 在左側，按一下 [Active Directory。
3. 目錄下的 [您想要在還原 mfa 使用者的目錄。
4. 在頂端，按一下 [使用者]。
5. 在頁面底部，按一下 [管理多重因素驗證 這會開啟 [多重因素驗證] 頁面。
6. 尋找您想要管理，並在名稱旁的方塊中的使用者。 您可能需要變更 [頂端] 檢視。
7. 這會顯示在右側的 [**管理使用者設定**] 連結。 按一下它。
8. **還原記憶的所有裝置上的多重因素驗證**時，置於核取
![刪除應用程式密碼](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. 按一下 [儲存]。
10. 按一下 [關閉]。
