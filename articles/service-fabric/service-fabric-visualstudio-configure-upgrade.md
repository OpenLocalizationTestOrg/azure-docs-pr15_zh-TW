<properties
   pageTitle="設定服務布料的轉印圖樣應用程式的升級 |Microsoft Azure"
   description="瞭解如何使用 Microsoft Visual Studio 升級服務布料的轉印圖樣應用程式的設定。"
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/29/2016"
   ms.author="cawa" />

# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>在 Visual Studio 中設定服務布料的轉印圖樣應用程式的升級

Azure 服務布料的轉印圖樣的 visual Studio 工具的發佈至本機或遠端叢集提供升級的支援。 有兩個升級至新版，而不是取代期間測試和偵錯應用程式的應用程式的優點︰

- 應用程式資料升級期間將不會遺失。
- 顯示狀態，因而高無法在升級期間，任何服務中斷如果有足夠的服務執行個體分散到升級的網域。

正在升級時，可以針對應用程式執行測試。

## <a name="parameters-needed-to-upgrade"></a>若要升級所需要的參數

您可以選擇兩種類型的部署︰ 一般或升級。 一般的部署會清除任何先前的部署資訊和叢集上的資料時的升級的部署保留它。 當您升級 Visual Studio 中的服務布料的轉印圖樣應用程式時，您需要提供應用程式升級參數和健康狀況] 核取原則。 應用程式升級參數協助控制升級時狀況核取原則可讓您決定是否順利完成升級。 請參閱[服務布料的轉印圖樣應用程式升級︰ 升級參數](service-fabric-application-upgrade-parameters.md)如需詳細資訊。

有三種升級模式︰*監視*、 *UnmonitoredAuto*及*UnmonitoredManual*。

  - 監視升級作業自動化升級和應用程式的狀況檢查。

  - UnmonitoredAuto 升級升級，會自動執行，但略過應用程式的狀況檢查。

  - 當您執行 UnmonitoredManual 升級時，必須手動升級的每個網域。

每個升級的模式需要組不同的參數。 若要進一步瞭解可用的升級選項的 [[應用程式升級參數](service-fabric-application-upgrade-parameters.md)，請參閱。

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>升級 Visual Studio 中的服務布料的轉印圖樣應用程式

如果您使用的升級的服務布料的轉印圖樣應用程式的 Visual Studio 服務布料的轉印圖樣工具，您可以指定發佈程序會升級，而不是一般的部署，請核取 [**升級應用程式**] 核取方塊。

### <a name="to-configure-the-upgrade-parameters"></a>若要設定的升級的參數

1. 按一下 [**設定**] 按鈕旁的核取方塊。 **編輯升級參數**] 對話方塊隨即出現。 **編輯升級參數**] 對話方塊支援監視、 UnmonitoredAuto 及 UnmonitoredManual 升級模式。

2. 選取您想要使用，然後再填入參數格線的升級模式。

    每個參數有預設值。 選用的參數*DefaultServiceTypeHealthPolicy*會雜湊資料表輸入。 以下是*DefaultServiceTypeHealthPolicy*雜湊表格輸入格式的範例︰

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap*是以下列格式的雜湊資料表輸入另一個選擇性參數︰

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    以下是真實範例︰

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```

3. 如果您選取 UnmonitoredManual 升級模式時，您必須以手動方式啟動 PowerShell 主控台] 以繼續，並完成升級程序。 請參閱[服務布料的轉印圖樣應用程式升級︰ 進階主題](service-fabric-application-upgrade-advanced.md)若要瞭解如何手動升級的運作。

## <a name="upgrade-an-application-by-using-powershell"></a>使用 PowerShell 升級應用程式

您可以使用 PowerShell cmdlet 來升級的服務布料的轉印圖樣應用程式。 如需詳細資訊，請參閱[升級教學課程的服務布料的轉印圖樣應用程式](service-fabric-application-upgrade-tutorial.md)，並[開始 ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) 。

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>應用程式資訊清單檔案中指定的狀況檢查原則

在服務布料的轉印圖樣應用程式中的每個服務的專屬狀況原則參數會覆寫預設值。 您可以提供這些應用程式資訊清單檔案中的參數值。

下列範例會示範如何套用應用程式顯示在每個服務的唯一的狀況核取原則。

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>後續步驟
如需有關部署應用程式的詳細資訊，請參閱[部署現有的應用程式中 Azure 服務布料的轉印圖樣](service-fabric-deploy-existing-app.md)。
