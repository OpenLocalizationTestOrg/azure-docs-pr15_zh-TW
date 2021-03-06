<properties
    pageTitle="在 [蝕偵錯 Azure 應用程式"
    description="瞭解使用蝕 Azure 工具組偵錯 Azure 應用程式。"
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# <a name="debugging-azure-applications-in-eclipse"></a>在 [蝕偵錯 Azure 應用程式 #

使用蝕 Azure 工具組，您可以偵錯應用程式是否執行或計算模擬器中 Azure，如果您使用的 Windows 作業系統。 下圖顯示的對話方塊用來啟用遠端偵錯的**偵錯**屬性︰

![][ic719504]

本教學課程，假設您已經有已成功建立，並有 Azure 熟悉如何計算模擬器並部署的應用程式。

我們將使用的起點的應用程式無法[使用 Azure 服務執行階段文件庫中 JSP][]教學課程此主題。 前，請建立該應用程式，如果您有不這麼做。

## <a name="to-debug-your-application-while-running-in-azure"></a>偵錯 Azure 中執行時的應用程式 ##

>[AZURE.WARNING] 此工具組目前支援遠端 Java 偵錯時，主要供部署 Azure 運算在模擬器中執行。 不安全的偵錯的連線，因為您不應該啟用遠端偵錯生產部署中。 如果您需要偵錯執行 Azure 雲端中的應用程式，請使用暫存的部署，但發現未經授權下的存取您的偵錯工作階段是可能如果其他人知道您的雲端部署的 IP 位址，即使是暫存的部署。

