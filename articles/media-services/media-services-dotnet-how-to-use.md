<properties 
    pageTitle="如何設定電腦的媒體服務使用.NET 開發" 
    description="瞭解使用.NET 媒體服務 SDK 的媒體服務的先決條件。 也了解如何建立 Visual Studio 應用程式。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/24/2016"  
    ms.author="juliako"/>

#<a name="media-services-development-with-net"></a>使用.NET 媒體服務開發

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

本主題探討如何啟動開發使用.NET 媒體服務應用程式。

**Azure 媒體服務.NET SDK**文件庫，可讓您針對媒體使用.NET 的服務。 若要使其更容易使用.NET 開發提供**Azure 媒體服務.NET SDK 擴充功能**文件庫。 此文件庫包含一組擴充方法和協助函數，簡化.NET 程式碼。 兩個文件庫，可透過**NuGet**和**GitHub**。


##<a name="prerequisites"></a>必要條件

-   在新的或現有的 Azure 訂閱媒體服務帳戶。 請參閱[如何建立媒體服務帳戶](media-services-portal-create-account.md)的主題。
-   作業系統︰ Windows 10、 Windows 7、 Windows 2008 R2 或 Windows 8。
-   .NET framework 4.5。
-    Visual Studio 2015、 Visual Studio 2013、 Visual Studio 2012 或 Visual Studio 2010 SP1 （Professional、 Premium、 Ultimate 或 Express）。


##<a name="create-and-configure-a-visual-studio-project"></a>建立及設定 Visual Studio 專案

本節說明如何在 Visual Studio 中建立專案並將其設為媒體服務開發。  在這個案例的專案是 C# Windows 主控台應用程式，但此處所示的相同設定步驟適用於其他類型的媒體服務應用程式 （例如，Windows 表單應用程式或 ASP.NET Web 應用程式），您可以建立的專案。

本節說明如何使用**NuGet**新增媒體服務.NET SDK 及其他相關文件庫。

或者，您可以從 GitHub （[github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services)和[github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)） 取得的最新的媒體服務.NET SDK 位元、 建置解決方案，以及新增用戶端專案的參照。 請注意，所有必要的相依性取得下載自動展開。

1. 在 Visual Studio 2010 SP1 或更新與版本中建立新 C# 主控台應用程式。 輸入**名稱**、**位置**，以及**方案名稱**，然後再按一下 [確定]。

2. 建置解決方案。

2. 使用**NuGet**安裝並新增**Azure 媒體服務.NET SDK 擴充功能**。 安裝此套件，也會安裝**媒體服務.NET SDK**並新增所有其他必要的相依性。

    請確定您已安裝的 NuGet 的最新版本。 如需詳細資訊和安裝指示，請參閱[NuGet](http://nuget.codeplex.com/)。

2. 在方案總管中，以滑鼠右鍵按一下專案的名稱，然後選擇 [管理 NuGet 封包...

    [管理 NuGet 套件] 對話方塊隨即出現。

3. 在線上的圖庫中，搜尋 Azure MediaServices 延伸選擇 Azure 媒體服務.NET SDK 擴充功能，再按一下 [安裝] 按鈕。

    修改專案，並新增媒體服務.NET SDK 擴充功能媒體服務.NET sdk，您可以與其他相依組件的參照。

4. 若要提升取得更簡潔的開發環境，請考慮啟用 NuGet 套件還原。 如需詳細資訊，請參閱[NuGet 套件還原 」](http://docs.nuget.org/consume/package-restore)。

3. 新增**System.Configuration**組件的參考。 此組件包含 System.Configuration。用來存取設定檔案 (例如，App.config) **ConfigurationManager**類別。

    若要新增使用 [管理參考資料] 對話方塊的參照，以滑鼠右鍵按一下 [方案總管] 中的專案名稱。 然後選取 [新增和參考資料]。

    管理參照] 對話方塊隨即出現。

4. .NET framework 組件，在尋找選取 System.Configuration 組件，並按 [確定]。
5. 開啟 App.config 檔案 （如果未新增根據預設，檔案新增至您的專案） 並將*和 appSettings*區段新增至該檔案。     
設定您 Azure 媒體服務帳戶名稱和帳戶金鑰] 的值，如下列範例所示。

    若要尋找 [名稱] 和 [索引鍵的值，請移至 Azure 入口網站，然後選取您的帳戶。 [設定] 視窗會顯示在右側。 在 [設定] 視窗中，選取 [索引鍵。 每個文字方塊旁的圖示上按一下 [將值複製到剪貼簿] 系統。


        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

6. 下列程式碼會覆寫現有**使用**陳述的式的開頭 Program.cs 檔案。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

此時，您準備好開始開發媒體服務應用程式。    


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
