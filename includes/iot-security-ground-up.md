# <a name="internet-of-things-security-from-the-ground-up"></a>設定為基礎而事項網際網路安全性

網際網路 」 的 (IoT) 事項具有唯一的安全性、 隱私權和規範挑戰至全世界的企業版。 與傳統網路技術其中這些問題為中心軟體及如何實作不同 IoT 涉及電腦及實體世界收斂時會發生什麼情況。 保護 IoT 解決方案必須先確保安全佈建的裝置，這些裝置與雲端，與期間處理與儲存在雲端的安全的資料保護之間的安全連線。 使用對等功能，不過，且資源限制裝置、 部署、 的地理位置分佈大量的方案中的裝置。

本文探討如何 Microsoft Azure IoT 套件提供安全與私人事項網際網路雲端解決方案。 Azure IoT 套件提供完整的端對端解決方案，與安全性設定為基礎而建置到每個階段。 在 Microsoft 開發安全軟體屬於軟體工程作法是根目錄中我們數十年長體驗的開發安全的軟體。 若要確保這，安全性開發生命週期 (SDL) 為基礎的開發方法、 搭配操作安全性保證 (OSA) 和 Microsoft 數位犯罪單位、 Microsoft 資訊安全回應中心，與 Microsoft 惡意程式碼防護中心等基礎結構層級安全性服務的主機。 

Azure IoT 套件提供獨特功能哪些進行佈建、 連線至，並儲存的資料從 IoT 裝置更容易且透明並充分運用 all、 安全。 本白皮書中我們檢查 Azure IoT 套件安全性功能，以確保安全性、 隱私權和規範挑戰部署策略會處理。 

## <a name="introduction"></a>簡介

網際網路 」 的 (IoT) 事項是提供企業立即的未來及降低營運成本、 增加 revenue、 並轉換為其商務真實世界機會波浪。 不過，許多企業是基於有關安全性、 隱私權和符合性考量其組織中部署 IoT 排斥。 考量的主要點來自 IoT 基礎結構，合併電腦及實體世界一起，複合固有下列兩個世界中的個別風險唯一性。 確保完整性的裝置上執行的程式碼屬於 IoT 的安全性提供裝置和使用者驗證、 defining clear 擁有權的裝置 （，以及這些裝置所產生的資料），以及恢復電腦及實體的攻擊。 

然後，有隱私權的問題。 公司想與相關資料集合中收集的項目以及原因、 透明度可以看到該、 存取等等的控制項。 最後，有搭配運作，人員設備的一般安全性問題以及維護產業標準的符合性的問題。

授與安全性、 隱私權、 透明度及符合性考量，選擇右 IoT 解決方案提供者會維持挑戰。 一起訂 IoT 軟體和各種不同的廠商所提供的服務的個別部分介紹在安全性、 隱私權、 透明度及規範這可能是難以偵測、 更遑修正的間距。 右 IoT 軟體和服務提供者根據尋找有大規模經驗執行服務可橫跨類別和地理位置，但也可適應安全且透明功能提供者的選擇。 同樣地，它可協助具有數十年的經驗開發十億做的全球性、 機器上執行的安全軟體且能夠欣賞網際網路的事項這個新 world 所造成的威脅橫向所選的提供者。

## <a name="secure-infrastructure-from-the-ground-up"></a>設定為基礎而安全基礎結構 

