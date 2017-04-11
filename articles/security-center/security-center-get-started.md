<properties
   pageTitle="Azure 資訊安全中心快速入門指南 |Microsoft Azure"
   description="本文可協助您快速開始使用 Azure 資訊安全中心引導您瞭解的安全性的監控和原則管理元件，您連結至下一個步驟。"
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
   ms.date="10/28/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-quick-start-guide"></a>Azure 資訊安全中心快速入門指南

本文可協助您快速以開始使用 Azure 資訊安全中心引導您瞭解資訊安全中心的安全性監視及原則管理元件。

> [AZURE.NOTE] 本文會使用部署範例介紹服務。 本文並未的逐步指南。

## <a name="prerequisites"></a>必要條件

若要開始與資訊安全中心，您必須使用 Microsoft Azure 的訂閱。 如果您沒有訂閱，您可以註冊[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。

免費的層的資訊安全中心會自動啟用與您的訂閱，並提供完善 Azure 資源的安全性狀態。 會從 Azure 的合作夥伴，提供基本的安全性原則管理、 安全性的建議，以及整合與安全性產品和服務。

您可以存取資訊安全中心從[Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)。 若要進一步瞭解 Azure 入口網站，請參閱[入口網站的文件](https://azure.microsoft.com/documentation/services/azure-portal/)。

## <a name="data-collection"></a>資料收集

從您的虛擬機器 (Vm)，來評估安全性狀態、 提供安全性的建議，並通知您威脅資訊安全中心 」 會收集的資料。 當您第一次存取資訊安全中心 」 時，您的訂閱中的所有 Vm 都啟用資料集合。 建議您資料的集合，但您可以關閉資料收集的資訊安全中心原則中退出。

下列步驟說明如何存取和使用資訊安全中心的元件。 按照這些步驟，教您如何關閉資料集合，如果您選擇退出。

## <a name="access-security-center"></a>存取資訊安全中心

在入口網站，請遵循下列步驟來存取資訊安全中心︰

1. 在 [ **Microsoft Azure** ] 功能表中，選取 [**資訊安全中心**]。
![Azure 功能表][1]

2. 如果您第一次存取資訊安全中心，隨即會開啟 [**歡迎使用**刀。 選取 [**是 ！我想啟動 Azure 資訊安全中心**開啟**資訊安全中心**刀並啟用資料集合。
![歡迎使用] 畫面][10]

3. 從 [歡迎使用刀啟動資訊安全中心 」，或從 Microsoft Azure 功能表選取 [資訊安全中心之後，隨即會開啟 [**資訊安全中心**刀。 輕鬆存取**資訊安全中心**刀以後選取**Pin 刀至儀表板**上的 [（左上方）。
![釘選刀至儀表板] 選項][2]

## <a name="use-security-center"></a>使用資訊安全中心

您可以設定您的訂閱 Azure 和資源群組的安全性原則。 讓我們來設定您的訂閱的安全性原則︰

1. 在**資訊安全中心**刀中，選取 [**原則**] 磚。
![安全性原則][3]

2. 在 [**定義每個訂閱] 或 [資源群組原則的安全性原則-**刀中，選取 [訂閱]。
3. **安全性原則**防禦，以**資料收集**被啟用自動收集記錄。 監控的副檔名是佈建後，在訂閱中的目前和新 Vm。 （您可以退出資料收集**資料收集**] 設定為**關閉**，但這可防止資訊安全中心 」 可讓您的安全性警告與建議事項）。
4. 在 [**安全性原則**刀中，選取 [**選擇每個地區的儲存空間帳戶**]。 必須 Vm 執行每個區域，您可以選擇從這些 Vm 收集的資料儲存位置的儲存空間帳戶。 如果您未選擇每個區域的儲存空間帳戶，則會建立適合您。 收集的資料是邏輯隔離基於安全性的其他客戶的資料。

     > [AZURE.NOTE] 我們建議您啟用收集資料，並選擇儲存帳戶的訂閱層級，第一次。 安全性原則可以設定 Azure 訂閱層級和資源群組層級，但只有訂閱層級時所發生的資料收集與儲存帳戶的設定。

5. 在 [**安全性原則**刀中，選取 [**防止原則**]。 這會開啟**防止原則**刀。
![防止原則][4]

6. 在**防止原則**刀中，開啟您想要查看您的安全性原則的一部分的建議。 範例︰

 - 設定**系統更新****上**掃描所有支援的虛擬機器遺漏 OS 更新。
 - 設定**OS 弱點****上**掃描所有支援的虛擬機器識別可能會讓虛擬機器更容易受到攻擊的任何 OS 設定。

### <a name="view-recommendations"></a>檢視建議

1. 返回**資訊安全中心**刀，然後選取 [**建議**] 方塊。 資訊安全中心定期分析 Azure 資源的安全性狀態。 資訊安全中心] 會識別潛在安全性漏洞，其會顯示建議**建議**刀上。
![Azure 資訊安全中心中的建議][5]

2.  選取 [若要檢視的詳細資訊，及/或採取行動以解決此問題的**建議**刀建議]。

### <a name="view-the-health-and-security-state-of-your-resources"></a>檢視您的資源的健康狀況與安全性狀態

1.  返回**資訊安全中心**刀。 [**資源安全性健康狀況**] 方塊包含虛擬機器、 網路、 資料及應用程式的安全性狀態指標。
2.  選取要檢視的詳細資訊的**虛擬機器**。 **虛擬機器**刀隨即開啟，並顯示的反惡意程式碼程式、 系統更新，重新啟動和 OS 弱點您 Vm 狀態摘要。
![Azure 資訊安全中心中的 [資源健康狀況] 磚][6]

3.  選取下檢視的詳細資訊，及/或採取動作的**虛擬機器建議**的建議設定必要的控制項。
4.  選取下若要檢視其他詳細資料的**虛擬機器**VM。

### <a name="view-security-alerts"></a>檢視的安全性警告

1.  返回**資訊安全中心**刀，然後選取 [**安全性警告**] 磚。 **安全性警訊**刀開啟，並顯示提醒的清單。 您的安全性記錄和網路活動的資訊安全中心分析會產生下列通知。 整合式的合作夥伴解決方案的通知都包含在內。
![在 Azure 資訊安全中心的安全性警告][7]

    > [AZURE.NOTE] 安全性警訊僅有提供的標準的層的資訊安全中心已啟用。 使用標準層 90 天免費試用版。 如何取得標準層的詳細資訊，請參閱[後續步驟](#next-steps)。

2.  選取 [若要檢視其他資訊的提醒]。 在此範例中，讓我們選取**修改系統二進位發現**。 這會開啟刀提供其他詳細資料的相關提醒。
![Azure 資訊安全中心中的安全性提醒詳細資料][8]

### <a name="view-the-health-of-your-partner-solutions"></a>檢視您的合作夥伴解決方案的狀況

1. 返回**資訊安全中心**刀。 **合作夥伴解決方案**磚可讓您一眼監控您的合作夥伴解決方案與 Azure 訂閱整合的健康狀態。
2. 選取 [**合作夥伴解決方案**磚。 刀開啟，並顯示您連線至資訊安全中心的合作夥伴解決方案清單。
![合作夥伴解決方案][9]

3. 選取 [合作夥伴解決方案]。 在此範例中，讓我們來選取**F5 WAF**方案。  刀隨即開啟，並顯示您的狀態合作夥伴解決方案與解決方案的相關聯的資源。 選取**方案主控台**] 以開啟此方案的合作夥伴管理體驗。

## <a name="next-steps"></a>後續步驟
本文為您簡介資訊安全中心的安全性監視及原則管理元件。 現在，您已經熟悉資訊安全中心 」，請嘗試下列步驟︰

- 設定您的 Azure 訂閱的安全性原則。 若要深入瞭解，請參閱[Azure 資訊安全中心中的設定安全性原則](security-center-policies.md)。
- 使用資訊安全中心中的建議，可協助您保護 Azure 資源。 若要深入瞭解，請參閱[在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)。
- 檢閱並管理您目前的安全性警告。 若要深入瞭解，請參閱[管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)。
- 進一步瞭解[進階威脅偵測功能](security-center-detection-capabilities.md)隨附的資訊安全中心[標準層](security-center-pricing.md)。 使用標準層 90 天免費試用版。
- 如果您有使用資訊安全中心的相關問題，請參閱[Azure 安全性中心常見問題集](security-center-faq.md)。

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
