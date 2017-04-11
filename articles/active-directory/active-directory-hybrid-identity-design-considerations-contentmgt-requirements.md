<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-決定內容管理需求 |Microsoft Azure"
    description="提供深入了解如何判斷您的企業內容管理需求。 通常使用者擁有自己的裝置可能會有會交替根據他所使用的應用程式的也多個的認證。 請務必區別內容建立使用個人的認證與使用公司認證所建立的項目。 您的身分識別的解決方案無法互動雲端服務，提供給使用者時的順暢的使用體驗確保他們的隱私權，並且增加資料外洩防護。"
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

# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>決定您混合式部署的身分識別解決方案的內容管理需求

瞭解您的企業內容管理需求直接可能會影響您決定要使用哪種混合式身分識別解決方案。 與多個裝置的使用者能夠將自己的裝置 ([BYOD](http://aka.ms/byodcg))，公司必須保護自己的資料，但它也必須得以維持不變使用者的隱私權。 通常使用者擁有自己的裝置可能會有會交替根據他所使用的應用程式的也多個的認證。 請務必區別內容建立使用個人的認證與使用公司認證所建立的項目。 您的身分識別的解決方案無法互動雲端服務，提供給使用者時的順暢的使用體驗確保他們的隱私權，並且增加資料外洩防護。 

您的身分識別的方案會利用不同技術的控制項，才能提供內容管理，如下圖所示︰
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**充分運用您的身分識別管理系統的安全性控制項**

一般而言，內容管理需求會使用您的身分識別管理系統的下列區域中︰

- 隱私權︰ 識別擁有資源的使用者並套用至維持完整性適當的控制項。
- 資料分類︰ 找出使用者或群組和層級根據其分類物件的存取權。 
- 資料外洩︰ 負責保護資料，以避免外洩的安全性控制項必須驗證使用者的身分識別系統與之互動。 這也是很重要的稽核線索用途。

>[AZURE.NOTE]
請閱讀[的雲端整備資料分類](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf)的最佳作法的詳細資訊與資料分類的方針。

當規劃混合式身分識別解決方案，確定下列問題的回答根據組織的需求︰

- 貴公司有的安全性控制項來強制執行的資料隱私權？
 - 如果是的將這些的安全性控制項能夠整合與您要採用混合式部署識別方案嗎？
- 您的公司是否使用資料分類？
 - 如果是的是目前方案整合要採用混合式身分識別解決方案嗎？
- 您的公司目前有任何資料外洩解決方案嗎？ 
 - 如果是的是目前方案整合要採用混合式身分識別解決方案嗎？
- 您的公司需要稽核資源的存取權嗎？
 - 如果是的輸入的內容的資源？
 - 如果是的是必要的層級的資訊嗎？
 - 如果是的其中必須位於稽核記錄？ 內部部署或雲端中？
- 您的公司是否需要加密含有機密資料 (SSNs，信用卡卡號等) 的任何電子郵件？
- 您的公司是否需要加密所有文件/內容與外部商務合作夥伴共用？
- 您的公司需要執行公司政策特定類型的電子郵件 （執行不所有回覆，[不要轉接）？
 
>[AZURE.NOTE]
請務必記錄的每個答案的筆記，並瞭解答案原理。 在可用的選項並優點/缺點每個選項，就會移[定義資料保護策略](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)。  依需要回答這些會選取最佳的選項適合您的業務需要。


## <a name="next-steps"></a>後續步驟
[決定存取控制需求](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)
