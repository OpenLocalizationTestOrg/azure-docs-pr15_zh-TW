<properties
   pageTitle="網際網路的項目安全性概觀 |Microsoft Azure"
   description=" Azure 的項目 (IoT) 服務的網際網路提供各式各樣的功能。 本文可協助您瞭解如何保護 IoT 方案 Azure 中。 "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="internet-of-things-security-overview"></a>網際網路的項目安全性概觀

Azure 的項目 (IoT) 服務的網際網路提供各式各樣的功能。 這些企業成績服務可讓您︰

- 從裝置收集資料
- 分析資料流中的影片
- 儲存及查詢的大型資料集
- 以視覺化方式呈現資料即時和歷程記錄
- 返回 office 系統整合

若要提供這些功能、 Azure IoT 套件套件一起多個 Azure 服務自訂副檔名為預先設定的解決方案。 這些預先設定的解決方案是基底實作常見 IoT 解決方案模式，以減少您需要發表您 IoT 解決方案的時間。 使用 IoT 軟體開發套件，您可以在簡報中自訂及擴充這些解決方案，以符合您自己的需求。 您也可以使用這些解決方案範例或範本時開發新 IoT 解決方案。

Azure IoT 套件是您 IoT 需求的功能強大的解決方案。 不過，它是 upmost 重要性 IoT 方案設計的安全性考量事項開始。 由於大量的 IoT 裝置，任何的安全性事件很快就嚴重影響廣泛事件。

若要協助您瞭解如何保護您 IoT 解決方案，我們具備下列資訊。

## <a name="security-architecture"></a>安全性架構

當設計系統時，請務必瞭解該系統的潛在威脅及時系統設計和架構，因此，新增適當的措施。 請務必特別設計的完整產品的安全性考量事項，因為瞭解如何攻擊可能破壞系統可以協助您進行確定適當的安全防護在從開始的位置。

您可以讀取[項目安全性架構網際網路](../iot-suite/iot-security-architecture.md)，以瞭解 IoT 安全性架構。

本文討論下列主題︰

- [安全性啟動威脅模型](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
- [IoT 中的安全性](../iot-suite/iot-security-architecture.md#security-in-iot)
- [潛在威脅模型 Azure IoT 參考架構](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>從最基礎的安全性

IoT 會造成的全球企業唯一的安全性、 隱私權和法規遵循挑戰。 與不同的位置是軟體和如何執行這些問題的傳統網路技術，IoT 疑慮網路和實體世界第一減少時，會發生什麼情況。 保護 IoT 解決方案需要確保安全佈建的裝置，這些裝置的雲端及處理和儲存在雲端安全資料保護之間的安全連線。 不過，使用針對這類功能，包括資源受限的裝置]、 [地理分佈的部署，以及 [大量的方案中的裝置。

您可以瞭解如何處理這些區域中的安全性閱讀[，從頭網際網路的項目安全性](../iot-suite/securing-iot-ground-up.md)。

本文將說明下列主題︰

- [安全的基礎結構從頭](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
- [Microsoft Azure – 安全 IoT 基礎結構，為您的企業](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>最佳作法

保護 IoT 基礎結構需要精確的安全性深度策略。 從保護資料在雲端，保護資料完整性傳輸中的公用網際網路，並讓您能夠安全地佈建裝置，開始各個圖層會建立更大的整體基礎結構中的安全性保證。

您可以瞭解網際網路的項目安全性的最佳作法閱讀[網際網路的項目安全性的最佳作法](../iot-suite/iot-security-best-practices.md)。

本文將說明下列主題︰

- [IoT 硬體製造商/積分](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
- [IoT 解決方案開發人員](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
- [IoT 解決方案部署者](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
- [IoT 解決方案運算子](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