[Microsoft Cloud](https://www.microsoft.com/enterprise/microsoftcloud/default.aspx#fbid=WzBsRQi6aGk)基礎結構支援多個十億客戶在 127 的國家/地區。 繪圖上建置企業軟體和世界中執行的最大的線上服務的部分我們數十年 long 經驗，我們提供更高層級增強的安全性、 隱私權、 規範、 及威脅減輕作法比大部分客戶可以自行達成。

[安全性開發生命週期 (SDL)](https://www.microsoft.com/sdl/)我們提供內嵌到整個軟體生命週期的安全性需求的必要全公司的開發程序。 為了協助確保作業活動遵循相同的層級的安全性作法，我們會使用我們操作安全性保證 (OSA) 程序中配置的嚴格的安全性指導方針。 我們也會使用第三方稽核公司的進行中的驗證我們符合我們規範責任，與我們連絡中廣泛的安全性努力透過建立卓越，包括 Microsoft 數位犯罪單位、 Microsoft 資訊安全回應中心，以及 Microsoft 惡意程式碼防護中心的中央。

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure-貴公司的安全 IoT 基礎結構

Microsoft Azure 提供完整的雲端解決方案，結合正比收藏的整合式的雲端服務的其中一個 — 分析、 機器學習、 儲存、 安全性、 網路、 及 web-與來保護和資料的隱私權業界領導承諾。 我們[假設破壞](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/)策略使用專用"紅色小組 」 的人員模擬攻擊、 測試功能來偵測 Azure、 針對新威脅保護和復原缺口軟體安全性專家提供。 「[全域應計](https://www.microsoft.com/TrustCenter/Security/DesignOpSecurity)小組的運作方式繞著時鐘以減輕攻擊和惡意活動的效果。 小組遵循事件管理、 通訊、 與復原的建立程序，並與內部和外部合作夥伴使用可供搜尋且可預測的介面。

我們系統提供持續的入侵偵測防護、 服務攻擊防護、 一般滲透、 測試及鑑工具可協助找出並降低威脅。 [多重要素驗證](../articles/multi-factor-authentication/multi-factor-authentication.md)提供額外一層的使用者存取網路安全性。 及應用程式與主機提供者，我們提供存取控制、 監視、 反惡意程式碼、 弱點掃描、 修補程式及設定管理。

Microsoft Azure IoT 套件運用的安全性與隱私權內建於安全開發及操作的所有 Microsoft software 我們 SDL] 及 [OSA 程序的 Azure 平台。 這些程序提供基礎結構保護、 網路保護及身分識別和一般管理功能的任何解決方案安全性的基礎。 

[Azure IoT 中樞](../articles/iot-hub/iot-hub-what-is-iot-hub.md)內[IoT 套件](../articles/iot-suite/iot-suite-what-is-azure-iot.md)提供使用每個裝置安全性認證及存取控制來啟用可靠且安全 IoT 裝置與 Azure 服務例如[Azure 機器學習](../articles/machine-learning/machine-learning-what-is-machine-learning.md)與[Azure 資料流分析](../articles/stream-analytics/stream-analytics-introduction.md)之間的雙向通訊完全受管理服務。  

最佳通訊的安全性和隱私權功能建 Azure IoT 套件，我們已細分成三個主要的安全性區域套件。 

![Azure IoT 套件](media/iot-security-ground-up/securing-iot-ground-up-fig2.png)

### <a name="secure-device-provisioning-and-authentication"></a>保護裝置佈建及驗證

Azure IoT 套件保護裝置於時取出] 欄位中提供可以使用 IoT 基礎結構與裝置進行通訊時作業中的每一個裝置的唯一識別金鑰。 程序是快速且更容易設定。 產生的金鑰與使用者所選取的裝置 ID 表單之基礎的裝置和 Azure IoT Hub 之間的所有通訊中所使用的 token。

裝置識別碼可以與裝置相關聯 （亦即中快閃硬體信任模組） 製造流程期間也可以使用現有的身分識別固定作為 proxy （例如 CPU 序列號碼）。 由於此中之裝置的識別資訊不變更簡單，務必引進邏輯的裝置 Id 以防基礎裝置硬體變更但邏輯裝置維持不變。 在某些情況下，間的裝置 identity 關聯可以發生在裝置的部署階段 （亦即經驗證的現場工程師實體設定的新裝置時與 IoT 解決方案後端通訊）。 [Azure IoT Hub identity 登錄](../articles/iot-hub/iot-hub-devguide.md)提供安全儲存裝置身分識別和安全性金鑰解決方案。 個人或群組的裝置身分識別可以新增至允許清單] 或 [封鎖] 清單中，啟用裝置存取的完整控制權。
 
