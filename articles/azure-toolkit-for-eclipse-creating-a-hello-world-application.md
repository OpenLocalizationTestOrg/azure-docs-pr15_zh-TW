<properties
    pageTitle="建立蝕 Azure 認識全球雲端服務"
    description="瞭解如何建立簡單的什麼應用程式，使用蝕 Azure 工具組。"
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# <a name="create-a-hello-world-cloud-service-for-azure-in-eclipse"></a>建立蝕 Azure 認識全球雲端服務 #

下列步驟說明如何建立並部署至 Azure 基本 JSP 應用程式使用蝕 Azure 工具組。 JSP 範例顯示簡單，但非常類似的步驟可適用於 Java servlet，就而言 Azure 部署。

應用程式的外觀如下︰

![][ic600360]

## <a name="prerequisites"></a>必要條件 ##

* Java 開發人員套件 (JDK)，v 1.7 或更新版本。
* Java 卻開發人員，（IDE 蝕色) Indigo 或更新版本。 這樣就可以從<http://www.eclipse.org/downloads/>下載。
* Java 的網頁伺服器或應用程式的伺服器，例如 Apache Tomcat、 GlassFish、 JBoss 應用程式伺服器、 Jetty 或 IBM® WebSphere® 應用程式伺服器自由核心的通訊群組。
* Azure 訂閱，可以從<http://azure.microsoft.com/pricing/purchase-options/>取得。
* 蝕 Azure 工具組。 如需詳細資訊，請參閱[安裝的蝕 Azure 工具組][]。

## <a name="to-create-a-hello-world-application"></a>若要建立什麼應用程式 ##

首先，我們會先建立 Java 專案。

