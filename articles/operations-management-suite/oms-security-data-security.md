<properties
   pageTitle="作業管理套件安全性和稽核解決方案資料安全性 |Microsoft Azure"
   description="本文說明如何管理及作業管理套件安全性和稽核解決方案的安全防護資料。"
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="yurid"/>

# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>作業管理套件安全性和稽核解決方案資料的安全性

若要協助防止、 偵測到，並回應威脅的客戶，[作業管理套件 (OMS) 的安全性和稽核方案](operations-management-suite-overview.md)會收集並處理資料的相關資源，包括︰

- 安全性事件記錄檔
- 事件追蹤 Windows (ETW) 事件
- Applocker 允許稽核的事件
- 在 Windows 防火牆記錄
- 進階的威脅分析事件
- 比較基準評估結果
- 反惡意程式碼評估結果
- 更新/修補程式評估結果
- Syslogs 資料流明確啟用代理程式

我們會將強式承諾以保護隱私權與安全性的資料。 Microsoft 遵循嚴格規範與安全性的指導方針，從編碼操作服務。
本文說明如何管理，安全防護 OMS 安全性和稽核方案中的資料。

## <a name="data-sources"></a>資料來源

OMS 安全性和稽核解決方案分析您的虛擬機器和實體 OMS 代理程式安裝的電腦上的資料。 OMS 安全性和稽核解決方案可以收集安全性事件，例如 Windows 事件、 稽核記錄、 IIS 記錄和系統訊息的設定資訊。 這類資料的範例︰ 作業系統類型與執行程序、 電腦名稱、 IP 位址] 的版本有登入的使用者，以及租用戶 id。  

## <a name="data-protection"></a>資料保護

**資料責任分隔**︰ 會保留資料邏輯上的獨立整個服務每個元件。 每個組織已標記的所有資料。 整個資料生命週期，此標記仍然存在，且執行服務的每個層級。 

**資料存取**︰ 提供安全性的建議和調查潛在安全性威脅，Microsoft 人員可以存取收集或分析服務的資訊。 我們將遵守[Microsoft 線上服務合約](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和[隱私權聲明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)，Microsoft 就不使用客戶資料，或衍生任何廣告或類似的商業用途從該資訊的狀態。 若要提供安全性的建議及調查潛在安全性威脅，Microsoft 人員可能存取收集或分析服務的資訊。 我們只使用客戶資料視需要提供 Azure 服務，包括用途相容於提供這些服務。 您可保留所有您自己的資料的權限。

**使用資料**︰ Microsoft 使用的圖樣與跨多個租用戶看到威脅智慧來強化我們防止和偵測功能;我們這麼做，根據我們[隱私權聲明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)所述的隱私權承諾的。

> [AZURE.NOTE] 資料的位置層級 OMS 工作區，設定工作區建立時，也就是初始 OMS 安全性和稽核設定程序的一部分。

## <a name="see-also"></a>另請參閱

在此文件中，您學到如何管理，安全防護 OMS 中的資料。 若要進一步瞭解 OMS 安全性和稽核方案，請參閱︰

- [作業管理套件 (OMS) 概觀](operations-management-suite-overview.md)
- [監控和回應作業管理套件安全性和稽核方案中的安全性警告](oms-security-responding-alerts.md)
- [監視作業管理套件安全性和稽核方案中的資源](oms-security-monitoring-resources.md)

