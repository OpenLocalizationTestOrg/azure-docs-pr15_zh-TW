<properties
   pageTitle="如何升級至新版 Azure 工具的 [專案 |Microsoft Azure"
   description="瞭解如何在 Visual Studio 中的 Azure 專案升級為最新版的 Azure 工具"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>如何升級 Azure 工具的目前版本的 Visual Studio 專案

## <a name="overview"></a>概觀

安裝 Azure 工具 （或 1.6 較舊版本） 的目前版本之後，請使用 Azure 工具所建立的任何專案發行之前 1.6 一旦您開啟自動更新 (11 2011)。 如果您使用這些工具的 1.6 (年 11 月 2011) 版本中建立專案，您仍有安裝的版本，您可以在較舊版本中開啟這些專案，稍後決定是否要升級它們。

## <a name="how-your-project-changes-when-you-upgrade-it"></a>當您在升級時，您的專案如何變更

如果專案會自動升級，或您指定您想要升級，請使用目前版本的特定的組件，修改您的專案與本節所述，也會變更某些屬性。 如果您的專案需要較新版本的工具與其他變更，您必須手動進行這些變更。

- Web.config 檔案的 web 角色和 app.config 檔案的工作者角色會更新為參照 Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll 的新版本。

- Microsoft.WindowsAzure.StorageClient.dll Microsoft.WindowsAzure.Diagnostics.dll，與 Microsoft.WindowsAzure.ServiceRuntime.dll 組件會升級至新的版本。

- Azure 專案檔案 (.ccproj) 中所儲存的發佈設定檔會移到另一個檔案，與副檔名.azurePubXml，在**發佈**子目錄。

- 發佈設定檔中的某些屬性會更新以支援新增及變更的功能。 因為您可以更新已部署的雲端服務，同時或從屬參照**DeploymentReplacementMethod**會取代**AllowUpgrade** 。

- 新增並設定為 false，以便用於偵錯的網頁伺服器不會自動變更從網際網路資訊服務 (IIS) IIS 表達**UseIISExpressByDefault**的屬性。 IIS Express 是預設的網頁伺服器，會以較新的版本類似的工具來建立的專案。

- 如果 Azure 快取裝載在一或多個專案的角色，當專案升級時，將會變更某些屬性，在服務設定 （.cscfg 檔案） 和服務定義 （.csdef 檔案）。 如果專案使用 Azure 快取 NuGet 套件，則會將專案升級為最新版本的套件。 您應該開啟 web.config 檔案，並確認 [用戶端設定已升級程序期間妥善維護。 如果您沒有使用 NuGet 套件新增參考 Azure 快取的用戶端組件，這些組件不會更新。您必須手動更新這些參照至新的版本。

>[AZURE.IMPORTANT] F # 專案，您必須手動更新 Azure 組件的參考使其參考這些組件的較新版本。

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>如何升級目前版本的 Azure 專案

1. 將您想要使用的升級的專案的 Visual Studio 安裝安裝 Azure 工具的目前版本，然後開啟 [您想要升級的專案。 如果使用 Azure 工具建立專案之前 1.6 放開 (2011 年 11 月)，project 自動升級為最新版本。 如果建立專案的年 11 月 2011年放開仍安裝的版本，project 會開啟該版本。

1. 在方案總管中開啟專案節點的快顯功能表，選擇 [**內容**]，然後選擇出現的對話方塊中的 [**應用程式**] 索引標籤。

    [**應用程式**] 索引標籤會顯示與專案相關聯的工具版本。 如果出現 Azure 工具的目前版本，已升級專案。 如果您已安裝新版的工具] 索引標籤會顯示於，就會出現 [**升級**] 按鈕。

1. 選擇 [**升級**] 按鈕，將專案升級至最新版的工具]。

1. 建立專案，然後地址的 API 變更任何錯誤。 如需如何修改您的新版本的程式碼的資訊，請參閱特定 API 的文件。
