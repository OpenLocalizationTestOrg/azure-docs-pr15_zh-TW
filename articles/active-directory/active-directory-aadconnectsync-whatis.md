<properties
    pageTitle="Azure AD Connect 同步處理︰ 了解並自訂同步處理 |Microsoft Azure"
    description="說明如何 Azure AD Connect 運作，以及如何自訂同步處理。"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect 同步處理︰ 了解並自訂同步處理
Azure [Active Directory 連線同步處理服務 （Azure AD Connect 同步處理） 是 Azure AD Connect 的主要元件。 它會處理進行同步處理您的內部部署環境和 Azure AD 之間的身分識別資料與相關的所有作業。 Azure AD Connect 同步處理是 DirSync、 Azure AD Sync 及與 Azure Active Directory 連接器設定 Forefront 身分管理員 」 的後續任務。

本主題是**Azure AD Connect 同步處理**（也稱為**同步處理引擎**） 常用，並列出所有其他主題相關的連結。 Azure AD Connect 的連結，請參閱[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。

同步處理服務包含兩個元件、 內部部署**Azure AD Connect 同步處理**元件和服務端中 Azure AD 稱為**Azure AD Connect 同步處理服務**。 服務是常見 DirSync、 Azure AD Sync，和 Azure AD Connect。

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect 同步處理主題

主題 | 它會說明及讀取的時機
----- | -----
**Azure AD Connect 同步處理的基本概念** |
[了解架構](active-directory-aadconnectsync-understanding-architecture.md) | 您誰是新使用者，同步處理引擎，並想要進一步瞭解架構和所用的字詞。
[技術的概念](active-directory-aadconnectsync-technical-concepts.md) | 精簡架構主題和簡要說明所用的字詞。
[拓撲 Azure ad 連線](active-directory-aadconnect-topologies.md) | 將說明的不同拓撲及同步處理引擎支援的案例。
**自訂的設定** |
[執行 [安裝] 精靈](active-directory-aadconnectsync-installation-wizard.md) | 說明什麼選項您有可用的當您再次執行 Azure AD Connect 安裝精靈。
[了解宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)| 描述稱為宣告式佈建的設定模型。
[了解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | 說明用於宣告式佈建運算式語言的語法。
[了解的預設設定](active-directory-aadconnectsync-understanding-default-configuration.md)| 說明如何現成的規則和預設設定。 也會說明如何規則共同作業的現成的案例中運作。
[了解使用者及連絡人](active-directory-aadconnectsync-understanding-users-and-contacts.md) | 繼續先前的主題，並說明使用者和連絡人的設定方式放在一起，特別是多樹系環境中。
[如何變更預設設定](active-directory-aadconnectsync-change-the-configuration.md) | 引導您如何變更屬性流程一般設定。
[變更預設設定的最佳作法](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | 支援的限制和變更現成可用的設定。
[設定篩選](active-directory-aadconnectsync-configure-filtering.md) | 說明的不同選項如何限制物件正在同步處理至 Azure AD 和逐步如何設定這些選項。
**功能與案例** |
[避免意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | 描述 [*防止意外刪除*] 功能，以及如何設定讓它。
[排程器](active-directory-aadconnectsync-feature-scheduler.md) | 將內建的排程器，匯入、 同步處理，並將資料匯出的說明。
[實作密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md) | 說明密碼同步處理如何運作，如何實作，以及如何運作，及進行疑難排解。
[裝置回寫](active-directory-aadconnect-feature-device-writeback.md) | 說明中 Azure AD Connect 裝置回寫的運作方式。
[目錄副檔名](active-directory-aadconnectsync-feature-directory-extensions.md) | 說明如何將您自己的自訂屬性與 Azure AD 架構。
**同步處理服務** |
[Azure AD Connect 同步處理服務功能](active-directory-aadconnectsyncservice-features.md) | 說明同步處理服務側邊，以及如何變更 Azure AD 同步處理設定]。
[重複屬性恢復功能](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) | 說明如何啟用並使用**userPrincipalName**和**proxyAddresses**重複屬性值恢復功能。
**作業及 UI** |
[同步處理服務管理員](active-directory-aadconnectsync-service-manager-ui.md) | 描述 [同步處理服務管理員使用者介面，包括[作業](active-directory-aadconnectsync-service-manager-ui-operations.md)、[連接器](active-directory-aadconnectsync-service-manager-ui-connectors.md)、 [Metaverse 設計工具](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)，以及[Metaverse 搜尋](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)] 索引標籤。
[操作工作及考量](active-directory-aadconnectsync-operations.md) | 說明操作的問題，例如損毀修復。
**如何...** |
[重設 Azure AD 帳戶](active-directory-aadconnectsync-howto-azureadaccount.md) | 如何重設從 Azure AD Connect 同步處理連線到 Azure AD 所用的服務帳戶的認證。
**詳細資訊與參照** |
[連接埠](active-directory-aadconnect-ports.md) | 列出您要開啟 [同步處理引擎和內部部署目錄和 Azure AD 之間的連接埠。
[若要 Azure Active Directory 同步處理的屬性](active-directory-aadconnectsync-attributes-synchronized.md) | 列出所有 AD 和 Azure AD 內部部署之間同步處理的屬性。
[函數參考](active-directory-aadconnectsync-functions-reference.md) | 列出所有可用的宣告式佈建的函數。

## <a name="additional-resources"></a>其他資源

* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
