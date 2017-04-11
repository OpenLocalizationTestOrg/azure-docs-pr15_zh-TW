<properties
    pageTitle="如何使用 Access 控制項 (Java) |Microsoft Azure"
    description="瞭解如何開發和 Azure 中 Java 搭配使用 Access 控制項。"
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>如何驗證使用蝕 Azure 存取控制服務的網路使用者

本指南會顯示如何使用蝕 Azure 存取控制服務 (ACS) 中 Azure 工具組。 如需有關 ACS 的詳細資訊，請參閱[下一步](#next_steps)] 區段。

> [AZURE.NOTE]
> Azure 存取服務的控制項篩選器是社群技術預覽。 測試版軟體，為它不正式支援由 Microsoft。

## <a name="what-is-acs"></a>ACS 是什麼？

大部分的開發人員沒有身分識別專家，而且通常不想要的應用程式及服務花時間開發的驗證及授權機制。 ACS 是 Azure 的服務，讓您輕鬆驗證需要省去因子變異數複雜驗證邏輯至程式碼存取您的 web 應用程式和服務的使用者。

下列功能可在 ACS:

-   「 整合 Windows 身分識別 Foundation (WIF)。
-   支援的常用的 web 身分識別提供者 (Ip) 包括 Windows Live ID、 Google、 yahoo ！ 或 facebook 的連絡人。
-   支援 Active Directory Federation Services (AD FS) 2.0。
-   開放式資料通訊協定 (OData)-以管理服務，提供 ACS 設定以程式設計方式存取權。
-   管理入口網站，可讓系統管理存取權的 ACS 設定。

如需有關 ACS 的詳細資訊，請參閱[Access 控制項服務 2.0][]。

## <a name="concepts"></a>概念

Azure ACS 內建的宣告式的身分識別-一致的方式建立的應用程式執行內部部署或雲端中的驗證機制主體上。 宣告式身分識別提供常見的方式，以取得其他組織中，並在網際網路上其組織內的使用者身分識別資訊所需的 [應用程式和服務。

若要完成本指南中的工作，您應該瞭解以下的概念︰

**用戶端**-本文相關的內容中這是在瀏覽器嘗試存取您的 web 應用程式。

**Relying 廠商 （資源點數） 應用程式**的資源點數應用程式是網站或 outsources 驗證到一個外部授權的服務。 在 [身分識別行話我們說出資源點數信任的授權。 本文說明如何設定信任 ACS 應用程式。

**權杖**-權杖是通常會發出時的使用者驗證成功的安全性資料集合。 包含一組*宣告*，已驗證的使用者的屬性。 宣告可代表使用者的名稱、 使用者角色的識別碼屬於，使用者的年齡，依此類推。 權杖通常數位簽署，這表示其永遠來源回到發行者，並無法竄改其內容。 在使用者存取資源點數應用程式所呈現的有效的權杖資源點數應用程式信任授權單位核發。

**身分識別提供者 (IP)** IP 已驗證使用者身分識別和發出的授權。 雖然稱為安全性 Token 服務 (STS) 的特殊服務實作發行權杖的實際工時。 一般範例 IPs 包含 Windows Live ID、 Facebook、 （如 Active Directory) 的商務使用者存放庫等等。
當 ACS 設定信任 IP 時，系統會接受並驗證的權杖發出的 IP。 ACS 可以信任多個 Ip 一次，也就是說，當您的應用程式信任 ACS 時，您可以立即提供您的應用程式所有已驗證的使用者所有 Ip ACS 信任代表您。

**同盟提供者 (FP)** -IPs 直接知道的使用者，驗證他們使用其認證，並問題有關他們知道這些宣告。 同盟提供者 (FP) 是不同種類的授權︰ 而不是直接驗證使用者，將其做為媒介和經紀人驗證一個資源點數，另一或多個 Ip。 每秒畫面格和 IPs 發出安全性權杖，因此這兩個使用安全性 Token 服務 (STS)。 ACS 是一個 FP。

**ACS 規則引擎**用來轉換連入權杖從信任 IPs 權杖是要使用的資源點數邏輯是編寫到在表單中的簡單的宣告轉換規則。 ACS 功能負責套用您指定給您的資源點數轉換邏輯的規則引擎。

**ACS 命名空間**命名空間是最上層的磁碟分割的 ACS 您用來組織您的設定。 命名空間保留的 Ip 您信任，您想要做的資源點數應用程式的清單，您預期的規則的規則引擎處理連入權杖，依此類推。 命名空間公開各種不同的端點，將會用於由的應用程式開發人員取得 ACS 若要執行的功能。

下圖顯示 ACS 驗證的 web 應用程式的運作方式︰

![ACS 流程圖][acs_flow]

1.  用戶端 （在此例瀏覽器） 會從資源點數要求網頁。
2.  由於要求尚未驗證，資源點數使用者重新導向至信任的授權，該網站是 ACS。 ACS 提供使用者針對此資源點數所指定的 Ip 的選項。 使用者選取適當的 IP。
3.  用戶端 IP 驗證] 頁面上，瀏覽，並會提示使用者登入。
4.  用戶端 （例如，輸入認證的身分識別） 驗證後，IP 發出安全性 token。
5.  後發出安全性 token，IP 將用戶端重新導向至 ACS 和用戶端傳送 IP 發給 ACS 的安全性 token。
6.  ACS 驗證安全性 token 發出 IP，輸入身分識別宣告在此 token 加到 ACS 規則引擎、 計算輸出的身分識別宣告，和新的安全性憑證包含這些輸出宣告中發生的問題。
7.  ACS 重新導向至資源點數用戶端。 用戶端傳送新的安全性憑證 ACS 發給資源點數。 資源點數驗證 ACS 發出安全性 token 的簽章、 驗證此權杖，宣告，並傳回原始要求的頁面。

## <a name="prerequisites"></a>必要條件

若要完成本指南中的工作，您將需要下列項目︰

- Java 開發人員套件 (JDK)，v 1.6 或更新版本。
- Java 卻開發人員，（IDE 蝕色) Indigo 或更新版本。 這樣就可以從<http://www.eclipse.org/downloads/>下載。 
- Java 的網頁伺服器或應用程式的伺服器，例如 Apache Tomcat、 GlassFish、 JBoss 應用程式伺服器或 Jetty 的通訊群組。
- Azure 訂閱，可以從<http://www.microsoft.com/windowsazure/offers/>取得。
- Azure 工具組的蝕，2014 年 4 月版本或更新版本。 如需詳細資訊，請參閱[安裝的蝕 Azure 工具組](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx)。
- 若要使用您的應用程式 X.509 憑證。 您必須在公用憑證 (.cer) 和個人資訊交換這個憑證 (。PFX) 格式。 （在本教學課程稍後會說明建立這個憑證的選項。）
- Azure 熟悉計算模擬器] 和 [討論在[建立 Azure 中蝕 Hello 全球應用程式](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)的部署技術。

## <a name="create-an-acs-namespace"></a>建立 ACS 命名空間

若要開始使用 Access 控制項服務 (ACS) Azure 中，您必須建立 ACS 命名空間。 命名空間提供處理 ACS 資源應用程式中的唯一範圍。

1. 登入[Azure 管理入口網站][]。
2. 按一下 [ **Active Directory**]。 
3. 若要建立新的存取控制命名空間，按一下 [**新增****應用程式服務**，按一下 [**存取控制**，然後按一下然後按一下 [**快速建立**。 
4. 輸入命名空間的名稱。 Azure 驗證名稱是唯一的。
5. 選取中使用命名空間的區域。 為獲得最佳效能，使用您部署您的應用程式的區域。
6. 如果您有多個訂閱，請選取您想要使用 ACS 命名空間的訂閱。
7. 按一下 [**建立**]。

Azure 會建立並啟動命名空間。 請等待在繼續之前，新的命名空間的狀態是**作用中**。 

## <a name="add-identity-providers"></a>新增身分識別提供者

在此工作中，您可以新增 IPs 使用與資源點數應用程式進行驗證。 示範的目的，這項工作會顯示如何新增 Windows Live ip，但您可以使用任何 Ip ACS 管理入口網站中所列。


1.  在[Azure 管理入口網站][]中，按一下 [ **Active Directory**，選取存取控制命名空間，然後按一下 [**管理**。 隨後便會開啟 [ACS 管理入口網站。
2.  在左側的功能窗格的 [ACS 管理入口網站，按一下 [**身分識別提供者**。
3.  Windows Live ID 根據預設，會啟用，而且您無法刪除。 為了本教學課程中，會使用只 Windows Live ID。 這個畫面，不過，是，您可以新增其他 IPs 中，按一下 [**新增**] 按鈕。

Windows Live ID 現在已啟用您 ACS 命名空間 ip。 接下來，您可以指定 Java web 應用程式 （於稍後建立） 為資源點數。

## <a name="add-a-relying-party-application"></a>新增依賴廠商應用程式

在此工作中，您可以設定 ACS 識別為正確的資源點數應用程式的 Java web 應用程式。

1.  在 [ACS 管理入口網站中，按一下 [ **Relying 廠商應用程式**]。
2.  在 [**依賴廠商應用程式**] 頁面上，按一下 [**新增**。
3.  在 [**新增依賴廠商應用程式**] 頁面中，執行下列動作︰
    1.  在 [**名稱**] 中，輸入資源點數的名稱。 為了本教學課程中，輸入**Azure Web 應用程式**。
    2.  在**模式**中，選取 [**輸入設定手動**。
    3.  在**領域**，輸入要套用 ACS 發出安全性 token URI。 此工作中，輸入**http://localhost:8080 /**。
        ![用於計算模擬器依賴廠商領域][relying_party_realm_emulator]
    4.  在**傳回的 URL 中，**輸入的 URL ACS 會傳回的安全性 token。 此工作中，輸入**http://localhost:8080/MyACSHelloWorld/index.jsp**
        ![Relying 廠商用於計算模擬器中傳回的 URL][relying_party_return_url_emulator]
    5.  接受] 欄位的其餘部分中的預設值。

4.  按一下 [**儲存**]。

您現在已成功設定 Java web 應用程式 Azure 計算模擬器中執行時 (在 http://localhost:8080 /)，在您 ACS 命名空間資源點數。 接下來，建立 ACS 用來處理的資源點數宣告的規則。

## <a name="create-rules"></a>建立規則

在此工作中，您可以定義驅動宣告如何從 IPs 傳送到您的資源點數的規則。 本指南中，我們將只會設定 ACS 直接在輸出權杖中，複製輸入的宣告類型和值，而不篩選或修改。

1.  在管理入口網站 ACS 主頁面上，按一下 [**規則群組**]。
2.  在 [**規則群組**] 頁面中，按一下 [ **Azure Web 應用程式的 [預設規則群組**]。
3.  在 [**編輯規則群組**] 頁面上，按一下 [**產生**]。
4.  在**產生的規則︰ 預設 Azure Web 應用程式的規則群組**頁面確定已核取 [Windows Live ID，再按一下 [**產生]**。    
5.  在 [**編輯規則群組**] 頁面上按一下 [**儲存**]。

## <a name="upload-a-certificate-to-your-acs-namespace"></a>上傳至您的 ACS 命名空間的憑證

在這個工作，您上傳。將用來登入您 ACS 命名空間所建立的權杖要求的 PFX 憑證。

1.  在管理入口網站 ACS 主頁面上，按一下 [**憑證與索引鍵**。
2.  在**憑證和金鑰**頁面上，按一下 [**新增**上面**權杖簽署**。
3.  在 [**新增權杖簽署憑證或索引鍵**的頁面︰
    1. **用來**在區段中，按一下**依賴廠商應用程式**，選取 [ **Azure Web 應用程式**（由您先前設定的信賴廠商應用程式名稱為）。
    2. 在 [**類型**] 區段中，選取 [ **X.509 憑證**]。
    3. 在 [**憑證**] 區段中，按一下 [瀏覽] 按鈕，然後瀏覽至您想要使用的 X.509 憑證檔案。 這將成為。PFX 檔案。 選取檔案，按一下 [**開啟**]，然後在 [**密碼**] 方塊中輸入憑證密碼。 請注意供測試之用，您可以使用 [自動-signed-憑證。 若要建立自我簽署的憑證，請使用 [**新增**] 按鈕 （稍後所述），[ **ACS 篩選文件庫**] 對話方塊中或使用從[專案網站][]的 Azure 入門套件**encutil.exe**公用程式 java。
    4. 請確定已**進行主要**核取。 您**新增權杖簽署憑證或索引鍵**的頁面看起來如下。
        ![新增權杖簽署憑證][add_token_signing_cert]
    5. 按一下 [儲存您的設定，並關閉 [**新增權杖簽署憑證或鍵**] 頁面的 [**儲存**]。

接下來，請檢閱整合應用程式] 頁面中的資訊，然後複製您需要設定 Java web 應用程式使用 ACS URI。

## <a name="review-the-application-integration-page"></a>檢閱整合應用程式] 頁面

您可以找到所有資訊和程式碼設定 Java web 應用程式 （資源點數應用程式） 若要使用 ACS ACS 管理入口網站的 [整合應用程式] 頁面上所需。 設定同盟驗證的 Java web 應用程式時，您會需要此資訊。

1.  在 [ACS 管理入口網站中，按一下 [**整合應用程式**]。  
2.  在**整合應用程式**] 頁面上，按一下 [**登入頁面**]。
3.  在**登入頁面整合**頁面上，按一下 [ **Azure Web 應用程式**]。

在**登入頁面整合︰ Azure Web App**頁面中, 所列的 URL**選項 1: ACS 裝載登入頁面連結**將 Java web 應用程式中使用。 當您新增的 Azure 存取控制服務篩選文件庫 Java 應用程式時，您會需要此值。

## <a name="create-a-java-web-application"></a>建立 Java web 應用程式
1. 內蝕，在功能表按一下 [**檔案]**，按一下 [**新增**]，然後按一下**動態 Web 專案**。 (如果您沒有看到**動態 Web 專案**列為可使用的專案後按一下 [**檔案**]**新增**]，然後執行下列動作︰ 按一下 [**檔案]**按一下 [**新增**]，按一下 [**專案**、 展開**網頁**、**動態 Web 專案**，，然後按一下**下一步**。)為了本教學課程的詳細資訊，請為專案**MyACSHelloWorld**名稱。 (請確定您使用此名稱，在此教學課程中的後續步驟預期應命名 MyACSHelloWorld 馬檔案)。 畫面會顯示如下︰

    ![建立 ACS exampple 什麼專案][create_acs_hello_world]

    按一下 [**完成**]。
2. 內蝕的專案總管] 檢視中，展開 [ **MyACSHelloWorld**]。 以滑鼠右鍵按一下**WebContent**，按一下 [**新增**]，再按一下 [ **JSP 檔案**。
3. 在 [**新 JSP 檔案**] 對話方塊中，名稱為檔案**index.jsp**。 保留 MyACSHelloWorld/WebContent 的上層資料夾，如下所示︰

    ![新增 JSP 檔案 ACS 範例][add_jsp_file_acs]

    按一下 [**下一步**]。

4. 在 [**選取 JSP 範本**] 對話方塊中選取 [**新 JSP 檔案 (html)** ，然後按一下 [**完成]**。
5. 在顯示的文字中新增的 index.jsp 檔案開啟時在蝕， **Hello ACS World ！** 在現有的`<body>`項目。 您更新`<body>`內容應該顯示為下列動作︰

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    儲存 index.jsp。
  
## <a name="add-the-acs-filter-library-to-your-application"></a>將 ACS 篩選文件庫新增至您的應用程式

1. 蝕的 [專案總管] 中以滑鼠右鍵按一下**MyACSHelloWorld**，按一下 [**建立路徑**]，然後按一下**設定建立路徑**。
2. 在 [ **Java 建立路徑**] 對話方塊中，按一下 [**文件庫**] 索引標籤。
3. 按一下 [**新增文件庫**。
4. 按一下 [ **Azure 存取控制服務篩選 （依 MS 開啟技術）** ]，然後按一下 [**下一步**。 **Azure 存取控制服務篩選器**] 對話方塊隨即顯示。  （[**位置**] 欄位可能會有不同的路徑，取決於您安裝蝕，然後可能有所不同，軟體更新的版本號碼）。

    ![新增 ACS 篩選文件庫][add_acs_filter_lib]

5. 使用瀏覽器會開啟至 [**登入頁面整合**] 頁面的 [管理] 入口網站中，複製列在 URL**選項 1: ACS 裝載登入頁面連結**欄位，並貼到 [蝕] 對話方塊的 [ **ACS 驗證結束點**] 欄位。
6. 使用瀏覽器會開啟至管理入口網站的 [**編輯依賴廠商應用程式**] 頁面，複製 [**領域**] 欄位中所列的 URL，並貼到蝕] 對話方塊的 [**依賴廠商領域**] 欄位。
7. 中 [**安全性**] 區段的 [蝕] 對話方塊中，如果您想要使用現有的憑證，按一下 [**瀏覽**、 瀏覽至您要使用，請選取它，然後按一下**開啟**的憑證。 或者，如果您想要建立新的憑證，請按一下 [**新增**]，即可顯示 [**新的憑證**] 對話方塊，然後指定密碼、.cer 檔案的名稱和新的憑證的.pfx 檔的名稱。
8. 核取**內嵌馬檔案中的憑證**。 以這種方式內嵌憑證其包含在您的部署中不會要求您手動將其新增為元件。 （如果是您必須從馬檔案外部儲存您的憑證，您可以將憑證新增為角色元件，取消核取 [**內嵌馬檔案中的憑證**。）
9. [選擇性]核取 [保留**需要 HTTPS 連線**。 如果您設定此選項時，您會需要存取使用 HTTPS 通訊協定的應用程式。 如果您不希望 HTTPS 連線，請取消核取此選項。
10. 讓計算模擬器部署，看起來類似下列**Azure ACS 篩選**設定。

    ![部署至計算模擬器 azure ACS 篩選設定][add_acs_filter_lib_emulator]

11. 按一下 [**完成**]。
12. 按一下 [ **]**時出現的對話方塊中指出 web.xml 檔案將會建立。
13. 按一下**[確定**] 關閉 [ **Java 建立路徑**] 對話方塊。

## <a name="deploy-to-the-compute-emulator"></a>若要計算模擬器部署

1. 蝕的 [專案總管] 中以滑鼠右鍵按一下**MyACSHelloWorld** **Azure**，然後按一下然後按一下 [**封裝成 Azure**。
2. [**專案名稱**] 中，輸入**MyAzureACSProject** ，按一下 [**下一步**。
3. 選取 JDK 和應用程式伺服器。 （這些步驟會說明的詳細資料[建立 Azure 中蝕 Hello 全球應用程式](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)的教學課程）。
4. 按一下 [**完成**]。
5. 按一下 [**執行 Azure 模擬器中**] 按鈕。
6. Java web 應用程式開始計算模擬器中之後，關閉所有執行個體的瀏覽器 （，讓任何目前的瀏覽器工作階段並不會影響您 ACS 登入測試）。
7. 在瀏覽器 （<>或/https://localhost:8080/MyACSHelloWorld/如果您已核取 [**需要 HTTPS 連線**） 中開啟<>http://localhost:8080/MyACSHelloWorld/執行您的應用程式。 您應該會提示您的 Windows Live ID 登入，然後您應該會傳回指定依賴廠商應用程式的 url。
99.  當您完成檢視您的應用程式之後時，請按一下 [**重設 Azure 模擬器**] 按鈕。

## <a name="deploy-to-azure"></a>部署至 Azure

若要部署至 Azure，必須變更依賴廠商領域，並傳回您 ACS 命名空間的 URL。

1. 在 [**編輯依賴廠商應用程式**] 頁面 [Azure 管理入口網站中修改**領域**要部署網站的 URL。 取代您指定為您的部署的 DNS 名稱的**範例**。

    ![在實際執行依賴廠商領域][relying_party_realm_production]

2. 修改**傳回 URL**是您的應用程式的 URL。 取代您指定為您的部署的 DNS 名稱的**範例**。

    ![在實際執行依賴廠商傳回 URL][relying_party_return_url_production]

3. 按一下 [儲存更新回覆廠商領域並返回 URL 變更的 [**儲存**]。
4. 請**登入頁面整合**頁面在瀏覽器中開啟，您要複製其引進了。
5. 蝕的 [專案總管] 中以滑鼠右鍵按一下**MyACSHelloWorld**，按一下 [**建立路徑**]，然後按一下**設定建立路徑**。
6. 按一下 [**文件庫**] 索引標籤，按一下**Azure 存取控制服務篩選器**]，再按一下 [**編輯**。
7. 使用瀏覽器會開啟至 [**登入頁面整合**] 頁面的 [管理] 入口網站中，複製列在 URL**選項 1: ACS 裝載登入頁面連結**欄位，並貼到 [蝕] 對話方塊的 [ **ACS 驗證結束點**] 欄位。
8. 使用瀏覽器會開啟至管理入口網站的 [**編輯依賴廠商應用程式**] 頁面，複製 [**領域**] 欄位中所列的 URL，並貼到蝕] 對話方塊的 [**依賴廠商領域**] 欄位。
9. 中 [**安全性**] 區段的 [蝕] 對話方塊中，如果您想要使用現有的憑證，按一下 [**瀏覽**、 瀏覽至您要使用，請選取它，然後按一下**開啟**的憑證。 或者，如果您想要建立新的憑證，請按一下 [**新增**]，即可顯示 [**新的憑證**] 對話方塊，然後指定密碼、.cer 檔案的名稱和新的憑證的.pfx 檔的名稱。
10. 保留**內嵌馬檔案中的憑證**核取 [，假設您想要內嵌馬檔案中的憑證。
11. [選擇性]核取 [保留**需要 HTTPS 連線**。 如果您設定此選項時，您會需要存取使用 HTTPS 通訊協定的應用程式。 如果您不希望 HTTPS 連線，請取消核取此選項。
12. 讓 Azure 部署，看起來類似下列 Azure ACS 篩選設定。

    ![生產部署 azure ACS 篩選設定][add_acs_filter_lib_production]

13. 按一下 [**完成**] 以關閉 [**編輯文件庫**] 對話方塊。
14. 按一下**[確定**] 關閉 [ **MyACSHelloWorld 屬性**] 對話方塊。
15. 在蝕，按一下 [**發佈至 Azure 雲端**] 按鈕。 回應提示類似為已完成[建立 Azure 中蝕 Hello 全球應用程式](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)主題的**部署至 Azure 應用程式**] 區段中。 

部署 web 應用程式後，關閉任何開啟的瀏覽器工作階段執行 web 應用程式，與您應該會提示您登入 Windows Live ID 認證，後面接著傳送到傳回依賴廠商應用程式的 URL。

當您完成使用 ACS 什麼應用程式，請記住，若要刪除部署 （您可以瞭解如何刪除在[建立 Azure 中蝕 Hello 全球應用程式](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)的主題中的部署）。


## <a name="next_steps"></a>後續步驟

檢查的安全性判斷提示標記語言 (SAML) ACS 傳回應用程式，請參閱[如何檢視 SAML 所傳回的 Azure 存取控制服務][]。 若要進一步瀏覽 ACS 的功能和試驗更複雜的情況，請參閱[Access 控制項服務 2.0][]。

此外，此範例使用**內嵌馬檔案中的憑證**] 選項。 此選項可讓您更容易部署憑證。 如果改為您想要讓您的簽署憑證分開馬檔案，您可以使用下列技巧︰

1. 在 [ **Azure 存取控制服務篩選器**] 對話方塊的 [**安全性**] 區段中，輸入 [ **${信封。JAVA_HOME}/mycert.cer**取消核取 [**內嵌馬檔案中的憑證**。 （調整 mycert.cer 如果您的憑證檔案名稱不同時）。按一下 [**完成**] 關閉對話方塊。
2. 將憑證複製為您的部署中的元件︰ 中蝕的專案總管] 中，展開**MyAzureACSProject**以滑鼠右鍵按一下**WorkerRole1**、**屬性**、 展開**Azure 角色**，按一下**元件**。
3. 按一下 [**新增**]。
4. 在 [**新增元件**] 對話方塊中︰
    1. 在 [**匯入**] 區段中︰
        1. 您可以使用 [**檔案**] 按鈕，瀏覽至您想要使用的憑證。 
        2. **方法**，請選取 [**複製**]。
    2. **為名稱**，請按一下 [文字] 方塊，並接受預設名稱。
    3. 在 [**部署**] 區段中︰
        1. **方法**，請選取 [**複製**]。
        2. **目錄**，輸入**%java_home%**。
    4. 您**新增的元件**] 對話方塊看起來如下。

        ![新增憑證元件][add_cert_component]

    5. 按一下**[確定]**。

此時，您的憑證會包含在您的部署。 請注意，無論您內嵌馬檔案中的憑證，或將其做為元件新增至您的部署，您需要上傳至您的命名空間 [[上傳至您的 ACS 命名空間的憑證][]] 區段中所述的憑證。

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[上傳至您的 ACS 命名空間的憑證]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[專案網站]: http://wastarterkit4java.codeplex.com/releases/view/61026
[如何檢視 SAML 所傳回的 Azure 存取控制服務]: /en-us/develop/java/how-to-guides/view-saml-returned-by-acs/
[存取控制服務 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure 管理入口網站]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 
