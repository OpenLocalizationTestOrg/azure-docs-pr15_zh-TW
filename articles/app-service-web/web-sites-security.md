<properties
    pageTitle="安全 Azure 應用程式服務中的應用程式"
    description="瞭解如何保護的 web 應用程式、 行動應用程式後端或在 Azure 應用程式服務的 API 應用程式。"
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="cephalin"/>


#<a name="secure-an-app-in-azure-app-service"></a>安全 Azure 應用程式服務中的應用程式

本文可協助您保護您的 web 應用程式、 行動應用程式後端或在 Azure 應用程式服務的 API 應用程式快速入門。 

Azure 應用程式服務中的安全性有兩種層級︰ 

- **基礎架構與平台安全性**-信任 Azure 實際執行安全地在雲端中的項目所需的服務。
- **應用程式的安全性**-您需要安全地設計應用程式本身。 包括整合 Azure Active Directory、 如何管理憑證，以及如何您確認您可以安全地與其他服務。 

#### <a name="infrastructure-and-platform-security"></a>基礎架構與平台的安全性
應用程式服務維護 Azure Vm 存放裝置、 網路連線、 web 架構、 管理和整合功能及更多，因為它是主動保護，並且強化經歷加強規範，並檢查持續，請確定︰

- 您的應用程式服務應用程式的兩個網際網路以及其他客戶的 Azure 資源的隔離。
- 機密資料 （例如連線字串） 應用程式服務應用程式與其他 Azure 中的資源 （例如 SQL 資料庫） 資源群組之間的通訊保持在 Azure 內，並不會跨任何網路界限。 永遠加密機密資料。
- 您的應用程式服務應用程式和外部資源，例如 PowerShell 管理、 命令列介面、 Azure Sdk、 REST Api 及混合式部署連線，之間的所有通訊正確都加密。
- 管理保護應用程式服務資源 24 小時制威脅惡意程式碼，從分散式拒絕-的-服務，在-攔截 (MITM)，以及其他威脅。 

如需有關 Azure 中的基礎架構與平台安全性的詳細資訊，請參閱[Azure 信任中心]](/support/trust-center/security/)。

#### <a name="application-security"></a>應用程式的安全性

Azure 保護基礎架構與平台上執行應用程式時，您必須負責來保護您的應用程式本身。 換句話說，您需要開發、 部署及管理您的應用程式碼和內容安全的方式。 不這麼做，您的應用程式碼或內容仍然可能受到威脅例如︰

- SQL 插入
- 工作階段攔截
- 跨網站-指令碼
- 應用程式層級 MITM
- 應用程式層級 DDoS

