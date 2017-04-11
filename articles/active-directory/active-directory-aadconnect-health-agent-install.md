<properties
    pageTitle="Azure AD 連線健康情況代理程式安裝 |Microsoft Azure"
    description="這是描述 AD FS 和同步處理的代理程式安裝的 Azure AD 連線健康狀況] 頁面。"
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect 狀況代理程式的安裝

這份文件會引導您安裝並設定 Azure AD 連線健康情況代理程式。 您可以從 [[在這裡](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent)下載代理程式。

##  <a name="requirements"></a>需求
下表是需求使用 Azure AD 連線狀況的清單。

| 要求 | 描述|
| ----------- | ---------- |
|Azure AD 進階版| Azure AD 連線狀況 Azure AD 進階功能，而且需要 Azure AD 進階版。 </br></br>如需詳細資訊，請參閱[快速入門 Azure AD 進階版](active-directory-get-started-premium.md) </br>若要開始的 30 天免費試用版，請參閱[開始試用。](https://azure.microsoft.com/trial/get-started-active-directory/)|
|您必須是全域管理員的您，即可開始使用 Azure AD 連線健康情況的 Azure AD|根據預設，只有全域管理員才可以安裝並設定開始、 存取入口網站，並執行任何作業中 Azure AD 連線健康狀況代理程式。 如需詳細資訊，請參閱[管理您的 Azure AD 目錄](active-directory-administer.md)。 <br><br> 使用角色以存取控制項您可以在您的組織允許 Azure AD 連線狀況給其他使用者存取。 如需詳細資訊，請參閱[角色 Azure AD 連線運作情況，以存取控制。](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**重要︰**安裝代理程式時所使用的帳戶必須是工作或學校帳戶。 不能以 Microsoft 帳戶。 如需詳細資訊，請參閱[註冊 Azure 做為組織](sign-up-organization.md)
|每個目標的伺服器上已安裝 Azure AD 連線狀況代理程式| Azure AD 連線狀況需要代理程式安裝目標在伺服器上，提供檢視入口網站中的資料。 </br></br>例如，以取得您 AD FS 內部部署基礎結構，代理程式必須安裝 AD FS 與 AD FS Proxy，Web 應用程式 Proxy 伺服器上。 同樣地，取得您的內部部署資料 AD DS 基礎結構，代理人必須安裝在網域上。 </br></br>**重要︰**安裝代理程式時所使用的帳戶必須是工作或學校帳戶。 不能以 Microsoft 帳戶。 如需詳細資訊，請參閱[註冊 Azure 做為組織](sign-up-organization.md)|
|輸出連線至 Azure 服務端點|在安裝和執行階段，代理程式會需要連線至 Azure AD 連線狀況服務端點。 如果輸出連線遭到封鎖，請確定下列端點會新增至允許的清單︰ </br></br><li>& #42;。blob.core.windows.net </li><li>& #42;。queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net-連接埠︰ 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|執行代理程式的伺服器上的防火牆連接埠。| 代理程式需要下列防火牆連接埠開啟 Azure AD 狀況服務端點與通訊代理程式的順序。</br></br><li>TCP/UDP 連接埠 443</li><li>TCP/UDP 連接埠 5671</li>
|允許下列網站，如果已啟用 IE 增強安全性| 如果已啟用 IE 增強的安全性，下列網站必須允許前往安裝代理程式的伺服器上。</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>您信任的 Azure Active Directory 的組織的同盟伺服器。 例如︰ https://sts.contoso.com</li>




## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>安裝 Azure AD 連線 AD fs 的健康情況代理程式
若要開始代理程式的安裝，請按兩下.exe 檔案下載。 在第一個畫面中，按一下 [安裝。

![確認 Azure AD 連線狀況](./media/active-directory-aadconnect-health-requirements/install1.png)

在安裝完成時，按一下 [立即設定]。

![確認 Azure AD 連線狀況](./media/active-directory-aadconnect-health-requirements/install2.png)

啟動命令提示字元 」，後面接著執行 Register AzureADConnectHealthADFSAgent 一些 PowerShell。 出現提示時登入 Azure，請繼續進行並登入。

![確認 Azure AD 連線狀況](./media/active-directory-aadconnect-health-requirements/install3.png)


登入後，會繼續 PowerShell。 它完成後，您可以關閉 PowerShell，並設定不完整。

此時，應該自動允許監控和收集資料代理程式啟動服務。 如果您有不符合所有前一節所述的必要條件，則會在 PowerShell 視窗內出現警告。 請務必在完成之前安裝代理程式的[需求](active-directory-aadconnect-health-agent-install.md#requirements)。 以下螢幕擷取畫面是這些錯誤的範例。

![確認 Azure AD 連線狀況](./media/active-directory-aadconnect-health-requirements/install4.png)

若要確認已安裝代理程式，請查看下列服務的伺服器上。 如果您完成設定時，他們應該已經在執行。 否則，請在停止直到完成設定。

- Azure AD Connect 狀況 AD FS 診斷服務
- Azure AD Connect 狀況 AD FS 獲得深入見解服務
- Azure AD Connect 監視服務健康狀況 AD FS

![確認 Azure AD 連線狀況](./media/active-directory-aadconnect-health-requirements/install5.png)


### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>在 Windows Server 2008 R2 伺服器上安裝代理程式

Windows Server 2008 R2 伺服器的步驟︰

1. 確定執行伺服器的 Service Pack 1 或更高。
1. 關閉 IE ESC 代理程式安裝的︰
1. 在每一個預先安裝 AD 狀況代理程式的伺服器上安裝 Windows PowerShell 4.0。 安裝 Windows PowerShell 4.0:
 - 安裝[Microsoft.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779)下載離線安裝程式中使用下列連結。
 - 安裝 PowerShell （從 Windows 功能） ise [以系統
 - 安裝[Windows Management Framework 4.0。](https://www.microsoft.com/download/details.aspx?id=40855)
 - 安裝 Internet Explorer 版本 10 或更新的伺服器上。 （需要狀況服務進行驗證，使用您的 Azure 管理員認證）。
1. 如需有關如何在 Windows Server 2008 R2 上安裝 Windows PowerShell 4.0 的詳細資訊，請參閱 wiki 文章[以下](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)。

### <a name="enable-auditing-for-ad-fs"></a>啟用稽核 AD FS

> [AZURE.NOTE] 此區段只適用於 AD FS 同盟伺服器。

收集並分析資料的使用狀況分析功能，Azure AD 連線狀況代理人必須 AD FS 稽核記錄中的資訊。 根據預設，不會啟用這些記錄。 若要啟用 AD FS 稽核與 AD FS 伺服器上找到 AD FS 稽核記錄，，請使用下列程序。

#### <a name="to-enable-auditing-for-ad-fs-20"></a>若要啟用稽核 AD fs 2.0

1. 按一下 [**開始****程式**、**系統管理工具**]，再按一下 [**原則]**。
2. 瀏覽至 [**安全性 \ \ 版權管理**] 資料夾，然後按兩下 [產生安全性稽核。
3. 在**本機的安全性設定**] 索引標籤中，確認 AD FS 2.0 服務帳戶列。 如果不存在，按一下 [**新增使用者或群組**並將其新增至清單中，，然後按一下**[確定]**。
4. 若要啟用稽核，以提高權限的權限開啟命令提示字元，然後執行下列命令︰<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. 關閉本機的安全性原則，然後再開啟嵌入式管理單元。 若要開啟嵌入式管理單元中，按一下 [**開始****程式**、**系統管理工具**]，再按一下 AD FS 2.0 管理。
6. 在 [動作] 窗格中，按一下 [編輯同盟服務屬性。
7. 在 [**同盟服務屬性**] 對話方塊中，按一下 [**事件**] 索引標籤。
8. 選取 [**成功稽核**和**失敗的稽核**核取方塊。
9. 按一下**[確定]**。

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>若要啟用稽核在 Windows Server 2012 R2 AD fs

1. 在桌面的工作列中開啟 [開始] 畫面上的**伺服器管理員**或伺服器管理員開啟 [**原則]** ，然後按一下 [**工具/本機的安全性原則**。
2. 瀏覽至 [**安全性本機 \ 權限指派**] 資料夾，然後按兩下 [**產生安全性稽核**。
3. 在**本機的安全性設定**] 索引標籤中，確認 AD FS 服務帳戶列。 如果不存在，按一下 [**新增使用者或群組**並將其新增至清單中，，然後按一下**[確定]**。
4. 若要啟用稽核，以提高權限的權限開啟命令提示字元，然後執行下列命令︰<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. 關閉**原則]**，然後開啟 AD FS 嵌入式**管理**單元 （在伺服器管理員按一下工具]，然後選取 [AD FS 管理）。
6. 在 [動作] 窗格中，按一下 [**編輯同盟服務屬性**。
7. 在 [同盟服務屬性] 對話方塊中，按一下 [**事件**] 索引標籤。
8. **成功稽核和失敗的稽核**核取方塊，然後按一下**[確定]**。






#### <a name="to-locate-the-ad-fs-audit-logs"></a>若要找出 AD FS 稽核記錄


1. 開啟 [**事件檢視器**]。
2. 移至 [Windows 記錄]，然後選取 [**安全性**]。
3. 在右側，按一下 [**篩選器目前的記錄檔**。
4. 事件來源] 下，選取 [ **AD FS 稽核**。

![AD FS 稽核記錄](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] 如果已停用 AD FS 稽核群組原則，然後 Azure AD 連線狀況代理程式無法收集資訊。 請確定您沒有可能稽核停用群組原則。

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>安裝 Azure AD 連線狀況代理程式的同步處理
同步處理的 Azure AD 連線狀況代理程式會自動安裝最新版本的 Azure AD Connect 中。 若要使用同步處理 Azure AD Connect，必須先下載最新版 Azure AD Connect，並將其安裝。 您可以下載最新版本[以下](http://www.microsoft.com/download/details.aspx?id=47594)。

若要確認已安裝代理程式，請查看下列服務的伺服器上。 如果您完成設定時，他們應該已經在執行。 否則，請在停止直到完成設定。

- Azure AD Connect 狀況同步處理的深入見解服務
- Azure AD Connect 監視服務健康狀況同步處理

![確認 Azure AD 連線的同步處理的狀況](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] 請記住，使用 Azure AD 連線狀況要求 Azure AD 進階版。 如果您沒有 Azure AD 進階版，您無法完成 Azure 入口網站中的設定。 如需詳細資訊，請參閱[需求] 頁面](active-directory-aadconnect-health-agent-install.md#requirements)。


## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Azure AD 手動同步處理註冊的連線健康狀況
如果同步處理代理程式註冊 Azure AD 連線 」 狀況無法成功安裝 Azure AD Connect 後，您可以使用下列 PowerShell 命令手動註冊代理程式。

>[AZURE.IMPORTANT] 使用這個 PowerShell 命令，才需要安裝 Azure AD Connect 後如果失敗代理程式註冊。

下列的 PowerShell 命令，必須具備只狀況代理程式註冊失敗時即使成功安裝與設定 Azure AD Connect。 已成功登錄代理程式之後，就會開始 Azure AD 連線健康情況的服務。

您可以手動註冊 Azure AD 連線狀況代理程式的使用下列 PowerShell 指令的同步處理︰

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

命令採用下列參數︰

- AttributeFiltering: $true （預設）-如果 Azure AD Connect 未同步處理的預設屬性設定，並已自訂為使用篩選的屬性設定。 否則 $false。
- StagingMode: $false （預設）-如果 Azure AD Connect 伺服器不在執行模式，$true，如果將伺服器設定要執行模式。

當系統提示您輸入驗證時，您就應該使用相同的全域管理員帳戶 (例如admin@domain.onmicrosoft.com)的用來設定 Azure AD Connect。

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>安裝 Azure AD 連線 AD ds 的健康情況代理程式
若要開始代理程式的安裝，請按兩下.exe 檔案下載。 在第一個畫面中，按一下 [安裝。

![確認 Azure AD 連線狀況](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

在安裝完成時，按一下 [立即設定]。

![確認 Azure AD 連線狀況](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

啟動命令提示字元 」，後面接著執行 Register AzureADConnectHealthADDSAgent 一些 PowerShell。 出現提示時登入 Azure，請繼續進行並登入。

![確認 Azure AD 連線狀況](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

登入後，會繼續 PowerShell。 它完成後，您可以關閉 PowerShell，並設定不完整。

此時，應該自動允許監控和收集資料代理程式啟動服務。 如果您有不符合所有前一節所述的必要條件，則會在 PowerShell 視窗內出現警告。 請務必在完成之前安裝代理程式的[需求](active-directory-aadconnect-health-agent-install.md#requirements)。 以下螢幕擷取畫面是這些錯誤的範例。

![確認 Azure AD 連線 AD ds 的狀況](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

若要確認代理程式已安裝，請查看網域控制站下列服務。

- Azure AD Connect 狀況 AD DS 的深入見解服務
- Azure AD Connect 監視服務健康狀況 AD DS

如果您完成設定時，應該已執行這些服務。 否則，請在停止直到完成設定。

![確認 Azure AD 連線狀況](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>安裝 Azure AD 連線伺服器核心在 AD ds 的健康情況代理程式。
安裝後.exe 檔案，您可以使用下列 PowerShell 命令，以完成註冊程序︰

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>設定 Azure AD 連線狀況代理程式使用 HTTP Proxy]
您可以設定 Azure AD 連線健康情況代理程式使用 [HTTP Proxy]。

>[AZURE.NOTE]
- 不支援使用 「 Netsh WinHttp 設定 ProxyServerAddress 」，因為代理程式使用 System.Net 來進行網頁，而不是 Microsoft Windows HTTP 服務的要求。
- 設定的 Http Proxy 位址用來傳遞加密 Https 郵件。
- 經過驗證的 proxy （使用 HTTPBasic） 不受支援。

### <a name="change-health-agent-proxy-configuration"></a>變更狀況代理程式 Proxy 設定
您有下列選項，設定 Azure AD 連線狀況代理程式使用 HTTP Proxy]。

>[AZURE.NOTE]Azure AD 連線健康情況代理程式的所有服務必須重新都啟動，更新的 proxy 設定的順序。 執行下列命令︰<br>
重新啟動服務 AdHealth *

#### <a name="import-existing-proxy-settings"></a>匯入現有的 proxy 設定

##### <a name="import-from-internet-explorer"></a>匯入從 Internet Explorer
Internet Explorer HTTP proxy 設定可以匯入，以供 Azure AD 連線健康情況代理程式。 在每一執行健康情況代理程式的伺服器時，執行下列 PowerShell 命令︰

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>從 WinHTTP 匯入
Azure AD 連線健康情況代理程式所使用，可以匯 WinHTTP proxy 設定。 在每一執行健康情況代理程式的伺服器時，執行下列 PowerShell 命令︰

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>手動指定 Proxy 位址
您可以手動指定的 proxy 伺服器，每個藉由執行下列動作的 PowerShell 命令執行健康情況代理程式的伺服器上︰

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

範例︰*設定 AzureAdConnectHealthProxySettings HttpsProxyAddress myproxyserver: 443*

- 「 位址 」 可以是 DNS 解析伺服器名稱或 IPv4 位址
- 可省略 「 連接埠 」。 如果省略然後 443 作為預設連接埠。

#### <a name="clear-existing-proxy-configuration"></a>清除現有的 proxy 設定
您可以執行下列命令以清除現有的 proxy 設定︰

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>了解目前的 proxy 設定
您可以執行下列命令以閱讀的目前設定的 proxy 設定︰

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>測試連線至 Azure AD 連線健康情況的服務
有可能的問題可能會導致 Azure AD 連線狀況代理程式遺失與 Azure AD 連線狀況服務的連線。 包括網路問題、 權限的問題或其他各種不同的原因。

如果代理程式無法傳送資料至超過兩個小時 Azure AD 連線狀況服務，表示與入口網站中將下列通知: 「 服務健康狀況的資料不是最新狀態。 」 您可以確認受影響的 Azure AD Connect 健康情況代理程式是否能夠上載到 Azure AD 連線狀況服務的資料，藉由執行下列動作的 PowerShell 命令︰

    Test-AzureADConnectHealthConnectivity -Role ADFS

角色參數目前採用下列的值︰

- 在 ADFS
- 同步處理
- 新增

若要檢視詳細的記錄，您可以在命令中使用-ShowResults 標幟。 使用下列範例︰

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]若要使用 [連線] 工具，您必須先完成代理程式註冊。 如果您無法完成代理程式註冊，請確定您有符合所有[需求](active-directory-aadconnect-health-agent-install.md#requirements)Azure AD 連線狀況。 預設執行此連線測試代理程式註冊期間。



## <a name="related-links"></a>相關的連結

* [Azure AD Connect 健康狀況](active-directory-aadconnect-health.md)
* [Azure AD Connect 狀況作業](active-directory-aadconnect-health-operations.md)
* [使用 Azure AD 連線健康狀況與 AD FS](active-directory-aadconnect-health-adfs.md)
* [使用同步處理 Azure AD 連線狀況](active-directory-aadconnect-health-sync.md)
* [使用 Azure AD 連線健康狀況與 AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect 狀況常見問題集](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect 狀況版本歷程記錄](active-directory-aadconnect-health-version-history.md)
