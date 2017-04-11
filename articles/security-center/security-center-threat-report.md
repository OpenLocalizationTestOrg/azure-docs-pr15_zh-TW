<properties
   pageTitle="Azure 資訊安全中心威脅智慧報表 |Microsoft Azure"
   description="這份文件可協助您尋找更多關於安全性警訊法律調查證據期間使用 Azure 安全性中心威脅智慧報表。"
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
   ms.date="10/17/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-threat-intelligence-report"></a>Azure 資訊安全中心威脅智慧報表
本文說明 Azure 安全性中心威脅智慧報表如何協助您進一步瞭解威脅產生的安全性提醒中。

## <a name="what-is-a-threat-intelligence-report"></a>什麼是威脅智慧報表？
資訊安全中心威脅偵測的運作方式監控從 Azure 資源、 網路和連線的合作夥伴解決方案的安全性資訊。 分析這項資訊，通常相互關聯來識別威脅的多個來源中的資訊。 此程序是資訊安全中心[偵測功能](security-center-detection-capabilities.md)。 

資訊安全中心另威脅，它會觸發程序中[的安全性提醒中](security-center-managing-and-responding-alerts.md)，其中包含有關特定的事件，包括補救建議的詳細的資訊。 若要協助團隊調查並修復威脅事件的回應，安全中心包含威脅智慧報表包含偵測到，例如包括資訊的潛在威脅的相關資訊: 

- 攻擊者的身分識別或者 （如果這項資訊可用） 的關聯
- 攻擊的目標
- 目前與歷史攻擊行銷活動 （如果就能使用這項資訊）
- 攻擊的戰術、 工具與程序
- 例如 Url 和檔案雜湊危害 (IoC) 的關聯的指標
- Victimology，且產業地理普遍性，以協助您判斷 Azure 資源是否風險
- 降低和補救的資訊

>[AZURE.NOTE] 在任何特定的報表中的資訊量而異。詳細程度根據惡意程式碼的活動和普遍性。

資訊安全中心 」 有三種類型的潛在威脅的報表，以根據攻擊而有所不同。 可用的報表有︰

- **活動群組報表**︰ 提供深層 dives 駭客、 目標和戰術。
- **行銷活動報表**︰ 著重於特定攻擊行銷活動的詳細資料。 
- **威脅摘要報表**︰ 涵蓋所有先前的兩個報表中的項目。

此類型的資訊會很有幫助[應計](security-center-incident-response.md)過程中，若要瞭解攻擊、 者推動和處理方式若要降低向前移動此問題的來源持續法律調查證據的地方。 

## <a name="how-to-access-the-threat-intelligence-report"></a>如何存取威脅智慧報表？

您可以查看 [**安全性警告**] 方塊，來檢視您目前的通知。 開啟 Azure 入口網站，然後依照下列步驟以查看每個通知相關的更多詳細資料︰

1. 上的資訊安全中心儀表板，您會看到**安全性警告**] 磚。

2. 按一下磚來開啟含有更多關於通知的詳細資料**的安全性警告**刀，然後按一下 [在您想要取得相關詳細資訊的安全性提醒中。

    ![安全性警告](./media/security-center-threat-report/security-center-threat-report-fig1.png)

3. 在此情況下**可疑的程序執行**刀顯示提醒詳細資料如下圖所示︰

    ![安全性警訊 detais](./media/security-center-threat-report/security-center-threat-report-fig2.png)

4.  可用的每個的安全性提醒中資訊的量會根據的提醒類型而有所不同。 在 [**報表**] 欄位中，您可以威脅智慧報表的連結。 按一下它，另一個瀏覽器視窗會顯示與 PDF 檔案。

    ![儲存選取項目](./media/security-center-threat-report/security-center-threat-report-fig3.png)

從這裡開始，您可以下載此報表和閱讀更多關於安全性問題偵測到之 PDF，並採取動作，根據所提供的資訊。

## <a name="see-also"></a>另請參閱

在此文件中，您學到如何 Azure 安全性中心威脅智慧報表協助調查安全性警告。 若要進一步瞭解 Azure 資訊安全中心 」 的資訊，請參閱下列各項︰

- [Azure 資訊安全中心常見問題集](security-center-faq.md)。 尋找關於使用服務的常見問題。
- [事件回應運用 Azure 資訊安全中心](security-center-incident-response.md)
- [Azure 資訊安全中心偵測功能](security-center-detection-capabilities.md)
- [Azure 資訊安全中心規劃及作業指南](security-center-planning-and-operations-guide.md)。 瞭解如何規劃及瞭解採用 Azure 資訊安全中心的設計考量。
- [管理及回應在 Azure 資訊安全中心的安全性警告](security-center-managing-and-responding-alerts.md)]。 瞭解如何管理和回應的安全性警告。
- [處理在 Azure 資訊安全中心的安全性事件](security-center-incident.md)
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 的安全性和法規遵循的部落格文章。
