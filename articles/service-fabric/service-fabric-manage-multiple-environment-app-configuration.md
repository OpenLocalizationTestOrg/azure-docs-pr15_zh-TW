<properties
   pageTitle="管理多個環境中服務布料的轉印圖樣 |Microsoft Azure"
   description="服務布料的轉印圖樣應用程式可以叢集，範圍從一部電腦的大小數以千計的電腦上執行。 在某些情況下，您會想要設定不同的這些不同環境的應用程式。 本文說明如何定義每環境不同的應用程式參數。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/19/2016"
   ms.author="seanmck"/>

# <a name="manage-application-parameters-for-multiple-environments"></a>管理多個環境的應用程式參數

您可以建立 Azure 服務布料的轉印圖樣叢集一數千機器到任何位置使用。 應用程式的二進位檔案可以修改而多個執行此寬範圍的環境，而您經常要根據您部署至電腦的數目不同，設定應用程式。

簡單範例，請考慮`InstanceCount`無狀態的服務。 當您執行的 Azure 中的應用程式時，您通常要將此參數設特殊 」-1] 的值。 如此一來，可確保您的服務正在執行的每個叢集節點。 不過，此設定不適合用於單電腦叢集因為您不能有相同的端點上一部電腦上接聽的多個程序。 不過，您通常會設定`InstanceCount`為 「 1 」。

## <a name="specifying-environment-specific-parameters"></a>指定特定環境的參數

此設定問題的解決方案是一組參數的預設服務及那些的參數值在特定的環境中填滿的應用程式參數檔案。 應用程式和服務的資訊清單中設定預設的服務與應用程式參數。 ServiceManifest.xml 和 ApplicationManifest.xml 檔案的結構描述定義已安裝的服務布料的轉印圖樣 SDK 和*C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*的工具。

### <a name="default-services"></a>預設的服務

服務布料的轉印圖樣應用程式所組成服務執行個體的集合。 雖然您可以建立空白的應用程式，然後建立動態所有服務執行個體，大部分的應用程式會有一組的核心服務時，應該一律建立應用程式會產生的。 這些被稱為 「 預設的服務]。 指定應用程式顯示的 [版面配置區以方括弧括住所包含的每個環境設定為使用中︰

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

每個命名參數必須定義在應用程式顯示參數項目︰

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

DefaultValue 屬性會指定要使用更多特定參數沒有指定環境的值。

>[AZURE.NOTE] 並非所有服務的執行個體參數都是適用於每個環境設定。 在上述範例中，服務的分割配置的 LowKey 和 HighKey 值明確定義的所有執行個體服務由於分割範圍的資料網域，而不是環境函數。


### <a name="per-environment-service-configuration-settings"></a>每個環境服務設定的設定

[服務布料的轉印圖樣應用程式模型](service-fabric-application-model.md)可讓服務包含設定套件包含自訂執行階段可讀取的關鍵值組。 這些設定的值可也區分環境所指定`ConfigOverride`應用程式資訊清單中。

假設您有下列設定的 Config\Settings.xml 檔案中`Stateful1`服務︰


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

若要覆寫特定的應用程式環境對此值，請建立`ConfigOverride`當您匯入服務中的資訊清單應用程式顯示。

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

然後您可以將此參數設定環境，如上所示。 您可以依宣告它的應用程式顯示的 [參數] 區段中，並在應用程式參數檔案中指定環境專屬的值。

>[AZURE.NOTE] 若是 [服務設定的設定，有三個機碼的值，可設定的位置︰ 服務設定封裝、 應用程式資訊清單和應用程式參數檔案。 服務布料的轉印圖樣一律會從應用程式參數檔案選擇第一個 （如果有指定），然後應用程式資訊清單，以及最後設定套件。


### <a name="application-parameter-files"></a>應用程式參數檔案

服務布料的轉印圖樣應用程式專案可以包含一或多個應用程式參數檔案。 每個定義的參數，應用程式清單中所定義的特定值︰

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

根據預設，新的應用程式會包含兩個應用程式參數檔案，「 Local.xml Cloud.xml:

![在方案總管中的應用程式參數檔案][app-parameters-solution-explorer]

若要建立新的參數檔案，只要複製並貼上現有的新名稱。

## <a name="identifying-environment-specific-parameters-during-deployment"></a>在部署識別環境專用的參數

部署時，您需要選擇適當的參數檔案]，套用您的應用程式。 您可以透過在 Visual Studio 中的 [發佈] 對話方塊中，或透過 PowerShell 來執行此動作。

### <a name="deploy-from-visual-studio"></a>從 Visual Studio 中部署

當您在 Visual Studio 中發佈您的應用程式時，您可以選擇從可用的參數檔案清單。

![選擇 [發佈] 對話方塊中的參數檔案][publishdialog]

### <a name="deploy-from-powershell"></a>從 PowerShell 部署

`Deploy-FabricApplication.ps1`應用程式專案範本中所包含的 PowerShell 指令碼接受做為參數，發佈設定檔並 PublishProfile 包含應用程式參數檔案的參考。

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>後續步驟

若要進一步瞭解在本主題中所討論的核心概念部分，請參閱[服務布料的轉印圖樣技術概觀](service-fabric-technical-overview.md)。 Visual Studio 中可用的其他應用程式管理功能的相關資訊，請參閱[管理您的服務布料的轉印圖樣應用程式，在 Visual Studio 中](service-fabric-manage-application-in-visual-studio.md)。

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
