<properties
    pageTitle="Azure 多重因素驗證-下一步是什麼"
    description="這是將說明如何下一步處理 MFA Azure 多重因素驗證頁面。  這包含報表、 網路詐騙提醒、 一次性略過、 快取，信任的 ip 和應用程式的密碼的自訂的語音訊息。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="kgremban"/>

# <a name="configuring-azure-multi-factor-authentication"></a>設定 Azure 多重因素驗證

本文可協助您管理 Azure 多重因素驗證，您都上手。  矩形各種不同的主題可協助您充分利用 Azure 多重因素驗證。  而非所有這些功能可在每個版本的 Azure 多重因素驗證。

Azure 多重因素驗證管理入口網站中找到的設定，下列功能。 有兩種方式可以存取 MFA 管理入口網站中，這兩者完成透過 Azure 入口網站。 首先，來管理多重因素驗證提供者，如果使用消耗型 MFA。 第二個是透過 MFA 服務設定。 第二個選項需要多重因素驗證提供者或 Azure MFA、 Azure AD 進階版或企業版行動性套件的授權。

若要存取透過 Azure 多重因素驗證提供者 MFA 管理入口網站，登入 Azure 入口網站，以系統管理員身分並選取 [Active Directory] 選項。 **多重因素驗證提供者**] 索引標籤，然後選取您的目錄，按一下 [**管理**] 按鈕，底部。

