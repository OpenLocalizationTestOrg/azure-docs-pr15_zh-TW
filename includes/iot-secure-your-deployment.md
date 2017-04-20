# <a name="securing-your-iot-deployment"></a>保護您 IoT 的部署

本文提供保護 Azure IoT 型網際網路的事項 (IoT) 基礎結構的下一個層級的詳細資料。 它會連結至實作設定及部署每個元件的層級詳細資料。 它也提供比較和各種使用競爭方法之間的選擇。

保護 Azure IoT 部署可以分成下列三種安全性區域：

- **裝置安全性**： 保障 IoT 裝置時部署中包含相對。
- **連線安全性**： 確保所有資料之間的 IoT 裝置和 IoT 集線傳輸的機密與竄改。
- **雲端 Security**： 提供安全資料它會隨其進入，以及儲存在雲端時方法。

![三個安全性區域][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>保護裝置佈建及驗證

Azure IoT 套件保護 IoT 裝置由下列兩種方法：

- 所提供的唯一識別機碼 （安全性權杖） 適用於每一個可以使用 IoT 中樞與通訊裝置的裝置。

- 使用裝置上[X.509 憑證][lnk-x509]與私密金鑰為驗證之裝置 IoT Hub 方法。 此驗證方法可確保該裝置上不知道私密金鑰外部裝置在任何時候，提供較高層級的安全性。

安全性 token 方法提供驗證每個所撥打通話之裝置到 IoT 集線器建立關聯每次呼叫對稱的按鍵。 X.509 型驗證的 TLS 連線建立一部分實體層級允許 IoT 裝置的驗證。 這是較不安全模式的 X.509 驗證沒有可用的安全性權杖型方法。 使用方式安全的是，裝置驗證需求及可用性 （來安全地儲存私密金鑰） 裝置上的安全認證儲存主要指定兩個方法之間的選擇。

## <a name="iot-hub-security-tokens"></a>IoT Hub 安全性權杖

IoT Hub 使用安全性權杖驗證裝置及服務以避免將按鍵傳送網路上。 此外，安全性權杖限制在有效時間與範圍。 Azure IoT Hub Sdk 自動產生權杖而不需要任何特殊的設定。 某些案例，但需要使用者產生並直接使用安全性權杖。 這些包括 MQTT、 AMQP、 或 HTTP 表面，直接使用或實作的 token 服務圖樣。

更多詳細資料與結構的安全性憑證及其使用方式上可以找到下列文章：

-   [安全性 token 結構][lnk-security-tokens]
-   [使用 SAS 權杖成為的裝置][lnk-sas-tokens]

每個 IoT 中樞具有[裝置 Identity 登錄][lnk-identity-registry]可用來建立服務，例如包含雲端-裝置訊息、 的佇列中的每個裝置的資源和允許存取裝置對向端點。 IoT Hub identity 登錄提供安全儲存裝置身分識別和安全性金鑰解決方案。 個人或群組的裝置身分識別可以新增至允許清單] 或 [封鎖] 清單中，啟用裝置存取的完整控制權。 下列文章提供更詳細的裝置 Identity 登錄結構及支援的作業。

[IoT 集線器支援等 MQTT、 AMQP、 和 HTTP 通訊協定][lnk-protocols]。 每個這些通訊協定以不同方式使用從 IoT 裝置的安全性權杖以 IoT Hub：

- AMQP: SASL 純文字和 AMQP 宣告式安全性({policyName}@sas.root.{iothubName}指 Hub 層級權杖;{deviceId} 發生裝置範圍權杖)。

- MQTT: {ClientId} 作為連接封包使用 {deviceId} {IoThubhostname} / {deviceId} **Username**功能變數及 SAS 權杖中 [**密碼**] 欄位中。

- HTTP︰ 授權要求標頭中是有效的 token。

IoT Hub 裝置 Identity 登錄可用來設定每個裝置的安全性認證及存取控制。 不過，如果 IoT 解決方案已經有重大的投資[自訂裝置 identity 登錄及 （或） 驗證配置]中[lnk-custom-auth]，它可以藉由建立 token 服務整合到 IoT 中樞與現有基礎結構。

### <a name="x509-certificate-based-device-authentication"></a>X.509 憑證型裝置驗證

[以裝置為基礎的 X.509 憑證]使用[lnk-use-x509]和其相關聯的私密與公開金鑰組允許其他驗證實體的層級。 私密金鑰安全地儲存裝置中並不是可探索外部裝置。 X.509 憑證包含裝置等裝置識別碼，以及其他組織的詳細資訊的相關資訊。 產生憑證的簽章時使用的私密金鑰。

高層級裝置佈建流程：

- 建立關聯的實體裝置 – 裝置 identity 和/或裝置的量產發行或 commissioning 期間裝置相關聯的 X.509 憑證識別碼。

- IoT 中樞 – 裝置 identity 和相關聯的裝置資訊 IoT Hub 裝置登錄中建立對應的身分識別項目。

- 安全地儲存在 IoT Hub 裝置登錄的 X.509 憑證指紋。

### <a name="root-certificate-on-device"></a>在裝置上的根憑證

建立安全 TLS 連線與 IoT 中樞，IoT 裝置的驗證方法使用根憑證的一部分裝置 SDK IoT 中樞。 C 用戶端 sdk （英文） 憑證位於資料夾下"\\c\\憑證 」 底下 repo 的根目錄。 這些根憑證的存留期長的但是他們仍然可能過期或要撤銷。 如果沒有方法的更新裝置上的憑證，裝置可能無法後續連線至 IoT 中樞 （或任何其他雲端服務）。 具有方法可更新的根憑證之後部署 IoT 裝置將會有效減輕至此風險。

