<properties
   pageTitle="Azure 資料目錄版本資訊 |Microsoft Azure"
   description="Azure 資料目錄的版本資訊。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-release-notes"></a>Azure 資料目錄版本資訊

## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>2015 年 11 月 20 日的備忘稿發行的 Azure 資料目錄

### <a name="opening-data-sources-in-power-bi-desktop"></a>在 Power BI Desktop 中開啟資料來源

使用從**Azure 資料目錄**入口網站 」 開啟在 Power BI Desktop 」 選項時，使用者可能會遇到的其中一個 Power BI Desktop 應用程式中的兩個問題︰

- 標題為 「 無法開啟文件] 對話方塊會顯示
- Power BI Desktop 應用程式開啟，但檔案似乎是空的

針對每一種情況下，可以下載並安裝最新版的 Power BI Desktop [PowerBI.com](https://powerbi.com)從解決問題。

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>2015 年 11 月 13 日的備忘稿發行的 Azure 資料目錄

### <a name="registering-and-connecting-to-teradata"></a>註冊和連線到 Teradata

當您連線到 Teradata 資料來源的使用者必須已安裝正確的 Teradata ODBC 驅動程式碼，符合所用的軟體位元 （32 位元或 64 位元）。

此 ADC 版本的日期，相容的最新的[windows （版本 15.10） 的 Teradata ODBC 驅動程式](http://downloads.teradata.com/download/connectivity/odbc-driver/windows)是搭配 Office 2013，但不是使用 Office 2016。

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>2015 年 7 月 13 日的備忘稿發行的 Azure 資料目錄

### <a name="registering-and-connecting-to-oracle-database"></a>註冊和連線到 Oracle 資料庫

當您連線到 Oracle 資料庫的資料來源的使用者必須已安裝正確的 Oracle 驅動程式符合所用的軟體位元 （32 位元或 64 位元）。

-   當註冊 Oracle 資料來源執行 32 位元的 Windows 電腦上，將會用於 32 位元 Oracle 驅動程式
-   當註冊 Oracle 資料來源執行 64 位元的 Windows 電腦上，將會用於 64 位元 Oracle 驅動程式
-   連接至 Oracle 資料來源執行 32 位元版本的 Microsoft Office 的電腦上使用 Excel 時，包括在 64 位元 Windows 32 位元 Oracle 驅動程式將會用於
-   連接至 Oracle 資料來源執行 64 位元版本的 Microsoft Office 的電腦上使用 Excel 時，將會用於 64 位元 Oracle 驅動程式

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>註冊及連線至 SQL Server Reporting Services

支援的 SQL Server Reporting Services (SSRS) 資料來源是目前僅適用於 [僅限原生模式伺服器。 支援 SSRS SharePoint 模式中的，將會新增新版本。

### <a name="opening-data-assets-in-excel"></a>開啟在 Excel 中的資料資產

當開啟 Microsoft Excel 中的資料資產，從**Azure 資料目錄**入口網站，可能會提示使用者， **Microsoft Excel 安全性注意事項**] 對話方塊。 這是標準、 預期的行為和使用者可以選取 [**啟用**] 以繼續。

如需詳細資訊，請參閱[啟用或停用連結和來自可疑網站之檔案的安全性警告](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE)。

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy 和原則設定和資料來源註冊

使用者可能會遇到的情況下，他們可以登入 Azure 資料目錄入口網站，但它們，嘗試登入所遇到錯誤訊息，使其無法登入的資料來源註冊工具。

有兩個可能的原因，為此問題的方式︰

**原因 1: Active Directory Federation Services 設定**資料來源註冊工具使用表單驗證驗證 Active Directory 對使用者的登入。 成功的登入表單驗證中必須啟用全域驗證原則 Active Directory 系統管理員。

在某些情況下，只有當使用者是在公司網路上，或只公司網路以外的使用者連線時，可能會發生此錯誤行為。 全域驗證原則可讓內部和外部網路連線分別啟用的驗證方法。 如果表單驗證不會啟用的使用者連線網路時，可能會發生登入錯誤。

如需詳細資訊，請參閱[設定驗證原則](https://technet.microsoft.com/library/dn486781.aspx)。

**原因 2︰ 網路的 proxy 設定**如果在公司網路使用 proxy 伺服器，註冊工具可能無法透過 proxy Azure Active Directory 連線。 使用者可以確保註冊工具編輯工具的設定檔中，以新增至檔案的 [此節︰


      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


若要找出 RegistrationTool.exe.config 檔案，請啟動 [註冊] 工具，然後開啟 [Windows 工作管理員公用程式。 在 [工作管理員] 的 [詳細資料] 索引標籤上 RegistrationTool.exe 上按一下滑鼠右鍵，然後從快顯功能表中選擇 [開啟檔案位置。
