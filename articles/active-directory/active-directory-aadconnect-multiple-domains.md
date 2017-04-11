<properties
    pageTitle="Azure AD Connect 多個網域"
    description="O365 與 Azure AD 設定多個最上層網域，將說明這份文件。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Azure AD 與同盟的多個網域支援
下列文件提供如何使用多個最上層網域和子網域搭配 Office 365 或 Azure AD 網域同盟的指引。

## <a name="multiple-top-level-domain-support"></a>多個最上層網域支援
Azure AD 頂層網域同盟的多個項目，需要時，不需要使用一個頂層網域同盟的一些其他設定。

Azure AD 與同盟網域時, 數個屬性設定 Azure 中的網域上。  一個重要的項目是 IssuerUri。  這是 URI，由 Azure AD 用來識別權杖相關聯的網域。  URI 不需要以解析為任何檔案，但必須是有效的 URI。  根據預設，Azure AD 將此值設同盟服務識別碼的值在您的內部部署 AD FS 設定。

>[AZURE.NOTE]同盟服務識別碼是 URI 可唯一識別同盟服務。  同盟服務安全性權杖服務做為 AD FS 該函數的執行個體。 

您可以使用 PowerShell 命令檢視 IssuerUri `Get-MsolDomainFederationSettings - DomainName <your domain>`。

