<properties
   pageTitle="管理 Azure 資訊安全中心中的安全性建議 |Microsoft Azure"
   description="這份文件會引導您建議在 Azure 資訊安全中心如何協助您保護您 Azure 的資源，並保持留存安全性原則。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="managing-security-recommendations-in-azure-security-center"></a>在 Azure 資訊安全中心管理安全性的建議

這份文件會引導您如何使用 Azure 資訊安全中心中的建議，可協助您保護 Azure 資源。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="what-are-security-recommendations"></a>安全性的建議是什麼？
資訊安全中心定期分析 Azure 資源的安全性狀態。 當資訊安全中心] 會識別潛在安全性漏洞時，它會建立建議。 建議會引導您進行必要的控制項的程序。

## <a name="implementing-security-recommendations"></a>實作安全性的建議

### <a name="set-recommendations"></a>設定建議

[設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，在您學習︰

- 設定安全性原則。
- 開啟資料集合。
- 選擇 [若要查看您的安全性原則的一部分的建議]。

目前原則建議中心周圍系統更新比較基準規則、 反惡意程式碼程式[網路安全性群組](../virtual-network/virtual-networks-nsg.md)子網路和網路介面、 SQL 資料庫稽核、 SQL 資料庫透明資料加密，以及 web 應用程式防火牆。  [設定安全性原則](security-center-policies.md)，可提供每個建議選項的描述。

### <a name="monitor-recommendations"></a>監視器的建議
設定的安全性原則之後, 資訊安全中心分析您的資源來識別潛在的弱點的安全性狀態。 **資訊安全中心**刀上的 [**建議**] 方塊可讓您知道 [識別資訊安全中心的建議的總數。

![建議方塊][1]

若要查看每個建議的詳細資料︰

1. 按一下 [**建議] 磚**的**資訊安全中心**刀]。 **建議**刀隨即會開啟。

建議會顯示每一列代表一個特定的建議的位置以表格格式。 在資料表的資料行如下︰

- **描述**︰ 說明建議，以及需要完成解決方法。
- **資源**︰ 列出要套用此建議的資源。
- **狀態**︰ 將告訴您目前狀態的建議︰
    - **開啟**︰ 建議尚未尚未處理。
    - **進行中**︰ 建議目前套用的資源，並由您需要執行任何動作。
    - **已解決**︰ 已完成的建議 （在此情況下，線條會會呈現灰色）。
- **嚴重性**︰ 說明該特定的建議的嚴重性︰
    - **高**︰ 弱點存在以有意義的資源 （例如應用程式、 VM 或網路安全性群組），而需要注意。
    - **中型**︰ 弱點和非要徑或額外的步驟所需將它移除或完成程序。
    - **低**︰ 存在的弱點應該收件者，但不需要立即關注。 （根據預設，不顯示低建議，但您可以篩選低建議事項如果您想要看到）。

使用下的表的參考，可協助您瞭解可用的建議，以及每個當您將它套用會進行的工作。

> [AZURE.NOTE] 您會想要了解[傳統和資源管理員部署模型](../azure-classic-rm.md)Azure 資源。

|建議|描述|
|-----|-----|
|[啟用資料收集適用於的訂閱](security-center-enable-data-collection.md)|建議您在您的訂閱中開啟的每一個您的訂閱及所有的虛擬機器 (Vm) 的安全性原則中的資料集合。|
|[修復 OS 弱點](security-center-remediate-os-vulnerabilities.md)|建議您對齊 OS 組態建議的設定規則，例如不允許儲存密碼。|
|[套用系統更新](security-center-apply-system-updates.md)|建議您先部署至 Vm 遺失系統安全與重要更新。|
|[系統更新後重新啟動](security-center-apply-system-updates.md#reboot-after-system-updates)|建議您重新啟動 VM 完成套用系統更新的程序。|
|[新增 web 應用程式防火牆](security-center-add-web-application-firewall.md)|建議您部署 web 端點的 web 應用程式防火牆 (WAF)。 您可以藉由新增到您現有的 WAF 部署這些應用程式來保護資訊安全中心中的多個 web 應用程式。 部署至不同的虛擬網路需要 WAF 就 （使用資源管理員部署模型建立）。 （建立使用傳統的部署模型） WAF 就會限制使用網路安全性群組。 這項支援會在未來延伸至 （傳統） 的 WAF 應用裝置完全自訂部署。 資訊安全中心會建議您佈建 WAF，以協助防範攻擊目標 web 應用程式上 Vm 和應用程式服務環境 (ASE)。 若要進一步瞭解 ASE 」 的資訊，請參閱[應用程式服務環境文件](../app-service/app-service-app-service-environments-readme.md)。 |
|[完成應用程式保護](security-center-add-web-application-firewall.md#finalize-application-protection)|若要完成 WAF 的設定，您必須路徑流量，以便 WAF 應用裝置。 追蹤此建議將完成所需的設定變更。|
|[新增下一步產生防火牆](security-center-add-next-generation-firewall.md)|建議您從 Microsoft 合作夥伴新增下一步產生防火牆 (NGFW)，以提升您的安全性保護。|
|[路由流量至 NGFW 只](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|建議您先設定您透過您 NGFW vm 強制輸入的流量的網路安全性群組 (NSG) 規則。|
|[安裝端點保護](security-center-install-endpoint-protection.md)|建議您佈建 Vm (Windows Vm) 的反惡意程式碼程式。|
|[解決端點保護狀況通知](security-center-resolve-endpoint-protection-health-alerts.md)|建議您解決端點保護失敗。|
|[在子網路或虛擬機器上啟用網路安全性群組](security-center-enable-network-security-groups.md)|建議您子網路或 Vm 上啟用 NSGs。|
|[限制存取透過網際網路圖示的端點](security-center-restrict-access-through-internet-facing-endpoints.md)|建議您在針對 NSGs 設定輸入的傳輸規則。|
|[啟用稽核 SQL server](security-center-enable-auditing-on-sql-servers.md)|建議您開啟稽核 Azure SQL server （SQL Azure 服務; 不包含 SQL 虛擬機器上執行）。|
|[啟用稽核 SQL 資料庫](security-center-enable-auditing-on-sql-databases.md)|建議您開啟稽核 Azure SQL 資料庫 （SQL Azure 服務; 不包含 SQL 虛擬機器上執行）。|
|[啟用 [SQL 資料庫上的 [透明資料加密](security-center-enable-transparent-data-encryption.md)|建議您啟用加密 SQL 資料庫 （SQL Azure 服務）。|
|[啟用 VM 代理程式](security-center-enable-vm-agent.md)|可讓您看到 Vm 需要 VM 代理程式。 VM 代理人必須安裝在 Vm，才能佈建修補程式掃描掃描的比較基準和反惡意程式碼的程式。 預設會安裝 VM 代理程式的部署從 Azure Marketplace 的 Vm。 文章[VM 代理程式 」 和 「 延伸模組-第 2 部分](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)提供如何安裝 VM 代理程式的資訊。|
| [套用磁碟加密](security-center-apply-disk-encryption.md) |建議您加密 VM 磁碟使用 Azure 磁碟加密 （Windows 和 Linux Vm）。 在您 VM OS 和資料區建議加密。|
|[提供安全性連絡人詳細資料](security-center-provide-security-contact-details.md) | 您提供的安全性的建議您的訂閱的每個連絡人資訊。 連絡人資訊是電子郵件地址和電話號碼。 如果我們安全性小組會找出已放棄您的資源，請連絡您的資訊會用。 |
| [更新作業系統版本](security-center-update-os-version.md) | 建議您可用的最新版本的雲端服務的更新作業系統 (OS) 版本，您的作業系統系列。  若要進一步瞭解雲端服務，請參閱[雲端服務的概觀](../cloud-services/cloud-services-choose-me.md)。 |
| [未安裝的弱點評估](security-center-vulnerability-assessment-recommendations.md) | 建議您在您 VM 安裝弱點評估解決方案。 |
| [修復弱點](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | 可讓您看到偵測到安裝在您 VM 弱點評估方案的系統和應用程式弱點。 |

您可以篩選，然後解除建議。

1. 按一下 [**建議**刀上的 [**篩選**]。 **篩選**刀隨即開啟，並選取您想要查看重要性] 和 [狀態值。

    ![篩選建議][2]

2. 如果您決定不適用建議，可以關閉建議，然後篩選出您的檢視。 有兩種方式來關閉建議。 其中一個方法是以滑鼠右鍵按一下項目，然後選取 [**解除]**。 其他可將游標停留在某個項目，按一下 [顯示] 右邊的三個點，然後選取 [**解除**。 您可以檢視關閉建議，按一下 [**篩選**]，然後選取 [ **Dismissed**。

    ![關閉建議][3]

### <a name="apply-recommendations"></a>套用建議
檢閱之後所有建議，決定您應該先套用。 我們建議您為要評估的建議的主要參數應該先套用使用嚴重性。

在上述建議資料表中，選取 [建議並逐一瀏覽為如何套用建議的範例。

## <a name="see-also"></a>另請參閱
在此文件中，您已簡介資訊安全中心中的安全性建議。 若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [監控 Azure 資訊安全中心的安全性狀況](security-center-monitoring.md)，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)，瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找使用服務的相關的常見問題集。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)，尋找相關 Azure 安全性和法規遵循的部落格文章。

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
