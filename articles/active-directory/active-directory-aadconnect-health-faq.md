<properties
    pageTitle="Azure AD Connect 狀況常見問題集"
    description="此常見問題集解答關於 Azure AD 連線狀況的問題。 此常見問題集涵蓋使用的服務，包括帳單模型、 功能、 限制，以及支援的相關問題。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect 常見問題集 (FAQ) 的狀況

此常見問題集解答關於 Azure AD 連線狀況的問題。 此常見問題集涵蓋使用的服務，包括帳單模型、 功能、 限制，以及支援的相關問題。

## <a name="general-questions"></a>一般問題



**問︰ 我管理多個 Azure AD 目錄。如何切換 Azure Active Directory 進階版的項目？**

您可以切換不同 Azure AD 租用戶，選取目前已簽署的使用者名稱的右上角，選擇適當的帳戶。 如果此處未列出該帳戶，請選取 [登出，然後使用 [已啟用登入 Azure Active Directory 進階版的目錄的全域管理員認證。

## <a name="installation-questions"></a>安裝程式的問題



**問︰ 在個別的伺服器上安裝 Azure AD 連線狀況代理程式的影響為何？**

安裝 Microsoft Azure AD 連線狀況代理程式 ADFS Web 應用程式 Proxy 伺服器、 Azure AD Connect (sycn) 伺服器網域控制站的影響會最小與 CPU、 記憶體使用量網路頻寬和儲存。

下列的數字是近似值。

- CPU 消耗︰ 增加 ~ 1%
- 記憶體使用量︰ 最多 10%的總系統記憶體

>[AZURE.NOTE] 時無法傳達給 Azure 代理程式，代理程式會儲存在本機資料定義的上限。 代理程式會覆寫 「 快取 「 資料 」 最近最服務 」 為基礎。

- Azure AD 連線健康情況代理程式的本機緩衝儲存空間: ~ 20 MB
- AD FS 伺服器，建議您佈建 1024 MB (1 GB) AD FS 稽核頻道 Azure AD 連線健康情況代理程式處理所有的稽核資料，則會覆寫之前的磁碟空間。

**問︰ 我必須重新啟動我的伺服器 Azure AD 連線狀況代理程式的安裝期間嗎？**

[否]。 代理程式的安裝不會要求您重新啟動伺服器。 不過，安裝某些必要的步驟可能需要重新啟動電腦的伺服器。

比方說，在 Windows Server 2008 R2 的.Net 安裝 4.5 Framework 需要重新啟動伺服器。


**問︰ 沒有 Azure AD 連線狀況服務工作透過傳遞 http proxy 嗎？**

[是]。  針對上進行的作業，您可以設定狀況代理程式轉送輸出 http 要求使用 HTTP Proxy]。 如需詳細資訊，請參閱[設定 Azure AD 連線健康情況代理程式使用 HTTP Proxy。](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)
如果您需要設定 proxy 代理程式註冊期間，您可能需要事先修改您的 Internet Explorer Proxy 設定。
1. 開啟 Internet Explorer]-> [設定]-> [網際網路選項]-> [連線]-> [區域網路設定]。
2. 選取您的區域網路使用 Proxy 伺服器。
3. 如果您擁有的 proxy 伺服器連接埠 HTTP 和 HTTPS/安全，請選取 [進階]。

**問︰ Azure AD 連線狀況服務支援基本驗證連線至 Http Proxy 時嗎？**

[否]。 目前不支援基本驗證指定任意的使用者名稱與密碼的機制。


**問︰ AD ds 版 AD DS 的支援由 Azure AD 連線狀況？**

監控 AD ds 支援下列 OS 版本上安裝時︰

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## <a name="operations-questions"></a>作業的問題



**問︰ 我需要啟用稽核我 AD FS 應用程式 Proxy 伺服器或 Web 應用程式 Proxy 伺服器？**

否，稽核並不需要 Web 應用程式 Proxy (WAP) 伺服器上啟用。 將它啟用 AD FS 伺服器上。


**問︰ 如何 Azure AD 連線狀況通知取得解決？**

Azure AD 連線狀況通知取得解決在成功條件。 Azure AD 連線健康情況代理程式偵測並定期報告服務的成功條件。 隱藏幾個通知，是以時間為基礎。 換句話說，如果相同的錯誤狀況不觀察從警示產生 72 個小時內，會自動解析提醒。




**問︰ 我需要哪些防火牆連接埠開啟 Azure AD 連線狀況代理程式搭配使用？**

您需要 TCP/UDP 連接埠 443 和 5671 開啟 Azure AD 連線狀況代理程式都能進行通訊的 Azure AD 狀況服務端點。


**問︰ 為什麼是否具有相同名稱 Azure AD 連線狀況入口網站中的兩個伺服器？**

當您從伺服器移除代理程式時，就不會從 Azure AD 連線入口網站自動移除伺服器。  如果您手動從伺服器移除代理程式，或移除本身的伺服器，必須手動刪除從 Azure AD 連線狀況入口網站的 [伺服器項目。 如需詳細資訊，請移至[刪除伺服器或服務的執行個體。](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)

如果您建立映像伺服器或建立新的伺服器使用相同的詳細資料 （例如電腦名稱），並沒有從 Azure AD 連線狀況入口網站中移除已註冊的伺服器，安裝代理程式的新伺服器上，您可能會看到具有相同名稱的兩個項目。  
在此情況下，您應該刪除手動較舊的伺服器屬於的項目。 此伺服器的資料應該已過期。

## <a name="related-links"></a>相關的連結

* [Azure AD Connect 健康狀況](active-directory-aadconnect-health.md)
* [Azure AD Connect 狀況代理程式的安裝](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect 狀況作業](active-directory-aadconnect-health-operations.md)
* [使用 Azure AD 連線健康狀況與 AD FS](active-directory-aadconnect-health-adfs.md)
* [使用同步處理 Azure AD 連線狀況](active-directory-aadconnect-health-sync.md)
* [使用 Azure AD 連線健康狀況與 AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect 狀況版本歷程記錄](active-directory-aadconnect-health-version-history.md)
