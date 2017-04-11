<properties
    pageTitle="Azure 函數開發人員參考 |Microsoft Azure"
    description="瞭解 Azure 函數的概念，以及所有的語言與繫結常見元件。"
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="azure 函數、 函數、 事件處理、 webhooks、 動態計算、 無架構"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-developer-reference"></a>Azure 函數開發人員參考

Azure 函數共用一些技術的核心概念和元件，無論您使用的繫結或語言。 您跳到特定語言或繫結的詳細資料之前，請務必詳閱所有適用於此概觀。

本文假設您已閱讀[Azure 函數概觀](functions-overview.md)並熟悉[WebJobs SDK 概念，例如引動程序、 繫結及 JobHost 執行階段](../app-service-web/websites-dotnet-webjobs-sdk.md)。 Azure 函數會根據 WebJobs SDK。 


## <a name="function-code"></a>函數的程式碼

*函數*是主要 Azure 函數的概念。 您在您所選擇的語言撰寫函數的程式碼，並將碼檔案，以及設定檔案儲存在同一個資料夾。 設定中 JSON，而檔案命名為`function.json`。 支援各種不同的語言，且每個位置略有不同的體驗最佳化，最適合使用該語言。 

`function.json`檔案包含函數，包括其繫結至特定的設定。 執行階段讀取此檔案來決定要關閉的觸發事件時呼叫函數，以及傳送資料的位置包含的資料傳遞函數本身。 

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

您可以防止設定來執行函數執行階段`disabled`屬性設定為`true`。

`bindings`屬性是您可在此設定引動程序和繫結。 每個繫結這幾個常見的設定，也就是特定類型的繫結的特定部分設定。 每個繫結需要下列設定︰

|屬性|值/類型|註解|
|---|-----|------|
|`type`|字串|繫結類型。 例如， `queueTrigger`。
|`direction`|「 」、 「 輸出 」| 指出是否接收到函數的資料，或從功能傳送資料繫結。
| `name` | 字串 | 用於繫結的資料在函數中的名稱。 對於 C# 這是引數名稱。JavaScript 它位於金鑰/值清單的按鍵。

## <a name="function-app"></a>函數應用程式

在函數應用程式被組成一或多個受一起 Azure 應用程式服務的個別函數。 所有函數函數應用程式中的共用相同的價格計劃、 連續的部署及執行階段版本。 多語言撰寫的函數可以共用相同的函數應用程式。 用來組織及共同管理您的函數將函數應用程式。 

## <a name="runtime-script-host-and-web-host"></a>執行階段 （指令碼裝載及網頁主機）

執行階段或指令碼主機是基礎 WebJobs SDK 主機接聽事件、 收集並傳送資料，並執行程式碼。 

若要加快 HTTP 引動程序，也有這設計用來坐在生產案例中的指令碼主機前面的 web 主機。 這可協助隔離指令碼主機管理的網站 」 主機的前端流量。

## <a name="folder-structure"></a>資料夾結構

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

時設定 Azure 應用程式服務中的函數應用程式部署函數的專案，您可以將此資料夾結構視為作為網站程式碼。 您可以使用現有的工具，例如持續整合及部署中，或自訂部署指令碼的實際操作部署時間套件安裝或程式碼 transpilation。

>[AZURE.NOTE] `wwwroot`以下的資料夾會取得部署至您的檔案。 不過，您必須包含該資料夾中的檔案部署時，會得到`wwwroot\wwwroot`。 不過，您`host.json`直接在您的部署的根目錄應該是檔案] 和 [函數] 資料夾。

## <a id="fileupdate"></a>如何更新函數應用程式檔案

