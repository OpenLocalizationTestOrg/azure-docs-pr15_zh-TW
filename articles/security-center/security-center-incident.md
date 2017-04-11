<properties
   pageTitle="處理在 Azure 資訊安全中心的安全性事件 |Microsoft Azure"
   description="這份文件可協助您使用 Azure 資訊安全中心 」 功能來處理安全性事件。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="handling-security-incident-in-azure-security-center"></a>處理在 Azure 資訊安全中心的安全性事件 
分類和調查的安全性警告耗時甚至最技術安全性分析師，而許多很難知道開始的位置。 藉由使用[狀況分析](security-center-detection-capabilities.md)連線之間不同[的安全性警告](security-center-managing-and-responding-alerts.md)的資訊，資訊安全中心可以提供單一檢視攻擊活動和所有相關通知，因此您可以快速瞭解攻擊採取什麼動作，以及哪些資源所影響。

本文討論如何使用在資訊安全中心的安全性提醒功能，協助您處理安全性事件。


## <a name="what-is-a-security-incident"></a>什麼是安全性事件？

安全性事件資訊安全中心是彙總的資源對齊[刪除鏈](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/)圖樣的所有通知。 事件會出現在[的安全性警告](security-center-managing-and-responding-alerts.md)] 方塊與。 事件會顯示相關通知，可讓您取得更多有關每個項目清單。

## <a name="managing-security-incidents"></a>管理安全性事件

您可以查看安全性通知磚來檢視您目前的安全性事件。 存取 Azure 入口網站，然後依照下列步驟以查看每個安全性事件的相關詳細資料︰

1. 上的資訊安全中心儀表板，您會看到**安全性警告**] 磚。

    ![在資訊安全中心的安全性警告] 磚](./media/security-center-incident/security-center-incident-fig1.png)

2.  按一下 [在這個並排顯示上以展開，而且如果安全性事件偵測到，它會出現在的安全性提醒 graph 下方，如下所示︰

    ![安全性事件](./media/security-center-incident/security-center-incident-fig2.png)

3.  請注意安全性事件描述有不同的圖示相較於其他提醒。 按一下以檢視更多關於此事件的詳細資料。

    ![安全性事件](./media/security-center-incident/security-center-incident-fig3.png)

4.  在**事件**刀更多，您會看到詳細資料] 此安全性事件，包括其完整的說明，其嚴重性 （在本例中為 [高），瞭解其目前狀態 (在此情況下仍*作用中*，這表示使用者尚未採取動作*關閉*-進行中**的安全性警告**刀事件上按一下滑鼠右鍵)（在此案例*VM1*) 受攻擊的資源補救步驟事件，以及在下方窗格中，您必須包含此事件的提醒。 如果您想要取得詳細資訊，在每個通知，只要按一下它與另一個會開啟，如下所示︰

    ![安全性事件](./media/security-center-incident/security-center-incident-fig4.png)

此刀上的資訊會根據提醒而有所不同。 如需有關如何管理這些通知，請閱讀[管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)。 此功能一些重要考量︰

- 新的篩選器可讓您自訂您的檢視，只事件、 通知、 或兩者。 
- 將相同的通知可以存在於一部分的事件 （如果適用的話），以及顯示為獨立的提醒。 
- 關閉非事件不會關閉相關的警示。

## <a name="see-also"></a>另請參閱

在此文件中，您學到如何使用中資訊安全中心的安全性事件的功能。 若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)
- [Azure 資訊安全中心偵測功能](security-center-detection-capabilities.md)
- [Azure 資訊安全中心規劃及作業指南](security-center-planning-and-operations-guide.md)
- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循。
