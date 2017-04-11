<properties
    pageTitle="Azure 多重因素驗證常見問題集"
    description="提供常見問題集和 Azure 多重因素驗證相關的解答的清單。 多重因素驗證是驗證需要個以上的使用者名稱和密碼的使用者的身分識別的方法。 會提供額外的使用者登入及交易的安全性層級。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="azure-multi-factor-authentication-faq"></a>Azure 多重因素驗證常見問題集


此常見問題集解答關於 Azure 多重因素驗證及使用多重因素驗證服務，包括付款的模型與合用性的相關問題。

## <a name="general"></a>一般

**問︰ Azure 多重因素驗證伺服器如何處理使用者資料？**

多重因素驗證伺服器，使用者資料會儲存只能在內部部署伺服器上。 沒有常設使用者資料儲存在雲端中。 當使用者執行雙步驟驗證時，則多重因素驗證伺服器會傳送資料至 Azure 多重因素驗證雲端服務進行驗證。 多重因素驗證伺服器和多重因素驗證雲端服務之間的通訊使用連接埠 443 輸出上的安全通訊端層 (SSL) 或傳輸層安全性 (TLS)。

報表時驗證邀請也會傳送至雲端服務，資料會收集的驗證及使用方式。 包含兩個步驟驗證記錄中的資料欄位所示︰

