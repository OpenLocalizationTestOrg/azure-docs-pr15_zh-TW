<properties
    pageTitle="Azure AD Connect︰ 連接埠 |Microsoft Azure"
    description="此頁面是技術參照頁面，必須是開啟 Azure AD Connect 的連接埠"
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
    ms.date="08/25/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-required-ports-and-protocols"></a>混合式身分識別所需的連接埠和通訊協定
下列文件是提供必要的連接埠與通訊協定所需執行混合式身分識別解決方案的相關資訊的技術參照。 使用下列圖例，請參閱對應表。

![什麼是 Azure AD Connect](./media/active-directory-aadconnect-ports/required1.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>資料表 1-Azure AD 連線和內部部署 AD
下表描述的連接埠和通訊協定所需的 Azure AD Connect 伺服器之間的通訊和內部部署 AD。

通訊協定 | 連接埠 | 描述
--------- | --------- |---------
DNS|53 (TCP/UDP)| 在目的地樹系 DNS 查閱。
Kerberos|88 (TCP/UDP)| AD 樹系 Kerberos 驗證。
MS RPC |135 (TCP/UDP)| 它繫結至 AD 樹系時，請用在初次 Azure AD 連線精靈] 的設定。
LDAP|389 (TCP/UDP)| 用於從 AD 資料匯入]。 利用 Kerberos 登和徽章加密資料。
LDAP/SSL|636 (TCP/UDP)| 用於從 AD 資料匯入]。 資料傳輸都簽章及加密。 只有當您使用的 SSL 使用。
RPC |49152-65535 (隨機高 RPC Port)(TCP/UDP)| 它繫結至 AD 樹系時，請使用期間的 Azure AD Connect 初始設定。 如需詳細資訊，請參閱[KB929851](https://support.microsoft.com/kb/929851) [KB832017](https://support.microsoft.com/kb/832017)，與[KB224196](https://support.microsoft.com/kb/224196) 。

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>資料表 2-Azure AD 連線和 Azure AD
下表描述的連接埠和通訊協定所需的 Azure AD Connect 伺服器和 Azure AD 之間的通訊。

通訊協定 |連接埠 |描述
--------- | --------- |---------
HTTP|80 (TCP/UDP)| 用來下載 Crl （憑證撤銷清單 」） 以驗證 SSL 憑證。
HTTPS|443(TCP/UDP)| 用來與 Azure AD 同步處理。

清單的 Url 與 IP 位址，您需要在開啟您的防火牆，請參閱[Office 365 Url 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)。

## <a name="table-3---azure-ad-connect-and-federation-serverswap"></a>資料表 3-Azure AD 連線和同盟伺服器/WAP
下表描述的連接埠和通訊協定所需的 Azure AD Connect 伺服器和同盟/WAP 伺服器之間的通訊。  

通訊協定 |連接埠 |描述
--------- | --------- |---------
HTTP|80 (TCP/UDP)| 用來下載 Crl （憑證撤銷清單 」） 以驗證 SSL 憑證。
HTTPS|443(TCP/UDP)| 用來與 Azure AD 同步處理。
WinRM|5985| WinRM 接聽程式

## <a name="table-4---wap-and-federation-servers"></a>表 4-WAP 和同盟伺服器
下表描述的連接埠和通訊協定所需的同盟伺服器和 WAP 伺服器之間的通訊。

通訊協定 |連接埠 |描述
--------- | --------- |---------
HTTPS|443(TCP/UDP)| 用來進行驗證。

## <a name="table-5---wap-and-users"></a>表 5-WAP 和使用者
下表描述的連接埠和通訊協定所需的使用者和 WAP 伺服器之間的通訊。

通訊協定 |連接埠 |描述
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| 用於裝置驗證。
TCP|49443 (TCP)| 用於憑證驗證]。

## <a name="table-6a--6b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>表格 6a 與 6b-Azure AD 連線健康情況代理程式的 （AD FS/同步處理） 和 Azure AD
下表描述的結束點、 連接埠和通訊協定所需的 Azure AD 連線健康情況代理程式和 Azure AD 之間的通訊

### <a name="table-6a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>表格 6a-連接埠與通訊協定 Azure AD 連線健康情況代理程式的 （AD FS/同步處理） 和 Azure AD
下表描述的下列輸出的連接埠和通訊協定所需的 Azure AD 連線健康情況代理程式和 Azure AD 之間的通訊。  

通訊協定 |連接埠  |描述
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| 輸出
Azure 服務匯流排|5671 (TCP/UDP)| 輸出

### <a name="6b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>6b-Azure AD 連線狀況代理程式的 （AD FS/同步處理） 的端點和 Azure AD
結束點的清單，請參閱[Azure AD 連線狀況代理程式的需求一節](active-directory-aadconnect-health-agent-install.md#requirements)。
