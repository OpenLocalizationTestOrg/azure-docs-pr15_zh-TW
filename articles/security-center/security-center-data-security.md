<properties
   pageTitle="Azure 資訊安全中心資料安全性 |Microsoft Azure"
   description="這份文件會說明如何管理，在 Azure 資訊安全中心安全防護資料。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-data-security"></a>Azure 資訊安全中心資料安全性
若要協助防止、 偵測到，並回應威脅，Azure 資訊安全中心 」 會收集並處理您 Azure 的資源，包括設定資訊、 中繼資料、 事件記錄檔的相關資料的客戶損毀傾印檔案及其他內容。 我們會將強式承諾以保護隱私權與安全性的資料。 Microsoft 遵循嚴格規範與安全性的指導方針，從編碼操作服務。 

本文說明如何管理，在 Azure 資訊安全中心安全防護資料。

## <a name="data-sources"></a>資料來源

Azure 資訊安全中心會從下列來源的資料︰

- Azure 服務︰ 讀取您已經部署，與該服務的資源提供者通訊設定 Azure 服務的相關資訊。
- 網路流量︰ 讀取取樣網路流量中繼資料從 Microsoft 的基礎結構，例如來源目的地 IP/連接埠封包大小和網路通訊協定。
- 合作夥伴解決方案︰ 從整合式的合作夥伴解決方案，例如防火牆和反惡意程式碼解決方案收集的安全性警告。 此資料會儲存於 Azure 資訊安全中心儲存空間，目前位於美國。
- 您的虛擬機器︰ Azure 資訊安全中心可以收集設定資訊和安全性事件，例如 Windows 事件的相關資訊，並稽核記錄、 IIS 記錄、 系統訊息，以及當機傾印檔案從您使用的資料集合代理程式的虛擬機器。 請參閱下方的其他詳細資料] 管理資料收集 」 區段。  

此外，相關的安全性警告、 建議和安全性健康狀態資訊會儲存於 Azure 資訊安全中心儲存空間，目前位於美國。 這項資訊可能會包含相關的設定資訊和安全性事件收集從您的虛擬機器視需要為您提供的安全性提醒中，建議或安全性健康狀態。

## <a name="data-protection"></a>資料保護

**資料責任分隔**︰ 會保留資料邏輯上的獨立整個服務每個元件。 每個組織已標記的所有資料。 整個資料生命週期，此標記仍然存在，且執行服務的每個層級。 此外，從您的虛擬機器中收集的資料儲存在您儲存帳戶。

**資料存取**︰ Microsoft 人員可能會提供安全性的建議和調查潛在安全性威脅，才能存取收集或由 Azure 服務，包括當機傾印檔案分析的資訊。 當機傾印檔案及程序建立事件可能不小心包含客戶資料或從您的虛擬機器個人資料。 我們將遵守[Microsoft 線上服務合約](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和[隱私權聲明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)，Microsoft 就不使用客戶資料，或衍生任何廣告或類似的商業用途從該資訊的狀態。 我們只使用客戶資料視需要提供 Azure 服務，包括用途相容於提供這些服務。 您可保留所有客戶資料的權限。

**使用資料**︰ Microsoft 使用的圖樣與跨多個租用戶看到威脅智慧來強化我們防止和偵測功能;我們這麼做，根據我們[隱私權聲明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)所述的隱私權承諾的。

**資料的位置**︰ 儲存帳戶為指定之位置的虛擬機器執行的每個區域。 這可讓您將資料儲存為從資料會收集虛擬機器相同的區域。 此資料，包括當機傾印檔案，會持續儲存在您儲存的帳戶。 服務也會儲存包括中 Azure 資訊安全中心儲存空間，目前位於美國的整合式的合作夥伴解決方案、 建議和安全性健康狀態的通知的安全性警告的相關資訊。

## <a name="managing-data-collection-from-virtual-machines"></a>從虛擬機器管理資料收集

當您選擇啟用 Azure 資訊安全中心時，資料集合會開啟您的訂閱的每個。 您可以關閉 Azure 安全性中心儀表板 」 的安全性原則 」 一節中的資料集合。 資料收集已開啟，Azure 資訊安全中心條款上所有現有的 Azure 監控代理程式受支援虛擬機器和新建立的文件。 Azure 安全性監視副檔名為掃描的各種相關的安全性設定和事件[的 Windows 事件追蹤](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)(ETW) 將它追蹤。 此外，系統會引發過程中執行電腦的事件記錄檔事件。 這類資料的範例︰ 作業系統類型與作業系統系統的記錄 （Windows 事件記錄]），執行程序、 電腦名稱、 IP 位址] 的版本，登入的使用者，以及租用戶 id。 Azure 監控代理程式會讀取事件記錄項目並 ETW 追蹤，並將其複製到您儲存的帳戶進行分析。 

儲存帳戶指定必須執行資料收集從虛擬機器中的儲存相同的區域的虛擬機器每個區域。 如此可讓您輕鬆地讓資料保持在相同的地理區域的隱私權與資料主權用途。 您可以設定每個地區儲存的帳戶的 Azure 安全性中心儀表板 」 的安全性原則] 區段中。

Azure 監控代理程式也會將當機傾印檔案複製至您儲存的帳戶。  Azure 資訊安全中心收集暫時當機傾印檔案的複本，並利用嘗試及成功折衷的分析它們。  Azure 資訊安全中心執行此儲存的帳戶，為相同的地理區域內的分析，並刪除暫時複本，當分析完成。

您可以停用虛擬機器中的資料收集在任何時間，將會移除先前安裝 Azure 資訊安全中心任何監控代理程式。


## <a name="see-also"></a>另請參閱

在此文件中，您學到如何管理，在 Azure 資訊安全中心安全防護資料。 若要進一步瞭解 Azure 資訊安全中心 」，請參閱︰

- [Azure 安全性中心規劃及作業指南](security-center-planning-and-operations-guide.md)，瞭解如何規劃及瞭解採用 Azure 資訊安全中心的設計考量。
- [監控 Azure 資訊安全中心的安全性狀況](security-center-monitoring.md)，瞭解如何監視 Azure 資源的狀況
- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)，瞭解如何管理和回應的安全性警告
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找使用服務的相關的常見問題集
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循
