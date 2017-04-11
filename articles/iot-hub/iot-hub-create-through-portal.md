<properties
     pageTitle="若要建立 IoT 中心使用 Azure 入口網站 |Microsoft Azure"
     description="概略瞭解如何建立及管理 Azure IoT 集線器透過 Azure 入口網站"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-the-azure-portal"></a>建立使用 Azure 入口網站 IoT 中心

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]


## <a name="introduction"></a>簡介

本文將說明如何尋找 IoT 中心服務在 Azure 入口網站，以及如何建立及管理 IoT 集線器。

## <a name="where-to-find-iot-hubs"></a>哪裡可以找到 IoT 集線器

有不同的位置，您可以在哪裡找到 IoT 集線器。

1. **+ 新增**︰ **Azure IoT 中心**是 IoT 服務，且可以找到在類別的**網際網路的項目**，底下 [ **+ 新增**，類似於其他服務。

2. IoT 集線器也可以透過服務商場為**網際網路的項目**示範用服務存取。

## <a name="create-an-iot-hub"></a>建立 IoT 中心

您可以建立使用下列方法 IoT 中心︰

- 建立透過**+ 新增**選項 IoT 中心引導至下一個畫面中顯示刀。 建立 IoT 中心，透過這種方式及服務商場的步驟都相同。

- 建立到服務商場 IoT 中心︰ 按一下 [**建立**開啟相同， **[+ 新增**的體驗上一個刀刀。 下一節中列出的幾個步驟建立 IoT 中心。

### <a name="choose-the-name-of-the-iot-hub"></a>選擇 [IoT 中心的名稱

若要建立 IoT 中心，您必須命名中樞。 在集線器，此名稱必須是唯一的。 不重複的集線器允許後端，所以建議此中心名稱為盡可能唯一。

### <a name="choose-the-pricing-tier"></a>選擇 [價格層

從四個階層，您可以選擇︰**免費**、**標準 1**和**標準 2**和**標準 S3**。 免費層可讓您只 500 裝置連線到 IoT 集線器，以及最 8000 每日的訊息。

**標準 S1**: IoT 集線器 S1 edition 專為有大量的裝置產生以往少量的資料，每個裝置的 IoT 解決方案。 每個單位 S1 版本的在所有連線的裝置上允許每日最 400,000 訊息。

**標準 S2**: IoT 中心 S2 edition 專為 IoT 解決方案的裝置產生的大量資料。 每個單位 S2 版本的可讓每日之間連線的所有裝置的最 6 百萬郵件。

**標準 S3**: IoT 中心 S3 edition 專為 IoT 解決方案，產生的大量資料。 每個單位 S3 版本的可讓每日之間連線的所有裝置的最多 300 百萬郵件。

![][4]

> [AZURE.NOTE] IoT 中心只允許一個免費的中心，每個 Azure 訂閱。

### <a name="iot-hub-units"></a>IoT 中心單位

IoT 中心單位包含每日訊息數。 支援此中樞的訊息總數是單位乘以每日的該層訊息的數目。 例如，如果您想 IoT 中樞支援 700000 郵件的 [輸入時，您可以選擇兩個 S1 層單位。

### <a name="device-to-cloud-partitions-and-resource-group"></a>裝置雲端分割] 和 [資源群組

您可以變更的磁碟分割區 IoT 中樞的數目。 設定預設的磁碟分割為 4。不過，您可以選擇不同的數字的磁碟分割區，從下拉式清單。

資源群組，您不需要明確建立空白的資源群組。 在建立資源時，您可以選擇建立新的資源群組] 或 [使用現有的資源群組。

![][5]

### <a name="choose-subscriptions"></a>選擇訂閱

Azure IoT 中心會自動顯示 Azure 訂閱的使用者帳戶連結的清單。 您可以選擇其中一個選項以下建立 IoT 中樞的 Azure 訂閱之間的關聯。

### <a name="choose-the-location"></a>選擇位置

[位置] 選項提供提供 IoT 中心是的區域的清單。 IoT 中心有部署在下列位置︰ 澳大利亞東亞澳大利亞 Southeast、 亞洲東亞、 亞洲 Southeast、 歐洲北美、 歐洲西、 日本東亞西日本美國東部] 適用於美國西部。

### <a name="create-the-iot-hub"></a>建立 IoT 中心

所有的上一個步驟完成後，IoT 中心已準備好建立的。 按一下 [**建立**啟動後端的程序建立此 IoT 中樞的特定選項，並將其部署到指定的位置。

可能需要幾分鐘 IoT 集線器建立會在適當的位置伺服器中發生的後端部署的時間。

## <a name="change-the-settings-of-the-iot-hub"></a>變更 IoT 中樞的設定

從 IoT 中心刀建立後，您可以變更現有的 IoT 中樞的設定。

![][8]

**共用 Access 原則**︰ 這些原則定義裝置和服務連線至 IoT 中樞的權限。 您可以按一下 [**一般**] 下的 [**共用存取原則**，來存取這些原則。 在此刀，您可以修改現有的原則，或新增原則。

### <a name="create-a-policy"></a>建立原則

- 按一下 [**新增**] 以開啟刀]。 這裡，您可以輸入新的原則名稱及您要與此原則，建立關聯，如下圖所示的權限。

    有數種可以與下列共用原則相關聯的權限。 前兩個原則，**登錄讀取**及**撰寫登錄**，授與讀取和寫入存取權限裝置身分識別存放區或身分識別登錄機碼。 自動選擇 [寫入] 選項中選擇以及已讀取] 選項。

    **服務連線**原則授與存取雲端端端點，例如服務連線到 IoT 中樞的消費者群組的權限。 **裝置連線**原則授與權限的傳送和接收訊息上 IoT 中心裝置端端點。

- 按一下 [**建立**加入新建立的原則至現有的清單。

![][10]

## <a name="messaging"></a>訊息

按一下要顯示的訊息正在修改 IoT 中樞的內容清單的**訊息**。 有兩種主要的屬性，您可以修改或複製︰**雲端裝置**和**裝置至雲端**。

- **雲端裝置**設定︰ 這項設定具有兩個 subsettings︰**雲端裝置 ttl** （時間存留） 與郵件**保留時間**。 第一次建立 IoT 中心時，會建立兩個這些設定，預設值為 1 小時。 若要調整這些值，使用滑桿或輸入值。

- **雲端裝置**設定︰ 這項設定有數種 subsettings，其中一些命名/指派 IoT 中心會建立並只可以將其複製到其他 subsettings 可自訂的。 下一節中，會列出這些設定。

**磁碟分割區**︰ 此值設 IoT 中心會建立並可透過這項設定變更。

**事件中心相容名稱] 和 [結束點**︰ 建立時 IoT 中心，事件中心建立內部，您可能需要在某些情況下的存取權。 此事件中心相容的名稱和端點無法自訂，但是可以使用透過 [**複製**] 按鈕。

**保留時間**︰ 依預設會設定為一天，但可以自訂為使用下拉式清單中其他值。 此值會是像雲端至裝置的類似設定天數至雲端的裝置，而不是在小時。

**消費者群組**︰ 消費者群組是類似其他可以用來擷取資料以特定方式連線到 IoT 集線器的其他應用程式或服務的郵件系統的設定。 每個 IoT 中心建立預設消費者群組。 不過，您可以新增或刪除您 IoT 集線器消費者群組。

> [AZURE.NOTE] 預設的消費者群組不能編輯或刪除。

![][11]

## <a name="pricing-and-scale"></a>定價和縮放比例

可以變更現有的 IoT 中樞的價格，透過**價格**設定，但具有下列例外︰

- 在目前的實作中，使用免費的 SKU IoT 中心無法變更層付費的 Sku，其中一項或反向操作。
- Azure 訂閱中只能有一個免費層 IoT 中心。

![][12]

從較高的層 （S2 或 S3） 移至較低層 （S1 或 S2） 的那一天的郵件數不是衝突時，只允許。 例如，如果每天的訊息的數目超過 400000，然後 IoT 中樞的層可以變更。 不過，如果您變更 S1 層然後中心是經流速控制那一天。

## <a name="delete-the-iot-hub"></a>刪除 IoT 中心

您可以瀏覽至您想要刪除，按一下 [**瀏覽**，，然後選擇 [刪除適當的中心 IoT 中心。 按一下 [刪除中樞中心名稱下方的 [**刪除**] 按鈕。

## <a name="next-steps"></a>後續步驟

請遵循這些連結，深入瞭解管理 Azure IoT 中心︰

- [大量管理 IoT 裝置][lnk-bulk]
- [使用指標][lnk-metrics]
- [監控的作業][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]
- [設定 secure 從頭 IoT 方案][lnk-securing]


  [4]: ./media/iot-hub-create-through-portal/create-iothub.png
  [5]: ./media/iot-hub-create-through-portal/location1.png
  [8]: ./media/iot-hub-create-through-portal/portal-settings.png
  [10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-create-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md