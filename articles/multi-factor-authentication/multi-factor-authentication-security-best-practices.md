<properties 
    pageTitle="使用 Azure MFA 的安全性最佳做法"
    description="這份文件提供 Azure MFA 使用 Azure 帳戶的最佳做法"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>安全性 Azure 多重因素驗證使用 Azure AD 帳戶的最佳作法

增強驗證程序時，多重因素驗證是慣用的選擇，大部分的組織。 Azure 多重因素驗證 (MFA) 可讓公司符合其安全性與規範要求時提供使用者簡單的登入體驗。 本文將討論 Azure MFA 採用規劃時，您應該考慮的一些最佳作法。

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Azure 多重因素驗證，在雲端的最佳作法
若要為所有使用者提供多重因素驗證，並取得 Azure 多重因素驗證所提供的擴充功能的優點，您必須啟用 Azure 多重因素驗證，在所有使用者。  這是使用下列其中一項︰

- Azure AD 進階版或企業版行動性套件
- 多重因素驗證提供者

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Azure AD 進階/企業行動性套件

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

在使用 Azure AD 進階版或企業版行動性套件雲端採用 Azure MFA 建議的第一步是將授權指派給使用者。  Azure 多重因素驗證是這些套件的一部分，因此您的組織不需要任何其他可延伸至所有使用者的多重因素驗證功能。

設定多重因素驗證時考慮下列動作︰

- 您不需要建立多重因素驗證提供者。  Azure AD 進階版和企業版行動性套件隨附 Azure 多重因素驗證。  如果您建立驗證提供者，您可能會收到雙付費。
- Azure AD Connect 是只需求，如果您與 Azure AD 目錄同步處理您的內部部署 Active Directory 環境。 如果您只使用一個未同步處理內部部署的 Active Directory 執行個體的 Azure AD 目錄，您不需要 Azure AD Connect。


### <a name="multi-factor-auth-provider"></a>多重因素驗證提供者

![多重因素驗證提供者](./media/multi-factor-authentication-security-best-practices/authprovider.png)

如果您沒有 Azure AD 進階版或企業版行動性套件在雲端的採用 Azure MFA 建議的第一步是建立 MFA 驗證提供者。 雖然 MFA 是依預設，當您為您的組織部署 MFA 有 Azure Active Directory，全域管理員必須先將延伸為所有使用者的多重因素驗證功能，您需要多重因素驗證提供者。
選取驗證提供者，您必須選取目錄，考慮下列事項︰

