<properties
    pageTitle="Azure 入口網站中建立 Azure Biztalk |Microsoft Azure"
    description="瞭解如何佈建，或在 Azure 入口網站; 建立 Azure BizTalk 服務MABS WABS"
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>



# <a name="create-biztalk-services-using-the-azure-portal"></a>建立 BizTalk 服務使用 Azure 入口網站

Azure 入口網站中建立 Azure BizTalk 服務。

> [AZURE.TIP] Azure 入口網站登入，您需要 Azure 帳戶和 Azure 訂閱。 如果您沒有帳戶，您可以在幾分鐘內建立的免費的試用帳戶。 請參閱[Azure 免費試用版](http://go.microsoft.com/fwlink/p/?LinkID=239738)。

## <a name="create-a-biztalk-service"></a>建立 BizTalk 服務
根據您所選擇的版本，可能是使用並非所有 BizTalk 服務設定。

1. [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)登入。
2. 在下導覽窗格中，選取 [**新增**]:  
![選取 [新增] 按鈕][NEWButton]

3. 選取 [**應用程式服務** > **BIZTALK 服務** > **自訂建立**︰  
![選取 BizTalk 服務，然後選取 [建立自訂][NewBizTalkService]

4. 輸入 BizTalk 服務設定︰

    <table border="1">
    <tr>
    <td><strong>BizTalk 服務名稱</strong></td>
    <td>您可以輸入任何名稱，但會特定。 部分範例包括︰<br/><br/>
    <em>mycompany</em>。 biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>。 biztalk.windows.net<br/>
    <em>所有使用者 myapplication</em>。 biztalk.windows.net<br/><br/>「。 biztalk.windows.net 」 會自動新增至您所輸入的名稱。 這樣會建立的 URL，會用來存取您的 BizTalk 服務想<strong>https://<em>的所有使用者 myapplication</em>。 biztalk.windows.net</strong>。
    </td>
    </tr>
    <tr>
    <td><strong>版本</strong></td>
    <td>如果您是開發測試階段中，選擇 [<strong>開發人員</strong>]。 如果您是實際執行階段中，使用<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">Biztalk︰ 版本圖表</a>以判斷<strong>進階版</strong>、<strong>標準</strong>、 或<strong>基本</strong>是否正確的選擇為您的商務案例。
    </td>
    </tr>
    <tr>
    <td><strong>區域</strong></td>
    <td>選取要裝載 BizTalk 服務的地理區域。</td>
    </tr>
    <tr>
    <td><strong>網域 URL</strong></td>
    <td><strong>選用</strong>。 根據預設，網域 URL 是<em>YourBizTalkServiceName</em>。 biztalk.windows.net。 也可以輸入自訂的網域。 例如，如果您的網域是<em>contoso</em>，您可以輸入︰ <br/><br/>
    <em>MyCompany</em>。 contoso.com<br/>
    <em>MyCompanyMyApplication</em>。 contoso.com<br/>
    <em>所有使用者 MyApplication</em>。 contoso.com<br/>
    <em>YourBizTalkServiceName</em>。 contoso.com<br/>
    </td>
    </tr>
    </table>
選取下一個箭號。

5. 輸入儲存空間及資料庫設定︰

    <table border="1">
    <tr>
    <td><strong>封存監控儲存帳戶</strong></td>
    <td>選取現有的儲存空間帳戶或建立新的儲存空間帳戶。 <br/><br/>如果您建立新的儲存空間帳戶時，輸入<strong>儲存體帳戶名稱</strong>。</td>
    </tr>
    <tr>
    <td><strong>追蹤資料庫</strong></td>
    <td>如果您使用現有的 Azure SQL 資料庫時，就無法使用另一個 BizTalk 服務。 您需要登入名稱，然後輸入密碼時建立 Azure SQL 資料庫伺服器。<br/><br/><strong>秘訣</strong>建立追蹤資料庫及監控/封存儲存帳戶 BizTalk 服務為相同的區域。</td>
    </tr>
    </table>
選取下一個箭號。

6. 輸入資料庫設定︰

    <table border="1">
    <tr>
    <td><strong>名稱</strong></td>
    <td>在上一個畫面中選取 [<strong>建立新的 SQL 資料庫執行個體</strong>時可使用。
    <br/><br/>
   輸入您的 BizTalk 服務所使用的 SQL 資料庫名稱。</td>
    </tr>
    <tr>
    <td><strong>伺服器</strong></td>
    <td>在上一個畫面中選取 [<strong>建立新的 SQL 資料庫執行個體</strong>時可使用。
    <br/><br/>
   選取現有的 SQL 資料庫伺服器或建立新的 SQL 資料庫伺服器。</td>
    </tr>
    <tr>
    <td><strong>登入的伺服器名稱</strong></td>
    <td>輸入登入使用者名稱。</td>
    </tr>
    <tr>
    <td><strong>伺服器登入密碼</strong></td>
    <td>輸入的登入密碼。</td>
    </tr>
    <tr>
    <td><strong>區域</strong></td>
    <td><strong>建立新的 SQL 資料庫執行個體</strong>為選取狀態時可使用。 選取要主控您的 SQL 資料庫的地理區域。</td>
    </tr>
    </table>

選取核取記號，以完成精靈。 [進度] 圖示會顯示︰  
![完成時，會顯示進度圖示][ProgressComplete]

完成時，Azure BizTalk 服務會建立並準備好您的應用程式。 已足夠的預設設定。 如果您想要變更預設設定，在左側的功能窗格中，選取**BIZTALK 服務**，然後選取 [BizTalk 服務。 中的[儀表板]、 [監視器] 和 [縮放比例標籤](biztalk-dashboard-monitor-scale-tabs.md)頂端會顯示其他的設定。

根據 BizTalk 服務的狀態，有一些作業無法完成。 這些作業的清單，請移至[BizTalk 服務狀態圖表](biztalk-service-state-chart.md)。


## <a name="post-provisioning-steps"></a>佈建後續步驟

-  [在本機電腦上安裝的憑證](#InstallCert)
-  [新增準備生產的憑證](#AddCert)
-  [取得存取控制命名空間](#ACS)

#### <a name="InstallCert"></a>在本機電腦上安裝的憑證
佈建 BizTalk 服務，一部分︰ 自我簽署的憑證，建立並與您 BizTalk 服務訂閱相關聯。 您必須下載這個憑證，並將其安裝於電腦從您 [部署 BizTalk 服務應用程式或將訊息傳送至 BizTalk 服務端點。

1. [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)登入。
2. 在左側的功能窗格中，選取**BIZTALK 服務**，然後選取 [BizTalk 服務訂閱。
3. 選取 [**儀表板**] 索引標籤。
4. 選取 [**下載 SSL 憑證**︰  
![修改 SSL 憑證][QuickGlance]
5. 按兩下憑證，然後執行精靈來安裝的憑證。 請確定您安裝的**信任的根憑證授權單位**存放區下憑證。

#### <a name="AddCert"></a>新增準備生產的憑證
建立 Biztalk 適用於 [僅限開發環境中使用，則自動建立自我簽署的憑證。 生產情況下，請先替換準備生產的憑證。

1. 在 [**儀表板**] 索引標籤中，選取 [**更新 SSL 憑證**]。
2. 瀏覽至您私人的 SSL 憑證 (*CertificateName*.pfx) 包含您 BizTalk 服務名稱，輸入密碼，，然後按一下核取記號。

#### <a name="ACS"></a>取得存取控制命名空間

1. [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)登入。
2. 在左側的功能窗格中，選取**BIZTALK 服務**，然後選取 [BizTalk 服務。
3. 任務列中，選取 [**連線資訊**︰  
![選取 [連線資訊][ACSConnectInfo]

4. Access 控制項的值，複製。

當您部署 BizTalk 服務從 Visual Studio 專案時，您可以輸入此存取控制命名空間。 存取控制命名空間會自動建立 BizTalk 服務。

若任何應用程式可存取控制項的值。 建立 Azure BizTalk 服務時，此存取控制命名空間控制 BizTalk 服務部署驗證。 如果您想要變更的訂閱或管理命名空間，選取 [左側的功能窗格中的 [ **ACTIVE DIRECTORY** ，然後選取您的命名空間。 [工作] 列會列出您的選項。

按一下 [**管理**開啟 Access 控制項管理入口網站。 在 Access 控制項管理入口網站中 BizTalk 服務會使用**服務的身分識別**︰  
![在 [存取 ACS 服務身分識別控制管理入口網站][ACSServiceIdentities]

存取控制服務身分識別為一組的應用程式或用戶端存取控制直接驗證方法及收到權杖的認證。

> [AZURE.IMPORTANT]BizTalk 服務使用預設的服務身分識別與**密碼**值的**擁有者**。 如果您使用對稱金鑰值，而不是密碼值時，可能會發生下列錯誤。<br/><br/>*無法連線至指定的認證以存取控制項管理服務帳戶*

[管理您的 ACS 命名空間](https://msdn.microsoft.com/library/azure/hh674478.aspx)列出一些規則和建議。

## <a name="requirements-explained"></a>說明的需求

這些需求不適用於免費版。
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>您的需要</strong></td>
        <td><strong>您需要的原因</strong></td>
</tr>
<tr>
<td>Azure 訂閱</td>
<td>訂閱會決定誰可以 Azure 入口網站登入。 帳戶擁有者會在<a HREF="https://account.windowsazure.com/Subscriptions">Azure 訂閱</a>建立訂閱。
<br/><br/>
Azure 帳戶可以有多個訂閱，並且可允許的任何人。 您 Azure 帳戶擁有者，例如，建立一個名為<em>BizTalkServiceSubscription</em>的訂閱及可讓您公司內 BizTalk 系統管理員 (例如，ContosoBTSAdmins@live.com)存取此訂閱]。 在此案例中，BizTalk 管理員 Azure 入口網站登入，並擁有訂閱，包括 Azure BizTalk 服務中的所有裝載的服務的完整系統管理員權限。 BizTalk 管理員不是 Azure 帳戶持有者，因此沒有任何帳單資訊的存取權。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">管理訂閱和 Azure 入口網站中的儲存空間帳戶</a>提供的更多資訊。
</td>
</tr>
<tr>
<td>Azure SQL 資料庫</td>
<td>儲存資料表、 檢視和 BizTalk 服務，包括追蹤資料所用的預存程序。
<br/><br/>
當您建立 BizTalk 服務時，您可以使用現有 Azure SQL Server Azure SQL 資料庫，或自動建立新的伺服器或資料庫。
<br/><br/>
自動設定 SQL 資料庫小數位數。 一般而言，預設刻度不足，BizTalk 服務。 變更小數位數，會影響價格。 請參閱<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">帳戶與帳單 Azure SQL 資料庫</a>
<br/><br/>
<strong>備忘稿</strong>
<br/>
<ul>
<li> 當您建立新的 Azure SQL Server 與資料庫時，會自動啟用 Azure 服務。 BizTalk 服務需要 Azure 服務會啟用。</li>
<li>如果您建立新的 Azure SQL 資料庫上現有的 Azure SQL Server 時，不會變更伺服器的防火牆規則。 如此一來，就可以其他 Azure 服務不允許存取伺服器的資料庫。</li>
</ul>
</td>
</tr>
<tr>
<td>Azure 存取控制命名空間</td>
<td>使用 Azure BizTalk 服務會驗證。 當您部署 BizTalk 服務從 Visual Studio 專案時，您可以輸入此存取控制命名空間。 當您建立 BizTalk 服務時，會自動建立存取控制命名空間。</td>
</tr>

<tr>
<td>Azure 儲存體帳戶</td>
<td>表格、 blob 和您 BizTalk 服務用來儲存下列佇列提供存取︰

<ul>
<li>監視 BizTalk 服務的記錄檔。 監控輸出也會顯示在 Azure 入口網站的**監視**] 索引標籤。</li>
<li>建立 X12 或協力廠商之間 AS2 合約時，您可以啟用 [封存] 功能來儲存郵件內容。 此資料會儲存在 [儲存的帳戶。</li>
</ul>
<br/>
當您建立 BizTalk 服務時，您可以使用現有的儲存空間帳戶，或自動建立新的儲存空間帳戶。
<br/><br/>
預設儲存設定已足夠 BizTalk 服務。
<br/><br/>
當您建立的儲存空間帳戶時，主索引鍵和次要鍵會自動建立。 這些按鍵控制存取您儲存的帳戶。 BizTalk 服務會自動使用主索引鍵。
<br/><br/>
如需詳細資訊，請參閱<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">儲存</a>。
</td>
</tr>

<tr>
<td>SSL 私人憑證</td>
<td>
建立 Azure BizTalk 服務時，也會建立 HTTPS URL，其中包含您 BizTalk 服務的名稱。 此 URL 會自動設定為使用開發專用的自我簽署的憑證。 實際運作，您需要私人的 SSL 憑證。
<br/><br/>
<strong>重要的 SSL 憑證資訊</strong>

<ul>
<li>憑證到期日必須小於 5 年。</li>
<li>所有的私人憑證要求輸入密碼。 知道密碼並最佳作法是這個密碼與共用您的系統管理員。</li>
<li>測試或開發環境中使用自我簽署的憑證。 使用自我簽署的憑證，匯入憑證，您的個人憑證存放區 」 和 「 信任的根憑證授權單位憑證存放區。</li>
</ul>
<br/>當生產憑證要求傳送給您的憑證授權單位，授與下列憑證屬性︰
<br/>

<ul>
<li><strong>增強按鍵用法</strong>︰ 至少 Azure BizTalk 服務需要伺服器驗證。</li>
<li><strong>一般名稱</strong>︰ 輸入 Azure BizTalk 服務 URL 的完整的網域名稱 (FQDN)。 本文中，請參閱<a HREF="#BizTalk">建立 BizTalk 服務</a>。</li>
</ul>
<br/>
建立 BizTalk 服務後，您可以加入新的或不同的憑證。
</td>
</tr>
</table>



## <a name="hybrid-connections"></a>混合式連線

您建立 Azure BizTalk 服務，就可以使用 [**混合式連線**] 索引標籤︰

![混合式連線] 索引標籤][HybridConnectionTab]

混合式連線用來將 Azure 的網站 」 或 「 Azure 的行動訊息服務連線至任何使用靜態 TCP 連接埠，例如 SQL Server、 MySQL、 HTTP Web Api、 行動電話服務及大部分的自訂 Web 服務的內部部署資源。  混合式連線和 BizTalk 介面卡服務會不同。 BizTalk 介面卡服務用來連線到內部部署的商務線條 (LOB) 系統的 Azure BizTalk 服務。

 若要瞭解詳細資訊，包括建立與管理混合式連線的[混合式部署連線](integration-hybrid-connection-overview.md)，請參閱。


## <a name="next-steps"></a>後續步驟

現在，建立 BizTalk 服務時，自我熟悉不同[Biztalk︰ 儀表板]、 監視器和縮放比例] 索引標籤](biztalk-dashboard-monitor-scale-tabs.md)。 您 BizTalk 服務可供您的應用程式。 若要開始建立應用程式，請移至[Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=235197)。

## <a name="see-also"></a>另請參閱
- [Biztalk︰ 版本圖表](biztalk-editions-feature-chart.md)<br/>
- [Biztalk︰ 狀態圖表](biztalk-service-state-chart.md)<br/>
- [Biztalk︰ 備份與還原](biztalk-backup-restore.md)<br/>
- [Biztalk︰ 節流](biztalk-throttling-thresholds.md)<br/>
- [Biztalk︰ 發行者名稱和發行者鍵](biztalk-issuer-name-issuer-key.md)<br/>
- [我要如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [混合式連線](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
