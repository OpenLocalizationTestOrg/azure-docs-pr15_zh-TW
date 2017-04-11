<properties
    pageTitle="Azure 部署蝕中啟用遠端存取"
    description="瞭解如何啟用遠端存取 Azure 部署使用蝕 Azure 工具組。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>Azure 部署蝕中啟用遠端存取

若要協助您部署，您可能會啟用，然後使用遠端存取連接到裝載您的部署的虛擬機器。 遠端存取功能是在遠端桌面通訊協定 (RDP)。 在它發佈到 Azure，或如果您使用的蝕與 Windows 作業系統，您發佈到 Azure 之前，您可以設定遠端存取之後，您可以設定遠端存取您的部署。 請注意，您需要遠端桌面用戶端才能連線到您的部署虛擬機器 Azure 中的相容於您的作業系統。

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>如何啟用遠端存取您部署至 Azure 之前

> [AZURE.NOTE] 若要在部署您的應用程式 Azure 之前，請啟用遠端存取，您需要在 Windows 上執行蝕。

下圖顯示的對話方塊用來啟用遠端存取的**遠端存取**內容。

![][ic719494]

有兩種方式來顯示 [**遠端存取**內容] 對話方塊︰

* 按一下 [**發佈至 Azure** ] 對話方塊的 [**遠端存取**] 區段中的 [**進階**] 連結。
* 開啟 Azure 專案的 [**屬性**] 對話方塊。

