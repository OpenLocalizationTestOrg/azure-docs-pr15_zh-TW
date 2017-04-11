<properties
   pageTitle="應用程式升級︰ 進階主題 |Microsoft Azure"
   description="本文說明一些有關升級的服務布料的轉印圖樣應用程式的進階的主題。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="service-fabric-application-upgrade-advanced-topics"></a>服務布料的轉印圖樣應用程式升級︰ 進階主題

## <a name="adding-or-removing-services-during-an-application-upgrade"></a>新增或移除服務應用程式升級期間

如果已部署，且發佈為升級應用程式新增新服務時，新服務會新增至應用程式中。  這種升級並不會影響任何一項服務已經過的應用程式組件。 不過，您必須先已新增的服務的執行個體啟動設為使用中的新服務 (使用`New-ServiceFabricService`指令程式)。

服務也會從屬於升級應用程式。 然而，所有目前執行個體即將刪除服務必須進行升級之前，先停止 (使用`Remove-ServiceFabricService`指令程式)。 

## <a name="manual-upgrade-mode"></a>手動升級模式

> [AZURE.NOTE]  僅適用於失敗或已擱置升級，應考慮監控手動模式。 監視的模式是服務布料的轉印圖樣應用程式的建議升級模式。

Azure 服務架構提供支援開發和生產叢集的多個升級模式。 選擇的部署選項可能會不同環境不同。

監視循環應用程式升級為最常見的升級生產環境中使用。 指定升級原則時，服務布料的轉印圖樣可確保應用程式健全升級繼續執行之前。

 應用程式管理員可以透過各種升級網域有升級進度的總控制使用手動的循環應用程式升級模式。 這個模式時，自訂或複雜的健康評估原則資格，或非傳統的升級的事情 （例如，應用程式已經資料遺失）。

最後，自動化的循環應用程式升級是很適合用於開發或測試環境，以提供服務開發期間快速反覆運算循環圖。

## <a name="change-to-manual-upgrade-mode"></a>變更為手動升級模式
**手動**-停止在目前的 「 ud 」 應用程式升級，並監控手動變更升級的模式。 管理員必須手動呼叫**MoveNextApplicationUpgradeDomainAsync**進行升級或觸發復原來啟動升級至新的版本。 後升級輸入手動模式時，它會保留在手動模式直到啟動升級至新的版本。 傳回布料的轉印圖樣**GetApplicationUpgradeProgressAsync**命令\_應用程式\_升級\_狀態\_循環\_轉寄\_擱置。

## <a name="upgrade-with-a-diff-package"></a>升級差異套件

可由佈建與完整、 獨立應用程式套件升級服務布料的轉印圖樣應用程式。 也可以使用包含只更新的應用程式的檔案、 應用程式更新資訊清單及服務資訊清單檔案的差異套件升級應用程式。

完整的應用程式套件包含啟動和執行服務布料的轉印圖樣應用程式所需的所有檔案。 差異套件僅包含的檔案的最後一個佈建和目前的升級，之間變更再加上完整的應用程式顯示和服務資訊清單檔案。 在應用程式資訊清單或服務資訊清單建立版面配置中找不到的任何參照搜尋市集中的圖像。

完整的應用程式套件，才能叢集應用程式的第一個安裝。 後續更新可完整的應用程式套件差異。

使用差異套件，就會很不錯的選擇的情況︰

* 您有大型的應用程式套件的參照多個服務的資訊清單檔案及/或多個程式碼套件、 設定套件或資料封包時，偏好差異套件。

* 您有部署系統產生建立版面配置，直接從您的應用程式建立程序之後，就慣用差異套件。 如此一來，即使沒有變更程式碼，新建立的組件會取得不同總和。 使用完整的應用程式套件，會要求您更新上所有的程式碼套件的版本。 使用差異套件，您僅提供變更的檔案及資訊清單檔案版本已變更的位置。

當使用 Visual Studio 升級應用程式時，便會自動發行差異套件。 若要手動建立差異套件，就必須更新應用程式資訊清單，以及服務資訊清單，但已變更的套件應該會包含在最後一個應用程式套件。 

例如，讓我們來啟動與下列應用程式 （版本數字，以便於瞭解提供）︰

```text
app1        1.0.0
  service1  1.0.0
    code    1.0.0
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

現在，假設您想要更新只程式碼的封裝 service1 使用使用 PowerShell 差異套件。 現在，您更新的應用程式有下列資料夾結構︰

```text
app1        2.0.0      <-- new version
  service1  2.0.0      <-- new version
    code    2.0.0      <-- new version
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

在此情況下，您可以更新應用程式顯示 2.0.0，並以反映程式碼套件更新 service1 的服務資訊清單。 應用程式套件的資料夾會有下列結構︰

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>後續步驟

[升級您的應用程式使用 Visual Studio](service-fabric-application-upgrade-tutorial.md)逐步引導您進行使用 Visual Studio 應用程式升級。

[升級您的應用程式使用 Powershell](service-fabric-application-upgrade-tutorial-powershell.md)逐步引導您進行使用 PowerShell 應用程式升級。

控制您的應用程式使用[升級參數](service-fabric-application-upgrade-parameters.md)升級的方式。

學習如何使用[資料序列化](service-fabric-application-upgrade-data-serialization.md)，讓您的應用程式升級相容。

參照中[的疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)的步驟，在應用程式升級中修正常見問題的解決辦法。
 
