# <a name="compute"></a>計算

Azure 可讓您部署，並監控執行 Microsoft 資料中心內的應用程式碼。 當您建立的應用程式和 Azure 上執行，[程式碼] 和 [組態分成一組稱為 Azure 裝載服務。 裝載的服務很容易管理、 不按比例縮放向上和向下、 重新設定，及更新與您的應用程式碼的新版本。 本文著重於裝載於 Azure 服務應用程式模型。<a id="compare" name="compare"></a>

## 目錄<a id="_GoBack" name="_GoBack"></a>

-   [Azure 應用程式模型優點][]
-   [代管的服務的核心概念][]
-   [代管的服務設計考量][]
-   [設計您的應用程式的小數位數][]
-   [代管的服務定義和設定][]
-   [服務定義檔案][]
-   [服務設定檔][]
-   [建立及部署裝載的服務][]
-   [參照][]

## <a id="benefits"> </a>Azure 的應用程式模型優點

當您部署的代管服務應用程式時，Azure 建立一或多個虛擬機器 (Vm) 包含您的應用程式碼，並啟動 Vm 實體位於其中一個 Azure 資料中心的電腦上。 主控應用程式的用戶端要求輸入的資料中心，負載平衡器分配 Vm 平均這些要求。 雖然 Azure 中裝載您的應用程式，它會取得三個主要優點︰

-   **高的可用性。** 可用性表示 Azure 可確保您的應用程式正在執行最大的和回應的用戶端要求。 如果您的應用程式結束 （因為處理的例外狀況，例如），然後 Azure 會偵測，並將會自動重新啟動應用程式。 如果電腦應用程式上正在執行體驗某種硬體失敗，然後 Azure 將也偵測此和自動另一個工作實體電腦上建立新的 VM 並從該處執行程式碼。 附註︰ 若要取得 Microsoft 服務等級協定 99.95%可用的應用程式的順序，您必須至少為兩個 Vm 執行應用程式碼。 這個選項可讓一個 VM 時 Azure 將您的程式碼從失敗 VM 移到新的好 VM 處理用戶端的要求。

-   **延展性。** Azure 可讓您輕鬆地與動態變更的 Vm 執行應用程式碼控點放在您的應用程式上的實際載入數。 這個選項可讓您以調整您的應用程式在其放置您的客戶，會將它時僅適用於您需要在需要時 Vm 支付的工作量。 當您想要變更 Vm 數時，Azure 回應讓動態變更的 Vm 視經常執行數分鐘內。

-   **管理能力。** Azure 提供的服務 (PaaS) 是一種平台，因為它會管理基礎結構 （硬體本身、 電源，與網路），才能繼續執行這些機器。 Azure 也會管理平台，請確保所有正確的更新與安全性更新，以及元件更新，例如.NET Framework 和網際網路資訊伺服器最新版本的作業系統。 所有 Vm 正在都執行 Windows Server 2008，因為 Azure 提供其他功能，例如診斷監控、 遠端桌面支援、 防火牆和憑證存放區 」 設定。 在沒有提供這些功能額外成本。 事實上，當您執行您的應用程式中 Azure 時，會包含的 Windows Server 2008 作業系統 (OS) 的授權。 所有 Vm 正在執行 Windows Server 2008，因為在 Azure 中執行時，可正常運作執行 Windows Server 2008 的任何程式碼。

## <a id="concepts"></a>代管服務的核心概念

Azure 中的代管服務為部署您的應用程式時，執行為一或多個*角色。* *角色*只是指的是應用程式檔案與設定。 您可以定義一個或多個角色應用程式，每一組自己的應用程式檔案與設定的。 在應用程式中的每一個角色，您可以指定數 Vm 或*角色執行個體*，若要執行。 下圖顯示兩個簡單範例化為使用角色與角色執行個體的代管服務應用程式。

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>圖 1︰ 單一角色與三個執行個體 (Vm) 執行在 Azure 資料中心

![圖像][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>圖 2︰ 兩個角色，各有兩個執行個體 (Vm，) 執行在 Azure 資料中心

![圖像][1]

角色執行個體通常是 [處理程序網際網路用戶端要求輸入資料中心，透過要求*輸入的結束點*。 單一角色可以有 0 或其他輸入結束點。 每個端點指出通訊協定 （HTTP、 HTTPS 或 TCP） 和連接埠。 一般設定有兩個輸入的端點角色︰ HTTP 傾聽連接埠 80 和接聽連接埠 443 的 HTTPS。 下圖顯示兩個不同的角色的範例使用不同的導向用戶端要求輸入結束點。

![圖像][2]

當您建立的代管的服務 Azure 中時，將它指派公開定位的 IP 位址的用戶端可以使用存取。 在建立裝載的服務，您也必須選取 URL 首碼對應至該 IP 位址。 當您基本上保留*前置詞*，這個字首必須是唯一。 cloudapp.net URL，因此沒有任何人都可以將它。 使用 URL，與您的角色執行個體通訊用戶端。 通常您會無法發佈或發佈 Azure*首碼*。 cloudapp.net URL。 不過，您會從您的 DNS 註冊機構所選擇的購買 DNS 名稱，並設定您的 DNS 名稱，即可將用戶端要求 Azure url 重新導向。 如需詳細資訊，請參閱[設定 Azure 中的自訂網域名稱][]。

## <a id="considerations"></a>代管服務設計考量

在雲端環境中執行應用程式時，有數個的考量，例如延遲、 高可用性和延展性考量下列事項。

決定要找出您的應用程式碼，就必須考慮 Azure 中執行的代管的服務。 是常見應用程式部署至最接近您的用戶端，以減少延遲，並獲得最佳效能可能的資料中心。
不過，您可選擇的資料中心，深入瞭解您的公司或深入瞭解您的資料，如果您有 jurisdictional 或法律問題的相關資料和所在的位置。 有可以裝載您的應用程式碼的全球的六個資料中心。 下表顯示可用的位置︰

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**國家/地區**

</td>
<td style="width: 200px;">
**子區域**

</td>
</tr>
<tr>
<td>
美國

</td>
<td>
南美洲 Central 及北美 Central

</td>
</tr>
<tr>
<td>
在 Europe

</td>
<td>
北美與西部

</td>
</tr>
<tr>
<td>
亞洲

</td>
<td>
Southeast 與東亞

</td>
</tr>
</tbody>
</table>
建立裝載的服務，便會選取子地區，指出您想要執行的程式碼的位置。

若要達到可用性能與延展性，是非常重要的應用程式的資料會保留在中央存放庫存取多個角色執行個體。 為了協助這，Azure 會提供多個儲存選項，例如二進位大型物件、 表格和 SQL 資料庫。 請參閱[Azure 中的資料儲存空間方案][]的這些儲存技術的詳細資訊。 下圖顯示如何負載平衡器 Azure 資料中心內分散到不同的角色均有相同的資料儲存區權存取的執行個體的用戶端要求。

![圖像][3]

通常，您想要應用程式碼，您的資料尋找相同的資料中心，因為這可讓低的延遲 （較佳的效能） 應用程式碼時存取資料。 此外，您不會頻寬時相同的資料中心內移動資料。

## <a id="scale"></a>設計您的應用程式的小數位數

有時候，您可能要花單一應用程式 （例如簡單的網站），並讓其裝載於 Azure。 但通常，您的應用程式可能包含數個角色所有共同作業。 例如，在下圖中，有網站角色的兩個執行個體，三個執行個體的順序處理角色、 報表產生器角色的執行個體之一。 這些角色所有合作的所有人的程式碼可以和一起封裝作為單一單位最 Azure 部署。

![圖像][4]

應用程式分割成不同的角色，每組自己的角色執行個體 (也就是 Vm) 上執行的主要原因是獨立不按比例縮放的角色。 例如，期間佳節，許多客戶可能會購買產品您公司，因此您可能要增加執行您網站的角色，以及執行您的順序處理角色的角色執行個體數目的角色執行個體數目。 佳節之後，您可能會收到產品傳回，許多，因此您可能仍需要許多網站執行個體，但較少的順序處理執行個體進行。 在一年中的其餘部分中，您可能只需要幾個網站和順序處理的執行個體。 整份，您可能需要只有一個報表產生器執行個體。 Azure 中的角色型部署的彈性，可讓您輕鬆地讓您的應用程式至您的業務需求。

通常會將您的代管服務中的執行個體彼此的角色。 例如，網站角色接受客戶的訂單，但它會然後卸載處理順序處理角色執行個體的順序。 將一組執行個體的另一組的角色執行個體使用提供的 Azure 佇列技術的工作表單的最佳方式的佇列中服務或服務匯流排佇列。 使用佇列中的要徑屬於故事。 佇列中可讓裝載的服務，來調整其大小可讓您平衡成本針對工作負載的角色。 如果在佇列中的訊息的數目會增加一段時間，您可以調整順序處理角色執行個體數目。 如果在佇列中的訊息的數目會減少一段時間，您可以調整下順序處理角色執行個體數目。 如此一來，您會只支付處理的實際工作負載所需的執行個體。

佇列中也會提供可靠性。 當規模順序處理角色執行個體數目，Azure 會決定要終止的例項。 它可能會決定終止正在處理佇列中郵件的執行個體。 不過，因為郵件處理程序沒有順利完成時，郵件就會看到一次到另一個順序處理角色例項，挑選與程序。 由於佇列中郵件的可見度，處理最後保證訊息。 佇列中也做為負載平衡器以有效發送它從該要求訊息的任何及所有的角色執行個體的訊息。

針對網站角色執行個體，您可以監視到他們的流量並決定要不按比例縮放數向上或向下以及。 佇列中可讓您不按比例縮放的網站角色執行個體，亦順序處理角色執行個體數目。 這是很強大，並讓您很大的彈性。 當然，如果您的應用程式包含其他的角色，您可以新增其他佇列為項目才運用相同的縮放比例與成本優點它們之間的通訊導管。

## <a id="defandcfg"></a>代管服務定義和設定

部署 Azure 裝載的服務要求您同時使用的服務定義檔案和服務設定檔。 這些檔案都 XML 檔案，並且讓您以宣告方式指定裝載的服務的部署選項。 服務定義檔案說明所有您裝載的服務，和如何通訊的角色。 服務設定檔將說明每個角色及設定用來設定各角色執行個體的執行個體數目。

## <a id="def"></a>服務定義檔案

提到前面，服務定義 (CSDEF) 檔案是說明應用程式完成的各種角色的 XML 檔案。 完整的結構描述 XML 檔案可以在這裡找到: [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx] []。
CSDEF 檔案包含您要在應用程式中的每一個角色 WebRole 或 WorkerRole 項目。 部署角色為 web 角色 （使用 WebRole 項目） 表示的程式碼會執行角色執行個體包含 Windows Server 2008 及網際網路資訊伺服器 (IIS)。
部署角色為工作者角色 （使用 WorkerRole 項目） 表示的角色執行個體，就可以在其上的 Windows Server 2008 （IIS 將不會安裝）。

當然可以建立及部署使用其他機制網站要求接聽工作者角色 （例如，您的程式碼無法建立及使用.NET HttpListener）。 您的程式碼之後角色執行個體正在執行 Windows Server 2008，可以執行任何作業，通常可在 Windows Server 上執行應用程式
2008.

每一個角色，您可以指定所要的虛擬記憶體大小，應該使用該角色的執行個體。 下表顯示目前可用的各種 VM 大小和每個屬性︰

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**虛擬記憶體大小**

</td>
<td>
**CPU**

</td>
<td>
**大的 RAM**

</td>
<td>
**磁碟**

</td>
<td>
**最大使用量網路 I/O**

</td>
</tr>
<tr align="left" valign="top">
<td>
**額外的小型企業**

</td>
<td>
1 x 1.0 GHz

</td>
<td>
768 MB

</td>
<td>
20 GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**小型**

</td>
<td>
1 x 1.6 GHz

</td>
<td>
1.75 GB

</td>
<td>
225 GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**媒體**

</td>
<td>
2 x 1.6 GHz

</td>
<td>
3.5 GB

</td>
<td>
490 GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**大型**

</td>
<td>
4 x 1.6 GHz

</td>
<td>
7 GB

</td>
<td>
1 TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**超大型**

</td>
<td>
8 x 1.6 GHz

</td>
<td>
14 GB

</td>
<td>
2 TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
您會每小時的每個 VM 作為角色執行個體，而且您也會任何資料的外部資料中心，傳送您的角色執行個體。 您不會輸入資料中心的資料。 如需詳細資訊，請參閱 [Azure 價格] 的 []。 一般而言，建議使用許多小角色執行個體，而不是幾個大型的執行個體，讓您的應用程式會更具彈性失敗。 您有較少的角色執行個體，更慘失敗的其中一個是您的整體應用程式。 此外，如之前所述，您就必須若要取得 Microsoft 提供 99.95%服務等級協定部署每一個角色至少兩個執行個體。

服務定義 (CSDEF) 檔案也是您在應用程式中指定多個屬性，每一個角色的相關的地方。 以下是一些更好用的項目您可以使用︰

-   **憑證**。 您可以使用憑證來加密資料，或如果您的 web 服務支援 SSL。 上傳至 Azure 需要任何認證。 如需詳細資訊，請參閱[Azure 中的 [管理憑證][]。 此 XML 設定的角色執行個體的憑證存放區安裝先前上傳的憑證，使其可由您的應用程式碼。

-   **設定設定名稱**。 對於您想要您閱讀角色執行個體上執行時的應用程式的值。 在可以隨時更新，而不需要重新部署您的程式碼服務設定 (CSCFG) 檔案中設定的設定值的實際值。 事實上，您可以撰寫您的應用程式，而不會任何停機時間偵測已變更的設定值的方式。

-   **輸入結束點**。 您在這裡指定任何 HTTP、 HTTPS 或 TCP 端點 （含連接埠） 您想要透過您*的前置詞*外面的世界公開。 cloadapp.net URL。 當 Azure 部署您的角色時，它會角色執行個體上自動設定防火牆。

-   **內部結束點**。 您在這裡指定任何 HTTP 或 TCP 端點您想要的其他應用程式組件與部署的角色執行個體上公開。 內部端點允許彼此交談的應用程式中的所有角色執行個體，但無法存取您的應用程式外的任何角色例項。

-   **匯入模組**。 這些您也可以很有用的元件上安裝角色執行個體。 元件存在診斷監控遠端桌面和 Azure 連線 （可讓您的角色執行個體，透過安全通道來存取內部部署資源）。

-   **本機的儲存空間**。 這會配置子目錄中您要使用的應用程式的角色執行個體。 更多詳細資料，請參閱[Azure 中的資料儲存空間方案][]中描述。

-   **啟動工作**。 啟動工作可讓您為其開機時，請在 [角色執行個體上安裝必要元件。 提高權限，如有必要，以系統管理員身分執行工作。

## <a id="cfg"></a>服務設定檔

服務設定 (CSCFG) 檔案是 XML 檔案，告訴您可以變更，而不必重新部署您的應用程式的設定。 完整的結構描述 XML 檔案可以在這裡找到︰ [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][]。
CSCFG 檔案包含您的應用程式中的每一個角色的角色項目。 以下是一些您可以指定 CSCFG 檔案中的項目︰

-   **作業系統版本**。 這個屬性可讓您選取您想要使用的所有執行應用程式碼的角色執行個體的作業系統 (OS) 版本。 此 OS 稱為*來賓 OS*中，以及每個新的版本的最新的安全性更新和發行來賓 OS 次可用的更新。 如果您將 osVersion 屬性值為 「\*」，然後 Azure 就會自動更新虛擬 OS 上每一個角色執行個體，當可以使用來賓 OS 的新版本。 不過，您可以退出自動更新選取特定的來賓作業系統版本。 例如，設定 osVersion 屬性設定值的 」 「 台北 」 來賓-OS 2.8\_201109-01 」 會以取得此網頁描述所有角色執行個體︰ [http://msdn.microsoft.com/library/hh560567.aspx][]。 如需有關來賓 OS 版本的詳細資訊，請參閱[管理升級 Azure 來賓 os]。

-   **執行個體**。 這個項目值會指出您想要能夠執行特定角色的程式碼的角色執行個體數目。 您可以 （而不用重新部署您的應用程式），新的 CSCFG 檔案上傳至 Azure，因為它是很簡單，若要變更之項目的值，然後上傳新 CSCFG 檔案，以動態方式增加或減少執行應用程式碼的角色執行個體數目。 這個選項可讓您輕鬆地調整您的應用程式，向上或向下符合實際的工作量需求，同時也控制您多少會執行角色執行個體。

-   **設定設定值**。 這個項目會指出設定值 （如 CSDEF 檔案中所定義）。 在執行中時，您的角色可以瞭解這些值。 這些設定的設定值通常會用於連接字串 SQL 資料庫或 Azure 儲存空間，但可以用於任何您想要的用途。

## <a id="hostedservices"></a>建立及部署裝載的服務

建立的代管的服務需要您第一次移至[Azure 管理入口網站]提供的代管的服務指定 DNS 前置詞和資料中心您最後想要執行程式碼。 在您的開發環境，然後您可以建立您的服務定義 (CSDEF) 檔案，建立您的應用程式碼和所有這些檔案的套件 (zip) 到服務套件 (CSPKG) 檔案。 您也必須準備服務設定 (CSCFG) 檔案。 若要部署您的角色，您上傳的 CSPKG 和 CSCFG 檔案和 Azure 服務管理 API。 部署，Azure 會佈建角色執行個體 （根據設定資料） 資料中心，從套件中解壓縮應用程式碼、 將其複製到 [角色執行個體，並啟動執行個體。 現在，您的程式碼是和使用。

下圖顯示您在開發電腦建立的 CSPKG 和 CSCFG 檔案。 CSPKG 檔案包含 CSDEF 檔案及兩個角色的程式碼。 上傳的 CSPKG 和 CSCFG 檔案和 Azure 服務管理 API 之後, Azure 會建立資料中心角色執行個體。 在此範例中，指定 Azure 應該建立三個執行個體的角色 CSCFG 檔案\#角色的 1 到兩個執行個體\#2。

![圖像][5]

如需有關部署的詳細資訊，請升級，以及重新設定您的角色，請參閱的[部署及更新 Azure 應用程式][]的文件。<a id="Ref" name="Ref"></a>

## <a id="references"></a>參照

-   [建立的 Azure 裝載的服務][]

-   [Azure 中管理裝載的服務][]

-   [Azure 移轉應用程式][]

-   [設定 Azure 的應用程式][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>撰寫 Jeffrey Richter (Wintellect)</p>

</div>

  [Azure 應用程式模型優點]: #benefits
  [代管的服務的核心概念]: #concepts
  [代管的服務設計考量]: #considerations
  [設計您的應用程式的小數位數]: #scale
  [代管的服務定義和設定]: #defandcfg
  [服務定義檔案]: #def
  [服務設定檔]: #cfg
  [建立及部署裝載的服務]: #hostedservices
  [參照]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Azure 中設定自訂網域名稱]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Azure 中的資料儲存空間方案]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
  [管理 Azure 中的憑證]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [管理 Azure 來賓的身分 OS 升級]: http://msdn.microsoft.com/library/ee924680.aspx
  [Azure 管理入口網站]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [部署及更新 Azure 的應用程式]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [建立的 Azure 裝載的服務]: http://msdn.microsoft.com/library/gg432967.aspx
  [Azure 中管理裝載的服務]: http://msdn.microsoft.com/library/gg433038.aspx
  [Azure 移轉應用程式]: http://msdn.microsoft.com/library/gg186051.aspx
  [設定 Azure 應用程式]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
