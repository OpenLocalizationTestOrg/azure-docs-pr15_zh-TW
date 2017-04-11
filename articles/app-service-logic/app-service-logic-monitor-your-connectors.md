<properties
    pageTitle="管理並監控您的連接器與應用程式服務中的 API 應用程式 |Microsoft Azure"
    description="檢視效能的連接器和 API 應用程式中的邏輯應用程式。microservices 架構"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger"
    manager="anneta"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="mandia"/>

# <a name="manage-and-monitor-your-built-in-api-apps-and-connectors"></a>管理及監視內建 API 應用程式與連接器

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽結構描述版本。

您建立內建的 API 應用程式。 現在該怎麼做？

Azure，在每個 API 應用程式會是不同的網站裝載於 Azure。 如此一來，您可以輕鬆地看見多少要求進行時，請參閱連接器正在使用的資料量。 您也可以備份您的 API 應用程式、 建立提醒、 啟用 Tinfoil 安全性]，並新增使用者與角色。

本主題說明一些不同的選項，來管理您的 API 應用程式。

若要查看這些內建功能，請在[Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)開啟 API 應用程式。 在您 startboard API 應用程式時，選取以開啟內容]。 您可以也選取 [**瀏覽**、 選取**API 應用程式**，然後選取 [API 應用程式︰

![][browse]

## <a name="see-the-properties-you-entered"></a>請參閱您輸入的內容

當您開啟 API 應用程式時，有幾個功能及工作︰

![][settings]

您可以︰

- **設定**API 應用程式，包括您的訂閱明細，顯示特定資訊]，列出您的 API 應用程式的權限的使用者。 您也可以增加或減少使用縮放功能; API 應用程式的執行個體數目在其他功能。
- 您可以使用 [**啟動**和**停止**] 按鈕來控制 API 應用程式。
- 當對 API 應用程式所使用的基礎檔案所做的產品更新時，您可以按一下以取得最新版本的**更新**。 例如，如果 「 修正 」 或 「 的 Microsoft 安全性更新，會自動按一下**更新**更新您的 API 應用程式，包括此 [修正]。
- 選取 [**變更規劃**升級或降級根據 API 應用程式的資料使用方式。 您也可以使用這項功能，以查看您的資料使用方式。
- 當您建立連接器的有資料表，例如 SQL 連接器，您可以選擇性地輸入資料表名稱，以連線至。 資料表為基礎的結構描述，就會自動建立並使用您按一下 [**下載結構描述**。 此下載的結構描述然後可用來建立轉換或地圖。

## <a name="change-your-connector-or-api-configuration-values-you-entered"></a>變更您的連接器或您輸入的 API 設定值

設定，或建立您的內建連接器之後，您可以變更您輸入的值。 例如，如果您已設定 SQL 連接器您想要變更的 SQL Server 名稱或資料表名稱，您可以在 API 應用程式刀的連接器。

步驟包括︰

1. 開啟您的連接器或 API 應用程式。 當您執行時，API 應用程式刀隨即會開啟。
2. 在 [**基本資訊**，按一下超連結在主屬性。 超連結名稱為項目等*slackconnector*或*microsoftsqlconnector123*:

    ![][apiapphost]

3. 在 API 應用程式主機刀中，選取 [**設定**]。 在 [設定刀中，選取 [**應用程式設定**]。 您設定的值會列在 [**應用程式設定**︰

    ![][hostsettings]

4. 按一下 [的設定您要變更，請輸入新的值，並**儲存**您的變更。


## <a name="install-the-hybrid-connection-manager---optional"></a>安裝混合式連接管理員-選擇性

![][hcsetup]

混合式連線管理員可讓您連線到內部部署系統，例如 SQL Server] 或 [SAP。 此混合式連線會使用 Azure 服務匯流排連線，並控制 Azure 資源與您的內部部署資源之間的安全性。

請參閱[使用 Azure 應用程式服務中的混合式連線管理員](app-service-logic-hybrid-connection-manager.md)。

> [AZURE.NOTE] 如果您連線到內部部署資源，您的防火牆後方需要混合式連線管理員。 如果您不連線到內部部署系統，混合式連線管理員可能未列出您的連接器刀中。

## <a name="monitor-the-performance"></a>監視效能
效能標準內建功能，並包含在您建立的每個 API 應用程式。 這些計量專屬於您的 API 裝載於 Azure 的應用程式。 範例指標︰

![][monitoring]

您可以︰

- 選取 [**要求與錯誤**新增不同的效能指標包括經常已知 HTTP 錯誤碼，例如 200、 400 或 500 HTTP 狀態碼。 您可以也查看回應的時間，查看多少要求 API 應用程式，並查看的資料量，若資料量超出。 根據效能指標，您可以建立電子郵件通知如果度量單位，超過您所選擇的臨界值。
- 在 [**使用情況**中，您可以看到多少**CPU**使用 API 應用程式與檢閱目前的**使用量配額**(mb)，請參閱根據您的成本層您最大值的資料使用方式。 **估計花**可以幫助您判斷執行 API 應用程式的潛在的成本。
- 選取 [**處理程序**，以開啟 [處理程序總管]。 這會顯示您 web 的執行個體和屬性，包括執行緒 count 和記憶體使用方式。

您可以使用這些工具，決定是否應用程式服務方案應向上或縮小，根據您的業務需求。 這些功能可在入口網站內建沒有所需的其他工具。

## <a name="control-the-security"></a>控制項的安全性

API 應用程式使用角色為基礎的安全性。 這些角色適用於整個 Azure 體驗，包括 API 應用程式與其他 Azure 的資源。 角色包括︰

角色 | 描述
--- | ---
擁有者 | 完整功能的管理體驗和可以授權讓其他使用者或群組存取。
參與者 | 有的完整存取權的管理體驗。 無法讓其他使用者或群組的存取。
閱讀程式 | 可以檢視機密以外的所有資源。
使用者存取系統管理員 | 可以檢視所有的資源，建立/管理角色，並建立/管理支援票證。

請參閱[Microsoft Azure 入口網站中的角色型存取控制](../active-directory/role-based-access-control-configure.md)。

您可以輕鬆地新增使用者並為其分派特定角色 API 應用程式。 入口網站會顯示您擁有存取和指派的角色的使用者︰

![][access]  

- 選取要新增使用者、 指派角色，以及移除使用者的**使用者**。
- 選取 [若要查看特定角色中的所有使用者的**角色**將使用者新增至角色，並移除使用者的角色。


## <a name="more-good-stuff"></a>更多建議的檔案
- 選取要開啟特定 API 應用程式的自動建立 Swagger 檔案的**API 定義**。
- 選取 [檢視您的 API 應用程式所需的檔案的**相依性**]。 例如，如果您使用 SAP 連接器，您可以安裝一些額外的檔案在內部部署混合式連線管理員。 這些相依性會顯示在您的 API 應用程式刀。

>[AZURE.IMPORTANT] 當您開啟您 API 應用程式的內容，並查看 [**基本資訊**] 下方時，有開啟新的刀的**主機**] 和 [**閘道器**的連結︰
>
> ![][host]
>
>這些屬性會有特定的網站裝載您的 API 應用程式。 使用內建的 API 應用程式或連接器時，大部分的這些屬性不是真正會套用，我們建議您不要更新這些屬性。 如果您在 Visual Studio 建立您自己的 API 應用程式，並將其部署至 Azure 訂閱，您可以使用主機和閘道刀。 <br/><br/>


>[AZURE.NOTE] 若要開始使用邏輯應用程式註冊 Azure 帳戶之前，請移至[試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic)。 您可以建立短暫入門邏輯應用程式。 沒有所需的信用卡和沒有承諾。

## <a name="read-more"></a>了解更多

[監控您的邏輯應用程式](app-service-logic-monitor-your-logic-apps.md)<br/>
[連接器與 API 應用程式清單，在應用程式服務](app-service-logic-connectors-list.md)<br/>
[Microsoft Azure 入口網站中的角色型存取控制](../active-directory/role-based-access-control-configure.md)<br/>
[Azure 應用程式服務中使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png
