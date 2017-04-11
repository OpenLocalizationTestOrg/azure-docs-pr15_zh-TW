<properties
   pageTitle="監控和回應作業管理套件安全性和稽核方案中的安全性警告 |Microsoft Azure"
   description="這份文件可協助您使用適用於 OMS 安全性和稽核威脅智慧選項來監控和回應的安全性警告。"
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>監控和回應作業管理套件安全性和稽核方案中的安全性警告

這份文件可協助您使用適用於 OMS 安全性和稽核威脅智慧選項來監控和回應的安全性警告。

## <a name="what-is-oms"></a>什麼是 OMS？

Microsoft 作業管理套件 (OMS) 是以 Microsoft 的雲端基礎可協助您管理及保護您的內部部署與雲端基礎結構的 IT 管理解決方案。 如需有關 OMS 的詳細資訊，請閱讀文章[作業管理套件](https://technet.microsoft.com/library/mt484091.aspx)。

## <a name="threat-intelligence"></a>威脅智慧

在企業環境使用者主要網路存取與使用各種不同的裝置連線至企業資料的位置，請務必您可以用來主動監控您的資源，並快速回應安全性事件。 這是特定的重要安全性生命週期觀點因為可能不會引發威脅一些 cybersecurity 通知或可疑可以由技術的傳統的安全性控制項的活動。 

藉由使用適用於 OMS 安全性和稽核**威脅智慧**選項，IT 系統管理員可以識別安全性風險環境，例如，找出特定的電腦是否[botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection)一部分。 電腦時攻擊非法安裝暗中連接至] 命令和控制項的 [此電腦的惡意程式碼，則可能會在 botnet 節點。 它也可以識別來自地底的通訊通道，例如[darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents)的潛在威脅。 

若要建立此威脅智慧，OMS 安全性和稽核使用來自 Microsoft 內的多個來源的資料。 OMS 安全性和稽核會利用此資料至識別您環境的潛在威脅。

威脅智慧窗格是由三個主要的搜尋選項所組成︰
- 具有輸出惡意流量的伺服器
- 偵測到的威脅類型
- 威脅智慧地圖

> [AZURE.NOTE] 針對所有這些選項的概觀，請閱讀[快速入門作業管理套件安全性和稽核方案](oms-security-getting-started.md)。

### <a name="responding-to-security-alerts"></a>回應的安全性警告

[安全性應計](https://technet.microsoft.com/library/cc512623.aspx)程序的步驟是識別入侵系統的嚴重性。 在此階段中，您應該執行下列工作︰

- 判斷攻擊的性質
- 決定攻擊的來源
- 決定攻擊的目的。 已攻擊是直接在您組織取得更具體的資訊，或隨機？
- 找出已洩露系統
- 找出已被存取並判斷這些檔案的敏感度的檔案

您可以利用**威脅智慧**OMS 安全性和稽核解決方案，以協助進行這些任務的資訊。 請遵循下列步驟來存取此**威脅智慧**選項︰

1. 在 [ **Microsoft 作業管理套件**主儀表板按一下**安全性和稽核**圖磚。

    ![安全性和稽核](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. 中的**安全性和稽核**儀表板，您會看到**威脅智慧**選項在右側，如下所示︰

    ![威脅 intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

下列三個方塊可讓您目前的潛在威脅的概觀。 您可以找出您正在監視 （內部或外部網路） 的任何電腦時的**輸出惡意流量伺服器**中的傳送惡意流量至網際網路。 

**偵測到威脅類型**] 方塊會顯示 「 在外 」 目前的潛在威脅的摘要，如果您按一下 [在這個並排顯示上您會看到這些潛在威脅的相關的更多詳細資料，如下所示︰

![偵測到的威脅類型](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

您可以在其上按一下 [解壓縮每個威脅的詳細資訊。 下列範例顯示 Botnet 相關的更多詳細資料︰

![關於潛在威脅的更多詳細資料](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

本節的開頭所述，這項資訊可以很有幫助期間的應計大小寫。 也很重要期間法庭法律調查證據要尋找的攻擊，系統已洩露，時間表來源。 例如，您可輕鬆識別攻擊的相關資訊的一些重要詳細資料報表中︰ 攻擊、 受損本機 IP 和目前的工作階段狀態的連線的來源。 

**威脅智慧地圖**可協助您識別有惡意流量各地的目前位置。 有橘色 （內送） 和紅色 （外寄） 箭號] 此圖中，以識別流量的方向，如果您按一下其中一個這些箭號，隨即會顯示類型威脅及流量方向，如下所示︰

![威脅 intel 地圖](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [AZURE.NOTE] 您可以觀看示範如何使用此功能的事件回應程序觀看簡報[降低資料中心的安全性威脅逐步調查使用作業管理套件與](https://myignite.microsoft.com/videos/5000)交付 Microsoft Ignite。

## <a name="see-also"></a>另請參閱

在此文件中您將學會如何使用**威脅智慧**選項 OMS 安全性和稽核解決方案回應的安全性警告。 若要進一步瞭解 OMS 安全性，請參閱下列文章︰

- [作業管理套件 (OMS) 概觀](operations-management-suite-overview.md)
- [開始使用作業管理套件安全性與稽核解決方案](oms-security-getting-started.md)
- [監視作業管理套件安全性和稽核方案中的資源](oms-security-monitoring-resources.md)
