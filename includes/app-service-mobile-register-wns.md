
1. Visual Studio 方案總管] 中以滑鼠右鍵按一下 Windows 市集應用程式專案，請按一下 [**市集** > **建立關聯的應用程式存放區...**。

    ![與 Windows 市集應用程式](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)

2. 在精靈中，按一下 [**下一步**、 使用您的 Microsoft 帳戶登入、 輸入您的應用程式的名稱中**保留新的應用程式名稱**，然後按一下 [**保留**。

3. 應用程式註冊已成功建立之後，請選取新的應用程式名稱，按一下 [**下一步**，，然後按一下**建立關聯**。 如此會將所需的 Windows 市集註冊資訊應用程式資訊清單。

7. Windows Phone Store 應用程式專案的 Windows 市集應用程式中使用相同的登錄您先前建立的重複步驟 1 到 3。  

7. 瀏覽至[Windows 開發人員中心](https://dev.windows.com/en-us/overview)中，使用您的 Microsoft 帳戶登入，按一下 [新增應用程式註冊在**我的應用程式**，然後展開**服務** > **推播通知**。

8. 在**推播通知**] 頁面上，按一下 [在**Windows 推入通知服務 (WNS) 與 Microsoft Azure Mobile 應用程式**] 底下的 [ **Live 服務網站**並記下**套件 SID**的值和*目前*的值在 [**應用程式密碼**。 

    ![在開發人員中心的應用程式設定](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT] [應用程式密碼] 和 [套件 SID 是重要的安全性憑證。 不使用任何人共用這些值或發佈您的應用程式。