![取得 MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

我們想要新增一個以上的頂層網域時，就會發生問題。  例如，假設您有安裝同盟之間 Azure AD 與內部部署環境。  這份文件我使用的 bmcontoso.com。  現在我已新增第二個的頂層網域，請 bmfabrikam.com。

![網域](./media/active-directory-multiple-domains/domains.png)

當我們嘗試轉換要同盟我們 bmfabrikam.com 的網域時，我們會收到錯誤訊息。  原因是，Azure AD 具有不允許 IssuerUri 屬性具有相同值的多個網域的限制式。  
  

![同盟錯誤](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain 參數

若要解決，我們需要新增不同的 IssuerUri 這可透過使用`-SupportMultipleDomain`參數。  這個參數用透過下列 cmdlet:
    
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

這個參數會 Azure AD 設定 IssuerUri，讓它為基礎的網域名稱。  這是唯一在目錄中 Azure AD。  使用參數，可讓順利完成的 PowerShell 命令。

![同盟錯誤](./media/active-directory-multiple-domains/convert.png)

您查看我們的新 bmfabrikam.com 網域的設定可以看到以下訊息︰

![同盟錯誤](./media/active-directory-multiple-domains/settings.png)

請注意，`-SupportMultipleDomain`不會變更其他還是設定為指向 adfs.bmcontoso.com 我們同盟服務的結束點。

另一個的`-SupportMultipleDomain`是否可確保 AD FS 系統 Azure AD 發出的權杖包含 [適當的發行者] 值。 它會以將網域的部分使用者 UPN 為 IssuerUri，亦即 https://{upn 尾碼中的網域設定這個} / adfs/服務/信任。 

Azure ad 進行驗證時，因此或 Office 365 使用者的權杖 IssuerUri 項目用來在 Azure AD 中找出該網域。  如果無法驗證將會失敗找到相符項目。 

例如，如果使用者的 UPN 是bsimon@bmcontoso.com,IssuerUri 中的項目權杖 AD FS 問題將被設定為 http://bmcontoso.com/adfs/services/trust。 這會比對 Azure AD 設定，並將成功驗證。

以下是自訂的宣告規則實作此邏輯︰

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]若要嘗試新增或轉換已新增網域時，請使用-SupportMultipleDomain 切換，您需要有設定同盟支援原本信任。  


## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>如何更新 AD FS 和 Azure AD 之間的信任
如果您不是設定之間 AD FS 和 Azure AD 執行個體的同盟的信任，您可能需要重新建立此信任。  這是因為其時原本設定，而不`-SupportMultipleDomain`參數，IssuerUri 設定與預設值。  在螢幕擷取畫面下方您可以看到 IssuerUri 設定為 [https://adfs.bmcontoso.com/adfs/services/trust。

如果我們順利 Azure AD 入口網站中新增新的網域，然後將其使用轉換，因此現在`Convert-MsolDomaintoFederated -DomainName <your domain>`，我們會收到下列錯誤。

![同盟錯誤](./media/active-directory-multiple-domains/trust1.png)

如果您嘗試新增`-SupportMultipleDomain`切換我們將會收到下列錯誤︰

![同盟錯誤](./media/active-directory-multiple-domains/trust2.png)

只要嘗試執行`Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain`原始的網域就會導致錯誤。

![同盟錯誤](./media/active-directory-multiple-domains/trust3.png)

若要新增額外的頂層網域中使用下列步驟。  如果您已新增網域，並不是使用`-SupportMultipleDomain`參數開始移除及更新您的原始網域的步驟。  如果您尚未新增頂層網域尚未您可以開始新增網域，使用 PowerShell 的 Azure AD 連線的步驟。

使用下列步驟來移除 [Microsoft Online 信任和更新您的原始網域。

2.  開啟您 AD FS 同盟伺服器上**AD FS 管理。** 
2.  在左側，展開 [**信任的關聯性**和**依賴廠商信任**
3.  在右側，刪除的**Microsoft Office 365 身分識別的平台**項目。
![移除 Microsoft 線上](./media/active-directory-multiple-domains/trust4.png)
1.  在已安裝的[Azure Active Directory 模組的 Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)的電腦上執行下列動作︰ `$cred=Get-Credential`。  
2.  輸入您要使用同盟的 Azure AD 網域的使用者名稱和密碼全域管理員
2.  在 PowerShell 中輸入`Connect-MsolService -Credential $cred`
4.  在 PowerShell 中輸入`Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`。  這是原始的網域。  使用上述的網域就是︰`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


若要新增新的頂層網域使用 PowerShell 中使用下列步驟

1.  在已安裝的[Azure Active Directory 模組的 Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)的電腦上執行下列動作︰ `$cred=Get-Credential`。  
2.  輸入您要使用同盟的 Azure AD 網域的使用者名稱和密碼全域管理員
2.  在 PowerShell 中輸入`Connect-MsolService -Credential $cred`
3.  在 PowerShell 中輸入`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

若要新增新的頂層網域使用 Azure AD Connect 使用下列步驟。

1.  啟動 Azure AD Connect 從桌面] 或 [開始] 功能表
2.  選擇 [新增其他 Azure AD 網域]![新增其他 Azure AD 網域](./media/active-directory-multiple-domains/add1.png)
3.  輸入您的 Azure AD 和 Active Directory 認證
4.  選取您想要設定的同盟的第二個網域。
![新增其他 Azure AD 網域](./media/active-directory-multiple-domains/add2.png)
5.  按一下 [安裝]


### <a name="verify-the-new-top-level-domain"></a>確認新的頂層網域
使用 PowerShell 命令`Get-MsolDomainFederationSettings - DomainName <your domain>`您可以檢視更新的 IssuerUri。  以下螢幕擷取畫面顯示同盟在我們的原始網域 http://bmcontoso.com/adfs/services/trust 更新的設定

![取得 MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

在我們的新網域 IssuerUri 已設定為 https://bmfabrikam.com/adfs/services/trust 和

![取得 MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##<a name="support-for-sub-domains"></a>支援的子網域
當您新增的子網域，由於的處理方式 Azure AD 網域時，它會繼承上層的設定。  這表示 IssuerUri 需要以符合父系。

因此，可讓假設我有 bmcontoso.com，然後新增 corp.bmcontoso.com 的範例。  這表示，為使用者從 corp.bmcontoso.com IssuerUri 必須是**http://bmcontoso.com/adfs/services/trust。**  不過標準規則實作上方 Azure ad，就會產生的權杖為發行者與**http://corp.bmcontoso.com/adfs/services/trust。** 這會與網域的必要值，驗證將會失敗。

### <a name="how-to-enable-support-for-sub-domains"></a>如何啟用支援功能的子網域
若要解決此問題 AD FS 依賴廠商信任使用 Microsoft Online 需要更新。  若要這麼做，您必須設定自訂宣告規則，讓它除去從使用者的 UPN 尾碼任何子網域建構自訂發行者值時。 

下列宣告會執行下列動作︰

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

若要新增自訂宣告支援功能的子網域中使用下列步驟。

1.  開啟 AD FS 管理
2.  以滑鼠右鍵按一下 [Microsoft Online 資源點數信任，然後選擇 [編輯宣告規則
3.  選取第三個宣告規則，並取代![編輯宣告](./media/active-directory-multiple-domains/sub1.png)
4.  取代目前的宣告︰
    
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
        
    使用
    
        `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
    
![取代宣告](./media/active-directory-multiple-domains/sub2.png)
5.  按一下 [確定]。  按一下 [套用]。  按一下 [確定]。  關閉 AD FS 管理。

