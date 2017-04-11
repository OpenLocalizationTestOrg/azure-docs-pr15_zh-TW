<properties
   pageTitle="最佳作法，項目安全性網際網路 |Microsoft Azure"
   description="本文提供 Microsoft Internet 的項目安全性的最佳作法和一般建議 curated 的清單。"
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="internet-of-things-security-best-practices"></a>Internet 的項目安全性的最佳作法

保護網際網路的項目 (IoT) 基礎結構是人 IoT 解決方案的相關的要徑任務。 由於裝置的相關的數目，這些裝置的分散的特性危害數百萬 IoT 裝置的相關的安全性事件的影響非一般，而具有廣泛的影響。

因此，IoT 安全性需要深度安全性方法。 在雲端安全地需要的資料，以及將其移至私人與公用網路。 方法必須在安全地佈建 IoT 裝置本身的位置。 每個層級]，從裝置，到雲端後端的網路，必須強大的安全性保證。

以下列方式可歸納 IoT 最佳作法︰

- IoT 硬體製造商或積分
- IoT 解決方案開發人員
- IoT 解決方案部署者
- IoT 解決方案運算子

本文摘錄了[網際網路的項目安全性的最佳作法](../iot-suite/iot-security-best-practices.md)。 請參閱該文章︰ 如需詳細資訊。

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT 硬體製造商或積分

如果您是 IoT 硬體製造商或硬體積分，請遵循下列最佳作法︰

- **最低需求的範圍硬體**︰ 硬體設計應該包含最小的硬體，而無其他作業所需的功能。 
- **進行竄改的硬體**︰ 建立機制偵測硬體，例如開啟裝置封面，移除裝置等部分的實體遭到竄改。 
- **建立安全的硬體周圍**︰ 如果[COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold)允許，建立安全性功能，例如保護及加密存放區 」 和 「 信任的平台模組 TPM 開機功能。
- **升級安全**︰ 升級韌體裝置的存留期間是不可避免。

## <a name="iot-solution-developer"></a>IoT 解決方案開發人員

如果您是 IoT 解決方案開發人員，請遵循下列最佳作法︰

- **追蹤安全的軟體開發方法**︰ 安全的軟體開發需要從專案的開始到其實作、 測試和部署的安全性的基本向上思考。
- **選擇 [開啟來源軟體謹慎**︰ 開啟來源軟體提供機會快速開發解決方案。
- **謹慎的整合**︰ 軟體的安全性漏洞許多存在的文件庫和 Api 邊界。 

## <a name="iot-solution-deployer"></a>IoT 解決方案部署者

如果您是 IoT 解決方案部署者，請遵循下列最佳作法︰

- **安全地部署硬體**︰ IoT 部署可能需要在不安全的位置，例如公用空格或不的地區設定中部署的硬體。
- **保留驗證金鑰安全**︰ 部署期間，每個裝置需要裝置識別碼和驗證金鑰產生的雲端服務的相關。 保留即使部署實際安全這些按鍵。 任何洩露的金鑰可讓惡意裝置來偽裝現有的裝置。

## <a name="iot-solution-operator"></a>IoT 解決方案運算子

如果您是 IoT 解決方案運算子，請遵循下列最佳作法︰

- **保持在最新的系統**︰ 確定裝置作業系統及所有的裝置驅動程式會更新為最新版本。 
- **保護針對惡意的活動**︰ 如果作業系統允許，將最新的防毒軟體與反惡意程式碼功能，在每個裝置的作業系統上。 
- **稽核經常**︰ 稽核 IoT 基礎結構的安全性相關問題，就鍵回應安全性事件。
- **實際保護 IoT 基礎結構**︰ 表現最差安全性攻擊 IoT 基礎結構使用實體存取的裝置尚未啟動。
- **保護雲端認證**︰ 設定和作業系統 IoT 部署使用雲端驗證憑證可能是最簡單的方法存取並侵入 IoT 系統。 
