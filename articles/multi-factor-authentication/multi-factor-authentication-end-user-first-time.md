<properties
    pageTitle="設定我的公司或學校帳戶進行雙步驟驗證"
    description="當您公司設定 Azure 多重因素驗證時，系統會提示註冊雙步驟驗證。 瞭解如何將其設定。 "
    services="multi-factor-authentication"
    keywords="如何使用 azure 目錄，在雲端，active directory 教學課程的 active directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="set-up-my-account-for-two-step-verification"></a>設定我的帳號的雙步驟驗證

雙步驟驗證是額外的安全性的步驟，可協助保護您的帳戶難以中斷中其他人。 如果您正在讀取這份文件，您可能是您電子郵件從您公司或學校的系統管理員有關多重因素驗證。 或也許您在嘗試登入並收到訊息，詢問您要設定其他安全性驗證。 如果這是**您完成自動註冊程序之前，您無法登入**的大小寫，。

本文可協助您設定您的**公司或學校帳戶**。 如果您想要啟用雙步驟驗證您自己，個人的 Microsoft 帳戶，請參閱[雙步驟驗證](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>決定使用多重因素驗證的方式

雙步驟驗證的運作方式提示您輸入的識別兩個部分，當您登入。 首先，我們要求您的使用者名稱和密碼像往常一樣。 然後，連絡人的電話，我們知道所屬，並且您我們要確認的登入嘗試並未正確。  

若要開始安裝程序，請嘗試等通常要執行您的帳戶登入。 如果您的系統管理員已設定為雙步驟驗證您的帳戶，系統會提示您開始自動註冊程序。 按一下 [開始此程序**現在設定。**

![設定](./media/multi-factor-authentication-end-user-first-time/first.png)

註冊程序中的第一個問題是您希望我們為您的連絡的方式。 在資料表中，看看這些選項，然後使用連結] 以移至每一種方法的設定步驟。

| 連絡方式 | 描述 |
| --- | --- |
[行動應用程式](#use-a-mobile-app-as-the-contact-method) | - **收到通知，以供驗證。** 這個選項會將推入通知驗證應用程式智慧型手機或平板電腦上。 檢視通知，並如果這是正確的選取 [應用程式中 [**驗證**]。 公司或學校，可能會要求您輸入 PIN，才能在您進行驗證。<br>- **使用 [驗證碼。** 在此模式中，驗證應用程式會產生更新每隔 30 秒的驗證碼。 在登入介面中輸入的最新的驗證碼。<br>使用[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、 [Android](http://go.microsoft.com/fwlink/?Linkid=825072)，以及[IOS](http://go.microsoft.com/fwlink/?Linkid=825073)Microsoft 驗證應用程式。 |
[行動電話或文字](#use-your-mobile-phone-as-the-contact-method) | - **電話**就會自動的語音通話所提供的電話號碼。 接聽電話，然後按電話鍵盤驗證 #。<br>- **文字訊息**結束文字訊息包含驗證碼。 在文字] 提示時，下列文字訊息回覆或是輸入至登入介面所提供的驗證碼。 |  
[辦公室電話](#use-your-office-phone-as-the-contact-method) | 撥打給您提供的電話號碼自動的語音通話。 接聽電話並在驗證的電話鍵盤中按下 #。 |

## <a name="use-a-mobile-app-as-the-contact-method"></a>使用行動應用程式的連絡人的方法

使用此方法需要您電話或平板電腦上安裝驗證應用程式。 根據 Microsoft 驗證應用程式時，這是適用於[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、 [Android](http://go.microsoft.com/fwlink/?Linkid=825072)、 和[IOS](http://go.microsoft.com/fwlink/?Linkid=825073)本文中的步驟進行。

1. 從下拉式清單中選取**行動應用程式**。
2. 選取**會收到通知，以供驗證**或**使用驗證碼**，然後選取 [**設定**]。

    ![其他安全性驗證畫面](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. 電話或平板電腦上，開啟應用程式，然後選取**+**新增帳戶。 （在 Android 裝置上選取三個點，然後**新增帳戶**）。
4. 指定您想要新增公司或學校帳戶。 在電話上的 QR 代碼掃描器隨即會開啟。 如果網路攝影機無法正常運作，您可以選取手動輸入您的公司資訊。 如需詳細資訊，請參閱[手動新增帳戶](#add-an-account-manually)。  
5. 掃描 QR 代碼圖片出現設定行動應用程式的畫面。  選取 [**完成**] 以關閉 [QR 代碼] 畫面。  

    ![QR 代碼畫面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

6. 在電話上的啟動完成時，選取 [**連絡人]**。  此步驟會傳送通知或驗證碼至您的手機。 選取 [**驗證**]。  
7. 如果您的公司核准登入驗證需要 PIN，請輸入。

    ![] 方塊中輸入 pin 碼](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. 釘選的項目完成之後，請選取 [**關閉**]。 此時，您應該是成功驗證。
9. 我們建議您輸入您的行動電話號碼，以防您無法存取您的行動應用程式。 指定您的國家/地區，從下拉式清單中，並在 [國家/地區名稱旁的方塊中輸入您的行動電話號碼。 選取 [**下一步**]。
10. 此時，系統會提示若要設定應用程式密碼的非瀏覽器應用程式，例如 Outlook 2010 或更舊版本或 Apple 裝置上的原生的電子郵件應用程式。 這是因為某些應用程式不支援雙步驟驗證。 如果您不使用這些應用程式，請按一下 [**完成**]，並略過其餘的步驟。
11. 如果您使用這些應用程式，複製應用程式密碼提供，並將它貼到您的應用程式，而不是您一般的密碼。 您可以使用相同的應用程式密碼的多個應用程式。 取得詳細資訊，[協助與應用程式密碼]。
12. 按一下 [**完成**]。


### <a name="add-an-account-manually"></a>手動新增帳戶
如果您想要的行動應用程式，而不是使用 QR 助讀程式中手動新增帳戶，請遵循下列步驟。

1. 選取 [**手動輸入帳戶**] 按鈕。  
2. 輸入的程式碼，並在同一頁的條碼的功能會顯示您所提供的 URL。 這項資訊會在行動應用程式上 [**程式碼**和**URL** ] 方塊。

    ![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. 完成啟動後，選取 [**與我的連絡人**]。 此步驟會傳送通知或驗證碼至您的手機。 選取 [**驗證**]。

## <a name="use-your-mobile-phone-as-the-contact-method"></a>使用您的行動電話的連絡人的方法

1. 從下拉式清單中選取 [**驗證電話**]。  

    ![設定](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)  

2. 從下拉式清單中，選擇您的國家/地區，然後輸入您的行動電話號碼。
3. 選取您想要使用您的行動電話的文字或通話的方法。
4. 選取 [**連絡人]**以驗證您的電話號碼。 根據您選取模式，我們會傳送簡訊給您，或撥打電話給您。 依照在畫面上，提供的指示，然後選取 [**驗證**]。
5. 此時，系統會提示若要設定應用程式密碼的非瀏覽器應用程式，例如 Outlook 2010 或更舊版本或 Apple 裝置上的原生的電子郵件應用程式。 這是因為某些應用程式不支援雙步驟驗證。 如果您不使用這些應用程式，請按一下 [**完成**]，並略過其餘的步驟。
6. 如果您使用這些應用程式，複製應用程式密碼提供，並將它貼到您的應用程式，而不是您一般的密碼。 您可以使用相同的應用程式密碼的多個應用程式。 取得詳細資訊，[協助與應用程式密碼]。
7. 按一下 [**完成**]。

## <a name="use-your-office-phone-as-the-contact-method"></a>使用您的辦公室電話的連絡人的方法

1. 從下拉式清單中選取 [**辦公室電話**  

    ![設定](./media/multi-factor-authentication-end-user-first-time-office-phone/office.png)  

2. 電話號碼] 方塊會自動填入您公司的連絡人資訊。 如果 number 是錯誤或遺漏，請要求管理員進行變更。
4. 選取 [**連絡人]**以驗證您的電話號碼，我們會撥打您的電話號碼。 依照在畫面上，提供的指示，然後選取 [**驗證**]。
5. 此時，系統會提示若要設定應用程式密碼的非瀏覽器應用程式，例如 Outlook 2010 或更舊版本或 Apple 裝置上的原生的電子郵件應用程式。 這是因為某些應用程式不支援雙步驟驗證。 如果您不使用這些應用程式，請按一下 [**完成**]，並略過其餘的步驟。
6. 如果您使用這些應用程式，複製應用程式密碼提供，並將它貼到您的應用程式，而不是您一般的密碼。 您可以使用相同的應用程式密碼的多個應用程式。 取得詳細資訊，請參閱[什麼是應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)。
7. 按一下 [**完成**]。

## <a name="next-steps"></a>後續步驟

- 變更您慣用的選項並[管理您的設定進行雙步驟驗證](multi-factor-authentication-end-user-manage-settings.md)
- 設定[應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)不支援雙步驟驗證的原生裝置應用程式。
- 即使您沒有儲存格的服務，請查看[Microsoft 驗證應用程式](multi-factor-authentication-microsoft-authenticator.md)快速、 安全驗證。
