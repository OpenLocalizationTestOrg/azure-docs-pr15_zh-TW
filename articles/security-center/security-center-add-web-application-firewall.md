<properties
   pageTitle="新增 web 應用程式防火牆在 Azure 資訊安全中心 |Microsoft Azure"
   description="這份文件為您示範如何實作 Azure 資訊安全中心建議**新增 web 應用程式防火牆**和**Finalize 應用程式保護**。"
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="add-a-web-application-firewall-in-azure-security-center"></a>新增 web 應用程式防火牆在 Azure 資訊安全中心

Azure 資訊安全中心可能建議您從 Microsoft 合作夥伴新增 web 應用程式防火牆 (WAF)，才能安全 web 應用程式。 這份文件會引導您執行此動作的範例。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議**刀中，選取 [**安全使用 web 應用程式防火牆 web 應用程式**]。
![安全的 web 應用程式][1]

2. 在**安全 web 應用程式使用 web 應用程式防火牆**刀中，選取 web 應用程式。 **新增 Web 應用程式防火牆**刀隨即會開啟。
![新增 web 應用程式防火牆][2]
3. 您可以選擇使用現有的 web 應用程式防火牆，如果有的話，或您可以建立新的項目。 在此範例中沒有任何現有的 WAFs 可用，好讓我們會建立新的 WAF。

4. 若要建立新的 WAF，選取 [整合式協力廠商的清單中的 [方案]。 我們會在此範例中，選取 [ **Barracuda Web 應用程式防火牆**。
5. 隨即會開啟**Barracuda Web 應用程式防火牆**刀，提供您的合作夥伴解決方案的相關資訊。 您可以選取 [**建立**資訊刀中。
![防火牆資訊刀][3]

6. **新的 Web 應用程式防火牆**刀隨即開啟，您可以執行**VM 設定**步驟及提供**WAF 資訊**的位置。 選取 [ **VM 設定**]。

7. 您可以在**VM 設定**刀輸入虛擬機器執行 WAF 向上微調所需的資訊。
![VM 設定][4]
8. 返回**新的 Web 應用程式防火牆**刀，並選取**WAF 資訊**。 您可以在**WAF 資訊**刀中設定 WAF 本身。 步驟 7 可讓您設定虛擬機器都能夠執行 WAF 並步驟 8 可讓您佈建 WAF 本身。

## <a name="finalize-application-protection"></a>完成應用程式保護

1. 返回**建議**刀。 建立 WAF，稱為**Finalize 應用程式保護**後產生新的項目。 這個項目可讓您知道您需要完成的實際線路圖 WAF 內 Azure 虛擬網路設定讓它可以保護應用程式的程序。
![完成應用程式保護][5]

2. 選取 [ **Finalize 應用程式保護**]。 隨後便會開啟新的刀。 您可以看到是需要變更其流量的 web 應用程式。
3. 選取 [web 應用程式]。 刀會開啟，可提供給您的步驟完成 web 應用程式的防火牆設定。 完成的步驟，然後再選取 [**限制流量**。 資訊安全中心接著將為您進行路向上。
![限制流量][6]

> [AZURE.NOTE] 您可以藉由新增到您現有的 WAF 部署這些應用程式來保護資訊安全中心中的多個 web 應用程式。 部署至不同的虛擬網路需要 WAF 就 （使用資源管理員部署模型建立）。 （建立使用傳統的部署模型） WAF 就會限制使用網路安全性群組。 這項支援會在未來延伸至 （傳統） 的 WAF 應用裝置完全自訂部署。 進一步瞭解[傳統和資源管理員部署模型](../azure-classic-rm.md)Azure 資源。

從該 WAF 記錄現在完全整合。 自動收集並分析記錄，使其可呈現重要的安全性提醒您，就可以開始資訊安全中心。

## <a name="see-also"></a>另請參閱

這份文件會示範如何實作資訊安全中心建議 」 新增 web 應用程式]。 若要進一步瞭解如何設定 web 應用程式防火牆，請參閱下列各項︰

- [設定 Web 應用程式防火牆 (WAF) 應用程式服務環境](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循。

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
