<properties
    pageTitle="快速入門 IoT 中心閘道器 SDK |Microsoft Azure"
    description="說明當您使用 Azure IoT 閘道器 SDK，您應該瞭解的重要概念使用 Windows azure IoT 閘道器 SDK 逐步解說。"
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-windows"></a>Azure IoT 閘道器 SDK （beta 版）-開始使用 Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何建立範例

在您開始之前，您必須[設定您的開發環境][lnk-setupdevbox]使用 SDK Windows 上的。

1. 開啟 [**開發人員 VS2015 的命令提示字元**命令提示字元]。
2. 瀏覽至您的本機複本的**azure iot-閘道器 sdk**存放庫中的根資料夾。
3. 執行**工具\\build.cmd**指令碼。 這個指令碼會建立 Visual Studio 解決方案檔案、 建置方案，並執行測試。 **Azure iot-閘道器 sdk**存放庫的本機複本中，您可以在 [**建立**] 資料夾中找到 Visual Studio 方案。

## <a name="how-to-run-the-sample"></a>如何執行範例

1. **Build.cmd**指令碼會建立本機複本存放庫中名為**建立**的資料夾。 這個資料夾包含在此範例中所使用的兩個模組。

    建立指令碼會放在**logger_hl.dll** **建立\\模組\\記錄器\\偵錯**資料夾和**hello_world_hl.dll**中**建立\\模組\\hello_world\\偵錯**資料夾。 使用這些路徑**模組路徑**值 JSON 設定檔案，如下所示。

2. 在 [檔案**hello_world_win.json** **範例\\hello_world\\src**資料夾是範例 JSON 用於設定檔視窗，您可以使用執行範例。 以下所示的範例 JSON 設定假設您複製到**c**磁碟機的根 IoT 閘道器 SDK 存放庫。 如果您下載到另一個位置，您需要調整**模組路徑**值**範例\\hello_world\\src\\hello_world_win.json**據此檔案。

3. **Logger_hl**模組，在 [**引數**] 區段中，將**檔案名稱**時值設為 [名稱] 和 [包含的記錄資料檔案的路徑。

    這是**c**磁碟機的根**log.txt**檔案會寫入的 Windows 版的 JSON 設定檔的範例。

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
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

3. 在命令提示字元中，瀏覽至您的**azure iot-閘道器 sdk**存放庫本機複本的根資料夾。
4. 執行下列命令︰
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md