完整的討論的 web 應用程式的安全性考量是這份文件的範圍之內。 為保護您的應用程式的進一步指南起點，請參閱[開啟 Web 應用程式的安全性專案 (OWASP)](https://www.owasp.org/index.php/Main_Page)，特別是[前 10 項專案。](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)，它會列出目前上方 10 的要徑 web 應用程式安全性漏洞，做為由 OWASP 成員。

## <a name="perform-penetration-testing-on-your-app"></a>執行滲透測試您的應用程式

其中一項以測試您的應用程式服務應用程式中的弱點尚未啟動的最簡單方式是使用[整合 Tinfoil 安全性](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)執行單鍵弱點掃描您的應用程式上。 您可以在容易瞭解報表中，檢視測試結果，並瞭解如何將修正每個階段的逐步指示。

如果您想要執行您自己的滲透測試，或想要使用另一個掃描器套件或提供者，您必須遵循[Azure 滲透測試核准程序](https://security-forms.azure.com/penetration-testing/terms)，並取得先前核准，才能執行所需的滲透測試。

##<a name="https"></a>安全通訊與客戶

如果您是使用**\*。 azurewebsites.net**網域名稱建立應用程式服務應用程式，您可以立即使用 HTTPS SSL 憑證提供所有**\*。 azurewebsites.net**網域名稱。 如果您的網站使用[自訂網域名稱](web-sites-custom-domain-name.md)，您可以在自訂網域的 [[啟用 HTTPS](web-sites-configure-ssl-certificate.md)上載 SSL 憑證。

啟用[HTTPS](https://en.wikipedia.org/wiki/HTTPS)可以協助防止 MITM 攻擊，在您的應用程式和其使用者之間的通訊。

## <a name="secure-data-tier"></a>安全的資料層

所有的連接字串加密跨版面，且只會解密 VM*和*上執行應用程式時，才執行的應用程式上高度與 SQL 資料庫整合應用程式服務。 此外，Azure SQL 資料庫包含許多安全性功能，可協助您保護電腦威脅，包括[在其餘加密](https://msdn.microsoft.com/library/dn948096.aspx)、[一律加密](https://msdn.microsoft.com/library/mt163865.aspx)、[動態資料 Masking](../sql-database/sql-database-dynamic-data-masking-get-started.md)，及[威脅偵測](../sql-database/sql-database-threat-detection-get-started.md)您應用程式的資料。 如果您有機密資料或規範要求，請參閱[保護 SQL 資料庫](../sql-database/sql-database-security.md)如需有關如何保護您的資料。

如果您使用協力廠商的資料庫的提供者，例如 ClearDB，請洽詢直接的安全性最佳的提供者的文件。  

##<a name="develop"></a>安全的開發及部署

### <a name="publishing-profiles-and-publish-settings"></a>發佈設定檔及發佈設定

開發應用程式，執行管理工作，或自動化工作使用公用程式，例如**Visual Studio**、 **Web 矩陣**、 **PowerShell 的 Azure**或**Azure 命令列介面 (Azure CLI)**中，您可以使用 [*發佈設定*的檔案] 或 [*發佈的設定檔*。 這兩種檔案類型驗證 Azure，應該保護來防止未經授權的存取。

* 包含**發佈設定**檔案

    * 您的 Azure 訂閱識別碼

    * 管理憑證，可讓您針對您的訂閱*不需提供帳戶名稱或密碼*執行管理工作。

* **發佈的設定檔**檔案包含

    * 發佈至您的應用程式的資訊

如果您使用的公用程式使用發佈的設定檔或發佈設定檔，匯入到公用程式，然後**刪除**檔案包含發佈設定檔的檔案。 如果您必須保持與其他人處理專案，例如共用檔案，請將其儲存在安全的位置，例如*加密*的目錄，具有限制權限。

此外，您應該要確定受到匯入的認證。 比方說， **PowerShell 的 Azure**和**Azure 命令列介面 (Azure CLI)**匯入的資訊儲存在您的**主目錄**(*~* Linux 或 OS X 系統和 Windows 系統*/users/yourusername* 。)如需額外的安全性，您可能會想要**加密**適合您作業系統使用加密工具提供下列位置。

### <a name="configuration-settings-and-connection-strings"></a>設定的設定和連線字串
是儲存在設定檔中的連接字串、 驗證認證，以及其他機密資訊的一般做法。 很抱歉，這些檔案可能會在您的網站上公開或核取 [將公用存放庫，公開這項資訊。 簡單搜尋[GitHub](https://github.com)，例如，可以發掘公開機密公用存放庫中使用無數的設定檔。

最佳作法是將這項資訊不在您的應用程式的設定檔。 應用程式服務可讓您將屬於執行階段環境的設定資訊儲存為**應用程式設定**] 和 [**連線字串**。 在執行階段透過大部分的程式設計語言的*環境變數*應用程式公開值。 對於.NET 應用程式，這些值會插入您在執行階段的.NET 設定。 除了這些情況下，這些設定會保留加密除非您檢視，或使用[Azure 入口網站](https://portal.azure.com)或公用程式，例如 PowerShell 或 Azure CLI 進行設定。 

設定資訊儲存在應用程式服務可讓應用程式的系統管理員鎖定生產應用程式的機密資訊。 開發人員可以使用應用程式開發的一組個別的設定的設定，設定可自動取代的應用程式服務中的設定。 即使開發人員必須知道設定生產應用程式的密碼。 如需有關如何設定 [應用程式服務中的 [應用程式設定] 和 [連線字串的詳細資訊，請參閱[設定 web 應用程式](web-sites-configure.md)。

### <a name="ftps"></a>FTPS

Azure 應用程式服務，提供安全 FTP 存取檔案系統**FTP**透過應用程式。 這個選項可讓您安全地存取應用程式碼 web 應用程式，以及診斷記錄。 建議您永遠使用 FTP，而不是 FTP。 

您的應用程式的 FTP 連結，請參閱下列步驟︰

1. 開啟[Azure 入口網站](https://portal.azure.com)。
2. 選取 [**全部瀏覽**]。
3. 從**瀏覽**刀中，選取 [**應用程式服務**]。
4. 從**應用程式服務**刀中，選取所要的應用程式。
5. 從應用程式的刀，選取 [**所有設定**]。
6. 從**設定**刀中，選取 [**內容**]。
7. **設定**刀所提供的 FTP 及 FTP 的連結。 

如需有關 FTP 的詳細資訊，請參閱[檔案傳輸通訊協定](http://en.wikipedia.org/wiki/File_Transfer_Protocol)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊的安全性 Azure 平台資訊報告的**安全性事件或不當使用**，或通知 Microsoft，您所執行的**滲透測試**您的網站，請參閱[Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/security/)] 的 [安全性] 區段。

應用程式服務應用程式中的**web.config**或**applicationhost.config**檔案的詳細資訊，請參閱[Azure 應用程式服務 web 應用程式中解除鎖定的設定選項](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)。

如需記錄資訊的應用程式服務應用程式，可用於偵測攻擊，請參閱[啟用診斷記錄功能](web-sites-enable-diagnostic-log.md)。

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="whats-changed"></a>變更的項目

* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)
