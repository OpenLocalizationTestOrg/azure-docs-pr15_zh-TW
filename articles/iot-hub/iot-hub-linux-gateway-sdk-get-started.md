<properties
    pageTitle="快速入門 IoT 中心閘道器 SDK |Microsoft Azure"
    description="此 Azure IoT 閘道器 SDK 逐步解說使用 Linux 說明當您使用 Azure IoT 閘道器 SDK，您應該瞭解的重要概念。"
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-linux"></a>Azure IoT 閘道器 SDK （beta 版）-使用 Linux 快速入門

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何建置範例

在您開始之前，您必須[設定您的開發環境][lnk-setupdevbox]使用 linux SDK 的。

1. 開啟命令介面。
2. 瀏覽至您的本機複本的**azure iot-閘道器 sdk**存放庫中的根資料夾。
3. 執行**tools/build.sh**指令碼。 這個指令碼會建立一個稱為**azure iot-閘道器 sdk**存放庫的本機複本的根資料夾中的 [**建立**資料夾，並產生 makefile 使用**cmake**公用程式。 指令碼然後建置解決方案，並執行的測試。

> [AZURE.NOTE]  每次執行**build.sh**指令碼，它刪除，然後重新建立**azure iot-閘道器 sdk**存放庫的本機複本的根資料夾中的 [**建立**] 資料夾。

## <a name="how-to-run-the-sample"></a>如何執行範例

1. **Build.sh**指令碼會產生輸出**azure iot-閘道器 sdk**存放庫的本機複本中的 [**建立**] 資料夾中。 這包含兩個這個範例中使用的模組。

    建立指令碼會放在**liblogger_hl.so** **建立/模組/記錄器/**資料夾和**libhello_world_hl.so**中**建立/模組/hello_world/**資料夾。 使用這些路徑**模組路徑**值 JSON 設定檔案，如下所示。

2. **範例/hello_world/src**資料夾中檔案**hello_world_lin.json**是您可以用來執行範例 Linux 範例 JSON 設定檔。 以下所示的範例 JSON 設定假設您將會從**azure iot-閘道器 sdk**存放庫的本機複本的根目錄執行範例。

3. **Logger_hl**模組，在 [**引數**] 區段中，將**檔案名稱**時值設為 [名稱] 和 [包含的記錄資料檔案的路徑。

    這是 JSON 設定檔案會寫入到您用來執行範例資料夾**log.txt**的 Linux 的範例。

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. 在您的命令介面中瀏覽至**azure iot-閘道器 sdk**資料夾。
4. 執行下列命令︰
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
