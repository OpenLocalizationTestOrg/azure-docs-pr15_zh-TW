
<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-決定事件 rResponse 需求 |Microsoft Azure 需求 "
    description="決定可以利用的混合式身分識別方案的監控和報告功能來採取動作，以找出並降低潛在威脅的 IT"
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>決定您混合式部署的身分識別解決方案的應計需求

大型或中型組織很可能會有[安全性應計](https://technet.microsoft.com/library/cc700825.aspx)可幫助 IT 會相應地採取動作層級的事件。 身分識別管理系統是應計程序中的重要元件，因為它可以用來協助找出誰執行特定動作對目標。 混合式部署識別方案必須為能提供監控和報告功能，可以利用採取動作來識別和減輕潛在威脅的 IT。 一般應計計劃中會包含下列階段計劃︰

1.  初始評量。
2.  事件的通訊。
3.  產生的損害控制項並降低風險。
4.  識別用的是在入侵及嚴重性。
5.  接收保留。
6.  適當的合作對象的通知。
7.  系統修復。
8.  文件。
9.  產生的損害與成本評量。
10. 程序和計劃的版本。

識別哪些 it 期間入侵及嚴重性階段，就必須以找出系統已遭入侵，存取與決定這些檔案的敏感度的檔案。 混合式身分識別系統應該能夠符合這些需求，以協助您識別使用者所做的那些變更。 

## <a name="monitoring-and-reporting"></a>監控與報告
多次身分識別系統也有助於初始評估階段主要系統具有內建於稽核和報告功能。 在初始評估中，IT 管理員必須為能識別可疑的活動，或系統應會自動根據預先設定任務的觸發程序。 許多活動可能表示攻擊，不過在其他情況下，設定不良的系統可能會導致誤判的數字入侵偵測系統。 

身分識別管理系統應該協助 IT 管理員要找出並報告這些可疑的活動。 監視所有系統，可以醒目提示潛在威脅的報告功能通常功能完成這些技術需求。 使用以下的問題，可協助您設計混合式身分識別解決方案時的考量應計需求︰

- 您的公司是否就地有證券的應計？
 - 如果是的目前的身分識別管理系統作為程序的一部分嗎？
- 您的公司需要識別使用者的可疑登入嘗試在不同的裝置上嗎？
- 您的公司是否需要偵測潛在洩漏的使用者的憑證？
- 您的公司是否需要稽核使用者的存取權和動作？
- 您的公司是否需要知道當使用者重設密碼？

## <a name="policy-enforcement"></a>強制原則

期間產生的損害控制項及其風險縮減階段，請務必快速減少實際和潛在攻擊的效果。 此時您會採取的動作進行次要與主要項目之間的差異。 完全回應取決於您組織和攻擊面臨的性質。 如果初始評估歸納出受損帳戶，您必須強制執行原則封鎖此帳戶。 這是一個範例會運用身分識別管理系統的位置。 使用下列的問題，可協助您設計混合式身分識別解決方案時如何原則強制回應進行中的事件記錄列入考慮︰

- 貴公司有原則就地封鎖使用者從 access 網路必要嗎？
 - 如果是的會目前方案整合與您要採用混合式身分識別管理系統？
- 您的公司需要強制執行條件中隔離的使用者存取權嗎？ 
 
>[AZURE.NOTE]
請務必記錄的每個答案的筆記，並瞭解答案原理。 在可用的選項並優點/缺點的每個選項，就會移[定義資料保護策略](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)。  依需要回答這些會選取最佳的選項適合您的業務需要。

## <a name="next-steps"></a>後續步驟
[定義資料保護策略](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)
