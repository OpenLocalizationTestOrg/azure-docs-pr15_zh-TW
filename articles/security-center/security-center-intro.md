<properties
   pageTitle="Azure 安全性中心簡介 |Microsoft Azure"
   description="深入了解 Azure 資訊安全中心、 其主要的功能及其運作方式。"
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# <a name="introduction-to-azure-security-center"></a>Azure 安全性中心簡介

深入了解 Azure 資訊安全中心、 其主要的功能及其運作方式。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。

## <a name="what-is-azure-security-center"></a>什麼是 Azure 資訊安全中心？
 資訊安全中心 」 可協助您避免、 偵測到，和回應威脅到增強可見性與控制您 Azure 資源的安全性。 整合式的安全性的監控和原則管理提供您 Azure 訂閱，有助於偵測威脅，否則請忽略和相關事務的安全性解決方案的主要生態系統。

##  <a name="key-capabilities"></a>主要功能
 資訊安全中心提供至 Azure 方便使用且有效威脅防止、 偵測和回應的內建的功能。 重要功能包括︰

| | |
|----- |-----|
| 防止 | 監視 Azure 資源的安全性狀態 |
| | 定義您的訂閱 Azure 和根據貴公司的安全性需求，資源群組原則的應用程式的使用及區分大小寫的資料類型。 |
| | 使用原則導向安全性的建議指南服務擁有者，透過實作的程序所需的控制項 |
| | 快速部署安全性服務及 Microsoft 和協力廠商裝置 |
| 偵測 |自動收集並分析您 Azure 資源、 網路、 與合作夥伴解決方案，例如反惡意程式碼的程式和防火牆的安全性資料 |
| | 全域 leverages 威脅智慧從 Microsoft 產品和服務、 Microsoft 數位犯罪單位 (DCU)、 Microsoft 安全性回應中心 (MSRC)，以及外部的摘要 |
| | 適用於進階的分析，包括電腦學習及行為的分析 |
| 回覆 | 提供優先順序的安全性事件/通知 |
| | 提供獲得深入見解的攻擊和受影響的資源來源 |
| | 建議停止目前攻擊，並協助防止未來攻擊的方法 |

## <a name="introductory-walkthrough"></a>簡介的逐步解說
 您可以存取資訊安全中心從[Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)。 [登入入口網站](https://portal.azure.com)，請選取 [**瀏覽]**，請然後捲動到 [**安全中心**] 選項或選取您先前釘選到入口網站的儀表板的 [**資訊安全中心**] 方塊。

![Azure 入口網站中的 [安全性] 磚][1]

從資訊安全中心，您可以設定安全性原則、 監控安全性設定，並檢視的安全性警告。

### <a name="security-policies"></a>安全性原則

您可以定義您的訂閱 Azure 和根據貴公司的安全性需求的資源群組原則。 您也可以調整到您正在使用的應用程式類型或資料的敏感度中每個訂閱。 例如，用於開發或測試資源可能比生產應用程式使用不同的安全性需求。 同樣地，例如 PII 規定資料的應用程式可能會要求較高的安全性。

> [AZURE.NOTE] 若要修改的訂閱層級或資源群組層級的安全性原則，您必須是訂閱擁有者或參與者它。

在**資訊安全中心**刀中，選取您的訂閱和資源群組清單的 [**原則**] 磚。   

![資訊安全中心刀][2]

在上**的安全性原則**刀選取訂閱檢視原則的詳細資訊。

![安全性原則刀訂閱][3]

**資料收集**（請參閱上述） 可讓資料收集的安全性原則。 啟用提供︰

- 每日掃描所有支援的安全性監控和建議的虛擬機器。
- 分析及威脅偵測安全性事件的集合。

**選擇每個地區的儲存空間帳戶**（請參閱上述） 可讓您選擇，每個地區必須虛擬機器執行，從這些虛擬機器中收集的資料儲存位置的儲存空間帳戶。 如果您未選擇每個區域的儲存空間帳戶，它會為您建立。 收集的資料是邏輯隔離基於安全性的其他客戶的資料。

> [AZURE.NOTE] 在訂閱層級設定資料收集與選擇每個地區的儲存空間帳戶。

若要開啟**防止原則**刀選取**防止原則**（請參閱上述）。 **顯示的建議**，可讓您選擇您想要監控和建議的安全性控制項根據訂閱中的資源的安全性需求。

接下來，請選取 [資源群組來檢視原則的詳細資料]。

![安全性原則刀資源群組][4]

**繼承**（請參閱上述） 可讓您定義為資源群組︰

- 繼承 （預設） 表示此資源群組的所有的安全性原則繼承自訂閱層級。
- 唯一這表示資源群組會有自訂的安全性原則。 您必須變更**顯示的建議**] 下。

