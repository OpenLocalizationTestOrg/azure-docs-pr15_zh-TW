<properties
    pageTitle="Azure AD Connect︰ 同步處理服務執行個體 |Microsoft Azure"
    description="此頁面文件 Azure AD 執行個體的特殊考量。"
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

# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect︰ 特殊考量的執行個體
Azure AD Connect 最常使用全球的執行個體 Azure AD 和 Office 365。 但還有其他執行個體，以下有不同的需求，Url 及其他特殊考量。

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud 德國
[Microsoft Cloud 德國](http://www.microsoft.de/cloud-deutschland)是營運德文資料信任統領雲端。

這項雲端目前是在預覽中。 通常您可以自行，例如的案例許多驗證網域，必須完成運算子。 請如需有關如何參與預覽，連絡您當地的 Microsoft 代表。

若要開啟的 proxy 伺服器的 Url |
--- |
\*。 microsoftonline.de |
\*。 windows.net |
+ 憑證撤銷清單 |

當您登入您的 Azure AD 目錄您必須使用 onmicrosoft.de 網域中的帳戶。

目前沒有顯示在 Microsoft Cloud 德國的功能︰

- Azure AD 連線狀況無法使用。
- 無法使用自動更新。
- 不使用密碼回寫。

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure 政府雲端
[Microsoft Azure 政府雲端](https://azure.microsoft.com/features/gov/)是美國政府雲端。

這項雲端具有已受舊版的目錄同步。 從建置 1.1.180 Azure AD Connect 下一步的支援產生的雲端。 此產生使用的僅適用於美國的根據結束點，並有不同的 Url，以開啟您的 proxy 伺服器中的清單。

若要開啟的 proxy 伺服器的 Url |
--- |
\*。 microsoftonline.com |
\*。 gov.us.microsoftonline.com |
+ 憑證撤銷清單 |

Azure AD Connect 無法自動偵測您 Azure AD 目錄位於政府雲端。 請改為您需要安裝 Azure AD Connect 時，請採取下列動作。

1. 啟動 Azure AD Connect 安裝。
2. 當您看到您所希望接受使用者授權合約的第一頁，請繼續但離開執行安裝精靈。
3. 啟動 regedit，然後變更登錄機碼`HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance`值`2`。
4. 移至 Azure AD Connect 安裝精靈中，接受使用者授權合約並繼續]。 在安裝期間，請務必使用**自訂設定**安裝路徑 （和表達安裝）。 然後像往常一樣繼續安裝。

目前沒有顯示在 Microsoft Azure 政府雲端功能︰

- Azure AD 連線狀況無法使用。
- 無法使用自動更新。
- 不使用密碼回寫。

## <a name="next-steps"></a>後續步驟
進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
