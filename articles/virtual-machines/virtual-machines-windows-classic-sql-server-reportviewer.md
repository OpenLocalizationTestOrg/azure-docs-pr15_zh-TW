<properties 
    pageTitle="在網站上使用 ReportViewer |Microsoft Azure"
    description="本主題說明如何建立 Microsoft Azure 網站 Visual Studio ReportViewer 控制項來顯示儲存在 Microsoft Azure 虛擬機器中的報告。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>在 [網站裝載於 Azure 中使用 ReportViewer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


您可以建立 Microsoft Azure 網站 Visual Studio ReportViewer 控制項來顯示儲存在 Microsoft Azure 虛擬機器中的報告。 ReportViewer 控制項是在 Web 應用程式中，您就會建立使用 ASP.NET Web 應用程式範本。

>[AZURE.IMPORTANT] ASP.NET MVC Web 應用程式範本不支援 ReportViewer 控制項。

若要 ReportViewer 併入您的 Microsoft Azure 」 網站，您必須完成下列工作。

- **新增**部署套件的組件

- **設定**驗證與授權

- **發佈**Azure ASP.NET Web 應用程式

## <a name="prerequisites"></a>必要條件

請檢閱[SQL Server 的商務智慧功能 Azure 虛擬機器中的商務連絡人](virtual-machines-windows-classic-ps-sql-bi.md)的 「 一般的建議及最佳作法 」 一節。

>[AZURE.NOTE] 使用 Visual Studio，Standard Edition 或上方運送 ReportViewer 控制項。 如果您使用的 Web 開發人員 Express Edition，您必須安裝[MICROSOFT 報表檢視器 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747)使用 ReportViewer 執行階段功能。
>
>Microsoft Azure 中不支援 ReportViewer 設定在本機處理模式。

檢閱[Reporting Services 報表檢視器] 控制項和 Microsoft Azure 虛擬機器為主的報表伺服器](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)白皮書 （英文）。

## <a name="adding-assemblies-to-the-deployment-package"></a>新增組件至部署封裝

當您主控您 ASP.NET 應用程式內部部署時，請 ReportViewer 組件通常直接在全域快取 (GAC) IIS 伺服器的安裝 Visual Studio 在安裝期間，並可直接存取應用程式。 不過，當您裝載 ASP.NET 應用程式在雲端，Microsoft Azure 不允許將 GAC，安裝，請務必在本機的應用程式的 ReportViewer 組件可讓任何項目。 您可以藉由新增它們的參考您專案中執行這項操作，並將其設定為在本機複製。

在遠端處理模式 ReportViewer 控制項，請使用下列的組件︰

- **Microsoft.ReportViewer.WebForms.dll**︰ 包含 ReportViewer 程式碼，您需要使用 ReportViewer 在您的頁面。 這個組件的參考會新增至您的專案到 ASP.NET 網頁 ReportViewer 控制項放在您的專案。

- **Microsoft.ReportViewer.Common.dll**︰ 包含在執行階段 ReportViewer 控制項所使用的類別。 它會不會自動新增至您的專案。

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>若要新增 Microsoft.ReportViewer.Common 參照

- 以滑鼠右鍵按一下**您的專案節點**及選取 [**加入參考**.NET] 索引標籤中選取的組件，然後按一下**[確定]**。

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>如要無障礙的組件至本機 ASP.NET 應用程式

1. 在 [**參考資料**] 資料夾中，按一下 [Microsoft.ReportViewer.Common 組件，使其內容會出現在 [屬性] 窗格。

1. 在 [屬性] 窗格中，設定**複製到本機**為 True。

1. 針對 Microsoft.ReportViewer.WebForms 重複步驟 1 和 2。

### <a name="to-get-reportviewer-language-pack"></a>若要取得 ReportViewer 語言套件

1. 從[Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=317386)安裝適當的 Microsoft 報表檢視器 2012 Runtime 可轉散發套件。

1. 從下拉式清單選取語言，頁面會被重新導向至對應的下載管理中心] 頁面。

1. 按一下 [**下載**]，即可開始下載的 ReportViewerLP.exe。

1. 下載 ReportViewerLP.exe 之後，請按一下 [立即安裝，或按一下 [**儲存**] 將其儲存到您的電腦的 [**執行**]。 如果您按一下 [**儲存**]，請記住您用來儲存檔案的資料夾名稱。

1. 找出您用來儲存檔案的資料夾。 以滑鼠右鍵按一下 ReportViewerLP.exe**系統管理員身分執行**]，再按一下**[是]**。

1. 執行之後 ReportViewerLP.exe，您會看到 c:\windows\assembly 具有資源檔案**Microsoft.ReportViewer.Webforms.Resources**和**Microsoft.ReportViewer.Common.Resources**。

### <a name="to-configure-for-localized-reportviewer-control"></a>若要設定的本地化 ReportViewer 控制項

1. 下載並安裝 Microsoft 報表檢視器 2012 Runtime 可轉散發套件，依照上述指定的指示。

1. 建立<language>中的專案及複製資料夾相關聯的資源組件那里檔案。 複製檔案的資源的組件︰ **Microsoft.ReportViewer.Webforms.Resources.dll**和**Microsoft.ReportViewer.Common.Resources.dll**。選取資源的組件的檔案，然後在 [屬性] 窗格中，設定**複製到輸出目錄**]，以 「**永遠複製**」。

1. 設定文化特性與 UICulture web 專案。 如需有關如何設定 ASP.NET 網頁的文化特性和 UI 文化特性的詳細資訊，請參閱[如何︰ 設定 ASP.NET 網頁全球化的文化特性和 UI 文化特性](http://go.microsoft.com/fwlink/?LinkId=237461)。

## <a name="configuring-authentication-and-authorization"></a>設定驗證和授權

ReportViewer 需要適當認證，以使用報表伺服器驗證與認證必須報表伺服器授權，以存取您要的報表。 驗證資訊，請參閱[Reporting Services 報表檢視器] 控制項和 Microsoft Azure 虛擬機器為主的報表伺服器](https://msdn.microsoft.com/library/azure/dn753698.aspx)白皮書 （英文）。

## <a name="publish-the-aspnet-web-application-to-azure"></a>發佈 Azure ASP.NET Web 應用程式

在發佈 Azure ASP.NET Web 應用程式的指示，請參閱[如何︰ 移轉及發佈 Web 應用程式至 Azure 從 Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) [ASP.NET Web 應用程式與](../app-service-web/web-sites-dotnet-get-started.md)[開始使用。

>[AZURE.IMPORTANT] 如果 [新增 Azure 部署專案或新增 Azure 雲端服務] 命令沒有出現在方案總管] 中的快顯功能表中，您可能需要變更專案的目標架構.NET Framework 4。
>
>兩個命令提供基本上相同的功能。 一個或另一個命令會出現在快顯功能表已安裝 Microsoft Azure SDK 的版本而定。

## <a name="resources"></a>資源

[Microsoft 報表](http://go.microsoft.com/fwlink/?LinkId=205399)

[Azure 虛擬機器中的 SQL Server 商務智慧功能](virtual-machines-windows-classic-ps-sql-bi.md)

[使用 PowerShell 來建立 Azure VM 與原生模式報表伺服器](virtual-machines-windows-classic-ps-sql-report.md)

[Reporting Services 報表檢視器] 控制項和 Microsoft Azure 虛擬機器基礎報表伺服器](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)
