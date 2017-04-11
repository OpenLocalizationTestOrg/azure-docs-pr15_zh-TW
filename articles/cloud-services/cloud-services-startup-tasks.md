<properties 
pageTitle="Azure 雲端服務中執行啟動工作 |Microsoft Azure" 
description="啟動工作可協助您的應用程式的雲端服務環境準備作業。 這會指導您啟動工作的運作方式，以及如何進行" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>



# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>如何設定和執行的雲端服務的啟動工作

您可以使用啟動工作之前角色開始執行作業。 您可能會想要執行的作業包含安裝元件、 登錄 COM 元件、 設定登錄機碼，或啟動長時間執行的程序。

>[AZURE.NOTE] 啟動工作不適用虛擬機器、 僅供雲端服務網頁，工作者角色。

## <a name="how-startup-tasks-work"></a>啟動工作的運作方式

啟動工作是您的角色開始，並利用[ServiceDefinition.csdef]檔案中的[啟動]項目內的[工作]項目之前所採取的動作。 經常啟動工作批次檔案，但也可以是主控台應用程式或啟動 PowerShell 指令碼的批次檔案。

環境變數會傳送資訊至啟動工作，並可用於本機存放區傳遞資訊不啟動工作。 例如，環境變數可以指定您想要安裝的程式的路徑，並且可以再稍後讀取您的角色的本機存放區寫入檔案。

啟動工作可以登**TEMP**環境變數所指定的目錄資訊和錯誤。 在啟動工作， **TEMP**環境變數會解析成*c:\\資源\\temp\\[guid]。 [角色名稱]\\RoleTemp*雲端上執行時的目錄。

啟動工作也可執行數次重新開機之間。 例如，角色回收，每次會執行啟動工作和角色回收可能不會永遠包含重新啟動電腦。 啟動工作應該撰寫的方式，讓他們的問題不執行數次。

啟動工作必須以**errorlevel** （或結束的程式碼） 為零的啟動程序完成結尾。 如果使用非零**errorlevel**啟動任務，角色不會啟動。


## <a name="role-startup-order"></a>角色啟動順序

以下列出 Azure 中的角色啟動程序︰

1. 執行個體標示為**開頭**，而且不會收到流量。

2. 所有的啟動工作的執行根據其**taskType**屬性。
    - **簡單**的任務同步，執行一次。
    - **背景**與**前景**工作啟動非同步、 平行啟動工作。  
       
    > [AZURE.WARNING] IIS 可能不是完全設定期間啟動工作階段中的啟動程序，因此可能無法使用特定角色的資料。 需要特定角色的資料啟動工作應該使用[Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx)。

3. 角色主機處理程序會啟動，IIS 中建立的網站。

4. 呼叫[Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx)方法。

5. 執行個體標記為**準備**及執行個體路由流量。

