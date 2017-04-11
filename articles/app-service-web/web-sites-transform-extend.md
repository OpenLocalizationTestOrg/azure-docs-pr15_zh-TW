<properties
    pageTitle="Azure 進階組態和延伸模組的應用程式服務 web 應用程式"
    description="若要轉換 ApplicationHost.config 檔案 Azure 應用程式服務 web 應用程式中的，並新增私人延伸啟用自訂管理動作，請使用 XML 文件 Transformation(XDT) 宣告。"
    authors="cephalin"
    writer="cephalin"
    editor="mollybos"
    manager="wpickett"
    services="app-service"
    documentationCenter=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="azure-app-service-web-app-advanced-config-and-extensions"></a>Azure 進階組態和延伸模組的應用程式服務 web 應用程式

藉由使用[XML 文件轉換](http://msdn.microsoft.com/library/dd465326.aspx)(XDT) 宣告，您可以在 web 應用程式中 Azure 應用程式服務轉換[ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig)檔案。 您也可以使用 XDT 宣告新增私人副檔名啟用自訂 web 應用程式管理動作。 本文包含範例 PHP 管理員 web 應用程式副檔名為可透過網頁介面 PHP 設定的管理。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>透過 ApplicationHost.config 的進階的組態
應用程式服務平台提供彈性和控制 web 應用程式設定。 雖然標準 IIS ApplicationHost.config 設定檔服務應用程式中直接編輯，在平台支援基礎 XML 文件轉換 (XDT) 的宣告式 ApplicationHost.config 轉換模型。

若要利用此轉換功能，您可以建立 ApplicationHost.xdt 檔案 XDT 內容與網站根目錄 (d:\home\site) [Kudu 主控台](https://github.com/projectkudu/kudu/wiki/Kudu-console)中檢視下的位置。 您可能需要重新啟動 Web 應用程式，變更才會生效。

下列 applicationHost.xdt 範例會示範如何使用 PHP 5.4 web 應用程式新增自訂的環境變數。

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.webServer>
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
                        </environmentVariables>
                    </application>
                </fastCgi>
        </system.webServer>
    </configuration>


使用從下 LogFiles\Transform FTP 根目錄轉換狀態和詳細資料的記錄檔。

如需其他範例，請參閱[https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)。

**附註**<br />
項目清單中的下的 [模組`system.webServer`無法移除或重新排列順序，但可能會出現新增至清單。


##<a id="extend"></a>擴充 web 應用程式

###<a id="overview"></a>私人 web 應用程式擴充功能的概觀

應用程式服務支援 web 應用程式副檔名為系統管理動作的擴充功能。 事實上，某些應用程式服務的平台功能實作為預先安裝的副檔名。 時無法修改預先安裝的平台副檔名，您可以建立並設定私人擴充 web 應用程式。 這項功能也依賴 XDT 宣告。 建立私人的 web 應用程式副檔名的主要步驟如下︰

1. Web 應用程式延伸**內容**︰ 建立應用程式服務支援的任何 web 應用程式
2. Web 應用程式延伸**宣告**︰ 建立 ApplicationHost.xdt 檔案
3. Web 應用程式副檔名**部署**︰ 置於底下的 [SiteExtensions 資料夾的內容`root`

Web 應用程式的內部連結指向相對於 ApplicationHost.xdt 檔案中所指定的應用程式路徑的路徑。 ApplicationHost.xdt 檔案的任何變更都需要 web 應用程式資源。

**附註**︰ [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)在有這些關鍵元素的其他資訊。

詳細的範例會包含要說明的步驟建立並啟用私人 web 應用程式延伸。 可透過[https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager)下載 PHP 管理員範例所示的程式碼。

###<a id="SiteSample"></a>Web 應用程式副檔名範例︰ PHP 管理員

PHP 管理員不允許 web 應用程式管理員輕鬆檢視並設定其使用 web 介面，而不由直接修改 PHP.ini 檔案的 PHP 設定 web 應用程式副檔名。 常見的設定檔的 PHP 包含 php.ini 檔案位於 [Program Files 及。 user.ini 檔案位於您的 web 應用程式的根資料夾中。 副檔名為 PHP 管理員由於 php.ini 檔案不是直接編輯應用程式服務平台上，使用。 若要套用設定變更 user.ini 檔案。

####<a id="PHPwebapp"></a>PHP 管理員 web 應用程式

以下是 PHP 管理員部署的 [首頁] 頁面︰

![TransformSitePHPUI][TransformSitePHPUI]

如您所見，web 應用程式副檔名為就像一般的 web 應用程式，但與其他 ApplicationHost.xdt 檔案放在 web 應用程式 （ApplicationHost.xdt 檔案相關的更多詳細資料可在本文的下一節中） 的根資料夾中。

使用 Visual Studio ASP.NET MVC 4 Web 應用程式範本建立 PHP 管理員副檔名。 從方案總管] 中的 [追蹤] 檢視會顯示 PHP 管理員副檔名的結構。

![TransformSiteSolEx][TransformSiteSolEx]

檔案 I/O 所需的唯一特殊邏輯是表示 web 應用程式的 wwwroot 目錄的位置。 下列範例所示，環境變數 [首頁] 」 會指出 web 應用程式的根路徑，並 wwwroot 路徑可以由附加 「 site\wwwroot 」:

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    }


您的目錄路徑之後，您可以使用一般檔案 I/O 作業讀取和寫入檔案。

提醒事項副檔名為 web 應用程式的一個點將內部連結之處理方式。  如果您有提供絕對的路徑至內部的連結，在您的 web 應用程式上的 HTML 檔案中的任何連結，您必須確定這些連結會加上您的副檔名為您的根目錄。 此需要因為您分機根現在 」 /`[your-extension-name]`/ 」 而不會出現只 「 / 」，因此任何內部連結必須隨之更新。 例如，假設您的程式碼會包含下列連結︰

`"<a href="/Home/Settings">PHP Settings</a>"`

Web 應用程式延伸的組件的連結時，連結必須是下列格式︰

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

您可以透過 [解決這項需求使用只相對路徑 web 應用程式中，或如果 ASP.NET 應用程式，使用`@Html.ActionLink`方法可為您建立的適當連結。

####<a id="XDT"></a>ApplicationHost.xdt 檔案

您的 web 應用程式延伸模組的程式碼進入下 %HOME%\SiteExtensions\[您副檔名]。 我們會呼叫此副檔名根目錄。  

若要註冊您的 web 應用程式擴充 applicationHost.config 檔案，您必須將檔案副檔名根目錄中名為 ApplicationHost.xdt。 ApplicationHost.xdt 檔案的內容應該如下所示︰

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

您選取作為副檔名名稱名稱應為您的副檔名根資料夾中有相同的名稱。

這是新增至新的應用程式路徑的效果`system.applicationHost`SCM 網站下的 [網站] 清單。 SCM 網站是使用特定的存取權的認證網站管理結束點。 有 URL `https://[your-site-name].scm.azurewebsites.net`。  

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

###<a id="deploy"></a>Web 應用程式副檔名部署

若要安裝您的 web 應用程式擴充功能，您可以使用 FTP 複製的 web 應用程式的所有檔案`\SiteExtensions\[your-extension-name]`您要安裝擴充 web 應用程式] 資料夾。  請務必將 ApplicationHost.xdt 檔複製到此位置以及。 重新啟動您的 web 應用程式啟用延伸模組。

您應該能夠看到在 web 應用程式擴充功能︰

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

請注意，URL 看起來就像 URL web 應用程式，使用 HTTPS，但其包含 「.scm 」。

若要新增應用程式設定與索引鍵在開發和調查期間停用所有私人 （不預先安裝的） 擴充 web 應用程式可能`WEBSITE_PRIVATE_EXTENSIONS`和值`0`。

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 
