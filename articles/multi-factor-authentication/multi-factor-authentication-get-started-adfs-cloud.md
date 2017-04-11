<properties
    pageTitle="Azure MFA 與 AD FS 安全雲端資源"
    description="這是說明如何開始使用 Azure MFA 與 AD FS 雲端中的 Azure 多重因素驗證頁面。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>保護 Azure 多重因素驗證與 AD FS 雲端資源

如果您的組織同盟與 Azure Active Directory 中，使用 Azure 多重因素驗證或 Active Directory Federation Services 安全存取 Azure AD 的資源。 您可以使用下列程序來保護 Azure 多重因素驗證或 Active Directory Federation Services 的 Azure Active Directory 資源。

## <a name="secure-azure-ad-resources-using-ad-fs"></a>安全使用 AD FS Azure AD 資源

若要保護您的雲端資源，請先啟用使用者的帳戶，然後設定宣告規則。 請遵循此程序，逐一進行步驟︰

1. 若要啟用帳戶使用[Turn-on 多重因素驗證的使用者](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users)所述的步驟。
2. 啟動 AD FS 管理主控台。
![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. 瀏覽至**依賴廠商信任**，然後以滑鼠右鍵按一下依賴廠商信任。 選取 [**編輯宣告規則...**
4. 按一下 [**新增規則...**
5. 從下拉式清單中，選取 [**傳送宣告使用自訂規則**，然後按一下 [**下一步**]。
6. 輸入宣告規則的名稱。
7. 下的 [自訂規則︰ 新增下列文字︰

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    相對應的宣告︰

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```

8. 按一下**[確定]**然後選擇 [**完成**]。 關閉 AD FS 管理主控台。

使用者然後可以自行完成登入使用內部部署方法 （例如智慧卡）。

## <a name="trusted-ips-for-federated-users"></a>信任的 IPs 同盟使用者
信任的 Ip 允許 by-pass 雙步驟驗證管理員，特定的 IP 位址或同盟要求針對自己內部網路中的使用者。 下列各節說明如何設定 Azure 多重因素驗證信任 IPs 同盟的使用者與 by-pass 雙步驟驗證，當要求來自同盟的使用者內部網路中。 這是設定以使用通過]、 或篩選內送的宣告範本內公司網路的宣告類型與 AD FS 來達成。

此範例使用 Office 365 我們依賴廠商信任。

### <a name="configure-the-ad-fs-claims-rules"></a>設定 AD FS 宣告規則

我們需要做的第一個項目是設定 AD FS 宣告。 我們會建立兩個宣告規則，一個用於內公司網路的宣告類型，保留使用者登入的其他項目。

1. 開啟 AD FS 管理]。
2. 在左側，選取 [**依賴廠商信任**]。
3. **Microsoft Office 365 身分識別的平台**上按一下滑鼠右鍵，然後選取 [**編輯宣告規則...**
![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. 在發行轉換規則] 中，按一下 [**新增規則。**
![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. 在新增轉換宣告規則精靈]，從下拉式清單選取**傳遞到使用中] 或 [篩選連入宣告**，按一下 [**下一步**。
![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. 在 [宣告規則名稱旁的方塊，為您的規則的名稱。 例如︰ InsideCorpNet。
7. 從下拉式清單中，連入旁宣告類型，請選取**內公司網路**。
![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. 按一下 [**完成**]。
9. 在發行轉換規則] 中，按一下 [**新增規則**]。
10. 在新增轉換宣告規則精靈]，從下拉式清單中選取 [**使用自訂規則傳送宣告**，按一下 [**下一步**。
11. 在 [宣告規則名稱下方的方塊中︰ 輸入*保留使用者登入*。
12. 在 [自訂規則] 方塊中，輸入︰

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. 按一下 [**完成**]。
14. 按一下 [**套用**]。
15. 按一下 [**確定**]。
16. 關閉 AD FS 管理。



### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>設定 Azure 多重因素驗證信任 IPs 與同盟使用者
宣告都在 [位置] 中，我們可以設定受信任的 Ip。

1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。
2. 在左側，按一下 [ **Active Directory**。
3. 在 [目錄] 中，選取您要設定受信任的 IPs 的目錄。
4. 在您所選取的目錄，按一下 [**設定**]。
5. 多重因素驗證在區段中，按一下 [**管理服務設定**]。
6. 在 [服務設定] 頁面的 [受信任 IPs，底下選取**從同盟使用者略過多因素驗證要求的我的網路。**
![雲端](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. 按一下 [**儲存**]。
8. 一旦已套用的更新，請按一下 [**關閉**]。


這樣就可以 ！ 此時，同盟的 Office 365 使用者只需要使用應該 MFA 時宣告來自外部公司內部網路。