在雲端 azure IoT Hub 存取控制原則可讓啟用和停用任何裝置 identity、 提供方法來解除關聯從 IoT 部署時所需的裝置。 此關聯及解除關聯的裝置根據每個裝置的 identity。

其他裝置的安全性功能包括：

- 裝置不接受來路不明的網路連線。 他們建立所有連線和路由易於僅限輸出的方式。 從後端接收命令裝置，裝置必須起始連線至檢查有任何擱置中的命令來處理。 一旦安全地建立裝置與 IoT Hub 之間的連線之後的裝置和裝置與雲端從雲端訊息可以傳送透明。  
- 裝置只能連線到或建立已知服務與它們 peered，例如 Azure IoT Hub 的路由。
- 使用每個裝置識別碼，進行存取認證與權限附近系統層級授權和驗證-立即可撤銷之。

### <a name="secure-connectivity"></a>安全的連線 

持續性的訊息任何 IoT 解決方案的重要功能。 需要長期傳遞命令和/或從裝置接收資料是由 IoT 裝置透過網際網路或其他類似的網路可以是不可靠連線事實加底線。 Azure IoT 中樞提供持續性的雲端和裝置透過認可以回應郵件系統之間的通訊。 額外的持續性的訊息可以透過多達七天的遙測及命令兩天的快取 IoT 中樞訊息達成。
 
效率請務必確定保育的資源與資源限制的環境中的作業。 HTTPS （HTTP 安全） 的常用 http 通訊協定的業界標準安全版本支援的 Azure IoT 中樞，啟用常用的通訊。 進階的訊息佇列通訊協定 (AMQP) 和訊息佇列遙測傳輸 (MQTT)、 Azure IoT Hub、 所支援的設計被用不僅適用於出現 [資源使用還可靠的郵件傳遞的效率。 

延展性需要能夠安全地與各種裝置互通。 Azure IoT hub 啟用同時啟用 IP 和非啟用 IP 之裝置的安全連線。 啟用 IP 的裝置是可以直接連線，並透過安全連線與 IoT Hub 通訊。 非 IP 啟用裝置資源限制，只能透過滑動通訊協定，例如 Zwave、 ZigBee，以及 Bluetooth 連線。 欄位閘道用來彙總這些裝置與執行啟用與雲端的安全雙向通訊的通訊協定翻譯。

其他連線安全性功能包括：

- 裝置和 Azure IoT 中樞，或閘道和 Azure IoT 中樞、 之間的通訊路徑受到保護使用 Azure IoT 中樞驗證使用 X.509 通訊協定的業界標準傳輸層安全性 (TLS)。
- 若要防止來路不明輸入連線的裝置、 Azure IoT Hub 無法開啟任何連線至裝置。 裝置起始所有連線。 
- Azure IoT Hub 長期儲存裝置的確認訊息與等候連線的裝置。 這些命令會儲存兩天啟用裝置偶發性，因為 power 或連線問題，以接收這些命令。 Azure IoT Hub 維護每個裝置中的每個裝置佇列。

### <a name="secure-processing-and-storage-in-the-cloud"></a>安全處理與雲端中的儲存空間 

從雲端中處理資料加密通訊，Azure IoT 套件可協助保護資料的安全。 它可提供彈性實作其他加密及管理安全性機碼。 使用 Azure Active Directory (AAD) 的使用者驗證與授權、 Azure IoT 套件可以提供原則為基礎的授權模型為雲端中，資料啟用容易存取管理可稽核及檢閱。 此模型也可讓幾近即時撤銷存取雲端中的資料和裝置連線至 Azure IoT 套件。

一旦資料位於雲端，它可以處理及儲存的所有使用者定義的工作流程。 存取資料的每個部分是根據所使用的儲存區服務控制與 Azure Active Directory。
   
IoT 基礎結構所使用的所有按鍵都儲存在安全認證儲存在雲端中具有可讓您以防機碼必須重新佈建。 資料可以儲存在[DocumentDB](../articles/documentdb/documentdb-introduction.md)或[SQL 資料庫](../articles/sql-database/sql-database-faq.md)啟用所需的安全性層級的定義。 此外，Azure 提供一種方式來監視及稽核所有存取您的資料，提醒您任何入侵或未經授權的存取。

## <a name="conclusion"></a>結論

件事網際網路開頭您事項 — 用於最重要的事項。 IoT 可以傳遞降低成本、 增加營收、 並將轉換商務絕妙值公司。 成功這個轉換的主要取決於選擇右 IoT 軟體和服務提供者。 這表示找 catalyzes 了解業務需求並需求，這個轉換不僅也提供服務和軟體與安全性、 隱私權、 透明度及規範做為主要的設計考量內建的提供者。 Microsoft 有大規模經驗開發及部署安全的軟體與服務] 繼續為以前置字元的事項網際網路這個新保留天數。 

