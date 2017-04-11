管理的身分識別是只為重要的公用雲端因為它是內部部署。 為了協助此，Azure 支援數種不同的雲端識別技術。 這些工作包括下列︰

- 您可以使用與 Azure 虛擬機器中建立的虛擬機器雲端中執行 Windows Server Active Directory （通常稱為剛才 AD）。 若要將您的內部部署資料中心擴充至雲端使用 Azure 時，此方法才有意義。


- 您可以使用 Azure Active Directory 提供您使用者單一登入[以服務 (SaaS) 的軟體](https://azure.microsoft.com/overview/what-is-saas/)應用程式。 Microsoft Office 365 使用這項技術，例如 [和 Azure 或其他雲端平台上執行應用程式也可以使用它。


- 執行內部部署或雲端中的應用程式可以使用 Azure Active Directory 存取控制可讓的使用者登入使用從 Facebook、 Google、 Microsoft 和其他身分識別提供者的身分識別。


本文將說明這三個選項。

## <a name="table-of-contents"></a>目錄

- [虛擬機器中執行 Windows Server Active Directory](#adinvm)

- [使用 Azure Active Directory](#ad)

- [使用 Azure Active Directory 存取控制](#ac)


## <a name="adinvm"></a>虛擬機器中執行 Windows Server Active Directory

Azure 虛擬機器中執行 Windows Server AD 很像執行內部部署。 [圖 1](#fig1) ] 顯示一般範例這樣的外觀。

![虛擬機器中的 azure Active Directory](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>圖 1︰ 連線至組織的內部部署資料中心使用 Azure 虛擬網路 Azure 虛擬機器中可以執行的 Windows Server Active Directory。

在此處所示的範例中，使用 Azure 虛擬機器的平台 IaaS 技術建立的 Vm 正在執行 Windows Server AD。 這些 Vm 和一些其他虛擬網路連線到內部部署資料中心使用 Azure 虛擬網路所組成。 虛擬網路 carves 出透過虛擬私人網路 (VPN) 連線的內部部署網路進行互動的雲端虛擬機器的群組。 此可讓這些 Azure 虛擬機器看起來像到內部部署資料中心的只是另一個子網路。 如圖所示，這些 Vm 的兩個正在執行 Windows Server AD 網域控制站。 虛擬網路中的其他虛擬機器可能在執行應用程式，例如 SharePoint 或以其他方式，例如使用開發和測試。 內部部署資料中心也會執行兩個 Windows Server AD 網域控制站。

有數種與執行內部部署連線雲端中的網域控制站的選項︰

- 讓所有的單一 Active Directory 網域的部分。

- 建立另一個 AD 網域內部部署和雲端屬於相同的樹系中。

- 建立不同的 AD 樹系雲端和內部部署，然後連線樹系使用跨樹系信任或 Windows Server Active Directory Federation Services (AD FS)，可以在 Azure 上也執行虛擬機器中。

進行任何選項，系統管理員應該確認驗證要求從內部部署使用者移至 [雲端網域控制站只有必要時，因為雲端連結有可能低於內部網路。 在連接雲端和內部部署網域控制站来考慮的另一個因素是複寫所產生的流量。 在雲端的網域控制站通常是在自己 AD 網站中，可讓系統管理員頻率排程複寫已完成。 Azure 流量傳送不在 Azure 資料中心，雖然不位元組傳送中，可能會影響系統管理員的複寫選項的費用。 也是值得時 Azure 並提供自己的網域名稱系統 (DNS) 支援，這項服務就會缺少 Active Directory （例如支援動態 DNS 和 SRV 記錄） 所需的功能。 因此，Azure 上執行 Windows Server AD 需要您自己的 DNS 伺服器雲端中的設定。

Azure Vm 在執行 Windows Server AD 可讓有意義的幾種不同情況。 以下是一些範例︰

- 如果您使用 Azure 虛擬機器您自己的資料中心的延伸，您可能需要處理項目，例如 Windows 整合式驗證要求或 LDAP 查詢本機網域控制站雲端來執行應用程式。 SharePoint 中，例如互動經常 Active Directory 和，雖然可以執行 SharePoint 伺服器陣列上 Azure 使用內部部署目錄，設定雲端中的網域控制站會大幅改善效能。 （很重要，這不需要，不過; 許多應用程式可以順利執行使用只有內部部署網域控制站雲端中發現。）

- 假設遠方分公司缺少執行自己的網域控制站的資源。 現在，其使用者必須驗證網域控制站另一端的世界-登入會變得很慢。 執行在 Azure Active Directory，深入瞭解 Microsoft 資料中心可加速這不需要更多分公司中的伺服器。

- 使用 Azure 損毀修復的組織可能會維持在雲端，包括網域控制站的作用中 Vm 一小群。 它可以準備好，展開 [此網站的其他地方失敗接手視。

還有其他的可能性。 例如，您不需要在雲端的 Windows Server AD 連線到內部部署資料中心。 如果您想要執行 served 一組特定使用者的 SharePoint 伺服器陣列，舉例來說，所有員工想要單獨使用雲端身分登入，您可能會建立獨立樹系 Azure 上。 使用此技術的方式取決於您的目標是什麼。 （適用於更詳細的指南與 Azure，[請參閱](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)使用 Windows Server AD。）

## <a name="ad"></a>使用 Azure Active Directory

隨著 SaaS 應用程式變得更常見，他們會引發一個問題︰ 哪一類的目錄服務應該這些雲端應用程式使用？ Microsoft 的問題的答案是 Azure Active Directory。

有兩種主要的選項，以在雲端中使用此目錄服務︰

- 個人與使用只有 SaaS 應用程式的組織可以依賴 Azure Active Directory 為其唯一的目錄服務。

- 執行 Windows Server Active Directory 的組織可以連接 Azure Active Directory 他們的內部部署目錄，然後用它 SaaS 應用程式為其使用者單一登入。


[圖 2](#fig2)說明這兩個選項，其中的 Azure Active Directory 是所有所需的第一筆。

![虛擬機器中的 azure Active Directory](./media/identity/identity_02_AD.png)

<a name="fig2"></a>圖 2: Azure Active Directory 可讓組織的使用者單一登入 SaaS 應用程式，包括 Office 365。

如圖所示，Azure AD 是多租用戶服務。 這表示它可以同時支援許多不同的組織，儲存在每個使用者目錄資訊。 在此範例中，在組織的使用者嘗試存取 SaaS 應用程式。 這個應用程式可能是 Office 365，例如 SharePoint Online 的一部分或其他項目可能是-非 Microsoft 應用程式也可以使用這項技術。 因為 Azure AD 支援 SAML 2.0 通訊協定，請從應用程式所需是以互動] 使用此業界標準的能力。 （事實上，使用 Azure AD 應用程式，可以執行的任何資料中心，而不只是 Azure 資料中心）。

使用者存取 SaaS 應用程式 （步驟 1） 開始此程序。 若要使用此應用程式，使用者必須呈現 Azure AD 所發行的權杖。

此 token 加包含使用者，會識別的資訊和 Azure AD 數位簽章。 若要取得權杖，使用者驗證自己 Azure AD 提供使用者名稱和密碼 （步驟 2）。 Azure AD 然後傳回權杖他需要 （步驟 3）。

此 token 加然後會傳送至 SaaS 應用程式 （步驟 4），驗證的權杖簽章，並使用它的內容 （步驟 5）。 一般而言，應用程式會使用權杖包含決定哪些資訊，則會允許使用者存取，可能是以其他方式的身分識別資訊。

如果應用程式需要比權杖中包含哪些使用者的詳細資訊，它可以直接從 Azure AD 使用 Azure AD 圖形 API （步驟 6） 將此要求。 版 Azure AD 初始目錄結構描述是相當簡單︰ 它包含只使用者和群組以及它們之間的關聯。 若要深入瞭解使用者之間的連線，應用程式可以使用這項資訊。 例如，假設應用程式需要知道誰此使用者的管理員會決定是否他具有允許存取某些區塊的資料。 它可以瞭解此查詢圖形 API 透過 Azure AD。

圖形 API 使用一般 RESTful 通訊協定，讓它直接使用大部分的用戶端，包括行動裝置。 API 也支援 OData，新增更多實用的方式讓用戶端存取資料的項目例如查詢語言所定義的副檔名。 （如需 OData 的詳細資訊，請參閱[介紹 OData](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf)）。圖形 API 可用於瞭解使用者之間的關聯，因為它會讓瞭解社交圖表內嵌於特定的組織 （這是為什麼，然後按一下 [圖形 API） 的 Azure AD 結構描述中的應用程式。 並進行自我驗證的圖形 API 邀請 Azure ad，應用程式使用 OAuth 2.0。

如果組織不會使用 Windows Server Active Directory-沒有內部部署伺服器或網域-且只需要使用 Azure AD 的雲端應用程式，使用只是此雲端目錄會讓公司的使用者單一登入至所有連絡人。 尚未時這個案例中取得每日較為常見，大多數的組織仍然使用內部部署建立與 Windows Server 的 Active Directory 網域。 Azure AD 有實用的角色，才能播放以下，如下[圖](#fig3)3。

![虛擬機器中的 azure Active Directory](./media/identity/identity_03_AD.png)
<a id="fig3"></a>圖 3︰ 組織可以聯盟與 Azure Active Directory SaaS 應用程式為其使用者單一登入 Windows Server Active Directory。

在此案例中，使用者在組織 B 想要存取 SaaS 應用程式。 她負責這項工作之前，組織的目錄系統管理員必須建立同盟關係，Azure AD 使用 AD FS，如圖所示。 這些管理員也必須設定組織的內部部署 Windows Server AD 和 Azure AD 之間的資料同步處理。 這會自動複製使用者和群組資訊從內部部署目錄 Azure AD。 請注意，這可讓︰ 組織生效，將其內部部署目錄延伸到雲端。 結合 Windows Server AD 和 Azure AD 這種方式可讓組織可為單一項目，管理時仍有所需的兩個內部部署和雲端中的目錄服務。

若要使用 Azure AD 時，使用者第一次登入其內部部署的 Active Directory 網域平常 （步驟 1）。 當她嘗試存取 SaaS 應用程式 （步驟 2） 時，同盟程序會導致 Azure AD 發行她的權杖此應用程式 （步驟 3）。 (如需同盟的運作方式的詳細資訊，請參閱[宣告型 Windows 版的身分識別︰ 技術及分析藍本](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx)。)包含相同之前，此 token 加識別使用者，並 Azure AD 數位簽章。 此 token 加然後會傳送至 SaaS 應用程式 （步驟 4），驗證的權杖簽章，並使用它的內容 （步驟 5）。 在上一個案例中，應用程式可以使用圖形 API，若要進一步瞭解此使用者如果 SaaS 是必要 （步驟 6）。

現在，Azure AD 無法完全內部部署 Windows Server AD 取代。 已經說明過，雲端目錄有太多簡單結構描述、，並將其缺少項目，例如群組原則，儲存電腦的相關資訊，並為 LDAP 支援的功能。 （事實上，Windows 電腦無法設定讓使用者登入並使用 Azure AD 只-這不是支援的情況）。不過，初始目標 Azure AD 包含不維護個別的登入讓企業使用者存取應用程式在雲端，然後釋放內部部署目錄系統管理員與他們的組織使用的每個 SaaS 應用程式，以手動方式同步處理他們的內部部署目錄。 一段時間，不過，預期地址較大範圍的案例這個雲端目錄服務。

## <a name="ac"></a>使用 Azure Active Directory 存取控制

雲端識別技術可用來解決許多問題。 Azure Active Directory 可以授權讓組織的使用者單一登入多個 SaaS 應用程式，例如。 但在雲端的身分識別技術也可以用其他方法。

例如，假設，應用程式，想要讓使用者使用多個*身分識別提供者 (IdPs)*者發出的權杖登入。 許多不同的身分識別提供者存在於今天，包括 Facebook、 Google、 Microsoft 和其他人]，並在應用程式常見問題可讓使用者使用其中一個這些身分登入。 為什麼應用程式應該若要維持它自己的使用者和密碼清單時，可以改為依賴已經存在的身分識別麻煩？ 接受現有的身分識別可生活較簡單的使用者，有一個較少的使用者名稱和要記住的密碼，和的人員建立應用程式中，若要維持自己的使用者名稱和密碼清單不再需要的人。

但是，每個身分識別提供者問題某種權杖，而這些權杖不標準-每個 IdP 有自己的格式。 此外，這些權杖中的資訊，也無法標準。 想要接受權杖發出，指出與 Facebook、 Google，Microsoft 應用程式被面臨的撰寫唯一的程式碼處理每個這些不同的格式。

但為什麼執行此動作？ 為什麼無法改為建立媒介可以產生單一的權杖格式，以一般表示的身分識別的資訊？ 這種方法可以簡化的開發人員建立應用程式，因為他們現在需要處理只有一個種類的權杖。 Azure Active Directory 存取控制完全負責這項工作，提供在雲端媒介使用各種不同的權杖。 [圖 4](#fig4)顯示其運作方式

![虛擬機器中的 azure Active Directory](./media/identity/identity_04_IdentityProviders.png)
<a id="fig4"></a>圖 4: Azure Active Directory 存取控制可讓您更輕鬆地接受發出不同的身分識別提供者的身分識別權杖的應用程式。

當使用者嘗試從瀏覽器存取應用程式，就會開始程序。 供自己的 IdP （和的應用程式也信任），會重新導向應用程式。 她驗證自己此 IdP，例如輸入使用者名稱和密碼 （步驟 1），並 IdP 傳回包含她 （步驟 2） 的相關資訊的權杖。

如圖所示，存取控制支援各種不同雲端 IdPs，包括 Google、 Yahoo、 Facebook、 Microsoft （先前稱為 Windows Live ID） 及任何 OpenID 提供者所建立的帳戶。 也支援使用 Azure Active Directory 所建立的身分識別和透過 Windows Server Active Directory AD FS 同盟。 現在，涵蓋最常用的身分識別的目標是，是否由 IdPs 內部部署或雲端中。

使用者的瀏覽器有從其選擇 IdP IdP 權杖之後, 會傳送此 token 存取控制 （步驟 3）。 存取控制驗證的權杖，確保它是真正核發此 IdP，然後建立新的權杖根據已為此應用程式定義規則。 Azure Active Directory 像存取控制多租用戶服務，但是租用戶應用程式，而不是客戶的組織。 每個應用程式如圖所示，能自己的命名空間，並可以定義當您的授權和更多關於不同的規則。

這些規則，讓每個應用程式的系統管理員定義如何從各種不同的 IdPs 權杖轉換為 Access 控制項權杖。 例如，如果不同 IdPs 使用不同類型的代表使用者名稱，存取控制規則可以轉換為所有這些常見的使用者名稱類型。 存取控制傳送此新 token 加回瀏覽器 （步驟 4），提交到的應用程式 （步驟 5）。 存取控制權杖後，在應用程式會驗證此 token 加實際上存取控制由發出的然後使用它包含 （步驟 6） 的資訊。

雖然這個程序可能有些複雜，它實際上是生活簡單了的應用程式的建立者。 而非處理各種不同的權杖包含不同的資訊，應用程式可以接受仍然收到的單一權杖熟悉的資訊時發出多個身分識別提供者的身分識別。 此外，而不需要設定為信任各種 IdPs 每個應用程式，這些信任關係會改為維持存取控制由-應用程式需要只允許存取]。

則值得不瞭解存取控制連結至 Windows，-也可能會用來接受只 Google 或 facebook 的連絡人的身分識別的 Linux 應用程式。 然後，即使存取控制的 Azure Active Directory 家人的組件，您可以將它視為未完全不同的服務，從什麼上一節所述。 雖然這兩個技術使用身分識別，請他們的問題完全不同 （雖然 Microsoft 具有說它預期整合有些時候兩個）。

與身分識別的工作是很重要的幾乎所有應用程式。 存取控制的目標是，使其更輕鬆地建立接受從各種不同的身分識別提供者的身分識別的應用程式開發人員。 將這項服務放在雲端，Microsoft 已提供其執行任何平台上的任何應用程式。

##<a name="about-the-author"></a>關於作者

David Chappell 是主要 Chappell 與同事[www.davidchappell.com](http://www.davidchappell.com)舊金山機場，加州。