- **唯一識別碼**（[使用者名稱或內部部署多重因素驗證伺服器識別碼）
- **姓氏和名字**（選用）
- **電子郵件地址**（選用）
- **電話號碼**（如果使用的語音通話或 SMS 驗證）
- **裝置權杖**（時使用行動應用程式驗證）
- **驗證模式**
- **驗證結果**
- **多重因素驗證伺服器名稱**
- **多重因素驗證伺服器 IP**
- **用戶端 IP**（如果有的話）

您可以設定多重因素驗證伺服器選用的功能變數。

驗證結果 （成功或拒絕），如果其拒絕，原因與驗證資料一起儲存，並提供驗證及使用情況報表。


## <a name="billing"></a>帳單

參照[多重因素驗證價格的頁面](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)，可以回答大部分的帳單問題。

**問︰ 我付費電話或文字訊息的組織會用於驗證我的使用者嗎？**

組織不個別放置的行動電話或文字會透過 Azure 多重因素驗證將使用者傳送的郵件。 行動電話或文字訊息接收，根據自己的個人電話服務，可能需要支付電話擁有者。

**問︰ 每位使用者帳單模型收費根據設定為使用多重因素驗證的使用者數目或執行驗證的使用者數目的用途？**

帳單根據設定為使用多重因素驗證的使用者數目。

**問︰ 多重因素驗證計費如何運作？**

當您使用模型、 Azure MFA 「 每個使用者 」 或 「 驗證 」 是消耗型的資源。 任何費用被付費組織 Azure 訂閱，就像虛擬機器、 網站等。

當您使用的授權模型時，Azure 多重因素驗證授權會購買，則指派給使用者，就像 for Office 365 和其他訂閱產品。

**問︰ 有免費版 Azure 多重因素驗證管理員嗎？**

在某些情況下，[是]。 多重因素驗證 Azure 系統管理員提供免費 Azure MFA 功能的子集合。 這項優惠適用於沒有連結到消耗型 Azure 多重因素驗證提供者的 Azure Active Directory 執行個體的 Azure 全域管理員群組的成員。 使用多重因素驗證提供者升級所有管理員和目錄中，設定為使用完整版的 Azure 多重因素驗證多重因素驗證的使用者。

**問︰ 有 Office 365 使用者的免費版 Azure 多重因素驗證嗎？**

在某些情況下，[是]。 多重因素驗證，在 Office 365 的使用者提供免費 Azure MFA 功能的子集合。 這項優惠適用於 Office 365 授權指派，當消耗型 Azure 多重因素驗證提供者未已連結至對應的 Azure Active Directory 執行個體的使用者。 使用多重因素驗證提供者升級所有管理員和目錄中，設定為使用完整版的 Azure 多重因素驗證多重因素驗證的使用者。

**問︰ 可以切換每位使用者與每個驗證消耗帳單模型之間隨時的組織嗎？**

建立資源時，您的組織選擇付款的模型。 資源佈建後，您無法變更付款的模型。 不過，您可以建立另一個多重因素驗證資源取代原始圖片。 使用者設定與設定選項不能轉移到新的資源。

**問︰ 可以組織切換消耗計費和授權模型之間隨時嗎？**

授權新增到已有每位使用者 Azure 多重因素驗證提供者的目錄時, 消耗型計費也會減少所擁有的授權數。 如果設定為使用多重因素驗證的所有使用者都有指派的授權，管理員可以刪除 Azure 多重因素驗證提供者。

您無法混合安裝授權模型的每個驗證消耗帳單。 每個驗證多重因素驗證提供者連結至目錄，組織被付費的所有多重因素驗證驗證要求，不論所擁有的任何授權。

**問︰ 我的組織沒有使用，並使用 Azure 多重因素驗證身分識別的同步處理嗎？**

如果組織是使用消耗為基礎的帳單模型，則不需要 Azure Active Directory。 連結至目錄的多重因素驗證提供者是選擇性的。 如果您的組織未連結至目錄，它可以部署 Azure 多重因素驗證伺服器或 Azure 多重因素驗證 SDK 內部部署。

Azure Active Directory 需要授權模型，因為當您購買並將其指派給使用者，在目錄中，將會新增至目錄的授權。


## <a name="usability"></a>合用性

**問: 「 使用者做什麼如果對方沒有收到回應對方的電話，或如果不是，使用者可以使用手機？**

如果使用者已設定備份的電話，他們應該再試一次，然後選取 [登入] 頁面上出現提示時，電話。 如果使用者沒有設定的另一種方法，組織的系統管理員可以更新指派給使用者的主要電話的號碼。


**問: 「 系統管理員做什麼如果使用者連絡系統管理員的使用者無法再存取帳戶的相關？**

系統管理員可以要求他們重新進行註冊程序重設使用者的帳戶。 進一步瞭解[管理使用者和裝置設定，與在雲端的 Azure 多重因素驗證](multi-factor-authentication-manage-users-and-devices.md)。

**問︰ 有什麼作用系統管理員？ 如果使用應用程式密碼的使用者的電話遺失或竊取**

管理員可以刪除該使用者的所有應用程式密碼來防止未經授權的存取。 使用者擁有取代裝置之後，使用者可以重新建立密碼。 進一步瞭解[管理使用者和裝置設定，與在雲端的 Azure 多重因素驗證](multi-factor-authentication-manage-users-and-devices.md)。

**問︰ 如果使用者無法登入非瀏覽器應用程式？**

設定為使用多重因素驗證的使用者需要應用程式密碼以登入有些非瀏覽器應用程式。 使用者需要清除 （刪除） 登入資訊、 重新啟動應用程式，並使用他們的使用者名稱和應用程式密碼登入。

取得有關建立應用程式的密碼和其他[協助應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)的詳細資訊。


>[AZURE.NOTE] Office 2013 用戶端的現代化驗證
>
> （包括 Outlook） office 2013 用戶端支援新的驗證通訊協定。 您可以設定為支援多重因素驗證的 Office 2013。 您設定 Office 2013 之後，就不需要的 Office 2013 用戶端應用程式密碼。 如需詳細資訊，請參閱[Office 2013 的現代化驗證公用預覽公告](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

**問︰ 有什麼作用使用者？ 如果使用者沒有接收文字訊息，或如果使用者回覆雙向文字訊息，但驗證時發生逾時**

發表的文字訊息，並不保證回條的回覆雙向 SMS 因為無法控制可能會影響到的服務可靠性的因素。 這些因素包括目的地國家/地區、 行動電話通信業者及訊號強度。

遇到困難可靠接收文字訊息的使用者應改為選取的行動應用程式或電話方法。 行動應用程式會收到通知，同時在行動電話並 Wi-fi 連線。 此外，即使裝置有完全沒有訊號行動應用程式可以產生驗證碼。 使用[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、 [Android](http://go.microsoft.com/fwlink/?Linkid=825072)，以及[IOS](http://go.microsoft.com/fwlink/?Linkid=825073)Microsoft 驗證應用程式。

如果您必須使用文字訊息，建議使用單向 SMS，而不是雙向 SMS 盡可能。 單向 SMS 可靠，而其可防止使用者會產生的回覆傳送從另一個國家/地區的文字訊息的全域 SMS 費用。


**問︰ 可以使用硬體權杖 Azure 多重因素驗證伺服器？**

如果您使用的 Azure 多重因素驗證伺服器，您可以匯入第三方開啟驗證 （誓言） 以時間為基礎的一次性密碼 (TOTP) 權杖，，然後將其用於雙步驟驗證。

您可以使用的是誓言 TOTP 權杖，如果您將機密的金鑰檔案放在 CSV 檔案匯入至 Azure 多重因素驗證伺服器 ActiveIdentity 權杖。 您可以使用誓言權杖使用 Active Directory Federation Services (ADFS)，遠端驗證電話撥入使用者服務 (RADIUS) 用戶端系統可以處理存取挑戰回應時，Internet 資訊伺服器 (IIS) 表單型驗證。

您可以匯入協力廠商誓言 TOTP 權杖，使用下列格式︰  
- 可攜式對稱金鑰容器 (PSKC)  
- 如果檔案包含序列與私密金鑰基底 32 格式的時間間隔的 CSV  

**問︰ 可以使用 Azure 多重因素驗證伺服器的安全性終端機服務？**

是的但如果您是使用 Windows Server 2012 R2 或更新版本中，僅使用遠端桌面閘道 （RD 閘道）。

在 Windows Server 2012 R2 中的安全性變更已變更 Azure 多重因素驗證伺服器連線到 Windows Server 2012 及更早版本中的本機安全性授權 (LSA) 安全性套件的方式。 在 Windows Server 2012 中或更舊版本的終端機服務的版本，您可以[安全應用程式，使用 Windows 驗證](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)。 如果您使用的 Windows Server 2012 R2，您會需要 RD 閘道器。

**問︰ 為什麼要使用者接收多重因素驗證通話從匿名的來電者設定來電後？**

多重因素驗證的來電放置到公用電話網路，有時候路由透過不支援來電者識別碼航空公司 因此，來電不保證，即使多重因素驗證系統永遠傳送。


## <a name="errors"></a>錯誤

**問︰ 該使用者如何如果時使用行動應用程式通知，他們會看到 「 驗證要求不是用於啟動帳戶 」 錯誤訊息？**

對方執行此程序來移除其帳戶從行動應用程式，然後再次新增︰

1. 移至 [ [Azure 入口網站設定檔](https://account.activedirectory.windowsazure.com/profile/)，然後使用您的組織帳戶登入。
2. 選取**額外的安全性驗證**。
4. 從行動應用程式中移除現有的帳戶。
5. 按一下 [**設定**]，然後依照指示重新設定行動應用程式。


**問︰ 該使用者如何如果登入的非瀏覽器應用程式時，就會看到 0x800434D4L 錯誤訊息？**

目前，使用者可以使用額外的安全性驗證只能與應用程式與服務的使用者可以透過瀏覽器存取。 非瀏覽器 （也稱為*豐富型用戶端應用程式*） 安裝的應用程式會在本機電腦上，例如 Windows PowerShell 上無法使用需要額外的安全性驗證的帳戶。 在此情況下，使用者可能會看到產生 0x800434D4L 錯誤的應用程式。

因應措施這是有不同的使用者帳戶的管理員相關和非管理者作業。 之後，您可以連結之間您的管理員帳戶和非管理帳戶的信箱，好讓您可以登入 Outlook 使用您的非管理員帳戶。 如需更多相關資訊，進一步瞭解如何[讓系統管理員能開啟與檢視使用者信箱的內容](http://help.outlook.com/141/gg709759.aspx?sl=1)。

## <a name="next-steps"></a>後續步驟

如果此處未回答您的問題，請將它留在頁面底部的註解。 或者，以下是一些取得說明的其他選項︰


**問︰ 如何能協助 Azure 多重因素驗證？**

- 搜尋[Microsoft 支援知識庫](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)技術常見問題的解決方案。

- 搜尋和瀏覽技術的問題及答案的社群，或提出您自己的[Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)的問題。

- 如果您是舊版的 PhoneFactor 客戶，您有任何問題或需要協助您重設密碼，請使用[重設密碼](mailto:phonefactorsupport@microsoft.com)的連結來開啟支援案例。

- 連絡支援人員透過[Azure 多重因素驗證伺服器 (PhoneFactor) 支援](https://support.microsoft.com/oas/default.aspx?prid=14947)。 當與我們連絡，還是有幫助您可以包含的資訊，瞭解您的問題，盡可能如果。 您可以提供的資訊包含您可在此看到錯誤、 特定錯誤碼、 特定工作階段識別碼，以及所看到的錯誤的使用者識別碼的頁面。