6. 呼叫[Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法。


## <a name="example-of-a-startup-task"></a>啟動工作的範例

啟動工作是在 [ [ServiceDefinition.csdef]檔案] 中的**工作**項目所定義。 **命令列**屬性指定 [名稱] 和 [啟動批次檔案的參數或主控台] 命令，[ **executionContext**屬性指定權限層級的啟動工作，而**taskType**屬性指定會執行工作的方式。

在此範例中，會環境變數， **MyVersionNumber**，建立啟動工作，並設定為 「**1.0.0.0**」 的值。

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

在下列範例中， **Startup.cmd**批次檔案會寫入線條 」 目前使用的是 1.0.0.0 」 StartupLog.txt 檔案中的 TEMP 環境變數所指定的目錄。 `EXIT /B 0`線條，可確保啟動工作句**errorlevel**為零。

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] 在 Visual Studio 中，[啟動批次檔案**複製到輸出目錄**] 屬性應該設為**一律複本**以確定您啟動批次檔案已適當部署至 Azure 在專案 (**approot\\筒**Web 角色和**approot**的工作者角色)。

## <a name="description-of-task-attributes"></a>工作屬性的描述

以下說明**工作**中的項目[ServiceDefinition.csdef]檔案的屬性︰

**命令列**-指定的命令列啟動工作︰

- 使用指令選用的命令列參數，一開始啟動工作。
- 這通常是.cmd 或.bat 批次檔案的檔案名稱。
- 任務是相對於 AppRoot\\Bin 資料夾，以供部署。 環境變數不會展開判斷路徑和檔案的工作。 如果需要環境擴充，您可以建立打電話給您啟動工作的小型.cmd 指令碼。
- 可以是主控台應用程式或啟動的[PowerShell 指令碼](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)批次檔案。

**executionContext** -指定啟動工作的權限等級。 可以有限制或更高的權限等級︰

- **有限**  
啟動工作會執行相同的權限的角色。 也**有限**[執行階段]項目的**executionContext**屬性時，會使用使用者權限。

- **提高權限**  
啟動工作執行具有管理員權限。 這個選項可讓啟動工作安裝程式、 進行變更 IIS 設定，執行登錄變更及其他管理員層級工作，而不增加本身的角色的權限等級。  

> [AZURE.NOTE] 啟動工作的權限等級不需要本身的角色相同。

**taskType** -指定執行啟動工作的方式。

- **簡單**  
工作的執行同步，一次，順序[ServiceDefinition.csdef]檔案中指定。 一個**簡單**的啟動工作結束時使用**errorlevel**為零，則會執行下一個**簡單**的啟動工作。 如果有任何其他**簡單**啟動来執行的工作，請將會啟動本身的角色。   

    > [AZURE.NOTE] 如果使用非零**errorlevel****簡單**的工作，則會遭到封鎖執行個體。 不會啟動後續**簡單**啟動工作] 和 [本身的角色。

    若要確保批次檔案句**errorlevel**為零，執行命令`EXIT /B 0`批次檔案程序的結尾。

- **背景**  
與角色的啟動平行非同步，執行工作。

- **前景**  
與角色的啟動平行非同步，執行工作。 **前景**] 與 [**背景**工作之間的主要差異，是**前景**任務可以防止回收或正在關閉，直到已結束工作的角色。 **背景**的工作並沒有這項限制。

## <a name="environment-variables"></a>環境變數

環境變數是將啟動任務資訊的方法。 例如，您可以將路徑 blob 包含程式安裝，或會使用您的角色的連接埠號碼或設定控制項的啟動工作的功能。

有兩種環境變數啟動工作。靜態環境變數和環境變數根據[RoleEnvironment]類別的成員。 都[ServiceDefinition.csdef]檔案的[環境]] 區段中，同時使用[可變]的項目和**名稱**屬性。

靜態環境變數使用**value**屬性的[變數]項目。 上述範例中所建立的環境變數**MyVersionNumber**靜態值為 「**1.0.0.0**」。 另一個範例就是建立您可以手動設定為 「**臨時**」 或 「**產品**」 的值，來執行**StagingOrProduction**環境變數值所根據的不同啟動動作**StagingOrProduction**環境變數。

根據 RoleEnvironment 類別的成員的環境變數，請勿使用**value**屬性的[變數]項目。 不過， [RoleInstanceValue]子項目，以適當的**XPath**屬性值，會用來建立根據特定類別的一個成員[RoleEnvironment]環境變數。 若要存取各種[RoleEnvironment]值**XPath**屬性的值可找到[以下](cloud-services-role-config-xpath.md)。



例如，若要建立環境變數在雲端中執行時，執行個體在 [計算模擬器] 和 ["**false**"中執行時的 「**true**」，使用下列的[變數]和[RoleInstanceValue]元素︰

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>後續步驟
了解如何執行雲端服務的一些[啟動的一般工作](cloud-services-startup-tasks-common.md)。

[封裝](cloud-services-model-and-package.md)雲端服務。  


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[任務]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[啟動]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[執行階段]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[環境]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[變數]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx