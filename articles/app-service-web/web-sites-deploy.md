<properties
    pageTitle="部署至 Azure 應用程式服務應用程式"
    description="瞭解如何將您的應用程式部署到 Azure 應用程式服務。"
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cephalin;dariac"/>
    
# <a name="deploy-your-app-to-azure-app-service"></a>部署至 Azure 應用程式服務應用程式

本文可協助您判斷部署[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)，為您的 web 應用程式、 行動應用程式後端或 API 應用程式的檔案的最佳選擇，然後會引導您慣用的選項的特定指示適當的資源。

## <a name="overview"></a>Azure 服務應用程式部署概觀

Azure 的應用程式服務就會在應用程式架構維護您 （ASP.NET、 PHP、 Node.js 等）。 有些架構 Java 等 Python 時，預設會啟用，可能需要啟用它簡單的核取記號的設定。 此外，您可以自訂您的應用程式架構，例如 PHP 版本或您執行階段的位元。 如需詳細資訊，請參閱[設定您的應用程式中 Azure 應用程式服務](web-sites-configure.md)。

因為您不需要擔心網頁伺服器或應用程式架構，將您的應用程式部署到應用程式服務是部署您的程式碼、 二進位檔案、 內容檔案，以及其個別目錄結構， [ **/site/wwwroot**目錄](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)Azure 中的 (或**/網站/wwwroot/App_Data/工作/** WebJobs 目錄)。 應用程式服務支援下列部署選項︰ 

- [FTP 或 FTP](https://en.wikipedia.org/wiki/File_Transfer_Protocol)︰ 使用您偏好使用的 FTP 或 FTP 啟用從[FileZilla](https://filezilla-project.org)以完整功能的 Ide，例如[NetBeans](https://netbeans.org)，將檔案移動至 Azure 工具。 這是嚴格檔案上傳程序。 沒有其他服務的服務所提供的應用程式，例如版本控制、 檔案結構管理等等。 

- [Kudu （給/水星或 OneDrive/Dropbox）](https://github.com/projectkudu/kudu/wiki/Deployment)︰ 使用[部署引擎](https://github.com/projectkudu/kudu/wiki)在應用程式服務。 推入 Kudu 的程式碼，直接從任何存放庫。 Kudu 在程式碼推入，包括版本控制、 套件還原、 MSBuild，以及[網頁連結](https://github.com/projectkudu/kudu/wiki/Web-hooks)連續的部署和其他自動化工作時，也會提供新增的服務。 Kudu 部署引擎支援 3 的不同類型的部署來源︰   
    * 從 OneDrive 和 Dropbox 的內容同步處理   
    * 自動同步處理 GitHub、 Bitbucket，與 Visual Studio 小組 Services 存放庫型連續部署  
    * 手動同步處理的本機給存放庫為基礎的部署  

- [Web 部署](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy)︰ 部署程式碼直接從您最愛的 Microsoft 應用程式服務工具，例如 Visual Studio 中使用相同的工具，可自動化部署 IIS 伺服器。 這項工具可支援差異專用的部署、 建立資料庫的連線字串] 等轉換。Web 部署不同於 Kudu]，則在部署至 Azure 之前內建應用程式的二進位檔案。 類似於 FTP，不提供其他服務應用程式服務。

常用的 web 開發工具支援一或多個這些部署處理程序。 當您選擇的工具會決定您可以利用部署程序時，實際 DevOps 功能一種方式而定部署程序與您選擇的特定工具的組合。 例如，如果您執行 Web 部署從[Visual Studio 與 Azure SDK](#vspros)，即使沒有收到來自 Kudu 自動化，請取得套件還原及 MSBuild 自動化 Visual Studio 中。 

>[AZURE.NOTE] 這些部署處理程序不實際[佈建 Azure 的資源](../resource-group-template-deploy-portal.md)，可能需要您的應用程式。 不過，大部分的連結的使用方法文章說明如何佈建應用程式並部署程式碼，以便端對端。 您也可以找到佈建 Azure 資源[自動化部署使用命令列工具](#automate)] 區段中的其他選項。
     
## <a name="ftp"></a>手動將檔案複製到 Azure 透過 FTP 部署
如果您是手動複製的網頁伺服器的 [網站內容的使用，您可以使用[FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol)公用程式複製檔案，例如 [Windows 檔案總管] 或 [ [FileZilla](https://filezilla-project.org/)。

手動複製檔案的優點是︰

- 認識和最小複雜度的 FTP 工具。 
- 了解完全檔案在何處。
- 新增與 FTP 安全性。

會將檔案複製手動缺點︰

- 需了解如何部署至正確的目錄服務應用程式中的檔案。 
- 復原發生錯誤時沒有版本控制項。
- 部署問題的疑難排解沒有內建的部署歷程記錄。
- 可能的長部署時間，因為許多 FTP 工具不提供差異專用複製，只要將複製的所有檔案。  

### <a name="howtoftp"></a>如何手動將檔案複製到 Azure 部署
將檔案複製到 Azure 包含幾個簡單的步驟︰

1. 假設您已經建立部署認證，您可以移至 [**設定]**，取得 FTP 連線資訊 > **屬性**和再複製**FTP/部署使用者**、 **FTP 主機名稱**，及**FTP 主機名稱]**的值。 請複製**FTP/部署使用者**的使用者值顯示 Azure 入口網站以 FTP 伺服器提供適當的內容，包括應用程式的名稱。
2. 從您的 FTP 用戶端，使用您所收集連線到您的應用程式的連線資訊。
3. 將您的檔案和其個別目錄結構複製到[**/site/wwwroot**目錄](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)中 Azure (或**/網站/wwwroot/App_Data/工作/** WebJobs 目錄)。
4. 瀏覽至您的應用程式的 URL，以確認正常執行應用程式。 

如需詳細資訊，請參閱下列資源︰

* [建立 PHP MySQL web 應用程式並部署使用 FTP](web-sites-php-mysql-deploy-use-ftp.md)。

## <a name="dropbox"></a>部署與雲端資料夾同步處理
[手動將檔案複製](#ftp)到其他正在同步處理檔案和資料夾至應用程式服務從 OneDrive 和 Dropbox 等雲端儲存服務。 雲端資料夾的同步處理利用部署 Kudu 程序 （請參閱[部署程序概觀](#overview)）。

雲端資料夾的同步處理的優點是︰

- 簡化的部署。 OneDrive 和 Dropbox 等服務會提供桌面的同步處理用戶端，使您的本機工作目錄也是您部署的目錄。
- 單鍵部署。
- 可以使用 Kudu 部署引擎中的所有功能 （例如套件還原、 自動化）。

是的雲端資料夾的同步處理缺點︰

- 復原發生錯誤時沒有版本控制項。
- 沒有自動的部署，就需要手動同步處理。

### <a name="howtodropbox"></a>如何部署與雲端資料夾同步處理
[Azure 入口網站](https://portal.azure.com)中，可以指定在您的 OneDrive 或 Dropbox 雲端儲存區中的內容的同步處理的資料夾，使用您的應用程式碼和內容的該資料夾，並按一下] 按鈕的同步處理應用程式服務。

* [從 Azure 應用程式服務雲端資料夾的同步處理內容](app-service-deploy-content-sync.md)。 

## <a name="continuousdeployment"></a>從雲端來源控制服務持續部署
如果您的開發小組使用[Visual Studio 小組服務](http://www.visualstudio.com/)、 [GitHub](https://www.github.com)或[BitBucket](https://bitbucket.org/)等雲端來源程式碼管理 (SCM) 服務，您可以設定應用程式服務整合您存放庫，並持續部署。 

部署來自雲端的來源控制服務的優點是︰

- 若要啟用復原版本控制項。
- 若要設定連續的部署，就可以給 （以及水星適用之處） 的功能存放庫。 
- 分支特定部署中，可以將不同的分支部署至不同的[位置](web-sites-staged-publishing.md)。
- 可以使用 Kudu 部署引擎中的所有功能 （例如部署版本設定、 復原、 套件還原、 自動化）。

部署來自雲端的來源控制服務的 con 是︰

- 所需的個別 SCM 服務的一些知識。

###<a name="vsts"></a>如何從雲端來源控制服務持續部署
在[Azure 入口網站](https://portal.azure.com)，您可以設定連續部署 GitHub、 Bitbucket，與 Visual Studio 小組服務。

* [持續部署至 Azure 應用程式服務](app-service-continuous-deployment.md)。 

## <a name="localgitdeployment"></a>從本機給部署
如果您的開發小組使用根據給內部部署本機來源程式碼管理 (SCM) 服務，您可以為部署來源應用程式服務設定這個。 

從本機給部署的優點是︰

- 若要啟用復原版本控制項。
- 分支特定部署中，可以將不同的分支部署至不同的[位置](web-sites-staged-publishing.md)。
- 可以使用 Kudu 部署引擎中的所有功能 （例如部署版本設定、 復原、 套件還原、 自動化）。

從本機給部署 con 是︰

- 所需的個別 SCM 系統的一些知識。
- 連續部署沒有開啟鍵解決方案。 

###<a name="vsts"></a>如何從本機給部署
在[Azure 入口網站](https://portal.azure.com)，您可以設定本機給部署。

* [本機給部署至 Azure 應用程式服務](app-service-deploy-local-git.md)。 
* [發佈至任何給/吋汞柱 repo 的 Web 應用程式](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。  

## <a name="deploy-using-an-ide"></a>使用 IDE 部署
如果您已使用[Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)與[Azure SDK](https://azure.microsoft.com/downloads/)或[Xcode](https://developer.apple.com/xcode/)、[蝕](https://www.eclipse.org)及[IntelliJ 想法](https://www.jetbrains.com/idea/)等其他 IDE 套件，您可以直接從 Azure 對部署在您的 IDE。 適用於個別的開發人員，這個選項。

Visual Studio 支援所有三個部署程序 （FTP 給，與 Web 部署），根據您的喜好設定，而其他 Ide 部署到應用程式服務 FTP 或給整合後，才 （請參閱[部署程序概觀](#overview)）。

部署使用 IDE 的優點是︰

- 可能會最小化您的應用程式端對端生命週期的工具。 開發、 偵錯、 追蹤及部署至 Azure 而不移動您的 IDE 以外的所有應用程式。 

是的部署使用 IDE 缺點︰

- 新增的工具中的複雜性。
- 仍需要來源控制系統小組專案。

<a name="vspros"></a>部署 Visual Studio 使用 Azure SDK 的其他優點是︰

- Azure SDK 讓 Azure 資源一等公民 Visual Studio 中。 建立、 刪除、 編輯、 開始，並停止應用程式、 查詢的後端 SQL 資料庫、 live 偵錯 Azure 應用程式，以及執行其他功能。 
- 即時編輯程式碼上的檔案 Azure。
- 請 live 偵錯的應用程式上 Azure。
- 整合式 Azure 總管]。
- 僅供差異部署。 

###<a name="vs"></a>如何直接從 Visual Studio 中部署

* [Azure 和 ASP.NET 快速入門](web-sites-dotnet-get-started.md)。 如何建立並部署簡單的 ASP.NET MVC web 專案使用 Visual Studio 與 Web 部署。
* [如何使用 Visual Studio 中部署 Azure WebJobs](websites-dotnet-deploy-webjobs.md)。 如何設定主控台應用程式的專案，好讓他們部署為 WebJobs。  
* [部署安全性 ASP.NET MVC 5 應用程式的成員資格，OAuth，與 Web 應用程式的 SQL 資料庫](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。 如何建立並部署 ASP.NET MVC web 專案使用 SQL 資料庫，以使用 Visual Studio、 Web 部署和實體架構的程式碼的第一個移轉。
* [ASP.NET Web 部署使用 Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。 12 個部分教學課程的數列涵蓋的部署工作比此清單中其他更完整的範圍。 由於撰寫教學課程，但稍後新增附註說明什麼是遺失已新增一些 Azure 部署功能。
* [部署給存放庫直接從 Visual Studio 2012 中 Azure ASP.NET 網站](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。 說明如何部署 Visual Studio 中，使用外掛程式給認可程式碼給以及連線 Azure 至給存放庫的 ASP.NET web 專案。 開始在 Visual Studio 2013，就可以給支援內建，而不需要安裝外掛程式。

###<a name="aztk"></a>如何使用 Azure 套件提供蝕和 IntelliJ 想法的部署

Microsoft 可以讓您直接從蝕和透過[Azure 工具組的蝕](../azure-toolkit-for-eclipse.md)和[Azure 工具組的 IntelliJ](../azure-toolkit-for-intellij.md)IntelliJ Azure 部署 Web 應用程式。 下列教學課程說明參與部署 Azure 使用任一 IDE 簡單"Hello"全球 Web 應用程式中的步驟︰

*  [建立 Azure 中蝕認識全球 Web 應用程式](./app-service-web-eclipse-create-hello-world-web-app.md)。 本教學課程教您如何使用 Azure 工具組蝕用來建立及部署 Hello 全球 Web 應用程式的 Azure。
*  [建立在 IntelliJ Azure 認識全球 Web 應用程式](./app-service-web-intellij-create-hello-world-web-app.md)。 本教學課程教您如何使用 Azure 工具組 IntelliJ 用來建立及部署 Hello 全球 Web 應用程式的 Azure。


## <a name="automate"></a>用命令列工具來自動化部署

* [自動化 MSBuild 部署](#msbuild)
* [複製檔案有 FTP 的工具和指令碼](#ftp)
* [自動化使用 Windows PowerShell 的部署](#powershell)
* [自動化.NET 管理 API 的部署](#api)
* [從 Azure 部署命令列介面 (Azure CLI)](#cli)
* [從 Web 部署命令列的部署](#webdeploy)
* [使用 FTP 批次指令碼](http://support.microsoft.com/kb/96269)。
 
使用雲端服務，例如[Octopus 部署](http://en.wikipedia.org/wiki/Octopus_Deploy)是另一個 [部署] 選項。 如需詳細資訊，請參閱[部署 ASP.NET 應用程式至 Azure 網站](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)。

###<a name="msbuild"></a>自動化 MSBuild 部署

如果您使用[Visual Studio IDE](#vs)開發時，您可以使用[MSBuild](http://msbuildbook.com/)來自動化您 IDE 中，您可以執行的任何項目。 您可以設定 MSBuild 使用[Web 部署](#webdeploy)或[FTP/FTP](#ftp)複製檔案。 Web 部署也可以自動化許多其他部署相關工作，例如部署資料庫。

如需有關使用 MSBuild 的命令列部署的詳細資訊，請參閱下列資源︰

* [ASP.NET Web 部署使用 Visual Studio︰ 命令列部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。 部署至 Azure 使用 Visual Studio 教學課程系列中的第十。 顯示如何使用部署之後設定 Visual Studio 中發佈設定檔的命令列。
* [內 Microsoft 建置引擎︰ 使用 MSBuild 和小組 Foundation 建立](http://msbuildbook.com/)。 包含章節以供部署使用 MSBuild 難複製活頁簿。

###<a name="powershell"></a>自動化使用 Windows PowerShell 的部署

您可以從[Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx)來執行 MSBuild 或 FTP 部署函數。 如果您執行這項作業時，您也可以使用輕鬆呼叫 Azure 其餘管理 API 的 Windows PowerShell cmdlet 的集合。

如需詳細資訊，請參閱下列資源︰

* [部署 web 應用程式連結至 GitHub 存放庫](app-service-web-arm-from-github-provision.md)
* [佈建與 SQL 資料庫的 web 應用程式](app-service-web-arm-with-sql-database-provision.md)
* [佈建及部署 microservices Azure 中預測](app-service-deploy-complex-application-predictably.md)
* [建置真實世界雲端應用程式搭配使用 Azure-自動化所有項目](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。 說明如何 e-活頁簿中的範例應用程式會使用 Windows PowerShell 指令碼，建立 Azure 測試環境，並將其部署的電子書章節。 請參閱 [[資源](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources)] 區段的其他 PowerShell 的 Azure 文件的連結。
* [使用 Windows PowerShell 指令碼發佈至開發和測試環境](../vs-azure-tools-publishing-using-powershell-scripts.md)。 如何使用 Windows PowerShell 部署指令碼的 Visual Studio 會產生。

###<a name="api"></a>自動化.NET 管理 API 的部署

您可以撰寫 C# 程式碼部署執行 MSBuild 或 FTP 函數。 如果您執行這項作業時，您可以存取 Azure 管理 REST API 來執行網站管理功能。

如需詳細資訊，請參閱下列資源︰

* [自動化 Azure 管理文件庫與.NET 的所有項目](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。 簡介的.NET 管理 API 及更多文件的連結。

###<a name="cli"></a>從 Azure 部署命令列介面 (Azure CLI)

您可以使用 Windows、 Mac 或 Linux 機器中的命令列使用 FTP 部署。 如果您執行這項作業時，您也可以存取 Azure 其餘管理 API 使用 Azure CLI。

如需詳細資訊，請參閱下列資源︰

* [Azure 命令列工具](/downloads/#cmd-line-tools)。 入口網站頁面中 Azure.com 命令列工具資訊。

###<a name="webdeploy"></a>從 Web 部署命令列的部署

[Web 部署](http://www.iis.net/downloads/microsoft/web-deploy)是以供部署到 IIS 不只提供智慧型檔案同步處理功能，但也可執行或其他部署相關工作的無法在使用 FTP 時自動進行共同作業的 Microsoft 軟體。 例如，網頁部署可以部署為新資料庫或資料庫的更新，以及您的 web 應用程式。 Web 部署也最小化更新現有的網站，因為它可以聰明地複製已變更的檔案所需的時間。 Microsoft Visual Studio 與小組 Foundation 伺服器支援網頁部署內建，但是您也可以使用 Web 部署直接從命令列可自動化部署。 Web 部署命令很強大，但可以陡峭學習曲線。

如需詳細資訊，請參閱下列資源︰

* [簡單的 Web 應用程式︰ 部署](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/)。 由 David Ebbo 他寫的使其更容易使用 Web 部署工具的相關的部落格。
* [Web 部署工具](http://technet.microsoft.com/library/dd568996)。 正式 Microsoft TechNet 網站上的文件。 標示日期，但還是建議開始的位置。
* [使用 Web 部署](http://www.iis.net/learn/publish/using-web-deploy)。 正式 Microsoft IIS.NET 網站上的文件。 同時標示日期，但若要開始的好地方。
* [ASP.NET Web 部署使用 Visual Studio︰ 命令列部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。 MSBuild 建置引擎使用 Visual Studio 中，並將其也可從命令列部署至 Web 應用程式的 web 應用程式。 本教學課程是一系列的主要的 Visual Studio 中部署的一部分。

##<a name="nextsteps"></a>後續步驟

在某些情況下，您可能要能夠輕鬆地臨時與您的應用程式的產品版本之間來回切換。 如需詳細資訊，請參閱[分段部署 Web 應用程式](web-sites-staged-publishing.md)。

備份及還原計畫就地是任何部署工作流程很重要的一部分。 應用程式服務的相關資訊的備份與還原功能，請參閱[Web 應用程式備份](web-sites-backup.md)。  

有關如何使用 Azure 的角色型存取控制管理服務應用程式部署的存取權，請參閱[RBAC 和發佈 Web 應用程式](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/)。


 