Microsoft Azure IoT 套件建立在安全性措施根據設計，啟用安全的監控] 來提高效率，若要啟用創新能力及採用進階的資料分析轉換企業的磁碟機操作效能的資產。 其分層方法向安全性、 多個安全性功能和設計模式中，使用 Azure IoT 套件可協助部署可能是受信任轉換任何商務基礎結構。 

## <a name="additional-information"></a>其他資訊

每個 Azure IoT 套件預先設定的方案建立 Azure services，如下所示的執行個體：

- [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/)︰ 您連線至"事項"雲端的閘道。 您可以擴充數以百萬計的個別的資料的中樞與程序大規模磁碟區連線至每個裝置驗證支援協助您保護您的解決方案。
- [**Azure DocumentDB**](https://azure.microsoft.com/services/documentdb/)： 管理裝置的中繼資料的半結構化資料的完整編製索引的可調整資料庫服務在佈建、 屬性、 設定及安全性屬性例如。 DocumentDB 提供高效能與高輸送量處理、 結構描述與無關編製索引的資料，以及各式的 SQL 查詢介面。
- [**Azure 資料流分析**](https://azure.microsoft.com/services/stream-analytics/)： 處理可讓您快速開發和部署低成本分析解決方案以從抽出從裝置、 感應、 基礎結構及應用程式的即時前瞻雲端中的即時資料流。 此完全受管理的服務中的資料可以擴充到任何磁碟區仍能達成限度高輸送量、 低延遲和彈性。
- [**Azure 應用程式服務**](https://azure.microsoft.com/services/app-service/)： 建置強大的網頁伺服器與行動裝置應用程式連線至資料無所不在; cloud 平台內部部署或雲端。 建置著讓 iOS、 Android、 與 Windows 行動應用程式。 整合在軟體即服務 (SaaS) 與的方塊 （英文） 連線至數十的雲端架構服務的企業應用程式及企業應用程式。 您偏好的語言和 IDE 中的程式碼 —.NET、 NodeJS、 PHP、 Python，或是 Java — 比以往更快速建立 web 應用程式及 api （英文）。
- [**邏輯應用程式**](https://azure.microsoft.com/services/app-service/logic/)： Azure 應用程式服務的邏輯應用程式功能可協助整合至您的商務系統的現有一行 IoT 解決方案及工作流程程序自動化。 邏輯應用程式可讓開發人員從觸發程序會啟動並執行一系列的步驟的設計工作流程-規則和整合商務程序使用強大連接器的動作。 邏輯應用程式提供的方塊 （英文） SaaS、 雲端式絕生態系統的連線以及內部應用程式。
- [**Azure blob 存放區**](https://azure.microsoft.com/services/storage/)： 可靠而經濟雲端儲存您的裝置傳送至雲端的資料。

