<properties
   pageTitle="在瀏覽器] 索引標籤，然後搜尋結果中顯示的頁面標題"
   description="將會顯示在登陸頁面，然後在大部分的搜尋結果中的文章描述"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="markdown-template-article-heading-1-or-h1-heading-at-the-top-of-the-article"></a>Markdown 範本 （文件標題 1] 或 [H1）︰ 在文件頂端的標題

若要將此範本，複製 markdown 複製，請參閱在您的本機 repo 或 GitHub ui 上的 [原始] 按鈕，然後按一下複製 markdown。

  ![替代文字。不留白。 說明圖像。][8]

簡介段落︰ Lorem dolor amet adipiscing elit。 Phasellus interdum nulla risus，lacinia porta 您 imperdiet sed. Mauris dolor mauris，新 sed lacinia nec，euismod 非 felis。 歡 semper porta ultrices。 Maecenas neque nulla，condimentum 傳統 ipsum 坐 amet，dignissim aliquet nisi。

## <a name="heading-2-h2"></a>標題 2 (H2)

Aenean 坐 amet 奧一 nec purus placerat fermentum ac gravida odio。 在中 faucibus sed urna rhoncus faucibus Aenean tellus lectus。  volutpat 英哩識別碼 purus ultrices iaculis nec 非 neque。 [Azure.microsoft.com 外部連結的連結文字](http://weblogs.asp.net/scottgu)。 在 aliquam pharetra Nullam dictum dolor。 Vivamus ac hendrerit mauris[服務資料夾中的文章連結的範例連結文字](../articles/expressroute/expressroute-bandwidth-upgrade.md)。

從[Google]取得更多 10 次流量 [gog]比從[Yahoo]  [yah]或[MSN] [msn]。

> [AZURE.NOTE] 縮排的註解文字。  在出版物時，便會新增 word 「 記事 」。 歐盟 pretium lacus。 Nullam purus est，iaculis sed est vel、 euismod vehicula odio。 Curabitur lacinia、 erat tristique iaculis rutrum、 erat sem sodales nisi 歐盟 condimentum turpis nisi purus。

1. Aenean 坐 amet 奧一 nec **Purus** placerat fermentum ac gravida odio。

2. 在 faucibus sed urna 中**Rhoncus** faucius Aenean tellus lectus。 Suspendisse volutpat 英哩識別碼 purus ultrices iaculis nec 非 neque。

    ![替代文字。不留白。 在 [賽紅色行程車。][5]

3. 在 aliquam pharetra Nullam dictum dolor。 Vivamus ac hendrerit mauris。 Sed dolor dui condimentum set varius vehicula 在您的。

    ![替代文字。不保留空白][6]


Suspendisse volutpat 英哩識別碼 purus ultrices iaculis nec 非 neque。 在 aliquam pharetra Nullam dictum dolor。 Vivamus ac hendrerit mauris。 Otrus informatus︰[連結至另一個 azure.microsoft.com 文件主題的 1](virtual-machines-windows-hero-tutorial.md)

## <a name="heading-h2"></a>標題 (H2)

歐盟 pretium lacus。 Nullam purus est，iaculis sed est vel、 euismod vehicula odio。

1. Curabitur lacinia、 erat tristique iaculis rutrum、 erat sem sodales nisi 歐盟 condimentum turpis nisi purus。

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. 在 [faucibus orci luctus Vestibulum ante ipsum primis set ultrices posuere cubilia。

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }


    > [AZURE.NOTE] Duis sed diam 非<i>您 molestie</i> pharetra eget est。 [另一個 azure.microsoft.com 文件主題連結 2](web-sites-custom-domain-name.md)


Quisque 輸入 eros vel lectus euismod auctor eget 坐 amet 奧一。 Proin faucibus 文字 tellus dignissim ultrices。

## <a name="heading-2-h2"></a>標題 2 (H2)

1. Maecenas sed condimentum nisi。 Suspendisse potenti。

  + Fusce
  + Malesuada
  + Sem

2. 在 [massa 歐盟 tellus 新 hendrerit Nullam。

    ![替代文字。不留白。 頻道 9 視訊的範例。][7]

3. Quisque felis enim fermentum 的 aliquam nec，pellentesque pulvinar 位於。




<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

在 [faucibus orci luctus Vestibul ante ipsum primis set ultrices posuere cubilia Curae;Nullam ultricies，ipsum 傳統 volutpat hendrerit，purus diam pretium eros、 傳統在 nulla lorem sed turpis︰[連結至另一個 azure.microsoft.com 文件主題的 3](storage-whatis-account.md)。

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    
