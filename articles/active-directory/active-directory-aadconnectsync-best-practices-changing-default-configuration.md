<properties
    pageTitle="Azure AD Connect 同步處理︰ 變更預設設定的最佳作法 |Microsoft Azure"
    description="提供最佳作法變更 Azure AD Connect 同步處理的預設設定。"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect 同步處理︰ 變更預設設定的最佳作法
本主題的目的是說明支援和不支援 Azure AD Connect 同步處理變更。

Azure AD Connect 所建立的設定將適合大部分 Azure AD 與同步處理內部部署的 Active Directory 的環境的 「 現狀 」。 不過，在某些情況下，就必須將變更套用至要符合的特定需求的組態。

## <a name="changes-to-the-service-account"></a>服務帳戶的變更
Azure AD Connect 同步處理正在執行安裝精靈 」 所建立的服務帳戶。 此服務帳戶會保留同步處理所使用的資料庫加密金鑰。 建立與很長達 127 個字元的密碼和密碼設定為過期。

- 它不**支援**變更或重設服務帳戶的密碼。 這樣會損壞的加密金鑰，不能存取資料庫及無法啟動的服務。

## <a name="changes-to-the-scheduler"></a>變更排程器
開始建立 1.1 （英文） 從版本 (年 2 月 2016)，您可以設定讓於預設的不同的同步處理循環圖 30 分鐘[排程器](active-directory-aadconnectsync-feature-scheduler.md)。

## <a name="changes-to-synchronization-rules"></a>變更同步處理規則
在安裝精靈提供應該可用於最常見的案例的設定。 如果需要對設定進行變更，您就必須遵循這些規則，您仍然有支援的設定。

- 您可以[變更屬性流](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes)如果預設直接屬性流不適合您的組織。
- 如果您想要[流程屬性](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute)，並在 Azure AD 中移除任何現有的屬性值，您需要為這種情況下建立規則。
- [停用不需要的同步處理規則](#disable-an-unwanted-sync-rule)，而不是先將它刪除。 升級時，重新建立已刪除的規則。
- 若要[變更的現成的規則](#change-an-out-of-box-rule)，請複製的原始的規則，並停用的現成的規則。 同步處理規則編輯器提示，並可協助您。
- 匯出自訂同步處理規則使用同步處理規則編輯器。 編輯器] 中提供的 PowerShell 指令碼，您可以輕鬆地在修復損毀的情況下重新建立這些使用。

>[AZURE.WARNING] 現成的同步處理規則有指紋。 如果您對這些規則的變更，指紋不再會比對。 在未來時套用 Azure AD Connect 新版本，您可能會遭遇問題。 只變更描述的方式本文中。

### <a name="disable-an-unwanted-sync-rule"></a>停用不需要的同步處理規則
不要刪除一個現成的同步處理規則。 它會重新建立下一個升級期間。

在某些情況下，安裝精靈具有所產生的拓撲無法運作的設定。 例如，如果您有帳戶資源樹系拓撲，但您有延伸帳戶樹系中的結構描述，使用 Exchange 的結構描述，Exchange 的規則會建立帳戶樹系和資源樹系。 在此情況下，必須先停用 exchange 的同步處理的規則。

![停用的同步處理規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

在上方的圖片，[安裝] 精靈已找到帳戶樹系舊的 Exchange 2003 結構描述。 之前資源樹系引進 Fabrikam 的環境中，已新增至此分機] 結構描述。 若要確保從舊的 Exchange 實作任何屬性有同步處理，同步處理規則應該停用如下圖所示。

### <a name="change-an-out-of-box-rule"></a>變更的現成的規則
如果您需要變更現成的規則，然後應該複製的現成的規則，並停用的原始的規則。 若要複製的規則，然後進行變更。 同步處理規則編輯器有助您取得這些步驟。 當您開啟一個現成的規則時，您會看到這個對話方塊︰  
![登出方塊規則的警告](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

選取 [ **]**來建立規則的複本。 複製的規則，然後會開啟。  
![複製的規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

這個複製的規則，進行任何必要的變更範圍、 加入並轉換。

## <a name="next-steps"></a>後續步驟

**概觀主題**

- [Azure AD Connect 同步處理︰ 了解並自訂同步處理](active-directory-aadconnectsync-whatis.md)
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
