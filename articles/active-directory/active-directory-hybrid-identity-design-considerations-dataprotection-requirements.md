<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-決定資料保護需求 |Microsoft Azure"
    description="規劃混合式身分識別解決方案時, 找出您的企業和哪些選項可獲得最佳 fulfil 這些需求的資料保護需求。"
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

#<a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>加強資料安全性強式的身分識別解決方案的計劃

若要保護資料的第一個步驟是找出誰能夠存取資料，並做為您必須具備身分識別此程序的一部分的解決方案，可以整合了您的系統提供驗證與授權功能。 驗證與授權通常與彼此混淆，其角色遭到誤解。 實際上，是非常不同，如下圖所示︰

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**行動裝置管理生命週期階段**

規劃混合式身分識別方案時您必須瞭解資料保護需求，您的企業] 與 [可獲得最佳 fulfil 這些需求。
 
>[AZURE.NOTE]
當您完成規劃資料的安全性時，請檢閱[決定多重因素驗證需求](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)以確保有關多重因素驗證需求選取項目已不受此區段中您所做的決策。

## <a name="determine-data-protection-requirements"></a>決定資料保護需求
在 [行動性的年齡，大部分的公司有常用的目標︰ 啟用使用者生產力他們在內部部署時，或從遠端的行動裝置從任何位置以提高生產力。 雖然這可能是常見的目標，例如需求的公司也會必須排除項目才保護公司的資料與維護使用者的隱私權的潛在威脅的相關問題。 每個的公司可能會有不同的需求出於;不同的規範規則會視情況而定，根據哪些業界做公司時會連到不同的設計決策。 

然而，有一些安全性方面的應該探索和驗證，無論產業下, 一節中所說明。

## <a name="data-protection-paths"></a>資料保護路徑

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**資料保護路徑**

在上述的圖表中，識別元件會驗證，才能存取資料的第一個。 不過，此資料可以在不同的狀態中存取的期間。 在此圖表上的每個數字表示的資料可位於有些時候時間路徑。 這些數字說明如下︰

1. 資料保護裝置層級。
2. 資料保護傳輸中。
3. 資料保護時，其餘內部部署。
4. 資料保護時，在雲端的其餘部分。

雖然技術控制項的就是啟用這些階段每一個保護資料本身的 IT，不會直接提供混合式身分識別方案，就必須混合式部署識別方案是能夠充分使用內部部署和雲端來識別之前使用者的身分識別管理資源授與存取權的資料。 當規劃混合式身分識別解決方案，確定下列問題的回答根據組織的需求︰

## <a name="data-protection-at-rest"></a>在其餘的資料保護
無論位置的資料是在其餘 （裝置、 雲端或內部部署），請務必執行在這方面了解組織需求評估。 適用於此區域中，確保會詢問下列問題︰

- 您的公司是否需要保護其餘的資料？
 - 如果是的是混合式部署識別方案與您目前的內部部署基礎結構整合？
 - 如果是的是混合式部署識別方案整合您位於雲端的工作負載？
- 是雲端身分識別管理能夠保護使用者的認證，以及其他儲存在雲端的資料？

## <a name="data-protection-in-transit"></a>在傳輸時會資料保護
傳輸裝置和資料中心或之間裝置和雲端中的資料必須受到保護。 不過，在傳輸中不一定通訊程序，您的雲端服務; 以外的元件移動內部，此外，例如兩個虛擬網路。 適用於此區域中，確保會詢問下列問題︰

- 您的公司是否需要保護資料在傳輸時會？
 - 如果是的是混合式部署識別方案例如 SSL/TLS 的安全控制項整合？
- 雲端身分識別管理維持不變的流量目錄存放區 （內和資料中心之間） 內登入嗎？


## <a name="compliance"></a>法規遵循
法規、 法律和法規遵循需求會根據您的公司所屬產業而有所不同。 公司高規定產業必須地址與相關法規遵循問題的身分識別管理問題。 關於身分識別與存取權，例如沙法案 (SOX)、 健康保險流通和責任法 (HIPAA)、 Gramm-Leach-Bliley 法 (GLBA) 及付款卡產業資料安全性標準 (PCI DSS) 法規是很嚴格。 您的公司會採用混合式身分識別解決方案必須核心功能將會執行的一或多個這些法規需求。 適用於此區域中，確保會詢問下列問題︰

- 是混合式部署識別方案符合法規需求，您的商務用？
- 會將啟用為遵守法規要求貴公司的功能具有建置混合式身分識別解決方案嗎？ 
 
>[AZURE.NOTE]
請務必記錄的每個答案的筆記，並瞭解答案原理。 在可用的選項並優點/缺點每個選項，就會移[定義資料保護策略](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)。  依需要回答這些會選取最佳的選項適合您的業務需要。

## <a name="next-steps"></a>後續步驟
 [決定內容管理需求](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## <a name="see-also"></a>另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)
