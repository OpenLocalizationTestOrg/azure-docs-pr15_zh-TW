<properties
   pageTitle="設定使用多個服務設定 Azure 專案 |Microsoft Azure"
   description="瞭解如何設定 Azure 雲端服務專案藉由變更的 ServiceDefinition.csdef 和 ServiceConfiguration.cscfg 檔案。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>設定 Azure 專案使用多個服務設定

Azure 雲端服務專案包含兩個設定檔︰ ServiceDefinition.csdef 和 ServiceConfiguration.cscfg。 這些檔案 Azure 雲端服務應用程式使用封裝並部署至 Azure。

- **ServiceDefinition.csdef**檔案含有所需的雲端服務應用程式，包括其包含哪些角色需求的 Azure 環境的中繼資料。 這個檔案也包含設定套用至所有執行個體的設定。 您可以在執行階段使用 Azure 服務裝載執行階段 API 讀取這些設定的設定。 您的服務 Azure 中執行時，此檔案無法進行更新。

- **ServiceConfiguration.cscfg**檔案設定服務定義檔案中所定義的設定值，並指定要執行每一個角色的執行個體數目。 您的雲端服務 Azure 中執行時，可以更新此檔案。

Microsoft Visual Studio Azure 工具提供可用來設定儲存這些檔案中的組態設定的屬性頁面。 若要存取屬性頁面，連按兩下下方 Azure 雲端服務專案方案總管] 中的角色參照或以滑鼠右鍵按一下該角色參照，選擇 [**屬性**]，如下圖所示。

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

