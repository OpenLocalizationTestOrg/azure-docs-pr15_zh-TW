<properties
    pageTitle="行動電話適用的 Microsoft 驗證應用程式 |Microsoft Azure"
    description="瞭解如何升級至最新版 Azure 驗證。"
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
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator"></a>Microsoft 驗證

Microsoft 驗證應用程式提供其他 Azure 帳戶中的安全性層級 (，例如bsimon@contoso.onmicrosoft.com),您的內部部署公司帳戶 (例如，bsimon@contoso.com),或您的 Microsoft 帳戶 (，例如bsimon@outlook.com)。

應用程式運作的兩種方法之一︰

- **通知**。 應用程式可以協助避免未經授權的存取帳戶] 或 [停止權利交易推入通知您的智慧型手機或平板電腦。 只要檢視通知，及如果正確，請選取 [**驗證**。 否則，您可以選取 [**拒絕**。 拒絕接受通知的相關資訊，請參閱如何使用 [多重因素驗證的 [拒絕] 和 [報表網路詐騙功能。

- **驗證碼與密碼**。 應用程式可用於軟體權杖產生 OAuth 驗證碼。 您輸入出現提示時登入畫面，以及使用者名稱和密碼，將提供應用程式的程式碼。 驗證碼提供驗證第二個的表單。

版本的 Microsoft 驗證應用程式的舊版 Azure 驗證應用程式會被取代。  Azure 驗證應用程式會繼續運作，但如果您決定要移至新的 Microsoft 驗證應用程式，本文可協助您。  

## <a name="install-the-app"></a>安裝應用程式

使用[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、 [Android](http://go.microsoft.com/fwlink/?Linkid=825072)，以及[IOS](http://go.microsoft.com/fwlink/?Linkid=825073)Microsoft 驗證應用程式。

## <a name="add-accounts-to-the-app"></a>將帳戶新增至應用程式

針對每個您想要新增至 Microsoft 驗證應用程式的帳戶，請使用下列程序的其中一個。

### <a name="add-an-account-to-the-app-by-using-the-qr-code-scanner"></a>使用 QR 代碼掃描器將帳戶新增至應用程式

1. 移至 [安全性驗證設定] 畫面。  如何取得此畫面上的資訊，請參閱[變更您的安全性設定](multi-factor-authentication-end-user-manage-settings.md)。

2. 選取 [**設定**]。

    ![在 [安全性驗證設定] 畫面上的 [設定] 按鈕](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    如此會開啟以 QR 代碼上一個畫面。

    ![提供 QR 代碼的畫面](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. 開啟 Microsoft 驗證應用程式。 在 [**帳戶**] 畫面上選取 [ **+**，然後指定您想要新增公司或學校帳戶。

    ![加號 [帳戶] 畫面](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![針對指定的工作或學校帳戶] 畫面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. 使用網路攝影機掃描 QR 代碼，然後按一下 [**完成**] 以關閉 [QR 代碼] 畫面。

    ![掃描 QR 代碼的畫面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

    如果網路攝影機無法正常運作，您可以輸入 QR 代碼和 URL 以手動方式。 如需詳細資訊，請參閱[手動新增至應用程式的帳戶](#add-an-account-to-the-app-manually)。

5. 啟動帳戶，請等候。 啟動完成時，選取 [**與我的連絡人**]。  這會傳送通知或驗證碼至您的手機。  選取 [**驗證**]。

    ![您可以在此選取驗證登入畫面](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. 如果您的公司核准登入驗證需要 PIN，請輸入。

    ![] 方塊中輸入 pin 碼](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. 釘選的項目完成之後，請選取 [**關閉**]。 此時，您應該是成功驗證。
8. 我們建議您輸入您的行動電話號碼，以防您無法存取您的應用程式。 指定您的國家/地區，從下拉式清單中，並在 [國家/地區名稱旁的方塊中輸入您的行動電話號碼。 選取 [**下一步**]。
9. 此時，您必須設定您的連絡方式。 現在是設定為非瀏覽器應用程式，例如 Outlook 2010 或更舊版本的應用程式密碼的時間。 如果您不是使用這些應用程式，請選取 [**完成**]。 否則，請繼續下一個步驟。

    ![建立應用程式密碼] 畫面](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. 如果您使用非瀏覽器應用程式，請複製提供的應用程式密碼，並貼上到您的應用程式的密碼。 在個別的應用程式，例如 Outlook 和 Lync 的步驟，請參閱如何變更您的電子郵件中的密碼的應用程式密碼以及如何變更您的應用程式密碼的應用程式中的密碼。
11. 選取 [**完成**]。

您現在應該會在 [**帳戶**] 畫面上看到新的帳戶。

![帳戶] 畫面](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>應用程式中手動新增帳戶

1. 移至 [安全性驗證設定] 畫面。  如何取得此畫面上的資訊，請參閱[變更您的安全性設定](multi-factor-authentication-end-user-manage-settings.md)。

2. 選取 [**設定**]。

    ![在 [安全性驗證設定] 畫面上的 [設定] 按鈕](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    如此會開啟以 QR 代碼上一個畫面。  請注意的程式碼及 URL。

    ![提供 QR 代碼和 URL 的螢幕](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. 開啟 Microsoft 驗證應用程式。 在 [**帳戶**] 畫面上選取 [ **+**，然後指定您想要新增公司或學校帳戶。

    ![加號 [帳戶] 畫面](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![針對指定的工作或學校帳戶] 畫面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. 在 [掃描器，選取 [**手動輸入的程式碼**]。

    ![掃描 QR 代碼的畫面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. 在應用程式中適當的方塊中輸入的程式碼，URL。

    ![輸入代碼和 URL 的螢幕](./media/multi-factor-authentication-azure-authenticator/manual.png)

    ![輸入代碼和 URL 的螢幕](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. 啟動帳戶，請等候。 啟動完成時，選取 [**連絡人]**。 這會傳送通知或驗證碼至您的手機。 選取 [**驗證**]。

您現在應該會在 [**帳戶**] 畫面上看到新的帳戶。

![帳戶] 畫面](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-by-using-touch-id"></a>使用觸控識別碼應用程式中新增帳戶

Microsoft 驗證上的應用程式在 iOS 支援觸控識別碼。  Azure 多重因素驗證可讓組織裝置需要 PIN。 觸控識別碼，不需要 iOS 使用者輸入 pin 碼。 不過，他們可以掃描其指紋的步驟，然後選取 [**核准**。

設定與 Microsoft 驗證的觸控識別碼很簡單。 在您完成 PIN 標準驗證難題。 如果您的裝置可支援觸控識別碼，Microsoft 驗證者會將其設定自動為該帳戶。

![驗證的觸控 ID 設定](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

從點之後時要求您確認您登入，您選取接收推入通知，並瀏覽您指紋，而不必輸入您的 PIN。

![推播通知](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## <a name="uninstall-the-old-azure-authentication-app"></a>解除安裝舊版 Azure 驗證應用程式

您已新增的所有帳戶至新的應用程式之後，您可以從您的手機來解除安裝舊版的應用程式。

## <a name="delete-an-account"></a>刪除帳戶

若要從 Microsoft 驗證應用程式移除帳戶、 選取帳戶，然後選取 [**刪除**。

![[刪除] 按鈕](./media/multi-factor-authentication-azure-authenticator/remove.png)
