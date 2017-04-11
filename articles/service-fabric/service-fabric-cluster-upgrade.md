<properties
   pageTitle="升級 Azure 服務布料的轉印圖樣叢集 |Microsoft Azure"
   description="升級的程式碼服務布料的轉印圖樣及/或執行服務布料的轉印圖樣叢集，包括設定叢集更新模式，升級憑證，新增應用程式的連接埠、 執行 OS 修補程式的設定等等。 執行升級時，您可以預期什麼？"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-an-azure-service-fabric-cluster"></a>升級 Azure 服務布料的轉印圖樣叢集

> [AZURE.SELECTOR]
- [Azure 叢集](service-fabric-cluster-upgrade.md)
- [獨立叢集](service-fabric-cluster-upgrade-windows-server.md)

設計 upgradability 的任何新的系統是產品的達成長期成功，您金鑰。 Azure 服務布料的轉印圖樣叢集是擁有，但只能由 Microsoft 的資源。 本文將說明什麼自動管理及什麼您可以設定您自己的內容。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>控制叢集執行布料的轉印圖樣版本

您可以設定叢集 Microsoft 釋放新版本時收到自動布料的轉印圖樣升級，或者選擇選取您想要在叢集支援布料的轉印圖樣版本。

您只要在入口網站設定 「 upgradeMode 」 叢集設定，或建立或更新版本上即時叢集時使用資源管理員 