Azure 入口網站內建函數編輯器，可讓您更新*function.json*檔案] 和 [函數程式碼檔案。 若要上傳或更新其他檔案，例如*package.json*或*project.json*或相依性，您必須使用其他部署方法。

函數應用程式的內建應用程式服務，所以的所有[可用的標準 web 應用程式的部署選項](../app-service-web/web-sites-deploy.md)可供函數應用程式。 以下是一些您可以使用上傳或更新函數應用程式檔案的方法。 

#### <a name="to-use-app-service-editor"></a>若要使用應用程式服務編輯器

1. 在 Azure 函數入口網站中，按一下 [**函數應用程式設定**]。

2. 在 [**進階設定**] 區段中，按一下 [**移至 [應用程式服務設定**]。

3. 按一下 [**開發工具**] 底下的 [**應用程式服務編輯器**中的應用程式功能表導覽]。

4.  按一下**[**]。

    應用程式服務編輯器載入後，您會看到*wwwroot*下的*host.json*檔案和 [函數] 資料夾。 

5. 開啟要編輯，或從拖放您的部署電腦上傳檔案的檔案。

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>若要使用的函數應用程式 SCM (Kudu) 端點

1. 瀏覽至︰ `https://<function_app_name>.scm.azurewebsites.net`。

2. 按一下 [**偵錯主控台 > CMD**。

3. 瀏覽至`D:\home\site\wwwroot\`更新*host.json*或`D:\home\site\wwwroot\<function_name>`更新函數的檔案。

4. 並拖曳您想要上傳至適當的資料夾，在 [檔案] 方格中的檔案。 在您將可以拖放檔案的檔案格線中有兩個區域。 用於*.zip*檔案，會出現的方塊以標籤 」 拖曳這裡上傳並將它解壓縮。 」 其他檔案類型，拖放在 [檔案] 方格中，但 [解壓縮] 方塊外面。

#### <a name="to-use-ftp"></a>若要使用 FTP

1. 依照指示進行[以下](../app-service-web/web-sites-deploy.md#ftp)取得設定 FTP。

2. 當您已連線至函數應用程式的網站時，更新的*host.json*檔案複製到`/site/wwwroot`或函數將檔案複製到`/site/wwwroot/<function_name>`。

#### <a name="to-use-continuous-deployment"></a>若要使用連續的部署

請遵循主題[連續部署 Azure 函數](functions-continuous-deployment.md)中的指示進行。

## <a name="parallel-execution"></a>平行執行

多個觸發事件發生時單一階層式函數執行階段可以處理速度，執行階段可以叫用多次中平行的函數。  如果函數應用程式使用的[動態服務方案](functions-scale.md#dynamic-service-plan)，函數應用程式無法自動縮放出。  使用函數的應用程式，每個執行個體是否在動態的服務方案或一般[應用程式服務方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)，請執行應用程式可能會處理同時函數引動，同時使用多執行緒。  在每個函數應用程式執行個體中的一個函數呼叫數目上限有所不同函數應用程式的記憶體大小。 

## <a name="azure-functions-pulse"></a>Azure 函數 Pulse  

脈衝是即時活動串流會顯示您函數的執行頻率，以及成功和失敗。 您也可以監視您平均的執行時間。 我們會加入更多的功能及自訂一段時間。 您可以從 [**監控**] 索引標籤存取**脈衝**頁面。

## <a name="repositories"></a>存放庫

Azure 函數的程式碼開啟來源，並儲存在 GitHub 存放庫︰

* [Azure 函數執行階段](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure 函數入口網站](https://github.com/projectkudu/AzureFunctionsPortal)
* [Azure 函數範本](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 副檔名](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>繫結

以下是支援的所有繫結的資料表。

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>報告問題

[AZURE.INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)] 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源︰

* [Azure 函數 C# 開發人員參考](functions-reference-csharp.md)
* [Azure 函數 F # 開發人員參考](functions-reference-fsharp.md)
* [Azure 函數 NodeJS 開發人員參考](functions-reference-node.md)
* [Azure 函數引動程序及連結](functions-triggers-bindings.md)
* [Azure 函數︰ 路](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/)Azure 應用程式服務小組部落格上。 歷程記錄的 Azure 函數所開發的方式。





