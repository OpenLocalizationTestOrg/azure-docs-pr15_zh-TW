<properties
    pageTitle="Azure MFA 雲端與伺服器 |Microsoft Azure"
    description="選擇最適合您問我試著保護哪些 am，並在哪裡找到我使用者的多重因素驗證 secutiry 方案。  請選擇 [雲端 MFA 伺服器或 AD FS。"
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
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

#<a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>為您選擇 Azure 多重因素驗證解決方案

有數種類別的 Azure 多重因素驗證 (MFA)，因為我們必須回答找出哪個版本最適當的項目，若要使用的幾個問題。  這些問題是︰

-   [我嘗試安全什麼](#what-am-i-trying-to-secure)
-   [使用者位於何處](#where-are-the-users-located)
- [我需要哪些功能？](#what-featured-do-i-need)

下列各節提供指導方針決定每個上述問題的答案。

## <a name="what-am-i-trying-to-secure"></a>我嘗試安全什麼？

若要判斷正確的兩個步驟驗證解決方案，第一次我們必須回答問題的什麼是您想要與第二個驗證的方法。  是 Azure 中的應用程式？  或遠端存取系統？  判斷我們嘗試安全，我們可以回答問題的多重因素驗證必須啟用的位置。  


您嘗試安全什麼| 在雲端的多重因素驗證|多重因素驗證伺服器
------------- | :-------------: | :-------------: |
第一方 Microsoft 應用程式|● |● |
在 [應用程式] 圖庫中的 SaaS 應用程式|● |● |
IIS 應用程式發佈到 Azure AD 應用程式 Proxy|● |● |
IIS 應用程式未發佈透過 Azure AD 應用程式 Proxy | |● |
遠端存取，例如 VPN、 RDG| |● |



## <a name="where-are-the-users-located"></a>使用者位於何處

接下來，請查看我們的使用者位於何處有助於判斷正確的方案中的雲端或內部部署使用 MFA Server 使用。



使用者的位置| 在雲端的多重因素驗證|多重因素驗證伺服器
------------- | :-------------: | :-------------: |
Azure Active Directory|● | |
Azure AD 和內部部署 AD 使用 AD FS 同盟|● |● |
Azure AD 和內部部署 AD 使用 DirSync，Azure AD Sync 的 Azure AD Connect-而沒有密碼同步處理|● |● |
Azure AD 和內部部署 AD Azure AD Connect DirSync，Azure AD Sync，-使用密碼同步處理|● | |
內部部署的 Active Directory| |● |

## <a name="what-features-do-i-need"></a>我需要哪些功能？

下表是的可在雲端中的多因素驗證與多重因素驗證伺服器功能比較。

 | 在雲端的多重因素驗證 | 多重因素驗證伺服器
------------- | :-------------: | :-------------: |
第二個因素的行動應用程式通知 | ● | ● |
第二個因素的行動應用程式驗證碼 | ● | ●
為第二個因子變異數的電話 | ● | ●
單向 SMS 為第二個因子變異數 | ● | ●
雙向 SMS 為第二個因子變異數 |  | ●
硬體權杖為第二個因子變異數 |  | ●
不支援 MFA 的用戶端應用程式密碼 | ● |  
管理員控制驗證方法 | ● | ●
釘選模式 |  | ●
網路詐騙提醒 | ● | ●
MFA 報表 | ● | ●
一次略過 |  | ●
自訂的問候語的行動電話 | ● | ●
行動電話的可自訂的本機號碼 | ● | ●
信任的 IPs | ● | ●
請記住，MFA 受信任的裝置  | ● |  
條件的存取權 | ● | ●
快取 |  | ●

現在，我們決定是否要使用雲端多重因素驗證或 MFA Server 內部部署，我們可以開始設定，並使用 Azure 多重因素驗證。 **選取圖示，表示您的狀況 ！**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>
