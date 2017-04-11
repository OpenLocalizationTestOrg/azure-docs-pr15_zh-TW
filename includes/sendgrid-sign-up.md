Azure 客戶可以取消每個月鎖定 25000 免費的電子郵件。 這些 25000 的免費月電子郵件會讓您存取進階的報告及分析及[所有 Api][] （網頁、 SMTP、 事件、 分析及其他資訊）。 如 SendGrid 所提供的其他服務的相關資訊，請參閱 [ [SendGrid 功能][]] 頁面。

### <a name="to-sign-up-for-a-sendgrid-account"></a>若要註冊 SendGrid 帳戶

1. [Azure 管理入口網站][]登入。

2. 在 [管理] 入口網站的左下窗格中，按一下 [**新增**]。

    ![命令列-新][command-bar-new]

3. 按一下 [**市集]**。

    ![sendgrid 存放區][sendgrid-store]

4. 在 [**選擇應用程式及服務**] 對話方塊中，選取**SendGrid**然後按一下右箭號。

5. **個人化的應用程式及服務**] 對話方塊中選取您要註冊**SendGrid**計劃。

6. 輸入名稱來識別您**SendGrid**在服務 Azure 的設定]，或使用**SendGridEmailDelivery.Simplified.SMTPWebAPI**的預設值。 名稱長度 1 到 100 個字元之間，並包含只英數字元、 虛線、 點和底線。 名稱必須是唯一的訂閱 Azure 市集項目清單中。

    ![存放區畫面 2][store-screen-2]

7. 選擇區域; 值例如，西美國的資料。

8. 按一下右箭號。

9. 在 [**檢視購買**] 索引標籤上檢閱方案與價格資訊，並檢閱法律條款。 如果您同意條款，請按一下核取記號。 按一下核取符號之後，您 SendGrid 帳戶就會開始[SendGrid 佈建程序]。

    ![存放區-畫面-3][store-screen-3]

10. 確認您購買後重新導向至附加元件儀表板，您會看到訊息**購買 SendGrid**。

    ![sendgrid 購買訊息][sendgrid-purchasing-message]

    立即佈建 SendGrid 帳戶，您會看到**成功購買附加元件 SendGrid**訊息。 現在建立您的帳戶和認證。 您準備好在此時傳送電子郵件。 

    若要修改您的訂閱方案，或是看到連絡人設定 SendGrid，請按一下以開啟 [SendGrid 服務商場儀表板您 SendGrid 服務的名稱。 

    ![sendgrid-新增-在-儀表板][sendgrid-add-on-dashboard]

    若要傳送電子郵件使用 SendGrid，您必須提供您的帳戶認證 （使用者名稱和密碼）。

### <a name="to-find-your-sendgrid-credentials"></a>若要尋找您的 SendGrid 認證 ###

1. 按一下 [**連線資訊**]。

    ![sendgrid-連線-資訊-] 按鈕][sendgrid-connection-info-button]

2. 在 [*連線資訊*] 對話方塊中，複製稍後在本教學課程中使用的**密碼**及使用者名稱。

    ![sendgrid 連線資訊][sendgrid-connection-info]

    若要設定您的電子郵件既設定，請按一下 [**管理**] 按鈕。 這會重新導向至您 SendGrid [控制台]。 

    ![sendgrid 控制台][sendgrid-control-panel]

    如需有關如何開始使用 SendGrid 的詳細資訊，請參閱[SendGrid 快速入門][]。

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[SendGrid 功能]: http://sendgrid.com/features
[Azure 管理入口網站]: https://manage.windowsazure.com
[SendGrid 快速入門]: http://sendgrid.com/docs
[SendGrid 佈建程序]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[所有的 Api]: https://sendgrid.com/docs/API_Reference/index.html

