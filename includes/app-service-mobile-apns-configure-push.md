

1. 在 Mac 上啟動 [**鑰匙圈存取**]。 開啟 [**我的憑證** **] 類別**底下左的 navigationn 列上。 尋找您下載前一節的 SSL 憑證，然後透露其內容。 選取 [只憑證 （未選取私密金鑰），並[將其匯出](https://support.apple.com/kb/PH20122?locale=en_US)。

2. 在[Azure 入口網站](https://portal.azure.com/)中，按一下 [**全部瀏覽** > **應用程式服務**> 您的行動應用程式後端。 在 [**設定**]**應用程式服務推入**，請按一下，然後按一下 [您的通知中心名稱。 移至 [ **Apple 推入通知服務** > **上傳的憑證**。 上傳.p12 檔案，選取正確的**模式**（根據您的用戶端 SSL 憑證從較舊版本的是否為生產或沙箱）。 儲存變更。

您的服務正在設定以使用推播通知在 iOS 上 ！

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