若要存取 MFA 管理入口網站透過 [MFA 服務設定] 頁面，登入 Azure 入口網站，以系統管理員身分並選取 [Active Directory] 選項。 按一下目錄，然後按一下 [**設定**] 索引標籤。 在 [多重因素驗證] 區段中，選取 [**管理服務設定**]。 在 [MFA 服務設定] 頁面底部，按一下 [**移至入口網站**連結。


功能| 描述| 涵蓋的項目
:------------- | :------------- | :------------- |
[網路詐騙提醒](#fraud-alert)|網路詐騙提醒可與設定，讓使用者可以在報告權利嘗試存取他們的資源。|如何設定、 設定和報表網路詐騙
[一次略過](#one-time-bypass) |一次略過，讓使用者進行驗證一次 [略過 「 多重因素驗證。|如何安裝和設定一次性略過
[自訂的語音訊息](#custom-voice-messages) |自訂的語音訊息可讓您自己的錄製或問候語使用多重因素驗證。 |如何安裝和設定自訂的問候語及郵件
[快取](#caching-in-azure-multi-factor-authentication)|快取，可讓您設定在特定時間期間，以便後續的驗證，才能成功自動。 |如何安裝和設定驗證快取。
[信任的 IPs](#trusted-ips)|信任 IPs 是允許的受管理或同盟租用戶系統管理員可略過多因素驗證的使用者，從公司的近端內部網路登入的多重因素驗證的功能。|設定並設定不受限於多重因素驗證的 IP 位址
[應用程式密碼](#app-passwords)|應用程式密碼 」 可讓應用程式的不是 MFA 可識別略過多因素驗證並繼續工作。|應用程式密碼的相關資訊。
[記住以記憶的裝置與瀏覽器的 [多重因素驗證](#remember-multi-factor-authentication-for-devices-users-trust)|可讓您記住設定數天後使用者已順利登入使用 MFA 的裝置。|啟用此功能，及設定天數的相關資訊。
[選取驗證方法](#selectable-verification-methods)|可讓您選擇的驗證方法，可供使用者使用。|啟用或停用特定的驗證方法，例如通話或文字訊息的相關資訊。



## <a name="fraud-alert"></a>網路詐騙提醒
網路詐騙提醒可與設定，讓使用者可以在報告權利嘗試存取他們的資源。  使用者可以在報告網路詐騙與行動應用程式，或透過他們的電話。

### <a name="to-set-up-and-configure-fraud-alert"></a>若要安裝和設定網路詐騙提醒

1.  登入 http://azure.microsoft.com
2.  瀏覽至 [MFA 管理入口網站，依照在此頁面頂端的指示。
3.  Azure 多重因素驗證管理入口網站中，按一下 [設定] 區段下的 [設定]。
4.  在 [設定] 頁面的 [網路詐騙通知] 區段中，核取 [允許使用者為送出網路詐騙提醒] 核取方塊。
5.  如果您想封鎖時提報網路詐騙的使用者，核取封鎖使用者時放置提報網路詐騙。
6.  在 [**程式碼至報表網路詐騙期間初始問候**] 文字方塊中輸入數字代碼，可在通話驗證。 如果使用者輸入此程式碼，再加上 #，而不是 # 符號，然後將報告詐騙警告。
7.  在底部，按一下 [儲存]。

>[AZURE.NOTE]
>Microsoft 的預設語音問候語指示使用者按下 0 # 提交詐騙警告。 如果您想要使用 0 以外的程式碼，您應該錄製，然後上傳您自己的自訂語音問候語適當的指示。


![雲端](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>報表網路詐騙通知
可以提報網路詐騙提醒的兩種方法。  請透過行動應用程式，或透過電話使用。  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>使用行動應用程式的報表網路詐騙通知



1. 當驗證傳送到您的電話時，選取要啟動 Microsoft 驗證應用程式。
2. 網路詐騙的報表，請按一下 [取消] 以及 [報表網路詐騙。 如此會開啟一個方塊，指出您組織的 IT 支援人員會收到通知。
3. 按一下 [報表網路詐騙。
4. 在應用程式，請按一下 [關閉]。

![雲端](./media/multi-factor-authentication-whats-next/report1.png)


![雲端](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>報表網路詐騙通知的電話

1. 當驗證來電至您的電話、 回答。  
2. 網路詐騙的報表，請輸入已設定為與報告透過電話然後 # 符號網路詐騙的程式碼。 將會通知您已送出詐騙警告。
3. 結束通話。

### <a name="to-view-the-fraud-report"></a>若要檢視網路詐騙報表

1. 登入[http://azure.microsoft.com](https://azure.microsoft.com/)
2. 在左側，選取 [Active Directory]。
3. 在頂端選取多重因素驗證提供者。 如此會開啟您的多重因素驗證提供者的清單。
4. 如果您有一個以上的多重因素驗證提供者，請選取您想要檢視的網路詐騙通知的報表，並按一下 [管理] 頁面的底部。 如果您有一個，請按一下 [管理]。 這會開啟 [Azure 多重因素驗證管理入口網站。
5. 按一下 [Azure 多重因素驗證管理入口網站上，在左側，在 [檢視報表，底下的 [網路詐騙提醒]。
6. 指定您想要檢視在報表中的日期範圍。 也，您可以指定任何特定的使用者名稱、 電話號碼和使用者的狀態。
7. 按一下 [執行]。 如此會開啟至下一個類似報表。 如果您想要將報表匯出，您也可以按一下 [匯出為 CSV。

## <a name="one-time-bypass"></a>一次略過

一次略過，讓使用者進行驗證一次 [略過 「 多重因素驗證。 略過暫時，而在指定的秒數後到期。  因此，在位置的行動應用程式或電話不能接收的通知或電話通話的情況下，您可以啟用一次性略過，讓使用者可以存取所需的資源。

### <a name="to-create-a-one-time-bypass"></a>若要建立一次性略過

1.  登入 http://azure.microsoft.com
2.  瀏覽至 [MFA 管理入口網站，依照在此頁面頂端的指示。
3.  Azure 多重因素驗證管理入口網站中，如果您看到您的租用戶或 Azure MFA 提供者的名稱左邊的旁邊，按一下 [+ + 看到不同的 MFA 伺服器複寫群組和 Azure 預設的群組。 按一下適當的群組。
4.  在 [使用者管理] 底下，按一下 [ **One-Time 略過**]。
![雲端](./media/multi-factor-authentication-whats-next/create1.png)
5.  在 [One-Time 略過] 頁面上，按一下 [**新 One-Time 略過**]。
6.  輸入使用者的使用者名稱的秒數，會有略過，略過的原因，然後按一下 [**略過**]。
![雲端](./media/multi-factor-authentication-whats-next/create2.png)
7.  此時，使用者必須登入一次性略過到期之前。



### <a name="to-view-the-one-time-bypass-report"></a>若要檢視一次性略過報表

1. 登入[http://azure.microsoft.com](https://azure.microsoft.com/)
2. 在左側，選取 [Active Directory]。
3. 在頂端選取多重因素驗證提供者。 如此會開啟您的多重因素驗證提供者的清單。
4. 如果您有一個以上的多重因素驗證提供者，請選取您想要檢視的網路詐騙通知的報表，並按一下 [管理] 頁面的底部。 如果您有一個，請按一下 [管理]。 這會開啟 [Azure 多重因素驗證管理入口網站。
5. 按一下 [Azure 多重因素驗證管理入口網站中，在左側，在 [檢視報表，底下的 [One-Time 略過]。
6. 指定您想要檢視在報表中的日期範圍。 也，您可以指定任何特定的使用者名稱、 電話號碼和使用者的狀態。
7. 按一下 [執行]。 如此會開啟至下一個類似報表。 如果您想要將報表匯出，您也可以按一下 [匯出為 CSV。

<center>![雲端](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>自訂的語音訊息

自訂的語音訊息可讓您自己的錄製或問候語使用多重因素驗證。  這些可另外，或若要取代 Microsoft 的記錄。

在您開始之前請注意下列任一動作︰

- 目前支援的檔案格式為.wav 和.mp3。
- 檔案大小限制為 5 MB。
- 建議的驗證訊息就能超過 20 秒。 任何大於這可能會導致驗證失敗，因為郵件完成並驗證時發生逾時前，使用者可能沒有回應。



### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>若要設定自訂的語音訊息中 Azure 多重因素驗證
1.  建立自訂的語音郵件使用其中一個支援的檔案格式。
2.  登入 http://azure.microsoft.com
3.  瀏覽至 [MFA 管理入口網站，依照在此頁面頂端的指示。
4.  Azure 多重因素驗證管理入口網站中，按一下 [在 [設定] 區段底下的 [語音郵件]。
5.  語音訊息] 區段下，按一下 [**新的語音訊息**]。
![雲端](./media/multi-factor-authentication-whats-next/custom1.png)
6.  在 [設定上︰ 新的語音訊息頁面上，按一下 [**管理音效檔案**。
![雲端](./media/multi-factor-authentication-whats-next/custom2.png)
7.  在 [設定上︰ 音效檔案] 頁面上，按一下 [**上傳音效檔案**。
![雲端](./media/multi-factor-authentication-whats-next/custom3.png)
8.  在 [設定上︰ 音效檔案上傳、 按一下 [**瀏覽**及瀏覽至您的語音訊息、 按一下 [**開啟**]。
![雲端](./media/multi-factor-authentication-whats-next/custom4.png)
9.  新增描述，然後按一下 [上傳]。
10. 一旦完成後，會出現確認訊息您順利上傳檔案。
11. 在左側，按一下 [語音郵件]。
12. 語音訊息] 區段下，按一下 [新的語音訊息]。
13. 從 [語言] 下拉式清單，選取 [語言]。
14. 如果這則訊息的特定應用程式，請在應用程式] 方塊中指定。
15. 從郵件類型] 中，選取 [覆寫我們新的自訂郵件的郵件類型。
16. 從音效檔] 下拉式清單，選取您的音效檔。
17. 按一下 [**建立**]。 您已成功建立語音訊息時，確認訊息。
![雲端](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## <a name="caching-in-azure-multi-factor-authentication"></a>快取中 Azure 多重因素驗證

快取，可讓您設定在特定時間期間，以便後續的驗證，才能成功自動。 這是主要時使用內部部署系統，例如 VPN 仍在進行中的第一個要求時，傳送多個驗證要求。 這個選項可讓使用者成功過程中的驗證後自動成功的後續要求。 請注意，快取不是用於 Azure AD 登增益集。


### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>若要設定快取中 Azure 多重因素驗證

1.  登入 http://azure.microsoft.com
2.  瀏覽至 [MFA 管理入口網站，依照在此頁面頂端的指示。
3.  Azure 多重因素驗證管理入口網站中，按一下 [在 [設定] 區段底下的 [快取]。
4.  在 [設定快取] 頁面上按一下 [新的快取
5.  選取 [快取類型及快取秒數。 按一下 [建立]。

<center>![雲端](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>信任的 IPs

信任 IPs 是允許的受管理或同盟租用戶系統管理員可略過多因素驗證的使用者，從公司的近端內部網路登入的多重因素驗證的功能。 功能可供有 Azure AD 進階版、 企業版行動性套件或 Azure 多重因素驗證授權的 Azure AD 租用戶。


Azure AD 租用戶的類型| 可用的受信任的 IP 選項
:------------- | :------------- |
管理|特定的 IP 位址範圍-系統管理員可以指定的 IP 位址範圍，可以略過多因素驗證的使用者，在公司內部網路的登入。
同盟|<li>所有聯盟使用者的登入從組織內所有同盟的使用者略過多因素驗證使用 AD FS 發出宣告。</li><li>特定的 IP 位址範圍-系統管理員可以指定的 IP 位址範圍，可以略過多因素驗證的使用者，在公司內部網路的登入。

這會略過僅適用於從公司內部網路內。 因此，例如，如果您只選取所有同盟的使用者]，使用者登入從外部在公司內部網路，該使用者有使用多重因素驗證，即使使用者提出 AD FS 宣告進行驗證。 下表說明當多重因素驗證與應用程式密碼必您網卡內部和外部您網卡啟用 [受信任的 Ip。


|信任 IPs 啟用| 信任停用的 Ip
:------------- | :------------- | :------------- |
內網卡|在瀏覽器流程多重因素驗證非必要。|多重因素驗證所需的瀏覽器，
|豐富的用戶端應用程式，一般密碼運作如果使用者未建立任何應用程式的密碼。 建立應用程式密碼之後, 是必要的應用程式密碼。|豐富的用戶端應用程式，所需的應用程式密碼
外部網卡|多重因素驗證所需的瀏覽器。|多重因素驗證所需的瀏覽器。
|所需的應用程式密碼的豐富型用戶端應用程式。|所需的應用程式密碼的豐富型用戶端應用程式。

### <a name="to-enable-trusted-ips"></a>若要啟用信任 IPs

1. 登入 Azure 傳統入口網站。
2. 在左側，按一下 [Active Directory。
3. 目錄下的 [在您想要設定受信任 IPsing 上的目錄。
4. 在您所選取的目錄，按一下 [設定]。
5. 多重因素驗證在區段中，按一下 [管理服務設定]。
6. 在 [服務設定] 頁面底下信任 IPs，選取以下一項︰

    - 要求同盟來自我內部網路的使用者-所有同盟的登入公司網路的使用者將會略過多因素驗證使用 AD FS 發出宣告。
    - 來自特定範圍的公用 Ip – 的要求，請使用 CIDR 標記法來提供的方塊中輸入的 IP 位址。 例如︰ xxx.xxx.xxx.0/24 的 IP 位址範圍 xxx.xxx.xxx.1 – xxx.xxx.xxx.254 或 xxx.xxx.xxx.xxx/32 單一的 IP 位址。 您可以輸入最 50 IP 位址範圍。

7. 按一下 [儲存]。
8. 一旦已套用的更新，請按一下 [關閉]。



![信任的 IPs](./media/multi-factor-authentication-whats-next/trustedips3.png)




## <a name="app-passwords"></a>應用程式密碼

在某些應用程式，例如 Office 2010 或更舊版本和 Apple Mail 中，您無法使用多重因素驗證。  若要使用這些應用程式，您會需要使用 」 應用程式密碼 」 來代替您傳統的密碼。  應用程式密碼 」 可讓應用程式略過多因素驗證並繼續工作。

>[AZURE.NOTE] Office 2013 用戶端的現代化驗證
>
> Office 2013 用戶端 (包括 Outlook) 現在支援新的驗證通訊協定，並可啟用支援多重因素驗證。  這表示，啟用之後，應用程式密碼並不需要使用與 Office 2013 用戶端。  如需詳細資訊，請參閱[Office 2013 的現代化驗證公用預覽宣告](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。



### <a name="important-things-to-know-about-app-passwords"></a>若要瞭解應用程式密碼的重要事項

以下是重要的清單項目，您應該瞭解的應用程式密碼。

- 使用者可以有多個應用程式密碼，可增加盜用的表面區域。 由於應用程式密碼難記得，它可能鼓勵這寫下的人員。 這不建議使用，並應正常動作，因為只有一個因子變異數，才能使用應用程式密碼登入。
- 快取密碼，並將其用於內部部署案例的應用程式可能會開始失敗，因為應用程式密碼為未知以外的組織的識別碼。 範例是內部部署的 Exchange 電子郵件，但在雲端，便封存的電子郵件。 相同的密碼無法運作。
- 實際的密碼會自動產生，並不會提供的使用者。 這是因為自動產生的密碼，難猜測攻擊，更加安全。
- 目前沒有 40 每位使用者的密碼的限制。 會提示您刪除其中一個您現有的應用程式密碼以建立新的項目。
- 一旦啟用多重因素驗證的使用者帳戶，與 Outlook 和 Lync 等的大部分非瀏覽器用戶端可以使用應用程式密碼，但無法使用透過非瀏覽器應用程式，例如 Windows PowerShell 的應用程式密碼，即使該使用者擁有系統管理帳戶執行系統管理的動作。  請確定您建立強式密碼來執行 PowerShell 指令碼的服務帳戶並不會啟用多因素驗證該帳戶。

>[AZURE.WARNING]  應用程式密碼不在混合式環境用戶端與兩個內部部署和雲端自動探索結束點的位置。 這是因為網域密碼才能驗證內部部署和雲端驗證方法所需的應用程式密碼。


### <a name="naming-guidance-for-app-passwords"></a>應用程式密碼的命名指引
建議您在應用程式的密碼名稱應該會反映在使用的裝置。 例如，如果您有非瀏覽器應用程式，例如 Outlook、 Word 和 Excel 的膝上型電腦，您只需要建立一個名為膝上型電腦的應用程式密碼，並在這些應用程式中使用該應用程式的密碼。 雖然您可以建立不同的密碼，針對您所有的這些應用程式，但不建議。 建議您方式是使用每個裝置的一個應用程式密碼。


<center>![雲端](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>同盟 (SSO) 應用程式密碼
Azure AD 支援與內部部署 Windows Server Active Directory 網域服務 (AD DS) 同盟。 如果您的組織是使用 Azure AD federated(SSO)，而且您要使用 Azure 多重因素驗證]，然後下面是，您應該注意時使用應用程式密碼的重要資訊。 僅適用於 federated(SSO) 客戶。

- 應用程式密碼由 Azure AD 驗證，因此略過同盟。 同盟積極只適用於設定應用程式密碼。
- 針對 federated(SSO) 的使用者，我們永遠不會移至與被動流程不同身分識別提供者 (IdP)。 密碼會儲存在組織的識別碼。 使用者離職，該資訊流程組織使用 DirSync 即時的識別碼。 帳戶停用/刪除可能需要三個小時，若要同步處理，在 Azure AD 延遲停用/刪除應用程式密碼。
- 內部部署用戶端存取控制設定不會被應用程式密碼
- 內部部署驗證記錄 / 稽核功能，都可以使用應用程式密碼
- 更多使用者教育版 Microsoft Lync 2013 用戶端資格。 所需的步驟，請參閱如何變更您的電子郵件中的密碼的應用程式的密碼。
- 某些進階架構設計可能會要求您使用多重因素驗證與用戶端，根據驗證位置時，使用組織的使用者名稱和密碼與應用程式密碼的組合。 驗證的內部部署基礎結構的用戶端，您可以使用組織的使用者名稱和密碼。 Azure AD 驗證的用戶端，您可以使用應用程式密碼。

例如，假設您有一個架構，包含下列︰

- 您同盟的內部部署的 Active Directory 與 Azure AD 執行個體
- 您使用的 Exchange online
- 您使用 Lync，特別是內部部署
- 您使用的 Azure 多重因素驗證


![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

 在下列情況下，您必須執行下列動作︰

- 當登入 Lync，請使用您組織的使用者名稱和密碼。
- 當您嘗試存取透過 Outlook 用戶端連線到 Exchange online 的通訊錄時, 使用應用程式密碼。

### <a name="allowing-app-password-creation"></a>允許建立應用程式密碼
根據預設，使用者無法建立應用程式密碼。  您必須啟用此功能。  若要讓使用者能夠建立應用程式密碼，請使用下列程序。

#### <a name="to-enable-users-to-create-app-passwords"></a>若要讓使用者建立應用程式的密碼



1. Azure 傳統入口網站登入。
2. 在左側，按一下 [Active Directory。
3. 目錄下的 [您想要啟用使用者的目錄。
4. 在頂端，按一下 [使用者]。
5. 在頁面底部，按一下 [管理多重因素驗證  
6. 在 [多重因素驗證] 頁面的頂端，按一下 [服務設定]。
7. 確定已選取 [允許使用者建立應用程式密碼以登入非瀏覽器應用程式旁的選項按鈕。


![建立應用程式的密碼](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>建立應用程式的密碼
使用者可以在其初始註冊時建立應用程式密碼。  授與他們在註冊程序可讓其建立結尾的選項。

此外使用者也可以建立應用程式密碼稍後藉由變更其設定 Azure 入口網站中，[Office 365 入口網站中或

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>若要在 Office 365 入口網站中建立應用程式密碼
--------------------------------------------------------------------------------


1. 登入 Office 365 入口網站
2. 在右上角，選取 [設定小工具
3. 在左側，選取 [其他安全性驗證
4. 在右側，請選取 [**更新我的電話號碼用於帳戶安全性**
5. 在 [proofup] 頁面頂端，選取 [應用程式密碼
6. 按一下 [**建立**]
7. 輸入應用程式密碼的名稱，然後按一下 [**下一步**
8. 將應用程式密碼複製到剪貼簿，並將它貼到您的應用程式。

<center>![雲端](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Azure 入口網站中建立應用程式的密碼
--------------------------------------------------------------------------------
1. Azure 傳統入口網站登入。
3. 在上方，以滑鼠右鍵按一下您的使用者名稱，然後選取其他安全性驗證。
5. 在 [proofup] 頁面頂端，選取 [應用程式密碼
6. 按一下 [**建立**]
7. 輸入應用程式密碼的名稱，然後按一下 [**下一步**
8. 將應用程式密碼複製到剪貼簿，並將它貼到您的應用程式。


![應用程式密碼](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>若要建立應用程式密碼，如果您沒有 Office 365 或 Azure 的訂閱
--------------------------------------------------------------------------------
1. 登入[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 在頂端，選取 [設定檔。
3. 按一下您的使用者名稱，然後選取其他安全性驗證。
5. 在 [proofup] 頁面頂端，選取 [應用程式密碼
6. 按一下 [**建立**]
7. 輸入應用程式密碼的名稱，然後按一下 [**下一步**
8. 將應用程式密碼複製到剪貼簿，並將它貼到您的應用程式。

![應用程式密碼](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>裝置使用者信任記住多重因素驗證

請記住多重因素驗證的裝置與瀏覽器使用者信任是免費的功能，所有的 MFA 使用者。  您可以授與使用者的選項 by-pass MFA 設定天數後執行成功的登入使用 MFA。 這可以增強您的使用者的可用性。

不過，使用者可以記住 MFA 受信任的裝置，因為這項功能可能會降低帳戶安全性。 若要確保帳戶安全性，您應該還原多重因素驗證其裝置下列情況其中一項︰

- 如果洩露其公司帳戶
- 如果遺失或竊取記憶的裝置

> [AZURE.NOTE] 此功能是以瀏覽器 cookie 快取實作。 如果您在瀏覽器 cookie 尚未啟用，其無法運作。

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>如何啟用或停用記住多重因素驗證

1. Azure 傳統入口網站登入。
2. 在左側，按一下 [Active Directory。
3. 在 [作用中的目錄，按一下您想要為記住多重因素驗證設定裝置的目錄。
4. 在您所選取的目錄，按一下 [設定]。
5. 多重因素驗證在區段中，按一下 [管理服務設定]。
6. 在 [服務設定] 頁面中管理使用者的裝置設定] 底下選取/取消選取**允許使用者請記住，這些信任的裝置上的多重因素驗證**。
![請記住裝置](./media/multi-factor-authentication-whats-next/remember.png)
8. 設定您想要讓暫停的天數。 預設值為 14 天。
9. 按一下 [儲存]。
10. 按一下 [關閉]。


## <a name="selectable-verification-methods"></a>選取驗證方法
在 [雲端] 和 [內部部署版本中，您可以選擇的驗證方法可供您的使用者。 下表提供每一種方法的簡介。

當您的使用者註冊 MFA 自己的帳戶時，選擇其慣用的驗證方法，顯示您啟用的選項。 在中[設定 [我的帳號的雙步驟驗證](multi-factor-authentication-end-user-first-time.md)涵蓋其註冊程序提供的指導方針

方法|描述
:------------- | :------------- |
撥電話 |  撥打到行動電話驗證自動的語音通話。 使用者的電話，並在驗證的電話鍵盤中按下 #。 這個電話號碼不到內部部署的 Active Directory 同步。
文字訊息至電話 | 傳送文字訊息包含使用者驗證碼。 [回覆文字訊息的驗證碼或登入介面中輸入驗證碼會提示使用者。
透過行動應用程式的通知 | 在此模式下，Microsoft 驗證應用程式可防止未經授權的存取帳戶，停止權利交易]。 這是使用電話或裝置註冊推入通知。 只要檢視通知，如果是合法您點選驗證。 否則，您可能會選擇拒絕，或選擇拒絕報告權利通知。 報告權利通知的詳細資訊，請參閱如何使用 [多重因素驗證的 [拒絕] 和 [報表網路詐騙功能。</br></br>使用[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、 [Android](http://go.microsoft.com/fwlink/?Linkid=825072)，以及[IOS](http://go.microsoft.com/fwlink/?Linkid=825073)Microsoft 驗證應用程式。|
從行動應用程式的驗證碼 | 在此模式中，Microsoft 驗證應用程式可以用於軟體權杖產生誓言驗證碼。 除了使用者名稱和密碼，才能提供第二個驗證格式的可以輸入此驗證碼。</li><br><p> 使用[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、 [Android](http://go.microsoft.com/fwlink/?Linkid=825072)，以及[IOS](http://go.microsoft.com/fwlink/?Linkid=825073)Microsoft 驗證應用程式。

### <a name="how-to-enabledisable-authentication-methods"></a>如何啟用或停用驗證方法

1. Azure 傳統入口網站登入。
2. 在左側，按一下 [Active Directory。
3. 在 [作用中的目錄，按一下您想要啟用或停用驗證方法目錄。
4. 在您所選取的目錄，按一下 [設定]。
5. 多重因素驗證在區段中，按一下 [管理服務設定]。
6. 在 [服務設定] 頁面的 [驗證選項] 底下，選取/取消選取您想要使用的選項。</br></br>
![驗證選項](./media/multi-factor-authentication-whats-next/authmethods.png)
9. 按一下 [儲存]。
10. 按一下 [關閉]。
