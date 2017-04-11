<properties
    pageTitle="從 DirSync 和 Azure AD Sync 升級 |Microsoft Azure"
    description="說明如何從 DirSync 和 Azure AD Sync 升級至 Azure AD Connect。"
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
    ms.date="06/27/2016"
    ms.author="billmath"/>


# <a name="upgrade-windows-azure-active-directory-sync-dirsync-and-azure-active-directory-sync-azure-ad-sync"></a>升級 Windows Azure Active Directory 同步處理 (「 DirSync 」) 和 Azure Active Directory 同步處理 （Azure AD 同步處理]）
Azure AD Connect 是與 Azure AD 連線內部部署目錄的最佳方法和 Office 365。 這是這些工具現在已被取代，並會達到結尾支援 2017 年 4 月 13，從 Windows Azure Active Directory 同步處理 (DirSync) 或 Azure AD Sync 升級至 Azure AD Connect 的好時機。

兩個身分識別同步處理工具已被取代所提供的單一樹系客戶 (DirSync) 和多樹系與其他進階客戶 (Azure AD Sync)。 這些舊版工具已被取代單一方案所提供的所有案例︰ Azure AD Connect。 提供的新功能與功能增強功能，新的案例的支援。 若要繼續同步處理內部部署識別資料 Azure AD 和 Office 365，我們強烈建議您升級至 Azure AD Connect。

於 2014 年 7 月發行 DirSync 的最後一個版本，並在 2015年發行 Azure AD Sync 的最後一個版本。

## <a name="what-is-azure-ad-connect"></a>什麼是 Azure AD Connect
Azure AD Connect 是 DirSync 和 Azure AD Sync 後續任務。 它會合併所有案例支援以下兩種。 您可以閱讀更多關於在[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)中。

## <a name="deprecation-schedule"></a>取代排程

日期 | 註解
 --- | ---
2016 年 4 月 13日日 | 已遭取代為宣佈 Windows Azure Active Directory 同步處理 (「 DirSync 」) 與 Microsoft Azure Active Directory 同步處理 （Azure AD 同步處理]）。
2017 年 4 月 13 日 | 支援結束。 客戶不再能開啟不含第一次升級至 Azure AD Connect 的支援案例。

## <a name="how-to-transition-to-azure-ad-connect"></a>如何切換到 Azure AD Connect
如果您執行的有兩種的方法可以升級的 DirSync︰ 就地升級及平行部署。 就地升級建議，為大部分的客戶，若您有一個最近作業系統與小於 50000 的物件。 在其他情況下建議執行平行部署 DirSync 設定移到新的伺服器執行 Azure AD Connect 的位置。

如果您是使用 Azure AD Sync 建議就地升級。 如果您想要可能是平行安裝新的 Azure AD Connect 伺服器，然後執行迴旋從您 Azure AD Sync 伺服器移轉至 Azure AD Connect。

解決方案 | 案例
----- | -----
[從 DirSync 升級](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>如果您已在執行中的現有 DirSync 伺服器。</li>
[從 Azure AD Sync 升級](active-directory-aadconnect-upgrade-previous-version.md)| <li>如果您從 Azure AD Sync 移動。</li>

如果您想要瞭解如何從 DirSync 如何 Azure AD Connect 就地升級，請參閱此通道 9 影片︰

> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]

## <a name="faq"></a>常見問題集
**問︰ 我從 Azure 小組及/或 Office 365 訊息中心，郵件收到的電子郵件通知，但我使用的連線。**  
通知也會傳送至 Azure AD Connect 使用建立數字 1.0 的客戶。\*.0 （使用 1.1 （英文） 預先發行版本）。 Microsoft 建議客戶保持最新的 Azure AD Connect 版本。 使用 1.1 （英文）[自動升級](active-directory-aadconnect-feature-automatic-upgrade.md)功能可讓您能夠輕鬆地都會有最新版 Azure AD Connect 的安裝。

**問︰ 將 DirSync/Azure AD Sync 停止處理 2017 年 4 月 13，嗎？**  
[否]。 將宣佈這些不再何時能夠與 Azure AD 通訊的日期，在稍後的日期。 您可以在 [可用時，本主題中，找到資訊。

**問︰ 我可以從升級哪些 DirSync 版本？**  
若要從目前正在使用任何 DirSync 版本升級支援。

**問︰ 什麼 FIM/MIM Azure AD 連接器？**  
Azure AD 連接器**的 FIM/MIM 已**宣告為已遭取代。 在 [**凍結功能**;沒有新的功能，並會收到任何錯誤修正。 Microsoft 建議使用它，將它從 Azure AD Connect 計劃的客戶。 若要啟動任何新的部署使用強烈建議。 此連接器會在未來已遭取代已宣告。

## <a name="additional-resources"></a>其他資源

* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
