<properties
   pageTitle="監控資源作業管理套件安全性和稽核方案 |Microsoft Azure"
   description="這份文件可協助您使用 OMS 安全性和稽核功能來監控您的資源，並找出安全性問題。"
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>監控作業管理套件安全性和稽核方案中的資源

這份文件可協助您使用 OMS 安全性和稽核功能來監控您的資源，並找出安全性問題。

## <a name="what-is-oms"></a>什麼是 OMS？

Microsoft 作業管理套件 (OMS) 是以 Microsoft 的雲端基礎可協助您管理及保護您的內部部署與雲端基礎結構的 IT 管理解決方案。 如需有關 OMS 的詳細資訊，請閱讀文章[作業管理套件](https://technet.microsoft.com/library/mt484091.aspx)。

## <a name="monitoring-resources"></a>監視資源

每當有可能是，您會想要先發生時，防止安全性事件。 不過，就無法防止所有的安全性事件。 當安全性事件並未發生時，您必須以確保其影響最小化。  有三個可用於數] 及 [安全性事件的影響，最小化的要徑建議︰

- 定期評估環境中的弱點。
- 定期檢查所有電腦系統，以確保他們擁有所有已安裝最新修補程式的網路裝置。
- 定期檢查所有記錄檔和記錄機制，包括作業系統事件記錄檔、 應用程式的特定記錄，以及侵入偵測系統記錄。

OMS 安全性和稽核方案可讓 IT 主動監控所有的資源，可協助最小化安全性工作量的影響。 OMS 安全性和稽核有可供監視資源的安全性網域。 安全性網域提供快速存取選項，請安全性監視將涵蓋下列網域的更多詳細資料︰

- 惡意程式碼評估
- 更新評估
- 身分識別與存取權

> [AZURE.NOTE] 針對所有這些選項的概觀，請閱讀[快速入門作業管理套件安全性和稽核方案](oms-security-getting-started.md)。

### <a name="monitoring-system-protection"></a>監控系統保護

深度方法防護，在每一層是資產的保護的很重要的整體安全性狀態。 偵測到的威脅的電腦並沒有足夠的保護電腦會顯示在下的 [安全性網域的 [惡意程式碼評估] 方塊中。 藉由使用惡意程式碼評估資訊，您可以找出計劃以需要的伺服器上套用保護。 若要存取此選項，請遵循下列步驟︰

1. 在 [ **Microsoft 作業管理套件**主儀表板按一下**安全性和稽核**圖磚。

    ![安全性和稽核](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. 在 [**安全性與稽核**儀表板，按一下**反惡意程式碼評估**下的 [**安全性網域**。 如下所示，就會出現**反惡意程式碼評估**儀表板︰

![惡意程式碼評估](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

您可以使用**惡意程式碼評估**儀表板，來找出下列安全性問題︰

- **作用中的潛在威脅**︰ 已洩露，而且系統中有作用中的潛在威脅的電腦。
- **Remediated 威脅**︰ 洩漏的電腦，但威脅已在於。
- **最新的簽章**︰ 已啟用惡意程式碼保護的電腦，但簽名已過期。
- **沒有即時保護**︰ 沒有安裝的反惡意程式碼的電腦。

### <a name="monitoring-updates"></a>監控更新 

套用最新的安全性更新安全性的最佳作法，而應該併入您更新管理策略。 Microsoft 監控代理程式服務 (HealthService.exe) 讀取從監視的電腦上的 [更新資訊，並傳送至雲端的 OMS 服務處理此更新的資訊。 Microsoft 監控代理程式服務設定為自動服務，它應該一律在目標電腦執行。

![監控更新](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

邏輯會套用至更新資料和雲端服務記錄的資料。 如果找到遺失的更新，其會顯示在 [**更新**儀表板。 您可以使用 [**更新**儀表板使用遺漏的更新及開發計劃以將其套用到需要的伺服器。 請遵循下列步驟來存取**更新**儀表板︰

1. 在 [ **Microsoft 作業管理套件**主儀表板按一下**安全性和稽核**圖磚。
2. 在的**安全性和稽核**儀表板中，按一下 [**更新評估**下的 [**安全的網域**。 如下所示，就會出現 [更新儀表板︰

![更新評估](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

在此儀表板，您可以執行瞭解您的電腦的目前狀態並解決最重要的潛在威脅更新評量。 藉由使用 [**要徑] 或 [安全性更新**] 方塊，IT 系統管理員可以存取遺失，如下所示的更新的詳細的資訊︰

![搜尋結果](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

此報表包含可用來識別的威脅系統很容易，其中包含安全性更新與 MS 公告含有更多詳細資料的弱點相關聯的 Microsoft 知識庫文章的類型的重要資訊。

### <a name="monitoring-identity-and-access"></a>監控身分識別與存取權

使用者從任何地方，使用不同的裝置和存取大量的雲端和內部部署的應用程式，請務必受到其認證。 認證盜用攻擊是在其中一開始攻擊的存取權一般使用者的認證以存取網路中的系統。 次數，這種初始攻擊取得網路存取權的方式，最終的目標是要探索權限的帳戶。 

攻擊會保留在網路中，使用免費的其他登入的帳戶的工作階段中擷取認證，工具。 根據系統設定，這些認證可擷取雜湊、 票證或甚至是純文字密碼的表單。  

> [AZURE.NOTE] 直接公開網際網路的電腦將會看到許多次嘗試登入使用所有類型的已知的使用者名稱 （例如系統管理員）。 在大多數情況下它是 [確定]，如果無法使用，已知的使用者名稱，然後密碼是。

若要找出這些入侵者，他們會危害權限的帳戶，可能是。 您可以利用**OMS 安全性和稽核解決方案**監控身分識別與存取權。 請遵循下列步驟可存取的**身分識別與存取權**的儀表板︰

1. 按一下 [ **Microsoft 作業管理套件**主儀表板中的 [安全性和稽核磚。
2. 中的**安全性和稽核**儀表板中，按一下 [下的 [**安全性網域**的 [**身分識別與存取權**]。 如下所示，就會出現**身分識別與存取權**的儀表板︰

![身分識別與存取權](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

一般監視策略的一部分，您必須包含監控身分識別。 如果有特定正確的使用者名稱含有多嘗試看起來應該 IT 管理員。 這可能表示取得的實際的使用者名稱的任一攻擊，並嘗試暴力或自動工具使用硬式編碼密碼過期的。

此儀表板啟用快速識別與身分識別和存取公司的資源相關的潛在威脅的 IT。 則特定重要也能識別出潛在趨勢，例如在 [登入透過時間] 方塊中，您可以看到某段時間的登入失敗的嘗試執行的次數。 在此情況下電腦**檔案伺服器**收到 35 嘗試登入。 您可以在其上按一下 [瀏覽此電腦的相關的更多詳細資料。 

![更多詳細資料](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

這台電腦所產生的報表顯示此模式的重要詳細資料。 注意到 [**帳戶**] 欄可讓您用來嘗試存取系統的使用者帳戶、 **TIMEGENERATED**欄可讓您在嘗試已完成工作的時間間隔和**LOGONTYPENAME**欄可讓您了此嘗試的位置。 如果這些嘗試執行本機系統中的程式，**程序**] 欄會顯示處理程序的名稱。 在登入嘗試從程式中的所在的情況下，您已經有可用的程序名稱，現在您可以執行進一步調查目標系統。

## <a name="see-also"></a>另請參閱

在此文件中，您學到如何使用 OMS 安全性和稽核監控您的資源的解決方案。 若要進一步瞭解 OMS 安全性，請參閱下列文章︰

- [作業管理套件 (OMS) 概觀](operations-management-suite-overview.md)
- [開始使用作業管理套件安全性與稽核解決方案](oms-security-getting-started.md)
- [監控和回應作業管理套件安全性和稽核方案中的安全性警告](oms-security-responding-alerts.md)