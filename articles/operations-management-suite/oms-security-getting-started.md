<properties
   pageTitle="開始使用作業管理套件安全性與稽核方案 |Microsoft Azure"
   description="這份文件可協助您開始使用作業管理套件安全性與稽核解決方案功能來監控您的混合式雲端。"
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="get-started-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>
 
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>開始使用作業管理套件安全性與稽核解決方案
這份文件可協助您快速開始使用作業管理套件 (OMS) 的安全性和稽核解決方案功能來引導您瞭解每個選項。

## <a name="what-is-oms"></a>什麼是 OMS？
Microsoft 作業管理套件 (OMS) 是 Microsoft 的雲端 IT 管理解決方案，可協助您管理及保護您的內部部署與雲端基礎結構。 如需有關 OMS 的詳細資訊，請閱讀文章[作業管理套件](https://technet.microsoft.com/library/mt484091.aspx)。

## <a name="oms-security-and-audit-dashboard"></a>OMS 安全性和稽核儀表板

OMS 安全性和稽核方案可讓您全面涵蓋所有內容檢視貴組織的 IT 安全性狀態主要問題需要您處理的內建搜尋查詢。 [**安全性與稽核**儀表板是主畫面的安全性 OMS 中相關的所有項目。 它會提供您電腦的安全性狀態的高層級深入。 它也包含可檢視過去 24 小時 7 天，從所有事件或任何其他自訂的時間範圍。 若要存取的**安全性和稽核**儀表板，請遵循下列步驟︰

1. 在 [ **Microsoft 作業管理套件**主要儀表板的左側的 [**設定**] 方塊。
2. 在**設定**刀，**解決方案**下的 [**安全性和稽核**選項。
3. 將會出現 [**安全性與稽核**儀表板︰

    ![OMS 安全性和稽核儀表板](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

如果您第一次存取此儀表板，而且您沒有監視 OMS 的裝置，並排也不會填入取得代理程式的資料。 一旦您安裝代理程式時，可能需要一些時間以填入，因此您會看到一開始可能會遺失某些資料時仍可上傳至雲端]。  在此情況下，則標準看到某些並排沒有具體的資訊。 如需有關如何使用 Windows 系統和[OMS 連線 Linux 電腦](https://technet.microsoft.com/library/mt622052.aspx)安裝 OMS 代理程式，如需有關如何執行此工作 Linux 系統中閱讀[連線的 Windows 電腦直接 OMS](https://technet.microsoft.com/library/mt484108.aspx) 。

> [AZURE.NOTE] 代理程式會收集根據目前事件的已啟用，例如電腦名稱，IP 位址與使用者名稱的資訊。 不過收集任何文件/檔案、 資料庫名稱或私人資料。   

解決方案的地址主要客戶的挑戰的邏輯，視覺效果和資料擷取規則集合。 安全性和稽核是一種解決方案，其他人可以個別新增。 閱讀文件[新增解決方案](https://technet.microsoft.com/library/mt674635.aspx)，如需有關如何新增新的方案。

四個主要的搜尋類別被組織的 OMS 安全性和稽核儀表板︰

- **安全性網域**︰ 在這個區域，您將能夠進一步瀏覽一段時間的安全性記錄，存取惡意程式碼評估、 更新評估、 網路安全性、 身分識別與存取權的資訊、 安全性事件的電腦並快速有權存取 Azure 資訊安全中心儀表板。
- **主要問題**︰ 此選項可讓您快速識別數作用中的問題] 及 [這些問題的嚴重性。
- **偵測 （預覽版本）**︰ 可讓您以視覺化的安全性警告時，針對您的資源進行識別攻擊模式。
- **威脅智慧**︰ 可讓您以視覺化的輸出惡意 IP 傳輸、 惡意威脅類型與顯示這些 IPs 所在的地圖的伺服器總數識別攻擊模式。 
- **一般安全性查詢**︰ 這個選項可讓您最常用的安全性查詢，您可用來監控您的環境的清單。 當您按一下其中一個這些查詢時，它會**搜尋**刀開啟該查詢的結果。

> [AZURE.NOTE] 如需有關如何 OMS 會保留您的資料安全的詳細資訊，請參閱如何 OMS 保護您的資料。

## <a name="security-domains"></a>安全性網域

監控資源時還能快速存取您的環境的目前狀態。 不過也很重要能夠追蹤後，可能會導致解有什麼新鮮事特定點環境中時間的過去的事件。 

> [AZURE.NOTE] 資料保留依據 OMS 價格計劃。 如需詳細資訊，請造訪[Microsoft 作業管理套件](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx)價格頁面。

事件的回應和鑑識調查分析藍本直接助益可用的**一段時間的安全性記錄**磚中的結果。

![一段時間的安全性記錄](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

當您按一下此圖磚上時，**搜尋**刀會開啟，顯示查詢結果的**安全性事件**(類型 = SecurityEvents) 資料根據過去七天，如下所示︰

![一段時間的安全性記錄](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

搜尋結果分成兩個窗格︰ 左的窗格可讓您找不到中, 這些事件找到，發現這些電腦中的帳戶數] 及 [的活動類型的電腦的安全性事件的數字的分析。 右窗格可讓您結果總計] 及 [依時間排列檢視的使用電腦的名稱和事件活動的安全性事件。 您也可以按一下**顯示更多**] 以檢視更多關於此事件，例如事件資料、 事件識別碼和事件的來源的詳細資料。

> [AZURE.NOTE] 如需有關 OMS 搜尋查詢的詳細資訊，請閱讀[OMS 搜尋參考](https://technet.microsoft.com/library/mt450427.aspx)。

### <a name="antimalware-assessment"></a>反惡意程式碼評估

這個選項可讓您快速識別沒有足夠的保護與都遭到惡意程式碼的電腦的電腦。 惡意程式碼評估狀態和監視伺服器上偵測到的威脅閱讀、，然後傳送資料至雲端的 OMS 服務進行處理。 使用伺服器偵測到威脅與伺服器沒有足夠的保護會顯示在惡意程式碼評估儀表板，按下 [**反惡意程式碼評估**] 方塊中可供存取。 

![惡意程式碼評估](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

就像任何其他動態磚用於 OMS 儀表板，當您按一下它，**搜尋**刀隨即會開啟查詢結果。 這個選項，如果您在**保護狀態**] 下的 [**不報告**] 選項中按一下您就必須查詢結果會顯示包含電腦的名稱和其排名]，此單一項目，如下所示︰

![搜尋結果](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [AZURE.NOTE] *排名*是以反映的保護狀態提供的等級 （上，關閉，更新，等） 及威脅所找到的。 有的為進行彙總的數字可協助。

如果您按一下電腦的名稱，則必須依時間排列的保護狀態，此電腦的檢視。 這是非常實用的案例中您要了解是否反惡意程式碼一次安裝及已移除某些點。   

### <a name="update-assessment"></a>更新評估 

這個選項可讓您快速判斷潛在安全性問題的整體風險和是否或如何重大更新您的環境。 OMS 安全性和稽核方案僅提供這些更新的視覺效果，實際資料是來自[系統更新解決方案](https://technet.microsoft.com/library/mt484096.aspx)，也就是 OMS 中不同的模組。 以下範例的更新︰

![系統更新](./media/oms-security-getting-started/oms-getting-started-fig6.png)

> [AZURE.NOTE] 如需有關更新的解決方案的詳細資訊，請閱讀[更新系統更新解決方案的伺服器](https://technet.microsoft.com/library/mt484096.aspx)。

### <a name="identity-and-access"></a>身分識別與存取權

身分識別應該控制項平面為您的企業，保護您的身分識別應該您上方的優先順序。 當過去沒有組織周圍的周邊並且這些周邊其中一個主要防衛邊界時，最近更多資料與移動至雲端的其他應用程式身分識別就成為新的外圍。 

> [AZURE.NOTE] 目前資料僅根據在未來的 office 365 登入的安全性事件登入資料 （事件識別碼 4624），而且會也包含 Azure AD 資料。

監控您的身分識別的活動，您可以做預防措施之前事件位置或若要停止嘗試反應動作。 **身分識別與存取權**的儀表板可讓您概略瞭解您的身分識別的狀態，包括登入失敗的次數，這些嘗試鎖定的帳戶、 有帳戶時所使用的使用者帳戶變更或重設密碼和目前的登入的帳戶的數字。 

當您按一下 [**身分識別與存取權**] 方塊中，您會看到下列儀表板︰

![身分識別與存取權](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

此儀表板中可用的資訊立即可協助您識別潛在可疑的活動。 例如，有 338 嘗試以**系統管理員**和 100%的這些嘗試登入失敗。 這可能因暴力針對此帳戶。 如果您按一下 [在此帳戶，您會取得可協助您判斷目標資源的潛在攻擊的詳細資訊︰

![搜尋結果](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

詳細的報表提供此事件的重要資訊包括︰ 在目標電腦、 登入 （在此案例的網路登入）、 （在此案例事件 4625） 的活動和全面涵蓋所有內容的每一次嘗試時間表的類型。 

### <a name="computers"></a>電腦

此方塊可用來存取所有的積極安全性事件的電腦。 當您按一下 [在這個並排顯示您會看到安全性活動和事件的數目的電腦的清單，在每一部電腦上︰

![電腦](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

您可以繼續調查每一部電腦上的 [，並檢閱已標幟的安全性事件。

### <a name="azure-security-center"></a>Azure 資訊安全中心

這個並排顯示基本上是用來存取 Azure 資訊安全中心儀表板的快速鍵。 如需此方案的相關資訊，請閱讀[快速入門 Azure 資訊安全中心](../security-center/security-center-get-started.md)。

## <a name="notable-issues"></a>主要問題

此群組的選項的主要目的是提供給您可以在您的環境中，在要徑、 警告和資訊分類這些問題的快速檢視。 作用中的問題類型方塊是視覺效果，這些問題，但它不允許您探索更多詳細資料，您需要使用具有問題 （名稱） 的名稱、 多少物件有這項工作 （計數） 及如何要徑為 （嚴重性） 這個並排顯示的下半部。

![主要問題](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

您可以看到這些問題已涵蓋在不同區域的強調的此檢視的 [**安全的網域**] 群組中︰ 以視覺化方式呈現您的環境，從單一位置中最重要的問題。

## <a name="detections-preview"></a>偵測 （預覽版本）

這個選項的主要目的是讓 IT 來快速找出潛在威脅透過環境及這個潛在威脅的嚴重性。

![威脅 Intel](./media/oms-security-getting-started/oms-getting-started-fig12.png)

這個選項，也可應計調查進行評估並取得關於攻擊的詳細資訊。

> [AZURE.NOTE] 如需有關如何使用 OMS 回應事件的詳細資訊，請觀看[如何運用 Azure 資訊安全中心與 Microsoft 作業管理套件事件回應](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703)。

## <a name="threat-intelligence"></a>威脅智慧

新的潛在威脅智慧一節的安全性和稽核解決方案文章數種方式可能的攻擊模式︰ 伺服器惡意的輸出 IP 流量、 惡意威脅類型與顯示這些 IPs 所在的地圖的總數。 您可以地圖與之互動，然後按一下 IPs 如需詳細資訊。

在地圖上的黃色圖釘表示惡意 IPs 的連入流量。 不應會以查看內送的惡意流量，網際網路上公開的伺服器，但我們建議您檢閱這些嘗試以確定都不成功。 這些標記根據 IIS 記錄，WireData 和 Windows 防火牆記錄。  

![威脅 Intel](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>一般安全性查詢

一般安全性查詢使用的清單可讓您快速存取 [資源資訊，並根據您的環境需求加以自訂。 這些常見的查詢為︰

- 所有的安全性活動
- 「 Computer01.contoso.com 」 （取代為您自己的電腦名稱） 的電腦上的安全性活動
- 「 Computer01.contoso.com 「 帳戶 「 系統管理員 」 （取代為您自己的電腦與帳戶的名稱） 的電腦上的安全性活動
- 登入電腦的活動
- 結束任何電腦上的 Microsoft 反惡意程式碼的帳戶
- 終止 Microsoft 反惡意程式碼處理程序的電腦
- 「 Hash.exe 」 所在的電腦執行 （以不同的程序名稱取代）
- 所有所執行的程序名稱
- 登入帳戶活動
- 「 Computer01.contoso.com 」 （取代為您自己的電腦名稱） 的電腦上從遠端登帳戶

## <a name="see-also"></a>另請參閱

在此文件中，您已簡介 OMS 安全性和稽核的解決方案。 若要進一步瞭解 OMS 安全性，請參閱下列文章︰

- [作業管理套件 (OMS) 概觀](operations-management-suite-overview.md)
- [監控和回應作業管理套件安全性和稽核方案中的安全性警告](oms-security-responding-alerts.md)
- [監視作業管理套件安全性和稽核方案中的資源](oms-security-monitoring-resources.md)
