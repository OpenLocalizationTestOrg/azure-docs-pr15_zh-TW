<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量-定義混合式身分識別採用策略 |Microsoft Azure"
    description="使用條件存取控制項，Azure Active Directory 檢查您挑選驗證使用者時，才能存取應用程式的特定條件。 一旦符合這些條件，使用者就是驗證，允許存取應用程式。"
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


# <a name="define-a-hybrid-identity-adoption-strategy"></a>定義混合式身分識別採用策略

在此工作中，您會定義您混合式身分識別的方案符合所述的業務需求的混合式身分識別採用策略︰

- [決定業務需求](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [決定目錄同步處理需求](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [決定多重因素驗證需求](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>定義商務需求策略
必須決定組織商務的第一個任務地址。  這可能是很大，如果您不小心，就會發生膨脹。  在開始保持簡單但記得要規劃的設計，可容納並在未來利於進行變更。  無論它是否簡單的設計或很複雜的項目，Azure Active Directory 是 Microsoft 身分識別的平台支援 Office 365、 Microsoft Online Services 和雲端應用程式。

## <a name="define-an-integration-strategy"></a>定義整合策略
Microsoft 有三個主要的整合案例的雲端身分識別與同步處理的身分識別，同盟的身分識別。  您應該計劃採用其中一個整合策略。  您選擇可以視情況而定中選擇一個, 決策可能包含，何種類型的使用者體驗，您想要提供您有一些現有的基礎結構已經就地，以及其中有什麼成本有效的策略。  
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

在上圖中定義的情況如下︰

- **雲端身分識別**︰ 這些是在雲端中存在的身分識別。  Azure AD，若是會位於特別 Azure AD 目錄。
- **同步處理**︰ 這些是身分識別存在內部部署和雲端中。  使用 Azure AD Connect，這些使用者的 [建立或加入現有的 Azure AD 帳戶。  使用者的密碼雜湊是從內部部署環境中稱為密碼雜湊雲端同步處理。  使用同步處理時必須注意的是，如果使用者已停用內部部署環境中，可以小時後才會最多 3 Azure AD 中顯示該帳戶的狀態。  這是因為同步處理的時間間隔。
- **聯盟**︰ 這些識別存在於兩個內部部署和雲端中。  使用 Azure AD Connect，這些使用者的 [建立或加入現有的 Azure AD 帳戶。  
 
>[AZURE.NOTE]
如需同步處理選項會閱讀[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。

下表將有助於決定的每個下列策略優缺點︰

| 策略         | 優點                                                                                                                                                                                                                                                  | 缺點                                                                                                                                                                                                                                                                                                                                                  |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **雲端身分識別** | 管理適用於小型組織變得更容易。 <br> 直接安裝上部署無其他硬體需求<br>如果使用者離職輕鬆地停用                                                                                                   | 使用者需要登入存取負載在雲端時 <br> 可能的密碼，或可能不相同的雲端和內部部署的身分識別                                                                                                                                                                                                                     |
| **同步處理**     | 內部部署的密碼會驗證兩個內部部署與雲端目錄 <br>管理適用於小型、 中型或大型組織變得更容易 <br>使用者可以有單一登入 (SSO) 的一些資源 <br> 同步處理的 Microsoft 慣用的方法 <br> 更容易管理 | 有些客戶可能願意與特定公司監督者到期雲端同步處理的目錄                                                                                                                                                                                                                                                  |
| **同盟**        | 使用者可以有單一登入 (SSO) <br>如果使用者已結束或離開時，可以立即停用的帳戶，和撤銷存取權，<br> 支援進階分析藍本，不能使用完成同步處理                                           | 其他步驟來安裝和設定 <br> 更高進行的維修作業 <br> 可能需要額外的硬體 STS 基礎結構 <br> 可能需要額外的硬體安裝同盟伺服器。如果使用 AD FS，不需要其他軟體 <br> SSO 需要更多設定 <br> 要徑失敗點的向下同盟伺服器時，使用者都無法進行驗證 |

### <a name="client-experience"></a>用戶端體驗
您使用的策略會要求使用者登入體驗。  下表提供資訊給您在哪些使用者應該要登入經驗。  注意︰ 並非所有同盟的身分識別提供者，支援所有案例中 SSO。

**網域加入與私人的網路應用程式**︰
 

|                              | 同步處理的身分識別      | 同盟的身分識別                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| 網頁瀏覽器                 | 表單型驗證 | 單一登入，有時也需要提供組織識別碼 |
| Outlook                      | 提示輸入認證     | 提示輸入認證                                       |
| 商務用 Skype (Lync)    | 提示輸入認證     | 單一登入 lync，提示您 Exchange 認證   |
| Skydrive Pro                 | 提示輸入認證     | 單一登入                                               |
| Office 專業增強版訂閱 | 提示輸入認證     | 單一登入                                               |

**外部或不受信任來源**:

|                              | 同步處理的身分識別      | 同盟的身分識別                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| 網頁瀏覽器                 | 表單型驗證 |  表單型驗證 |
| Outlook 中，在商務用 Skype (Lync)，Skydrive Pro 的 Office 訂閱| 提示輸入認證     | 提示輸入認證                                       |
| Exchange ActiveSync    | 提示輸入認證     | 單一登入 lync，提示您 Exchange 認證   |
| 行動應用程式                 | 提示輸入認證     | 提示輸入認證                                               |

如果您已認定從任務必須派對進入使用其中一個提供同盟 Azure AD IdP 或 3rd 1，您必須注意下列支援的功能︰
- 任何 SAML 2.0 提供者，這是標準的 [預存程序精簡的設定檔可支援 Azure AD 的驗證及相關應用程式
- 支援被動式驗證]，以方便您驗證 OWA、 SPO 等。
- Exchange Online 的用戶端可以支援透過 SAML 2.0 增強型用戶端的設定檔 (ECP)

您也必須知道哪些功能將無法使用︰

- WS-信任同盟支援使用，不會中斷所有其他作用中的用戶端
 - 這表示沒有 Lync 用戶端、 OneDrive 用戶端，Office 訂閱，Office Mobile 之前 Office 2016
- 轉場效果的 Office 被動式驗證可讓，以支援純粹 SAML 2.0 IdPs，，但仍可在用戶端的用戶端支援


>[AZURE.NOTE]
最更新清單的讀取文章 http://aka.ms/ssoproviders。

## <a name="define-synchronization-strategy"></a>定義同步處理策略
在您將會定義的工具，將會用來同步處理這項任務組織的內部部署資料至雲端，以及拓撲，您應該使用。  因為大多數的組織使用 Active Directory，請使用 Azure AD Connect 地址上述問題的資訊提供的一些詳細資料。  沒有 Active Directory 的環境，沒有使用 FIM 2010 R2 或 MIM 2016，以協助規劃這種方法的相關資訊。  不過，未來的版本的 Azure AD Connect 將支援 LDAP 目錄，因此，根據您的時間表，協助您或許可以使用這項資訊。

###<a name="synchronization-tools"></a>同步處理工具
年來，多個同步處理工具已存在於而用於各種情況。  目前 Azure AD Connect 是移至所選擇的所有支援的案例的工具。  AAD Sync DirSync 仍然是周圍，並可能也會出現在您的環境現在。 

>[AZURE.NOTE]
有關支援的功能每項工具的最新資訊，請參閱[目錄整合工具比較](active-directory-hybrid-identity-design-considerations-tools-comparison.md)文件。  

### <a name="supported-topologies"></a>受支援的拓撲
在定義同步處理策略時，必須決定使用拓撲。 根據資訊所決定在步驟 2 您可以決定哪些拓撲是使用適當的項目。 在單一樹系單一 Azure AD 拓撲是最常用的及包含單一的 Active Directory 樹系和 Azure AD 一個執行個體。  這要用於大部分的情況下預期的拓撲使用時，則 Azure AD 連線 Express 安裝下圖所示。
 
![](./media/hybrid-id-design-considerations/single-forest.png)單一樹系案例是很常見大、 更小的組織，可以有多個，如下圖所示圖 5。

>[AZURE.NOTE]
如需有關不同內部部署和 Azure AD 拓撲與 Azure AD Connect 同步處理，請閱讀的文件[的 Azure AD Connect 拓撲](active-directory-aadconnect-topologies.md)。


![](./media/hybrid-id-design-considerations/multi-forest.png) 

多樹系案例

如果這種情況然後多重-forest-單一 Azure AD 拓撲考慮下列項目是否均為 true:

- 使用者在所有樹系之間有僅 1 的身分識別，可唯一識別使用者] 區段下方說明更多詳細資料。
- 驗證其身分識別位於樹的使用者
- UPN 和來源錨點 (不變 id)，將來自此樹系
- 所有樹系都可以存取 Azure AD Connect，這表示不需要為網域加入，如果這可協助這可置於 DMZ。
- 使用者會有一個信箱
- 樹系裝載使用者信箱的已在 Exchange 全域通訊清單 (GAL) 顯示屬性的最佳資料品質
- 如果使用者有沒有信箱，然後任何樹系可用於參與這些值
- 如果您有連結的信箱，然後還有另一個帳戶不同樹系用來登入。

>[AZURE.NOTE]
存在於兩個內部部署和雲端中的物件是 」 「 透過連線的唯一識別碼。 在目錄同步處理的內容，此唯一識別碼被指 SourceAnchor。 在內容中的單一登入，這被稱為 ImmutableId。 考量更多關於使用的 SourceAnchor [Azure AD Connect 的設計概念](active-directory-aadconnect-design-concepts.md#sourceanchor)。

如果上述不是則為 true，且您有多個作用中的帳戶或多個信箱，Azure AD Connect 將挑選，並忽略另。  如果您有連結信箱，但沒有其他帳戶，這些帳戶將不會匯出至 Azure AD，該使用者不會任何群組的成員。  這是如何使用 DirSync 過去和你們刻意使其更多樹系細想這些案例的不同。 下圖顯示多樹系案例。
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 
 
**多樹系多個 Azure AD 案例**

建議您為組織 Azure AD 中有只單一目錄，但支援 1:1 關聯保留之間的 Azure AD Connect 同步處理伺服器和 Azure AD 目錄它。  Azure AD 每一個執行個體，您需要 Azure AD Connect 的安裝。  此外，Azure AD，所設計的隔離與 Azure AD 一個執行個體中的使用者將無法看到其他執行個體中的使用者。

就可能與支援連線到多個 Azure AD 目錄的一個的內部部署的 Active Directory 執行個體，如下圖所示︰

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 
 

**單一樹系篩選案例**

才能執行此動作必須為 true:

- Azure AD Connect 同步處理伺服器必須設定篩選，各有一組互斥的物件。  此完成，例如，以設定特定的網域或 OU 每個伺服器的範圍。
- DNS 網域可以只在單一註冊 Azure AD 目錄，因此 Upn 的內部部署使用者 AD 必須使用不同的命名空間
- Azure AD 一個執行個體中的使用者只能查看他們的執行個體的使用者。  對方將無法看到其他執行個體中的使用者
- 僅的 Azure AD 目錄可以啟用與內部部署的 Exchange 混合式 AD
- 相互專用性也適用於回寫功能。  如此，因為這些假設單一內部部署設定不支援此拓撲某些回寫功能。  包括︰
 - 群組回寫預設設定
 - 裝置回寫功能


請注意下列不受支援，而且不應為實作選擇︰

- 若要有多個 Azure AD Connect 同步處理伺服器連線到相同的 Azure AD 目錄，即使其設定為同步處理互斥設定物件的是不受支援
- 它不支援同步處理相同的使用者，以多個 Azure AD 目錄。 
- 也不支援變更為另一個 Azure AD 目錄中的連絡人顯示一個 Azure AD 中讓使用者設定。 
- 此外，也不支援修改 Azure AD Connect 同步處理連線至多個 Azure AD 目錄。
- Azure AD 目錄都隔離的設計。 這是不受支援，若要變更讀取另一個 Azure AD 目錄中的資料，以建立目錄之間的常見與整合 GAL Azure AD Connect 與同步處理的設定。 若要將使用者匯出為連絡人時也不支援另一個內部部署 AD 使用 Azure AD Connect 同步處理。


>[AZURE.NOTE]
如果您的組織會限制您的網路上的電腦連線至網際網路，本文列出的結束點 （Fqdn、 IPv4 和 IPv6 位址範圍），您應該包含在您輸出允許清單和 Internet Explorer 信任的網站] 區域的用戶端電腦，以確保您的電腦可以順利使用 Office 365。 如需詳細資訊，請閱讀[Office 365 Url 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)。

## <a name="define-multi-factor-authentication-strategy"></a>定義多重因素驗證策略
在這項任務中，您將定義使用多重因素驗證策略。  Azure 多重因素驗證有兩個不同的版本。  其中一個是以雲端為基礎，另一個是內部部署基礎使用 Azure MFA 伺服器。  根據評估您並未上方您可以判斷哪一個解決方案是正確的策略。  請使用下表決定最佳的設計選項滿足貴公司的安全性需求︰

多重因素設計選項︰

| 資產的安全性                                               | 在雲端的 MFA | MFA 內部部署 |
|---------------------------------------------------------------|------------------|----------------|
| Microsoft 應用程式                                                | [是]              | [是]            |
| 在 [應用程式] 圖庫中的 SaaS 應用程式                                  | [是]              | [是]            |
| IIS 應用程式發佈到 Azure AD 應用程式 Proxy         | [是]              | [是]            |
| IIS 應用程式未發佈透過 Azure AD 應用程式 Proxy | 沒有               | [是]            |
| 為 VPN、 RDG 遠端存取                                     | 沒有               | [是]            |

雖然您可能必須結束的方案策略，您仍需要使用上述評估您的使用者的所在位置。  這可能會導致變更解決方案。  使用下表，協助您決定這樣︰

| 使用者的位置                                                       | 慣用的設計選項                 |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory                                              | 在雲端的多重-FactorAuthentication |
| Azure AD 和內部部署 AD 使用 AD FS 同盟             | 同時                                    |
| Azure AD 和內部部署 AD 使用 Azure AD 連線沒有密碼同步處理 | 同時                                    |
| Azure AD 和內部部署 Azure AD Connect 使用密碼同步處理  | 同時                                    |
| 內部部署 AD                                                      | 多重因素驗證伺服器      |

>[AZURE.NOTE]
您也應該確保您已選取多重因素驗證設計選項支援所需的設計的功能。  如需詳細資訊，請閱讀[選擇多因素安全性的解決方案](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure)。

## <a name="multi-factor-auth-provider"></a>多重因素驗證提供者
使用預設的 Azure Active Directory 租用戶的全域管理員多重因素驗證。 不過，如果您想要將多因素驗證延伸到所有的使用者及/或想將全域管理員才能利用功能，例如管理入口網站、 自訂的問候語及報表，然後必須購買並設定多重因素驗證提供者。

>[AZURE.NOTE]
您也應該確保您已選取多重因素驗證設計選項支援所需的設計的功能。 

##<a name="next-steps"></a>後續步驟
[決定資料保護需求](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-overview.md)
