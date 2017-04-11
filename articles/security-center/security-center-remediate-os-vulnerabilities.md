<properties
   pageTitle="修復 Azure 資訊安全中心 OS 弱點 |Microsoft Azure"
   description="這份文件為您示範如何實作 Azure 資訊安全中心建議**補救 OS 弱點**。"
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>修復 OS 弱點 Azure 資訊安全中心

Azure 資訊安全中心分析設定可能會使 VM 更容易受到攻擊，建議您變更設定來解決這些弱點每天虛擬機器 (VM) 作業系統 (OS)。 如需有關監視特定設定的詳細資訊，請參閱[建議的設定規則清單](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 資訊安全中心建議您解決弱點，當您 VM OS 設定不符合建議的設定規則。

> [AZURE.NOTE] 這份文件會使用部署範例介紹服務。  這不是逐步指示。

## <a name="implement-the-recommendation"></a>實作建議

1. 在 [**建議**刀中，選取 [**補救 OS 弱點**]。
![修復 OS 弱點][1]

    **修復 OS 弱點**刀隨即開啟，並列出您的 Vm OS 設定的不相符的建議的設定規則。  每個 VM 刀識別︰

   - **無法規則**-VM OS 設定失敗的規則的數目。
   - **最後一個掃描時間**-日期及時間資訊安全中心上次掃描 VM 的作業系統設定。
   - **狀態**--弱點的目前狀態︰

        - 開啟︰ 弱點不已處理尚未
        - 進度︰ 弱點目前正在套用，您需要執行任何動作
        - 解決︰ 弱點已經有處理 （當問題已解決，項目會呈現灰色）
  - **嚴重性**-所有弱點會都設定為 [低、 嚴重性表示弱點應該收件者，但不需要立即關注。

選取 [VM]。 針對該 VM 刀開啟，並顯示失敗的規則。
   ![設定規則失敗][2]

選取 [規則]。 在此範例中，可讓選取**密碼必須符合複雜性需求**。 隨即會開啟刀描述失敗的規則和影響。 檢視詳細資料，請考慮作業系統設定套用方式。
  ![描述失敗的規則][3]

  資訊安全中心會使用一般設定列舉 (CCE) 來指定設定規則的唯一識別碼。 在此刀上提供下列資訊︰

  - 規則的名稱--名稱
  - 要徑、 重要事項，或警告的嚴重性-CCE 嚴重性值
  - 規則的 CCIED-CCE 唯一識別碼
  - 規則的描述，描述
  - 弱點或風險，如果不套用規則的弱點-說明
  - 套用規則時的影響，商務影響
  - 預期資訊安全中心會根據規則 VM OS 設定時的預期的值-值
  - 規則作業-規則作業期間分析 VM OS 設定根據規則的使用資訊安全中心
  - 實際值-VM OS 設定根據規則的分析後傳回值
  - 評估結果--分析的結果︰ 傳遞時，會失敗。


## <a name="see-also"></a>另請參閱

本文會展示如何實作資訊安全中心建議 」 Remediate OS 弱點 」。 您可以檢閱設定規則集[以下](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 資訊安全中心使用 CCE （一般設定列舉），來指定設定規則的唯一識別碼。 請造訪[CCE](http://cce.mitre.org)網站，如需詳細資訊。

若要進一步瞭解資訊安全中心 」 的資訊，請參閱下列各項︰

- [設定在 Azure 資訊安全中心的安全性原則](security-center-policies.md)，瞭解如何設定您的訂閱 Azure 和資源群組的安全性原則。
- [在 Azure 資訊安全中心管理安全性的建議](security-center-recommendations.md)，了解建議如何協助您保護 Azure 資源。
- [安全性狀況監控 Azure 資訊安全中心 」](security-center-monitoring.md) ，瞭解如何監視 Azure 資源的狀況。
- [管理及回應在 Azure 資訊安全中心的安全性提醒](security-center-managing-and-responding-alerts.md)--瞭解如何管理和回應的安全性警告。
- [監控合作夥伴解決方案與 Azure 資訊安全中心](security-center-partner-solutions.md)，瞭解如何監控您的合作夥伴解決方案的健康狀態。
- [Azure 安全性中心常見問題集](security-center-faq.md)-尋找常見問題集使用服務。
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-尋找部落格文章瞭解 Azure 安全性和法規遵循。

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