1. 建立您的專案，以在模擬器中進行測試︰ 中蝕的專案總管] 中，以滑鼠右鍵按一下**MyAzureProject**按一下 [**內容**、 **Azure**，然後按一下設**為建置****部署至雲端**。
1. 重建專案︰ 從蝕] 功能表中，按一下 [**專案**]，然後按一下 [**建立所有**。
1. 部署與 Azure 中的 [*執行*應用程式。
    >[AZURE.IMPORTANT] 如上所述，我們建議您在大多數情況下，計算模擬器中偵錯，然後所需要的其他偵錯時，才執行環境中偵錯。 建議您偵錯生產環境中。
1. Azure 中準備好您的部署後，請從[Azure 管理入口網站][]取得部署的 DNS 名稱。 暫存的部署具有 DNS 名稱在表單中的 [http://*&lt;guid&gt;*。 cloudapp.net，其中*&lt;guid&gt;*是由 Azure 指派 GUID 值。
1. 蝕的 [專案總管] 中以滑鼠右鍵按一下**WorkerRole1** **Azure**，然後按一下然後按一下 [**偵錯**。
1. 在 [**偵錯 WorkerRole1 屬性**] 對話方塊中︰
    1. 檢查**啟用此角色的遠端偵錯。**
    1. **若要使用的輸入結束點**，請使用 [**偵錯 （公用︰ 8090、 私人︰ 8090）**。
    1. 請確定未選取**將 JVM 開始在已擱置模式中，正在等待偵錯工具連線**。
        >[AZURE.IMPORTANT] **在已擱置模式中，正在等待偵錯工具連線的開始 JVM**選項為了的進階偵錯時，只計算模擬器中的案例 （不針對雲端部署）。 如果使用**開始 JVM，在已擱置模式中，正在等待偵錯工具連線**選項，它就會暫停伺服器的啟動程序，直到蝕偵錯工具已連線至其 JVM。 雖然您可以使用運算模擬器偵錯工作階段的使用這個選項，請勿使用它雲端部署中的偵錯工作階段。 伺服器的初始化發生在 Azure 啟動任務中，而 Azure 雲端無法讓公開端點使用直到啟動工作完成。 因此，啟動程序將順利完成，如果在雲端部署中，啟用此選項，因為它不能接收外部蝕用戶端連線。
1。 按一下 [**建立偵錯組態**]。
1. 在 [ **Azure 偵錯設定**] 對話方塊中︰
    1. **Java 偵錯的專案**中，選取 [ **MyHelloWorld**專案。
    1. **設定的偵錯**，請**Azure 雲端 （執行）**。
    1. 請確定未**Azure 計算模擬器**。
    1. **Host （主機）**，輸入您分段部署中，但是沒有上述**http://**DNS 名稱。 例如 （使用 GUID 取代 GUID 此處所示）︰ **4e616d65 6f6e-6 維 65 6973 526f62657274.cloudapp.net**
1. 按一下**[確定**] 關閉 [ **Azure 偵錯設定**] 對話方塊。
1. 按一下**[確定**] 關閉 [ **WorkerRole1 偵錯屬性**] 對話方塊。
1. 如果您沒有安裝中斷點 index.jsp 中已經設定，請將它設定︰
    1. 蝕的 [專案總管] 中展開**MyHelloWorld**、 展開**WebContent**，然後按兩下**index.jsp**。
    1. 在 index.jsp，以滑鼠右鍵按一下藍色列左邊的 Java 程式碼，並按一下**切換中斷點**，如下所示︰ ![][ic551537]
1. 內蝕的功能表中，請按一下 [**執行**]，，然後按一下 [**偵錯設定**。
1. 在 [**偵錯設定**] 對話方塊左窗格中展開**遠端 Java 應用程式**，選取**Azure 雲端 (WorkerRole1)**，然後按一下**偵錯**。
1. 在瀏覽器中執行分段應用程式， **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**，取代從您的 DNS 名稱 GUID * &lt;guid&gt;*。 如果提示您**確認透視圖參數**] 對話方塊中，按一下 [**是**]。 設定中斷點的程式碼的行現在應該執行您偵錯工作階段。

>[AZURE.NOTE] 如果您嘗試啟動遠端偵錯時，具有多個執行的角色執行個體，您無法目前控制哪一個執行個體的部署連線偵錯工具將一開始連線，如 Azure 負載平衡器將隨機選取執行個體。 您已連線的執行個體，但您仍繼續偵錯時，相同的執行個體。 請注意此外，如果有多個 4 分鐘 （例如，您要停止太久中斷點時） 的期間，Azure 可能會關閉連線。

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>偵錯時，在多個執行個體部署中的特定角色執行個體 ##

執行您的部署時，在雲端，您會很可能會執行，在多個計算，或角色，請執行個體。 這可讓您利用 Azure 99.95%可用性保證，並以擴展您的應用程式。

在這種情況下，您可能需要從遠端偵錯 Java 應用程式中的特定角色執行個體。 不過，如果您啟用只一般輸入的端點偵錯，Azure 負載平衡器會使其幾乎不可能您連線至特定角色執行個體的偵錯工具。 請改為它會將您連線至隨機挑選一個角色執行個體。

這是案例的位置善用執行個體輸入結束點會更方便您偵錯特定角色執行個體的類型。

例如，假設您打算部署的最多 5 角色執行個體。 使用角色的 [屬性] 對話方塊中的**結束點**] 屬性頁面，建立執行個體輸入的端點，並將其指派公用連接埠、 範圍，而不是單一的連接埠號碼。 例如，在**公用連接埠**輸入方塊中，指定**81 85**。

部署與此執行個體端點的應用程式之後，Azure 會指派的唯一的連接埠號碼此範圍至每個角色執行個體。 然後，若要找出哪一個執行個體您使用的是指定的連接埠號碼，您可以使用*InstanceEndpointName***_PUBLICPORT**環境變數 （其中*InstanceEndpointName*是當您建立的執行個體結束點時，您指派的名稱） 自動設定 （例如，藉由傳回其值的網頁，頁尾中，讓您可以閱讀，當您瀏覽至該） 部署中的工具組。

一旦您知道哪些公用的連接埠號碼指派給該執行個體，參照中蝕，偵錯設定中，將其附加於方法到主機名稱，您的服務。 這樣會啟用蝕偵錯工具連線到該特定的執行個體，並不是任何其他執行個體。

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>僅在 Windows︰ 偵錯時計算在模擬器中執行的應用程式 ##

>[AZURE.NOTE] 在 Windows 上，才可以使用 Azure 模擬器。 如果您使用 Windows 以外的作業系統，請略過此區段。 

1. 建立您的專案，以在模擬器中進行測試︰ 中蝕的專案總管] 中，以滑鼠右鍵按一下**MyAzureProject**按一下 [**內容**、 **Azure**，然後按一下設**為建置****模擬器中的測試**。
1. 重建專案︰ 從蝕] 功能表中，按一下 [**專案**]，然後按一下 [**建立所有**。
1. 蝕的 [專案總管] 中以滑鼠右鍵按一下**WorkerRole1** **Azure**，然後按一下然後按一下 [**偵錯**。
1. 在 [**偵錯 WorkerRole1 屬性**] 對話方塊中︰
    1. 檢查**啟用此角色的遠端偵錯。**
    1. **若要使用的輸入結束點**，請使用 [自動產生的工具組，**偵錯 （公用︰ 8090、 私人︰ 8090）**的方式列出的預設端點。
    1. 請確定未選取**將 JVM 開始在已擱置模式中，正在等待偵錯工具連線**選項。
        >[AZURE.IMPORTANT] **在已擱置模式中，正在等待偵錯工具連線的開始 JVM**選項為了的進階偵錯時，只計算模擬器中的案例 （不針對雲端部署）。 如果使用**開始 JVM，在已擱置模式中，正在等待偵錯工具連線**選項，它就會暫停伺服器的啟動程序，直到蝕偵錯工具已連線至其 JVM。 雖然您可以使用運算模擬器偵錯工作階段的使用這個選項，請勿使用它雲端部署中的偵錯工作階段。 伺服器的初始化發生在 Azure 啟動任務中，而 Azure 雲端無法讓公開端點使用直到啟動工作完成。 因此，啟動程序將順利完成，如果在雲端部署中，啟用此選項，因為它不能接收外部蝕用戶端連線。
1。 按一下 [**建立偵錯組態**]。
1. 在 [ **Azure 偵錯設定**] 對話方塊中︰
    1. **Java 偵錯的專案**中，選取 [ **MyHelloWorld**專案。
    1. **設定的偵錯**，請**Azure 計算模擬器**。
1. 按一下**[確定**] 關閉 [ **Azure 偵錯設定**] 對話方塊。
1. 按一下**[確定**] 關閉 [ **WorkerRole1 偵錯屬性**] 對話方塊。
1. 設定 index.jsp 中斷點︰
    1. 蝕的 [專案總管] 中展開**MyHelloWorld**、 展開**WebContent**，然後按兩下**index.jsp**。
    1. 在 index.jsp，以滑鼠右鍵按一下藍色列左邊的 Java 程式碼，並按一下**切換中斷點**，如下所示︰ ![][ic551537]

       如果您看到藍色列中的中斷點圖示左邊的 Java 程式碼，設定中斷點。
1. 在計算模擬器中啟動應用程式，即可 Azure 工具列上的 [**執行 Azure 模擬器中**] 按鈕。
1. 內蝕的功能表中，請按一下 [**執行**]，，然後按一下 [**偵錯設定**。
1. 在 [**偵錯設定**] 對話方塊左窗格中展開**遠端 Java 應用程式**、 選取**Azure 模擬器 (WorkerRole1)**，然後按一下**偵錯**。
1. 計算後模擬器會指出您的應用程式正在執行，在您的瀏覽器中，然後再執行**http://localhost:8080/MyHelloWorld**內。
    如果提示您**確認透視圖參數**] 對話方塊中，按一下 [**是**]。
    設定中斷點的程式碼的行現在應該執行您偵錯工作階段。

以下示範如何計算模擬器; 中偵錯[下一步] 區段會顯示如何偵錯部署至 Azure 的應用程式。

## <a name="debugging-notes"></a>偵錯備忘稿 ##

* 之後偵錯時，您可以透過按一下蝕的功能表中，按一下 [**視窗**]**開啟的觀點來看**，並選取您想要使用的觀點來看切換**java**從**偵錯**觀點。
* 若要啟用遠端偵錯 GlassFish 中，不使用遠端偵錯設定功能的 Azure 工具組蝕。 請改為手動設定 GlassFish。 因為 GlassFish 會將預先定義的環境變數中的 Java 選項的方式，此工具組遠端偵錯設定功能不會無法使用適當 GlassFish。 如果啟用該工具組遠端偵錯設定功能時，它可能會防止 GlassFish 開始。

## <a name="see-also"></a>另請參閱 ##

[針對蝕 azure 工具組][]

[建立 Azure 中蝕認識全球應用程式][]

[安裝蝕 Azure 工具組][] 

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure 管理入口網站]: http://go.microsoft.com/fwlink/?LinkID=512959
[針對蝕 azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[建立 Azure 中蝕認識全球應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝蝕 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546
[使用中 JSP Azure 服務執行階段文件庫]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png