>[AZURE.NOTE] 請確定保留叢集一律執行支援布料的轉印圖樣版本。 時，我們宣告服務布料的轉印圖樣的新版本的版本時，先前的版本已標示的支援結束後的 60 天的日期從最小值。 新的版本是公告[服務布料的轉印圖樣小組部落格上](https://blogs.msdn.microsoft.com/azureservicefabric/ )。 使用，然後選擇新的版本。 

執行叢集，發行的到期之前的 14 天健康事件會產生的叢集放警告健康狀態。 支援布料的轉印圖樣版本升級之前，叢集仍會保留在警告狀態。


### <a name="setting-the-upgrade-mode-via-portal"></a>設定升級模式透過入口網站 

當您建立叢集時，您可以自動或手動設定叢集。

![Create_Manualmode][Create_Manualmode]

您可以設定叢集，自動或手動時在即時叢集上，使用 [管理體驗。 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>升級到叢集的設定為手動模式透過入口網站上的新版本。
 
若要升級至新的版本，您只需要是從下拉式清單中選取 [可用的版本，並儲存。 布料的轉印圖樣升級會自動開始。 叢集健康原則 (節點健康狀況和健康情況的組合叢集中執行的所有應用程式) 遵守升級期間。

如果不符合叢集健康原則，就會回復升級。 若要閱讀更多這份文件中向下的捲動如何設定原則，則自訂的狀況。 

一旦您已修正導致復原的問題，您必須按照與之前相同的步驟，再次啟動升級。

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>設定資源管理員範本透過升級模式 

Microsoft.ServiceFabric/clusters 資源定義中加入 「 upgradeMode 」 設定並設定為支援布料的轉印圖樣版本，如下所示的 「 clusterCodeVersion 」，然後部署範本。 「 UpgradeMode 」 有效值為 「 手動 」 或 「 自動 」
 
![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>升級為新的版本設定為手動模式透過資源管理員範本叢集上。
 
手動模式叢集時，若要升級至新的版本，變更是受支援版本的 「 clusterCodeVersion 」，並將其部署。 範本的部署，kicks 布料的轉印圖樣升級的取得開始自動。 叢集健康原則 (節點健康狀況和健康情況的組合叢集中執行的所有應用程式) 遵守升級期間。

如果不符合叢集健康原則，就會回復升級。 若要閱讀更多這份文件中向下的捲動如何設定原則，則自訂的狀況。 

一旦您已修正導致復原的問題，您必須按照與之前相同的步驟，再次啟動升級。

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>指定訂閱的所有環境中取得所有可用的版本清單

執行下列命令，且您應收到輸出如下所示。

「 supportExpiryUtc 」 會告訴您當指定的版即將過期，或已過期。 最新版本沒有有效的日期，-它的值為 「 9999-12-31T23:59:59.9999999 」，這就表示未尚未設定到期日。

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/clusterVersions?api-version= 2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>叢集升級模式時自動布料的轉印圖樣升級行為

Microsoft 維護布料的轉印圖樣程式碼和 Azure 叢集中執行的設定。 我們在需要時執行自動監視的升級軟體。 這些升級可能的程式碼、 設定或兩者。 若要確保您的應用程式有不影響或最小的影響，因為這些升級，我們會執行升級下列階段︰

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>階段 1︰ 使用所有叢集健康原則來執行升級

在此階段，升級進行一個升級的網域一次，繼續執行沒有任何停機時間叢集所執行的應用程式。 叢集健康原則 (節點健康狀況和健康情況的組合叢集中執行的所有應用程式) 遵守升級期間。

如果不符合叢集健康原則，就會回復升級。 然後電子郵件會傳送至訂閱的擁有者。 電子郵件包含下列資訊︰

- 我們已復原叢集升級的通知。
- 建議改善動作，如果有的話。
- 日 (n) 我們在執行階段 2，直到數。

我們嘗試執行相同的升級幾次以防基礎結構的原因的任何升級失敗。 N 天後的電子郵件已傳送的日期，我們會繼續階段 2。

如果符合叢集健康原則，會視為成功升級，並將其標示為完成。 就會發生此期間初始的升級或任何升級重播在此階段。 沒有電子郵件的確認訊息成功執行。 這是為了避免傳送您太多電子郵件-接收電子郵件應視為標準的例外狀況。 我們預期大部分的叢集升級順利完成，而不會影響您的應用程式的可用性。

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>階段 2︰ 使用預設健康原則只來執行升級

在此階段中的狀況原則設定的應用程式的 「 良好的升級開頭的數字保持不變升級程序期間的方式。 階段 1，如階段 2 升級繼續一個升級的網域一次，在叢集所執行的應用程式繼續執行沒有任何停機時間。 叢集健康原則 (節點健康狀況和健康情況的組合叢集中執行的所有應用程式) 遵守升級期間。

如果叢集健康原則作用中不符合，就會回復升級。 然後電子郵件會傳送至訂閱的擁有者。 電子郵件包含下列資訊︰

- 我們已復原叢集升級的通知。
- 建議改善動作，如果有的話。
- 日 (n) 我們在執行階段 3，直到數。

我們嘗試執行相同的升級幾次以防基礎結構的原因的任何升級失敗。 提醒的電子郵件會傳送數天前 n 天設定。 N 天後的電子郵件已傳送的日期，我們繼續階段 3。 我們傳送給您階段 2] 中的電子郵件必須謹慎，必須採取改善動作。

如果符合叢集健康原則，會視為成功升級，並將其標示為完成。 就會發生此期間初始的升級或任何升級重播在此階段。 沒有電子郵件的確認訊息成功執行。

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>階段 3︰ 使用積極健康原則來執行升級

在此階段這些狀況原則都能升級完成，而不是應用程式的狀況。 少數幾份叢集升級最後此階段。 如果您的叢集取得此階段，有可能的應用程式會變成不佳，及/或遺失可用性。

類似的其他兩個階段，階段 3 升級繼續一個升級的網域一次。

如果不符合叢集健康原則，就會回復升級。 我們嘗試執行相同的升級幾次以防基礎結構的原因的任何升級失敗。 之後，叢集現已釘選，以便將不會收到支援及/或升級。

此資訊的電子郵件會傳送至訂閱擁有者，以及改善的動作。 我們不會預期任何叢集取得失敗階段 3 的狀態。

如果符合叢集健康原則，會視為成功升級，並將其標示為完成。 就會發生此期間初始的升級或任何升級重播在此階段。 沒有電子郵件的確認訊息成功執行。

## <a name="cluster-configurations-that-you-control"></a>您可以控制的叢集設定

除了設定叢集的功能升級模式，以下是您可以變更即時叢集的設定。

### <a name="certificates"></a>憑證

您可以新增或刪除叢集，並透過入口網站的用戶端的憑證，輕鬆地。 請參閱[本文件的詳細指示](service-fabric-cluster-security-update-certs-azure.md)

![如果螢幕擷取畫面顯示 [Azure 入口網站中的 [憑證憑證碼。][CertificateUpgrade]


### <a name="application-ports"></a>應用程式的連接埠

您可以藉由變更負載平衡器資源內容類型相關聯的變更應用程式的連接埠。 您可以使用入口網站，或者您可以直接使用資源管理員 PowerShell。

若要開啟新的連接埠上所有的 Vm 節點類型，請執行下列動作︰

1. 新增新的探查的適當的負載平衡器。

    如果您使用入口網站部署叢集，負載平衡器名為 「 LB 名稱的資源群組 NodeTypename 」，一個用於每個節點類型。 由於負載平衡器名稱是唯一的只有在 [資源群組內，將其最適合您搜尋特定的資源群組] 底下。

    ![螢幕擷取畫面顯示探查加負載平衡器在入口網站。][AddingProbes]

2. 新增規則的負載平衡器。

    新增規則到相同的負載平衡器，使用您在上一個步驟中建立探查。

    ![負載平衡器在入口網站中加入新的規則。][AddingLBRules]


### <a name="placement-properties"></a>位置屬性

針對各個節點類型，您可以新增您想要使用應用程式中的自訂位置屬性。 節點類型是您可以使用不用明確的預設屬性。

>[AZURE.NOTE] 使用位置的限制式、 節點屬性及如何定義它們的詳細資訊，請參閱 「 位置的限制式節點中的屬性 」 服務布料的轉印圖樣叢集資源管理員上的文件[描述您叢集](service-fabric-cluster-resource-manager-cluster-description.md)] 區段。

### <a name="capacity-metrics"></a>容量的指標

針對各個節點類型，您可以新增您要在報表載入您的應用程式中使用的自訂容量指標。 如需詳細資料的報表的容量計量使用載入，請參閱[說明您叢集](service-fabric-cluster-resource-manager-cluster-description.md)和[指標與負載](service-fabric-cluster-resource-manager-metrics.md)服務布料的轉印圖樣叢集資源管理員文件。

### <a name="fabric-upgrade-settings---health-polices"></a>布料的轉印圖樣的升級設定-健康原則

您可以指定自訂的健康原則布料的轉印圖樣升級。 如果您已設定叢集自動布料的轉印圖樣升級，這些原則會套用到自動布料的轉印圖樣升級的階段 1。
如果您已設定為手動布料的轉印圖樣叢集升級，這些原則會套用每次選取觸發系統布料的轉印圖樣升級叢集中開始進行新的版本。 如果您不會覆寫原則，則會使用預設值。

您可以指定自訂健康原則，或檢閱的目前設定底下 」 布料的轉印圖樣升級 「 刀中，選取 [進階的升級設定。 檢閱下列如何圖片。 

![管理自訂健康原則][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>自訂您的叢集布料的轉印圖樣設定

請參閱[服務布料的轉印圖樣叢集布料的轉印圖樣設定](service-fabric-cluster-fabric-settings.md)內容和自訂它們。

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>在構成叢集 Vm OS 修補程式

此功能被規劃未來的自動化功能。 但目前，您負責更新您的 Vm。 讓您不會執行下一個以上的時間，您必須執行這個一個 VM 一次。

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>在構成叢集 Vm OS 升級

如果您必須將升級 OS 圖像叢集的虛擬機器上，您必須執行一個 VM 一次。 您負責此升級，目前沒有此沒有自動化。

## <a name="next-steps"></a>後續步驟
- 瞭解如何自訂部分[服務布料的轉印圖樣叢集布料的轉印圖樣設定](service-fabric-cluster-fabric-settings.md)
- 瞭解如何[調整叢集和縮小](service-fabric-cluster-scale-up-down.md)
- 深入瞭解[應用程式升級](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG