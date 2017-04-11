<properties
   pageTitle="作業管理套件安全性和稽核解決方案的比較基準 |Microsoft Azure"
   description="本文說明如何使用 OMS 安全性和稽核執行所有監視電腦的比較基準評估規範與安全性用途的解決方案。"
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="yurid"/>

# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>在作業管理套件安全性和稽核解決方案的比較基準評估

這份文件可協助您使用[作業管理套件 (OMS) 的安全性和稽核解決方案](operations-management-suite-overview.md)的比較基準評估功能存取您監視資源的安全的狀態。

## <a name="what-is-baseline-assessment"></a>比較基準評估是什麼？

Microsoft 與產業與政府版的組織全球，定義代表高度安全性伺服器部署 Windows 設定。 此設定是一組的登錄機碼、 稽核原則設定，以及安全性原則設定，以及 Microsoft 建議使用下列設定值。 此設定的規則就是安全性比較基準。 OMS 安全性、 稽核的比較基準評估功能可以順暢完美地掃描法規遵循所有您的電腦。 

有三種類型的規則︰

- **登錄規則**︰ 檢查登錄機碼是否已正確設定。
- **稽核原則規則**︰ 關於稽核原則的規則。
- **安全性原則規則**︰ 在電腦上的使用者權限的規則。

> [AZURE.NOTE] 讀取此功能的簡短概觀[使用 OMS 安全性評估安全性設定比較基準](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/)。

## <a name="security-baseline-assessment"></a>安全性比較基準評估

您可以檢閱目前的安全性比較基準評估監視 OMS 安全性和稽核使用儀表板的所有電腦。  執行下列步驟，以存取安全性比較基準評估儀表板︰

1. 在 [ **Microsoft 作業管理套件**主儀表板，按一下**安全性和稽核**圖磚。
2. 在的**安全性和稽核**儀表板中，按一下 [**比較基準評估**下的 [**安全的網域**。 下圖所示，就會出現 [**安全性比較基準評估**儀表板︰
    
    ![OMS 安全性和稽核比較基準評估](./media/oms-security-baseline/oms-security-baseline-fig1.png)

此儀表板的除數三個主要領域︰

- **比較基準，比較的電腦**︰ 此區段所提供的電腦所存取號碼]，然後傳送評估的電腦的百分比的摘要。 同時也會提供前 10 個電腦和百分比結果評定。
- **所需的規則狀態**︰ 本節有想来讓嚴重性失敗規則的線上狀態，而且無法依類型的規則。 查看第一個圖形，以您可以快速識別如果大部分失敗的規則重要，或不。 同時也會提供清單上方的 10 個失敗的規則和嚴重性。 第二個圖表會顯示評估期間失敗的規則的類型。 
- **電腦的遺失比較基準評估**︰ 此節] 清單中的電腦所未存取作業系統不相容性或失敗。 

### <a name="accessing-computers-compared-to-baseline"></a>存取比較基準的電腦

理想的情況下，所有您的電腦都是與安全性比較基準評估相容。 不過預期，在某些情況下這不會發生這種情形。 安全性管理程序的一部分，請務必包含檢閱無法通過安全性評估測試的電腦。 以視覺化方式呈現的快速的方法是選取**電腦存取**位於 [**電腦與比較基準**] 區段中的選項。 您應該會看到記錄搜尋結果，顯示清單的電腦，如下列畫面所示︰

![電腦存取結果](./media/oms-security-baseline/oms-security-baseline-fig2.png)

搜尋結果會顯示表格的格式，位置第一欄有電腦的名稱，而第二個色彩有失敗的規則的數字。 若要擷取的失敗的規則類型的相關資訊，請按一下失敗的規則，除了電腦名稱的數目。 您應該會看到類似下圖中所顯示的結果︰

![電腦存取結果詳細資料](./media/oms-security-baseline/oms-security-baseline-fig3.png)

此搜尋結果中，您有存取、 失敗的要徑規則的數目，警告規則和資訊失敗規則總計。

### <a name="accessing-required-rules-status"></a>存取所需的規則狀態

取得之後的傳遞評估的電腦的百分比數字的相關資訊，您可能會想要取得的規則會根據嚴重性失敗的詳細資訊。 這個視覺效果，可協助您應該先處理哪些電腦，以確保他們會在下一個評估相容的優先順序。 將游標停留在要徑的組件的圖形位於**無法依嚴重性規則**] 方塊底下**所需的規則狀態**，然後按一下它。 您應該會看到類似下列畫面的結果︰

![規則失敗嚴重性詳細資料](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

此記錄結果中，您會看到失敗，這項規則的描述與此安全性規則的常見設定列舉 (CCE) 識別碼的比較基準規則的類型。 這些屬性應該可以執行在目標電腦修正此問題的修正動作。

> [AZURE.NOTE] 如需有關 CCE 的詳細資訊，請存取[國際弱點資料庫](https://nvd.nist.gov/cce/index.cfm)。

### <a name="accessing-computers-missing-baseline-assessment"></a>存取遺失比較基準評估的電腦

OMS 支援 Windows Server 2008 R2 進位到 Windows Server 2012 R2 網域成員比較基準設定檔。 在 Windows Server 2016 比較基準最終目前尚不，並將會新增為發佈。 透過 OMS 安全性和稽核比較基準評估掃描所有其他作業系統會出現在**電腦的遺失比較基準評估**一節。

## <a name="see-also"></a>另請參閱

在此文件中，您學 OMS 安全性和稽核的比較基準評量。 若要進一步瞭解 OMS 安全性，請參閱下列文章︰

- [作業管理套件 (OMS) 概觀](operations-management-suite-overview.md)
- [監控和回應作業管理套件安全性和稽核方案中的安全性警告](oms-security-responding-alerts.md)
- [監視作業管理套件安全性和稽核方案中的資源](oms-security-monitoring-resources.md)

