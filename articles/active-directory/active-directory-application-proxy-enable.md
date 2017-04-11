<properties
    pageTitle="啟用 Azure AD 應用程式 Proxy |Microsoft Azure"
    description="開啟應用程式 Proxy 在 Azure 傳統入口網站，並安裝連接線的反向 proxy。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>

# <a name="enable-application-proxy-in-the-azure-portal"></a>Azure 入口網站中啟用應用程式 Proxy

本文會引導您在 Azure AD 啟用 Microsoft Azure AD 應用程式 Proxy 雲端目錄的步驟。

如果您不熟悉哪些應用程式 Proxy 可協助您執行，進一步瞭解[如何提供安全遠端存取內部部署的應用程式](active-directory-application-proxy-get-started.md)。

## <a name="application-proxy-prerequisites"></a>應用程式 Proxy 的必要條件
您可以啟用，並使用應用程式 Proxy 服務之前，您必須具備︰

- [Microsoft Azure AD 基本或付費訂閱](active-directory-editions.md)，您是全域管理員 Azure AD 目錄。
- 執行 Windows Server 2012 R2 或 Windows 8.1 或更高的伺服器，要在其上，您可以安裝應用程式 Proxy 連接器。 伺服器會傳送要求給在雲端，應用程式 Proxy 服務，需要 HTTP 或 HTTPS 連線至您要發佈的應用程式。

    - 單一登入的已發佈應用程式，這台電腦應該會網域相同 AD 網域為您要發佈的應用程式中。

- 如果在路徑中有防火牆，請確定它已開啟，讓連接線可以 HTTPS (TCP) 要求應用程式 Proxy。 連接器一部分的高層級的網域 msappproxy.net 及 servicebus.windows.net 的子網域搭配使用這些連接埠。 請確定開啟下列連接埠**輸出**流量︰

  	| 連接埠號碼 | 描述 |
  	| --- | --- |
  	| 80 | 啟用安全性驗證的輸出 HTTP 流量。 |
  	| 443 | 啟用使用者驗證針對 Azure AD （僅適用於連接器註冊程序必填） |
  	| 10100 – 10120 | 啟用傳送至 proxy LOB HTTP 回應 |
  	| 9352、 5671 | 啟用趨近於 Azure 服務傳入的邀請連接器之間的通訊。 |
  	| 9350 | 選擇性的若要啟用傳入要求較佳的效能 |
  	| 8080 | 啟用連接器啟動安裝順序] 和 [連接器自動更新 |
  	| 9090 | 啟用連接器登錄 （僅適用於連接器註冊程序必填） |
  	| 9091 | 啟用連接器信任憑證自動更新 |

    如果您的防火牆會強制執行流量根據原始的使用者，開啟下列連接埠流量來自執行為網路服務的 Windows 服務。 此外，請確定啟用 NT Authority\System 連接埠 8080。

- 如果您的組織使用 proxy 伺服器連線到網際網路，請看看部落格文章[使用現有的內部部署 proxy 伺服器](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/)，如需詳細資訊，如何將其設定。

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>步驟 1: Azure AD 中啟用應用程式 Proxy
1. 在[Azure 傳統入口網站](https://manage.windowsazure.com/)以系統管理員身分登入。
2. 移至 [Active Directory，然後選取您要啟用應用程式 Proxy 的目錄。

    ![Active Directory-圖示](./media/active-directory-application-proxy-enable/ad_icon.png)

3. 從 [目錄] 頁面中，選取 [**設定]** ，然後向下**應用程式 Proxy**捲動。
4. 切換**啟用此目錄的應用程式 Proxy 服務****啟用**。

    ![啟用應用程式 Proxy](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. 選取 [**立即下載**]。 會帶您到**Azure 的 AD 應用程式 Proxy 連接器下載**。 閱讀並接受授權合約，按一下 [儲存連接器的 Windows Installer 檔 (.exe) 的 [**下載**]。

## <a name="step-2-install-and-register-the-connector"></a>步驟 2︰ 安裝並註冊連接器
1. 在您準備好根據必要條件伺服器上執行**AADApplicationProxyConnectorInstaller.exe** 。
2. 遵循精靈中的指示安裝。
3. 在安裝期間，您將會提示您 Azure AD 租用戶的應用程式 Proxy 登錄連接器。

  - 提供 Azure AD 全域管理員認證。 全域管理員租用戶，可能會從您的 Microsoft Azure 認證。
  - 請確定您的應用程式 Proxy 服務已啟用的目錄中管理員登錄連接器。 例如，contoso.com 租用戶網域時，管理員應該admin@contoso.com或在該網域的任何其他別名。
  - 如果**IE 增強的安全性設定**設定為**在**伺服器上安裝連接器，可能會封鎖註冊畫面。 請依照下列錯誤訊息，允許存取] 中的指示。 請確定 Internet Explorer 增強安全性關閉。
  - 如果未成功連接器註冊，請參閱[疑難排解應用程式 Proxy](active-directory-application-proxy-troubleshoot.md)。  

4. 安裝完成後，請兩個新服務將會新增到您的伺服器︰

    - **Microsoft AAD 應用程式 Proxy 連接器**可讓連線
    - **Microsoft AAD 應用程式 Proxy 連接器更新程式**會自動的更新服務，定期檢查新版本的連接器，並視需要更新連接器。

    ![應用程式 Proxy 連接器服務的螢幕擷取畫面](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. 按一下 [安裝] 視窗中的 [**完成**]。

為了可用性，您應該部署兩個以上的連接器。 若要部署更多的連接器，重複步驟 2 和 3，上方。 每個連接器必須分別註冊。

如果您想要解除安裝連接器，解除安裝連接器服務和更新程式服務。 重新啟動您的電腦完全移除這項服務。


## <a name="next-steps"></a>後續步驟

您已準備好[發佈](active-directory-application-proxy-publish.md)應用程式的應用程式 Proxy。

如果您有不同的網路或不同的位置上的應用程式，您可以使用 [連接器群組，將不同的連接器組織成邏輯的單位。 進一步瞭解[使用應用程式 Proxy 連接器](active-directory-application-proxy-connectors.md)。