> [AZURE.NOTE] 如果沒有衝突訂閱層級原則] 和 [資源群組層級原則，資源群組層級原則優先順序。

### <a name="security-recommendations"></a>安全性的建議

 資訊安全中心分析來識別潛在安全性漏洞 Azure 資源的安全性狀態。 建議的清單會引導您完成設定必要的控制項的程序。 範例包括︰

- 佈建協助找出並移除惡意軟體的反惡意程式碼
- 設定網路安全性群組以及規則來控制流量虛擬機器
- 佈建 web 應用程式防火牆，以協助防範攻擊的目標的 web 應用程式
- 部署遺失系統更新
- 處理 OS 設定不符合建議的比較基準

按一下 [建議的清單的 [**建議**] 磚。 按一下每個建議，若要檢視其他資訊，或採取行動以解決此問題。

![在 Azure 資訊安全中心的安全性建議][5]

### <a name="resource-health"></a>資源狀況

[**資源安全性健康狀況**] 方塊會顯示依資源類型，包括虛擬機器、 web 應用程式，與其他資源的環境的整體安全性狀態。   

選取 [檢視詳細的資訊，包括任何潛在的安全性漏洞已識別之清單的 [**資源安全性狀況**圖磚上的資源類型]。 （在下面的範例中已選取**虛擬機器**）。

![資源健康狀況] 磚][6]

### <a name="security-alerts"></a>安全性警告

 資訊安全中心會自動收集、 分析，並整合 Azure 資源、 網路、 與合作夥伴解決方案，例如反惡意程式碼的程式和防火牆記錄資料。 未偵測到威脅，就會建立的安全性提醒中。 範例包括偵測的︰

- 洩漏已知惡意的 IP 位址與通訊的虛擬機器
- 使用 Windows 錯誤報告偵測到的進階惡意程式碼
- 強制攻擊針對虛擬機器
- 從 [整合式反惡意程式碼的程式和防火牆的安全性警告

按一下 [**安全性警告**] 方塊顯示的優先順序提醒的清單。

![安全性警告][7]

選取通知會顯示攻擊和如何修復其建議的詳細資訊。

![安全性警訊的詳細資訊][8]

### <a name="partner-solutions"></a>合作夥伴解決方案

**合作夥伴解決方案**磚可讓您監控您的合作夥伴解決方案的健康狀態整合 Azure 訂閱概覽。 資訊安全中心 」 會顯示來自解決方案的提醒。

選取 [**合作夥伴解決方案**磚。 刀隨即開啟並顯示的所有連線的合作夥伴解決方案清單。

![合作夥伴解決方案][9]

## <a name="get-started"></a>快速入門
若要開始與資訊安全中心，您需要 Microsoft Azure 的訂閱。 資訊安全中心被啟用 Azure 訂閱。 如果您沒有訂閱，您可以註冊[免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

 您可以存取資訊安全中心從[Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)。 請參閱若要深入瞭解[入口網站的文件](https://azure.microsoft.com/documentation/services/azure-portal/)。

快速[入門 Azure 資訊安全中心](security-center-get-started.md)引導您透過安全性監視及原則管理元件的資訊安全中心。

## <a name="see-also"></a>另請參閱
在此文件中，您已簡介資訊安全中心、 其主要功能及如何開始使用。 若要深入瞭解，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [管理 Azure 資訊安全中心中的安全性建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [監控 Azure 資訊安全中心的安全性狀況](security-center-monitoring.md)，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)，瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找使用服務的相關的常見問題集。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-取得最新的 Azure 安全性新聞及資訊。

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png