## <a name="securing-the-connection"></a>保護連線

使用傳輸層安全性 (TLS) 標準都被安全網際網路 IoT 裝置與 IoT 中心之間的連線。 Azure IoT 支援[TLS 1.2][lnk-tls12]、 TLS 1.1 與 TLS 1.0，順序如下。 支援 TLS 1.0 提供回溯相容性。 使用 TLS 1.2 由於它可提供最安全性建議。

Azure IoT 套件支援下列編碼器套件中，依此順序。

| 編碼器套件 | 長度 |
|--------------|--------|
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (eq. 7680 個位元 RSA） FS | 256    |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (eq. 3072 個位元 RSA） FS | 128    |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA-1 (0xc014) ECDH secp384r1 (eq. 7680 個位元 RSA） FS           | 256    |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA-1 (0xc013) ECDH secp256r1 (eq. 3072 個位元 RSA） FS           | 128    |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) | 256    |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) | 128    |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) | 256    |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) | 128    |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA-1 (編號︰ 0x35)    | 256    |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA-1 (0x2f)    | 128    |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA-1 (0xa)    | 112    |

## <a name="securing-the-cloud"></a>保護雲端

Azure IoT Hub 允許的[存取控制原則]定義[lnk-protocols]的每個安全性金鑰。 它會使用下列一組權限授與存取權給每個 IoT Hub 端點。 權限限制根據功能 IoT Hub 存取。

- **RegistryRead**。 讀取權限授與裝置 Identity 登錄。 如需詳細資訊，請參閱[裝置 Identity 登錄][lnk-identity-registry]。

- **RegistryReadWrite**。 授與讀取和寫入權限裝置 Identity 登錄。 如需詳細資訊，請參閱[裝置 Identity 登錄][lnk-identity-registry]。

- **ServiceConnect**。 授與存取雲端服務向通訊及監視端點。 例如，其授與後端雲端服務接收裝置-雲端訊息、 傳送 cloud-裝置郵件，並擷取對應傳遞認可的權限。

- **DeviceConnect**。 裝置向通訊端點授與權限。 例如，其授與裝置-雲端郵件傳送及接收雲端-裝置訊息的權限。 裝置會使用此權限。

有兩種方法來取得**DeviceConnect**權限與[安全性權杖]IoT Hub[lnk-sas-tokens]︰ 使用裝置 identity 機碼或存取共用的原則機碼。 此外，請務必注意從裝置可存取的所有功能已都公開的前置詞的端點上的設計`/devices/{deviceId}`。

[服務元件可以只會產生安全性權杖][lnk-service-tokens]使用共用存取原則授與適當的權限。

Azure IoT Hub 及其他服務可能是解決方案的一部分允許使用 Azure Active Directory 使用者的管理。

Ingested 由 Azure IoT 中樞資料可供服務如 Azure 資料流分析及 Azure blob 儲存各種不同。 這些服務允許管理存取。 閱讀更多關於這些服務及提供下列選項：

- [Azure DocumentDB][lnk-docdb]： 管理裝置的中繼資料的半結構化資料的可擴充、 完全編製索引的資料庫服務在佈建，例如屬性、 設定及安全性屬性。 DocumentDB 提供高效能與高輸送量處理、 結構描述與無關編製索引的資料，以及各式的 SQL 查詢介面。

- [Azure 資料流分析][lnk-asa]： 處理可讓您快速開發和部署低成本分析解決方案以從抽出從裝置、 感應、 基礎結構及應用程式的即時前瞻雲端中的即時資料流。 此完全受管理的服務中的資料可以擴充到任何磁碟區仍能達成限度高輸送量、 低延遲和彈性。

- [Azure 應用程式服務][lnk-appservices]: cloud 平台，以建立強大的網頁伺服器與連接到任何地方; 資料的行動應用程式內部部署或雲端。 建置著讓 iOS、 Android、 與 Windows 行動應用程式。 整合在軟體即服務 (SaaS) 與的方塊 （英文） 連線至數十的雲端架構服務的企業應用程式及企業應用程式。 您偏好的語言和 IDE （.NET、 NodeJS、 PHP、 Python、 或 Java） 比以往更快速建立 web 應用程式及 api （英文） 中的程式碼。

- [應用程式邏輯][lnk-logicapps]： Azure 應用程式服務的邏輯應用程式功能可協助整合至現有的企業營運系統 IoT 解決方案及工作流程程序自動化。 邏輯應用程式可讓開發人員從觸發程序啟動並執行步驟一系列的設計工作流程-規則和整合商務程序使用強大的連接器的動作。 邏輯應用程式提供的方塊 （英文） SaaS、 雲端式絕生態系統的連線以及內部應用程式。

- [Azure blob 儲存][lnk-blob]： 可靠而經濟雲端儲存您的裝置傳送至雲端的資料。

## <a name="conclusion"></a>結論

本文提供概觀實作設計及部署使用 Azure IoT IoT 基礎結構的層級詳細資料。 設定為 [安全的每個元件是在保護的整體 IoT 基礎結構的索引鍵。 Azure IoT 中可用的設計選擇提供彈性和選擇; 一些層的級不過，每種選擇可能會有安全性含意。 建議每一個這些選擇要評估風險/成本評估透過。

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-as-a-device
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#using-security-tokens-from-service-components
[lnk-docdb]: https://azure.microsoft.com/services/documentdb/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
