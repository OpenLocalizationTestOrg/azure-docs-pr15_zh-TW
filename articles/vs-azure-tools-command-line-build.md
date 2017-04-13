<properties
   pageTitle="Azure 的命令列建置 |Microsoft Azure"
   description="Azure 的命令列建置"
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

# <a name="command-line-build-for-azure"></a>Azure 的命令列建置

## <a name="overview"></a>概觀

您可以藉由在命令提示字元中執行 MSBuild 建立 Azure 部署的套件。 您可以設定，並定義組建偵錯、 臨時和生產，除了自動化一些建立程序。


## <a name="microsoft-build-engine-msbuild"></a>Microsoft 建置引擎 (MSBuild)

藉由使用 Microsoft 建置引擎 (MSBuild)，您可以建立在建立產品未安裝 Visual Studio 實驗環境。 MSBuild 專案檔案的可延伸和完全支援由 Microsoft 使用的 XML 格式。 在這種檔案格式，您可以說明項目必須是內建的一或多個平台和設定。

您也可以在命令提示字元中，執行 MSBuild，本主題說明的方法。 在命令提示字元中設定屬性，您可以建立專案的特定設定。 同樣地，您也可以定義 MSBuild] 命令將會建立的目標。 如需有關命令列參數與 MSBuild 的詳細資訊，請參閱[MSBuild 命令列參考](https://msdn.microsoft.com/library/ms164311.aspx)。

## <a name="installation"></a>安裝

下列程序所述，您必須安裝軟體和工具建立伺服器上之前，您可以使用 MSBuild 建立 Azure 套件︰

1. 安裝.NET Framework 4 或更新版本，其中包括 MSBuild。

1. 安裝[Azure 撰寫工具](http://go.microsoft.com/fwlink/?LinkId=394615)（MicrosoftAzureAuthoringTools x64.msi 或 MicrosoftAzureAuthoringTools x86.msi 外觀。

1. 安裝[.net Azure 文件庫](http://go.microsoft.com/fwlink/?LinkId=394616)（MicrosoftAzureLibsForNet x64.msi 或 MicrosoftAzureLibs x86.msi 外觀。

1. 複製 Microsoft.WebApplication.targets 檔案從 Visual Studio 安裝另一部電腦上。

    檔案中有目錄 C:\Program Files (x86) \MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v11.0 for Visual Studio 2012)，您應該將它複製到相同的目錄建立伺服器上。

1. 安裝的[Visual Studio Azure 工具](http://go.microsoft.com/fwlink/?LinkId=394616)。

    尋找 WindowsAzureTools.vs120.exe 建置 Visual Studio 2013 專案。

## <a name="msbuild-parameters"></a>MSBuild 參數

若要建立套件最簡單的方法是執行與 MSBuild`/t:Publish`選項。 根據預設，這個命令會建立目錄相對於專案，例如 ProjectDir\bin\Configuration\app.publish 的根資料夾\.當您在 Azure 專案時，您會產生兩個檔案、 本身的套件檔案和隨附的設定檔︰

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

根據預設，每個 Azure 專案包含一個會建立本機 （偵錯） 服務設定檔，另一個雲端 （臨時或生產） 建置，但是您可以新增或移除服務設定檔，視需要。 當您在 Visual Studio 中的套件時，系統會要求同時套件中包含的服務設定檔案。 當您使用 MSBuild 套件時，預設會包含本機的服務設定檔。 若要包括不同的服務設定檔案，請將`TargetProfile`MSBuild 命令屬性 (`MSBuild /t:Publish /p:TargetProfile=ProfileName`)。

如果您想要用於儲存的套件和設定檔中的另一個目錄，請使用設定路徑`/p:PublishDir=Directory\`選項，包括 [句尾反斜線分隔符號。

## <a name="deployment"></a>部署

內建套件之後，您可以將其部署至 Azure。 示範該程序的教學課程，請參閱 Azure 網站。 如需如何自動化該程序，請參閱[Azure 中的雲端服務的連續傳遞](./cloud-services/cloud-services-dotnet-continuous-delivery.md)資訊。