當您建立新的 Azure 部署專案時，專案不會預設為啟用遠端存取。 不過，您可以在 [**發佈至 Azure**對話方塊中指定的使用者名稱和密碼，輕鬆地啟用遠端存取。 遠端存取密碼已使用 X.509 憑證來加密。 如果您沒有使用提供給您自己的憑證，加密依賴自我簽署憑證隨附蝕 Azure 外掛程式。 這個自我簽署的憑證是 Azure 專案，為公用憑證檔案 (SampleRemoteAccessPublic.cer) 和個人資訊交換 (PFX) 憑證檔案 (SampleRemoteAccessPrivate.pfx) 為儲存的 [**憑證**] 資料夾中。 後者包含私密金鑰的憑證，並有預設的密碼， **Password1**。 不過，由於這個密碼公用知識庫，預設憑證應僅適用於學習的目的，不適用於生產部署。 因此以外學習的目的，當您要啟用遠端工作階段，針對您的部署，您應該按一下，指定您自己的憑證的 [**發佈至 Azure** ] 對話方塊中的 [**進階**] 連結。 請注意，您必須將上傳的憑證 PFX 版本中 Azure 管理入口網站，您裝載的服務的 Azure 可以解密使用者的密碼。

教學課程的其餘部分將示範如何啟用 Azure 部署專案的初始建立與遠端存取停用的遠端存取。 為了本教學課程中，我們會建立新的自我簽署的憑證，並其.pfx 檔會有您所選擇的密碼。 您也可以使用的憑證授權單位發出的憑證的選項。

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>如何啟用遠端存取您的部署 Azure 之後

若要啟用遠端存取，您已部署至 Azure 之後，請使用下列步驟︰

1. 登入使用 Azure 帳戶 Azure 管理入口網站
1. 在**雲端服務**的清單中，選取您的部署的雲端服務
1. 在雲端服務網頁上，按一下 [**設定**] 連結
1. 在 [設定] 頁面的底部，按一下 [**遠端**的連結
1. 出現快顯對話方塊時︰
    * 指定您想要啟用遠端存取的角色
    * 按一下以選取 [**啟用遠端桌面**核取方塊
    * 指定使用者名稱和密碼，您想要使用的遠端存取
    * 選取要使用的憑證
1. 按一下**[確定]** 

您會看到一個訊息，指出您的設定變更位於進度，可能需要幾分鐘才能完成。 設定變更完畢後，請遵循本文中的 [**遠端登入**] 區段中的步驟進行。
    
## <a name="how-to-enable-remote-access-in-your-package"></a>如何在套件中啟用遠端存取

1. 蝕的 [專案總管] 窗格] 中以滑鼠右鍵按一下 Azure 專案，然後按一下 [**內容**。

1. 在 [**屬性**] 對話方塊中，展開**Azure**在左窗格中，按一下 [**遠端存取**。

1. 在 [**遠端存取**] 對話方塊中，確定已核取 [**啟用所有角色接受遠端桌面連線，使用這些登入認證**。

1. 指定遠端桌面連線的使用者名稱。

1. 指定，並確認使用者的密碼。 當您進行遠端桌面連線，將會使用這個對話方塊中設定的使用者名稱和密碼值。 （請注意，這是不同的密碼 PFX 密碼）。

1. 指定的使用者帳戶的到期日。

1. 按一下 [**新增**]，建立新的自我簽署的憑證]。 （或者，您可以選擇從工作區或檔案系統的**工作區**或**檔案系統**的按鈕，透過的憑證分別但進行本教學課程中，我們會建立新的憑證）。

    * 在 [**新的憑證**] 對話方塊中，指定並確認您會使用 PFX 檔案的密碼。

    * 接受的**名稱 (CN)**，提供的值，或使用的自訂名稱。

    * 指定要儲存新的憑證，在.cer 表單中，路徑和檔名。 此步驟和下一個步驟，您可以使用 [**憑證**] 資料夾的 Azure 專案，但是您可以自由選擇另一個位置。 為了本教學課程中，我們將使用**c:\mycert\mycert.cer**。 （建立**c:\mycert**資料夾，再繼續，或使用現有的資料夾，如有需要）。

    * 指定要儲存新的憑證及私密金鑰，在.pfx 表單中，路徑和檔名。 為了本教學課程中，我們將使用**c:\mycert\mycert.pfx**。 您的**新 [憑證**] 對話方塊看起來類似下列 （如果您沒有使用**c:\mycert**要更新的資料夾路徑）︰

        ![][ic712275]

    * 按一下**[確定**] 關閉 [**新的憑證**] 對話方塊。

1. 您**遠端存取**的對話方塊看起來如下︰</p>

    ![][ic719495]

1. 按一下**[確定**] 關閉 [**遠端存取**] 對話方塊。
    
重建應用程式，與組建設定以供部署至雲端。

## <a name="to-log-in-remotely"></a>若要從遠端登入

您的角色執行個體後，您可以從遠端登入虛擬機器裝載您的應用程式。

* 如果使用蝕會在 Windows 和您選取的**開始遠端桌面部署**選項在您的部署 Azure 時，將會出現您的部署啟動時的遠端桌面連線登入畫面。 當系統提示您的使用者名稱和密碼時，輸入您為遠端使用者指定的值，都能登入。

* 若要從遠端登入另一個方法是透過<a href="http://go.microsoft.com/fwlink/?LinkID=512959">Azure 管理入口網站</a>︰

    * Azure 管理入口網站的 [**雲端服務**] 檢視中按一下您雲端服務，按一下**執行個體**、 按一下 [特定的執行個體，，然後按一下 [**連線**] 按鈕。 [**連線**] 按鈕會顯示為下列的命令列中︰

        ![][ic659273]

    * 後按一下 [**連線**] 按鈕，系統會提示您開啟 RDP 檔案。 開啟檔案，然後依照提示操作。 （您可能也將此檔案儲存至本機電腦，然後再執行檔案至遠端登入您的虛擬機器按兩下，並不需要先前往 [管理] 入口網站。）

    * 當系統提示您的使用者名稱和密碼時，輸入您為遠端使用者指定的值，都能登入。

> [AZURE.NOTE] 如果您是在非 Windows 作業系統上，您需要使用與您的作業系統相容的遠端桌面用戶端，並遵循步驟來設定用戶端 RDP 下載的檔案，您的設定。

## <a name="see-also"></a>另請參閱

[針對蝕 azure 工具組][]

[建立 Azure 中蝕認識全球應用程式][]

[安裝蝕 Azure 工具組][] 

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[針對蝕 azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[建立 Azure 中蝕認識全球應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝蝕 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png