- 您不需要建立多重因素驗證提供者的 Azure AD 目錄。
- 您必須與 Azure AD 目錄中建立關聯多重因素驗證提供者，如果您想要將多重因素驗證延伸到所有的使用者及/或希望全域系統管理員，才能利用功能，例如管理入口網站、 自訂的問候語及報表。
- 如果您與 Azure AD 目錄同步處理您的內部部署 Active Directory 環境，DirSync 或 AAD Sync 是只要求。 如果您只使用一個未同步處理內部部署的 Active Directory 執行個體的 Azure AD 目錄，您不需要 DirSync 或 AAD Sync。
- 如果您有 Azure AD 進階版或企業版行動性套件，您不需要建立多重因素驗證提供者。 您只需要指派授權的使用者，然後您就可以開始 MFA 使用者開啟。
- 請務必選擇正確的使用狀況模型，為您的企業 （授權或每週報告啟用的使用者），當您選取 [使用模型無法變更。

### <a name="user-account"></a>使用者帳戶
使用者帳戶時啟用 MFA 使用者時，可以會在下列三種核心狀態︰ 停用、 啟用或強制執行。
使用以下的指導方針，以確保您使用您的部署最適當的選項︰

- 當使用者的狀態設定為 [停用時，該使用者不使用多重因素驗證。 這是預設狀態。
- 當使用者的狀態設定為啟用時，這表示使用者已啟用，但尚未完成註冊程序。 系統會提示完成程序，在下一個登入。 此設定不會影響非瀏覽器應用程式。 所有應用程式會繼續運作，除非已完成註冊程序。
- 當使用者的狀態設定為強制時，這表示使用者可能或未完成註冊。 如果已經完成註冊程序，他們會使用多重因素驗證。 否則，您會提示使用者完成註冊程序，在下一個登入。 此設定會影響非瀏覽器應用程式。 這些應用程式會無法運作，除非建立和使用應用程式密碼。
- 使用使用者通知範本可用，請參閱[快速入門 Azure 多重因素驗證，在雲端](multi-factor-authentication-get-started-cloud.md)，傳送電子郵件給您的使用者有關 MFA 採用。

### <a name="supportability"></a>支援性

由於大部分的使用者慣用的只密碼進行驗證，請務必貴公司將此程序有關的所有使用者的線上狀態。 此線上狀態降低使用者會來電服務台次要問題 MFA 相關的機率。
然而，有一些案例暫時停用 MFA 必要。 若要瞭解如何處理這些案例使用以下的指導方針︰

- 請確定您的技術支援人員會訓練位置的行動應用程式或電話不能接收的通知或電話通話的控點分析藍本，因此使用者無法登入。 他們可以啟用一次性略過] 選項，以允許使用者進行驗證一次 [略過 「 多重因素驗證。 略過暫時，而在指定的秒數後到期。
- 如有需要，您可以利用 Azure MFA 中的信任 IPs 功能。 此功能可讓系統管理員的受管理或同盟租用戶能力略過多因素驗證的使用者，從公司的近端內部網路登入。 功能可供有 Azure AD 進階版、 企業版行動性套件或 Azure 多重因素驗證授權的 Azure AD 租用戶。


## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Azure 多重因素驗證內部部署的最佳作法
如果貴公司決定要使用自己的基礎結構 MFA，您必須部署 Azure 多重因素驗證伺服器內部部署。 下圖中顯示 MFA 伺服器元件︰

![多重因素驗證提供者](./media/multi-factor-authentication-security-best-practices/server.png)
*預設未安裝** 但未啟用預設安裝


Azure 多重因素驗證伺服器可用來保護雲端資源 Azure AD 帳戶以存取的內部部署資源。  不過這僅能使用同盟完成。  也就是您必須有 AD FS 並將其與您 Azure AD 租用戶同盟。
設定多重因素驗證伺服器時考慮下列動作︰

- 如果您是保護 Azure AD 資源使用 Active Directory Federation Services，，然後執行第 1 的因素驗證的內部部署使用 AD FS 和第 2 因子變異數為內部執行接受宣告。
- 無法要求，不過執行 AD FS Windows Server 2012 R2 上必須安裝多重因素驗證介面卡 AD fs，會 AD FS 同盟伺服器上安裝 Azure 多重因素驗證伺服器。 您可以在不同的電腦上安裝伺服器，只要是受支援的版本，並 AD FS 同盟伺服器上分別安裝 AD FS 介面卡。 另外安裝介面卡，請參閱下列程序，如需相關指示。
- 多重因素驗證 AD FS 介面卡安裝精靈建立您的 Active Directory 中名為 PhoneFactor 管理員安全性群組，並 AD FS 服務帳戶的同盟服務給此群組。建議您先確認您網域控制站確實建立 PhoneFactor 管理員群組] 和 [AD FS 服務帳戶是此群組的成員。 如有需要，請以手動方式在您的網域控制站 PhoneFactor 管理員群組新增 AD FS 服務帳戶。

### <a name="user-portal"></a>使用者入口網站
在 [網際網路資訊伺服器 (IIS) 網站，可讓自助功能，並提供一組完整的使用者管理功能中，執行此入口網站。 若要設定此元件使用以下的指導方針︰

- IIS 6 個或更大資格
- ASP.NET v2.0.507207 必須安裝並註冊
- 此伺服器可以部署周邊網路中。



### <a name="app-passwords"></a>應用程式密碼
如果您的組織同盟使用 Azure AD SSO，您將會使用 Azure MFA，然後注意下列時使用應用程式密碼 (請記住，這只適用於同盟 (SSO) 使用):

- 應用程式密碼由 Azure AD 驗證，因此略過同盟。 設定應用程式密碼時，才會使用同盟。
- 同盟 (SSO) 的使用者的密碼會儲存在組織的識別碼。 使用者離職，該資訊流程組織使用 DirSync 即時的識別碼。 帳戶停用/刪除可能需要 3 小時，若要同步處理，在 Azure AD 延遲停用/刪除應用程式密碼。
- 內部部署用戶端存取控制設定不會被應用程式密碼
- 內部部署驗證記錄 / 稽核功能，都可以使用應用程式密碼
- 更多使用者教育版 Microsoft Lync 2013 用戶端資格。
- 某些進階架構設計可能會要求您使用多重因素驗證與用戶端，根據驗證位置時，使用組織的使用者名稱和密碼與應用程式密碼的組合。 驗證的內部部署基礎結構的用戶端，您可以使用組織的使用者名稱和密碼。 Azure AD 驗證的用戶端，您可以使用應用程式密碼。
- 根據預設，使用者無法建立應用程式密碼，如果您的公司需要的或如果您要讓使用者建立應用程式密碼在某些情況下，確定已選取 [建立應用程式密碼以登入非瀏覽器應用程式的選項允許使用者。

## <a name="additional-considerations"></a>其他考量
若要瞭解部署內部部署的一些其他考量和會每個元件的最佳作法，使用下列清單︰

方法|描述
:------------- | :------------- |
[Active Directory Federation 服務](multi-factor-authentication-get-started-adfs.md)|設定與 AD FS Azure 多重因素驗證的相關資訊。
[針對 Authenticaton](multi-factor-authentication-get-started-server-radius.md)|  安裝和設定 RADIUS Azure MFA 伺服器的相關資訊。
[IIS 驗證](multi-factor-authentication-get-started-server-iis.md)|安裝及設定 Azure MFA 伺服器 iis 的相關資訊。
[Windows Authenticaton](multi-factor-authentication-get-started-server-windows.md)|  安裝及設定 Azure MFA Server 使用 Windows 驗證的相關資訊。
[LDAP 驗證](multi-factor-authentication-get-started-server-ldap.md)|安裝和設定 LDAP 驗證 Azure MFA 伺服器的相關資訊。
[遠端桌面閘道並使用 RADIUS Azure 多重因素驗證伺服器](multi-factor-authentication-get-started-server-rdg.md)|  安裝及設定 Azure MFA 伺服器使用 RADIUS 遠端桌面閘道器的相關資訊。
[與 Windows Server 的 Active Directory 同步處理](multi-factor-authentication-get-started-server-dirint.md)|安裝和設定 Active Directory 和 Azure MFA 伺服器之間的同步處理的相關資訊。
[部署 Azure 多重因素驗證伺服器行動應用程式的 Web 服務](multi-factor-authentication-get-started-server-webservice.md)|安裝和設定 Azure MFA 伺服器 web 服務的相關資訊。
[進階的 VPN Azure 多重因素驗證設定](multi-factor-authentication-advanced-vpn-configurations.md)|設定使用 LDAP 或半徑 Cisco ASA Citrix Netscaler 與 Juniper/脈衝安全 VPN 應用裝置的相關資訊。


## <a name="additional-resources"></a>其他資源
雖然這份文件醒目提示的 Azure MFA 的一些最佳作法，有其他資源，您也可以使用時規劃 MFA 部署。 下列清單具有金鑰文章也許可以協助您在此程序︰

- [Azure 多重因素驗證中的報表](multi-factor-authentication-manage-reports.md)
- [Azure 多重因素驗證設定體驗](multi-factor-authentication-end-user-first-time.md)
- [Azure 多重因素驗證常見問題集](multi-factor-authentication-faq.md)