基礎結構服務定義和服務設定檔的相關資訊，請參閱[結構描述參考](https://msdn.microsoft.com/library/azure/dd179398.aspx)。 如需有關服務設定的詳細資訊，請參閱[如何設定雲端服務](./cloud-services/cloud-services-how-to-configure.md)。

## <a name="configuring-role-properties"></a>設定角色屬性

網頁角色及工作角色的屬性頁面類似，但有幾項差異，指出下列各節中。

從 [**快取**] 頁面中，您可以設定快取服務 Azure。

### <a name="configuration-page"></a>設定] 頁面

在 [**設定**] 頁面上，您可以設定這些屬性︰

**執行個體**

**執行個體**計數屬性設為此角色應該執行服務的執行個體數目。

將**額外小**，**小型**、**中型**、**大**，或**額外大****虛擬記憶體大小**屬性。  如需詳細資訊，請參閱[雲端服務的大小](./cloud-services/cloud-services-sizes-specs.md)。

**啟動巨集指令**（web 角色）

設定此屬性，以指定當您啟動偵錯時，Visual Studio 應該啟動 HTTP 端點或 HTTPS 端點，或兩者的網頁瀏覽器。

HTTPS 端點選項是您的角色已定義 HTTPS 端點，才能使用。 您可以定義 HTTPS 端點**端點**屬性頁面。

如果您已新增 HTTPS 端點，HTTPS 端點啟用選項，根據預設，而且 Visual Studio 便會啟動此端點的瀏覽器，當您啟動偵錯，除了您 HTTP 端點的瀏覽器。 這是假設這兩個啟動選項已啟用。

**診斷程式**

根據預設，診斷已啟用網頁角色。 Azure 雲端服務 project 和儲存帳戶設定為使用本機存放區模擬器。 當您準備好要部署至此 Azure 時，您可以選取建立器] 按鈕 （**...**） 更新使用 Azure 儲存在雲端儲存空間的帳戶。 視需要，或在自動排程的時間間隔，您可以轉移診斷資料儲存的帳戶。 如需有關 Azure 診斷的詳細資訊，請參閱[Azure 雲端服務與虛擬機器中的 [啟用診斷](./cloud-services/cloud-services-dotnet-diagnostics.md)。

## <a name="settings-page"></a>設定] 頁面

在 [**設定**] 頁面中，您可以新增設定的設定您的服務。 設定是名稱 / 值組。 角色中執行的程式碼可以讀取您的組態設定在執行階段使用類別[Azure 受管理的文件庫](http://go.microsoft.com/fwlink?LinkID=171026)所提供的值。 具體來說， [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx)方法會傳回在執行階段命名的組態設定的值。

### <a name="configuring-a-connection-string-to-a-storage-account"></a>設定儲存帳戶的連線字串

連線字串是提供連線和驗證資訊，儲存模擬器或 Azure 儲存體帳戶的設定。 每當您的程式碼必須存取 Azure 儲存服務資料 – 也就是 blob、 佇列中或表格資料 – 從角色中執行的程式碼，您必須定義該儲存帳戶的連線字串。

指向 [Azure 儲存體帳戶的連線字串必須使用已定義的格式。 如需如何建立連線字串的資訊，請參閱[設定 Azure 儲存空間的連接字串](./storage/storage-configure-connection-string.md)。

當您準備好測試您的服務 Azure 儲存服務，或當您準備好要將您的雲端服務部署至 Azure 時，您可以變更任何指向 Azure 儲存體帳戶的連線字串值。 選取****（...），選取 [ **Enter 儲存帳戶認證**。 輸入您的帳戶資訊，其中包含您的帳戶名稱和帳戶金鑰]。 在**儲存帳戶的連線字串**] 對話方塊中，您也可以指出您是否要使用預設 HTTPS 端點 （預設選項）、 預設 HTTP 端點或自訂的結束點。 您可能決定要使用自訂的端點，如果您已經註冊將自訂網域名稱用於您的服務，[設定自訂網域名稱 blob 中的資料 Azure 儲存體帳戶](./storage/storage-custom-domain-name.md)所述。

>[AZURE.IMPORTANT] 您必須修改以在部署您的服務之前指向 Azure 儲存體帳戶連接字串。 若要這麼做失敗可能會導致您無法啟動，或初始化與忙碌，停止狀態循環的角色。

## <a name="endpoints-page"></a>結束點] 頁面

工作者角色可以有 HTTP、 HTTPS 或 TCP 端點的任何數字。 結束點可輸入結束點，可提供給外部用戶端或內部結束點，可提供給其他服務中執行的角色。

- 若要使用 HTTP 端點的外部用戶端和網頁瀏覽器，請變更結束點類型] 以輸入，並指定名稱及公用的連接埠號碼。

- 若要進行 HTTPS 端點可用的外部用戶端和網頁瀏覽器，結束點類型變更為**輸入**，並指定一個名稱、 公用連接埠號碼，以及管理憑證的名稱。

    請注意，您可以指定管理憑證之前，您必須定義憑證**憑證**的 [屬性] 頁面上。

- 若要讓其他雲端服務中的角色適用於內部存取端點，結束點類型變更為內部，並指定的名稱，以及這個端點可能私人的連接埠。

## <a name="local-storage-page"></a>本機的儲存空間頁面

若要保留角色的一或多個本機存放區資源，您可以使用**本機存放區**的 [屬性] 頁面。 本機存放區資源是 Azure 虛擬機器執行角色的執行個體中的檔案系統中保留的目錄。

## <a name="certificates-page"></a>憑證] 頁面

在 [**憑證**] 頁面中，您可以建立憑證關聯與您的角色。 您新增的憑證用於**結束點**的 [屬性] 頁面上設定您的 HTTPS 結束點。

**憑證**的 [屬性] 頁面會將您的憑證的相關資訊加入至您的服務設定。 請注意，您的憑證不封裝服務;您必須以透過[Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)的 Azure 分別上傳您的憑證。

若要建立憑證關聯與您的角色，提供憑證的名稱。 您可以使用此名稱來參照至的憑證，當您設定 HTTPS 端點**端點**屬性頁面。 接下來，指定的憑證存放區是**本機電腦**或**目前的使用者**和存放區的名稱。 最後，請輸入憑證的指紋。 如果目前的使用者 \ 個人 (My) 存放區位於憑證，您可以輸入憑證的指紋從填入內容的清單中選取憑證。 如果它位於其他位置時，請手動輸入指紋值。

當您新增的憑證存放區的憑證時，中繼憑證會自動新增至您的組態設定。 這些中繼憑證必須也上傳至 Azure 才能正確地設定您的服務為 SSL。

在雲端執行時，才，任何您與您的服務建立關聯的管理憑證會套用至您的服務。 在本機的開發環境執行您的服務時，它會使用標準由計算模擬器管理的憑證。

## <a name="configuring-the-azure-cloud-service-project"></a>設定 Azure 雲端服務專案

若要設定套用至整個 Azure 雲端服務專案的設定，您第一次開啟該專案節點的快顯功能表，然後您選擇屬性]，開啟其屬性頁面。 下表顯示屬性頁面。

|屬性] 頁面|描述|
|---|---|
|應用程式|此頁面上，您可以顯示的 Azure 工具，這個雲端服務的專案使用，並可以升級至最新版的工具版本的相關資訊。|
|建立事件|此頁面上，您可以設定測試建置和建置後的事件。|
|開發|此頁面上，您可以指定建立設定指示與執行任何建置後事件的條件。|
|網頁|此頁面上，您可以設定關聯到網頁伺服器的設定。|