*  啟動蝕，和在功能表上，按一下 [**檔案**，按一下 [**新增**]，然後按一下**動態 Web 專案**。 (如果您沒有看到**動態 Web 專案**列為可使用的專案後按一下 [**檔案**]**新增**]，然後執行下列動作︰ 按一下 [**檔案]**按一下 [**新增**]、 按一下 [**專案**、 展開**網頁**、**動態 Web 專案**，然後按一下**下一步**。)
*  為了本教學課程的詳細資訊，請為專案**MyHelloWorld**名稱。 (請確定您使用此名稱，在此教學課程中的後續步驟預期應命名 MyHelloWorld 馬檔案)。 畫面會顯示如下︰ ![][ic589576]
* 按一下 [**完成**]。
* 內蝕的專案總管] 檢視中，展開 [ **MyHelloWorld**]。 以滑鼠右鍵按一下**WebContent**，按一下 [**新增**]，再按一下 [ **JSP 檔案**。
* 在 [**新 JSP 檔案**] 對話方塊中，名稱為檔案**index.jsp**。 保留**MyHelloWorld/WebContent**的上層資料夾，如下所示︰  ![][ic659262]
* 在 [**選取 JSP 範本**] 對話方塊中進行本教學課程中，選取 [**新 JSP 檔案 (html)** ，然後按一下 [**完成]**。
* Index.jsp 檔案開啟時蝕中，新增動態顯示文字中**Hello World ！** 在現有的`<body>`項目。 您更新`<body>`內容應該顯示為下列動作︰
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* 儲存 index.jsp。

## <a name="to-deploy-your-application-to-azure-the-quick-and-simple-way"></a>部署至 Azure 應用程式快速又簡單的方法 ##

一旦您能夠準備好測試 Java web 應用程式，您可以使用下列快速鍵，可以嘗試直接在 Azure 雲端上。

1. 在蝕的專案總管] 中，按一下 [ **MyHelloWorld**。
1. [蝕] 工具列上，按一下 [**發佈**] 下拉式按鈕，然後按一下 [**發佈為 Azure 雲端服務**
    ![][publishDropdownButton]
1. 如果您要發佈 Azure 第一次此應用程式，您還沒有建立此應用程式之前 Azure 部署專案 Azure 部署專案會自動建立。 您應該會看到下列提示時，也會列出 JDK 套件，將會自動執行您的應用程式部署的應用程式伺服器。
    ![][ic789598]

    這個捷徑方法可讓您快速又簡單方法可以測試 Azure 中的應用程式，而不需要設定特定的伺服器或 JDK 不同於預設值。 如果您滿意預設值，您可以按一下**[確定**] 以繼續執行下列步驟。
    不過，如果您想要變更 JDK 或應用程式伺服器使用您的應用程式，您可以執行的稍後編輯 Azure 部署專案會自動為您建立的或者您可以立即按一下 [**取消]** ，並閱讀本教學課程中的**關於 Azure 部署專案區段**。
1. 在 [**發佈至 Azure** ] 對話方塊中︰
    1. 如果您有**訂閱**清單中選取 [還沒有訂閱，請遵循下列步驟，匯入您的訂閱資訊︰
        1. 按一下 [**從發佈設定檔案匯入**]。
        1. 在 [**匯入訂閱資訊**] 對話方塊中，按一下 [**下載發佈設定檔案**。 如果您還沒有登入 Azure 帳戶，系統會提示您登入。 然後您會提示您儲存 Azure 發佈的設定檔。 將其儲存到本機電腦。
        1. 仍在 [**匯入訂閱資訊**] 對話方塊中，按一下 [**瀏覽**] 按鈕，選取您在上一個步驟中，儲存在本機的發佈設定檔，然後按一下**開啟**。 看起來類似下列畫面︰ ![][ic644267]
        1. 按一下**[確定]**。
    1. 如需**訂閱**，選取 [訂閱所要使用您的部署]。
    1. **儲存帳戶**，選取您要使用的儲存空間帳戶，或按一下 [**新增**]，建立新的儲存空間帳戶。
    1. 針對**服務名稱**，請選取您要使用雲端服務，或按一下 [**新增**]，建立新的雲端服務。
    1. **目標 OS**，選取您想要為您的部署使用的作業系統的版本。
    1. **目標環境**中，進行本教學課程中，選取 [**執行**]。 （當您準備好要部署至此生產網站時，您會變更此**生產**）。
    1. 可省略︰ 確認已選取**覆寫先前部署**，如果您想要自動覆寫先前部署新部署。 當您啟用此選項時，您將會不體驗 」 409 衝突 」 問題時的同一個位置發佈。
        請注意 [**發佈至 Azure** ] 對話方塊，包含**遠端存取**的某一節。 根據預設，不會啟用遠端存取，且我們並不會啟動它在這個範例。 若要啟用遠端存取，您可以輸入使用者名稱和密碼，才能使用遠端登入。 如需有關遠端存取的詳細資訊，請參閱[Azure 部署蝕中啟用遠端存取][]。
        您**發佈至 Azure**對話方塊看起來如下︰ ![][ic719488]
1. 按一下 [**發佈]**發佈至臨時環境。
    若要執行完整建置出現提示，請按一下 [**是**]。 這可能需要幾分鐘的第一個建立時間。
    **Azure 活動記錄**會顯示在蝕索引標籤的 [檢視] 區段。
    ![][ic719489]
   您可以使用此記錄，以及 [**主控台**] 檢視中，若要查看您的部署的進度。 替代方案是[Azure 管理入口網站][]中，登入並使用**Cloud Services**區段來監控狀態。
1. 您的部署成功部署時， **Azure 活動記錄**會顯示**已發佈**的狀態。 如下圖所示，在下圖中，按一下 [**發佈**您的瀏覽器會開啟您的部署的執行個體。
    ![][ic719490]

這是暫存的環境的部署，因為 DNS 名稱會的表單 http://&lt;*guid*&gt;。 cloudapp.net，並包含 DNS 名稱後面加應用程式的 URL 會。 例如，http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld。 （ **MyHelloWorld**部分是區分大小寫）。如果您按一下 [部署名稱 Azure 平台管理入口網站 （在雲端服務的部分管理入口網站） 中，您也可以查看 DNS 名稱。

雖然逐步解說已部署暫存的環境，生產部署追蹤相同的步驟，除了 [**發佈至 Azure** ] 對話方塊中，選取**生產**，而不是**臨時****目標環境**。 生產部署會根據您的選擇，而不是 GUID 用來執行 DNS 名稱的 URL。

>[AZURE.WARNING] 現在您已經部署至雲端 Azure 應用程式。 然而，進行前，先瞭解的部署的應用程式，即使未執行，會繼續累算訂閱計費時間。 因此，是相當重要 Azure 訂閱刪除不需要的部署。

## <a name="about-azure-deployment-projects"></a>有關 Azure 部署專案 ##

部署至 Azure 的一或多個 Java 應用程式，才需要 Azure 部署專案。 播放的 「 套件 」 應用程式需要將才能 Azure 上發佈換行的角色。

您的應用程式的相關資訊，除了 Azure 部署專案也包含的相關資訊其他主要元件您部署中，最重要的是︰ 執行，web 應用程式的應用程式伺服器容器和 Java 執行階段上執行。 Azure 支援大型 Java 執行階段和 Java 應用程式伺服器，您可以選擇從選取範圍。

使用這裡的範例簡化基於教育目的，雖然 Azure 部署專案也可以包含其他可讓您的應用程式建立幾乎複雜、 縮放、 可用性、 多層雲端服務的重要的設定資訊。 您可以啟用**工作階段相關性 （「 自黏的工作階段 」）**、**快速快取**、**遠端偵錯**、 **SSL 卸載**、**防火牆/連接埠路由**、**遠端存取**和數字的其他功能強大的功能。

如果您已完成此教學課程 （」 以應用程式部署至 Azure 快速又簡單的方法 」） 的前一節，您將會看到新 Azure 部署專案會自動產生的 [專案總管]，並名為 「**MyHelloWorld_onAzure**」。

您可能也開始本教學課程中，第一次自行建立空白的 Azure 部署專案，以及新增您的應用程式，以便。 較長程序，但可讓您進一步控制開始的初始設定。

若要從頭開始建立新的 Azure 部署專案，按一下 [**新的 Azure 部署專案**按鈕![][ic710876]。

無論您是使用現有的 Azure 部署專案，或從從頭開始建立，您就可以變更其設定的設定和元件，例如 JDK 或應用程式伺服器平均輕鬆地在任何時間。

若要變更 JDK，或應用程式伺服器或應用程式] 清單中現有的 Azure 部署專案︰

1. 展開專案節點 (例如**MyHelloWorld_onAzure**) 在 [專案總管
2. 以滑鼠右鍵按一下**WorkerRole1**
3. 展開內容功能表中的 [ **Azure** ] 子功能表
4. 按一下 [**伺服器設定**

不論是您開始著手伺服器設定的步驟來編輯現有的 Azure 部署專案，如下圖所示的上方，或建立新中從頭開始，您會看到相同類型的對話方塊，讓您可以設定您的 JDK、 伺服器和應用程式的元件。 若要深入瞭解如何變更的設定，這些對話方塊中，例如變更 JDK，應用程式伺服器及新增或移除應用程式在部署中，請參閱[伺服器設定內容][]。

## <a name="windows-only-to-deploy-your-application-to-the-compute-emulator"></a>僅在 Windows︰ 部署至計算模擬器應用程式 ##

>[AZURE.NOTE] 在 Windows 上，才可以使用 Azure 模擬器。 如果您使用 Windows 以外的作業系統，請略過此區段。

如果您已建立新的 Azure 部署專案之前，亦即隱含地所述的步驟進行發佈您的應用程式 Azure、 JDK 與應用程式的伺服器設定本機模擬雲端，但不是。 在本機模擬器中進行測試準備您的專案，請遵循下列步驟︰

1. 在蝕的專案總管] 中，按一下 [ **MyHelloWorld_onAzure**。
1. 以滑鼠右鍵按一下**WorkerRole1**。
1. 展開**Azure** ] 子功能表中的內容功能表。
1. 按一下 [**伺服器設定**]。
1. 在 [ **JDK** ] 索引標籤上核取工具組是否已經預先設定的預設為您的本機 JDK。 如果不是，或如果您想要變更假設為預設值，請確定已核取 [**使用 JDK 從本機測試此檔案路徑**] 核取方塊，然後指定您想要使用的 JDK 安裝位置。 如果您想要變更，按一下 [**瀏覽**] 按鈕，然後使用瀏覽] 控制項，選取目錄的位置使用 JDK。
1. 按一下 [**伺服器**] 索引標籤。
1. 在**本機伺服器路徑**] 文字方塊中的 [底部的對話方塊中，輸入本機安裝的伺服器符合類型和主要版本號碼選取頂端的對話方塊] 底下的**部署伺服器此類型的**核取方塊的伺服器的路徑。 如果您想要使用不同的類型或應用程式伺服器的主要版本，請先變更該核取方塊下的選取範圍。
1. 按一下**[確定]**。
1. 在 [蝕] 工具列上，按一下 [ **Azure 模擬器中執行**按鈕， ![][ic710879]。 如果未啟用**Azure 模擬器中執行**] 按鈕，確定已選取 [ **MyHelloWorld_onAzure**蝕的 [專案總管] 中，並確保蝕的專案總管] 中，具有焦點目前的視窗。 第一次將啟動完整建置您的專案，並啟動 Java web 應用程式中的計算模擬器。 （請注意，根據您的電腦效能特性，第一個建置可能需要幾秒的時間才能幾分鐘，但後續建置會更快獲得。）首先建立完成後，您會提示您的 Windows 使用者帳戶控制 (UAC) 若要允許這個命令，若要變更您的電腦。 按一下**[是]**。

>[AZURE.IMPORTANT] 如果您沒有看到 UAC 提示，請檢查 Windows 工作列 UAC 圖示，然後再按一下該第一次。 有時 UAC 提示不會顯示為最上層的視窗中，但會顯示只為工作列的圖示。

1. 檢查計算模擬器使用者介面，來判斷是否有任何問題，您的專案的輸出。 根據您的部署中的內容，可能需要幾分鐘內計算模擬器完全啟動應用程式。
1. 開始您的瀏覽器，並使用 URL`http://localhost:8080/MyHelloWorld`為地址 ( `MyHelloWorld` URL 部份是區分大小寫)。 您應該會看到 MyHelloWorld 應用程式 （index.jsp 的輸出），類似下列圖像︰ ![][ic589579]

當您已準備好停止應用程式執行的計算模擬器中，在蝕工具列中，按一下 [**重設 Azure 模擬器**] 按鈕， ![][ic710880]。

## <a name="to-delete-your-deployment"></a>若要刪除您的部署 ##

若要刪除您的部署內的蝕 Azure 工具組，確定已選取 [ **MyHelloWorld_onAzure**蝕的專案總管] 中，請確認蝕專案總管] 中有目前的視窗著重，，然後按一下 [**解除發佈**] 按鈕， ![][ic710883]，蝕工具列中。 （您可能會以滑鼠右鍵按一下**MyHelloWorld_onAzure**蝕的專案總管] 中，按一下**Azure** ，然後按一下 [**從 Azure 雲端解除部署**執行相同作業）。這會顯示 [**解除發佈 Azure 專案**] 對話方塊。

![][ic719491]

選取包含您的部署的訂閱和雲端服務，選取您想要刪除的部署，再按一下 [**解除發佈**。

(若要刪除的部署使用此工具組的替代方法是使用 Azure 管理入口網站的**Cloud Services**區段︰ 瀏覽至您的部署，加以選取，再按一下 [**刪除**] 按鈕。 這會停止]，然後再刪除，部署。 如果您只想要停止部署，並將它刪除，按一下 [**停止**] 按鈕，而不是 [**刪除**] 按鈕，但如上所述，如果您不會刪除部署，計費費用會繼續即使已停止，為您的部署累算）。

## <a name="see-also"></a>另請參閱 ##

[針對蝕 azure 工具組][]

[安裝蝕 Azure 工具組][] 

[Azure 工具組蝕的新增功能][]

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure 管理入口網站]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[針對蝕 azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure 部署蝕中啟用遠端存取]: http://go.microsoft.com/fwlink/?LinkID=699538
[安裝蝕 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546
[伺服器設定屬性]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Azure 工具組蝕的新增功能]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png
