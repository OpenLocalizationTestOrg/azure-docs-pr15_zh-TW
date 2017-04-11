**目標 C**: 

1. 在 Mac 上開啟_QSTodoListViewController.m_中 Xcode 並加入下列方法。 如果您不使用 Google 做為您的身分識別提供者，請變更為_microsoftaccount_、 _twitter_、 _facebook_或_windowsazureactivedirectory_的_google_ 。 如果您是使用 Facebook，[您將需要 whitelist Facebook 應用程式中的網域](https://developers.facebook.com/docs/ios/ios9#whitelist)。

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. 取代`[self refresh]`中`viewDidLoad` _QSTodoListViewController.m_以下列中︰

            [self loginAndGetData];

3. 按下 [_執行_] 以啟動應用程式，然後再登入。 當您登入時，您應該可以檢視待辦清單，並進行更新。

**快速**︰

1. 在 Mac 上開啟_ToDoTableViewController.swift_中 Xcode 並加入下列方法。 如果您不使用 Google 做為您的身分識別提供者，請變更為_microsoftaccount_、 _twitter_、 _facebook_或_windowsazureactivedirectory_的_google_ 。 如果您是使用 Facebook，[您將需要 whitelist 應用程式中的 Facebook 網域](https://developers.facebook.com/docs/ios/ios9#whitelist)。
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. 移除線條`self.refreshControl?.beginRefreshing()`和`self.onRefresh(self.refreshControl)`結尾的`viewDidLoad()` _ToDoTableViewController.swift_中。 加入通話`loginAndGetData()`在其位置︰

            loginAndGetData()

3. 按下 [_執行_] 以啟動應用程式，然後再登入。 當您登入時，您應該可以檢視待辦清單，並進行更新。
