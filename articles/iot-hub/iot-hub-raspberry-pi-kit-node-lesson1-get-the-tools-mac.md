<properties
 pageTitle="取得工具 (macOS 10.10) |Microsoft Azure"
 description="下載並安裝必要的工具和您 Pi 的第一個範例應用程式軟體 macOS 上。"
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="12-get-the-tools-macos-1010"></a>1.2 取得工具 (macOS 10.10)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="121-what-you-will-do"></a>1.2.1 您將會執行的動作

下載開發工具和您覆盆子 Pi 3 的第一個範例應用程式的軟體。 如果您符合任何問題，請搜尋中的[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)的解決方案。

## <a name="122-what-you-will-learn"></a>1.2.2 您將學習什麼
在此區段中，您將瞭解︰

- 如何安裝給和 Node.js
    - [給](https://git-scm.com)是開啟來源分散式版本控制系統。 本課程中的範例應用程式會儲存在給。
    - [Node.js](https://nodejs.org/en/)是使用 rtf 套件生態 JavaScript 執行階段。
- 如何使用 NPM 安裝其他 Node.js 開發工具。
  - 必要的最低 Node.js 新版是 4.5 LTS。
  - [NPM](https://www.npmjs.com)是套件的經理 Node.js。

## <a name="123-what-you-need"></a>1.2.3 您需要的什麼

- 若要下載的開發工具與軟體網際網路連線
- Mac 執行 macOS Yosemite (10.10) 或更新版本

## <a name="124-install-git-and-nodejs"></a>1.2.4 安裝給和 Node.js

若要安裝給和 Node.js，使用[Homebrew](http://brew.sh)套件管理公用程式遵循下列步驟︰

1. 安裝 Homebrew。 如果您已經安裝 Homebrew，請移至步驟 2。
  1. 按下`Cmd + Space`輸入`Terminal`開啟終端機視窗。
  2. 執行下列命令︰

    ```bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
2. 安裝給和 Node.js 藉由執行下列命令︰

    ```bash
    brew install node git
    ```

## <a name="125-install-additional-nodejs-development-tools"></a>1.2.5 安裝其他 Node.js 開發工具

您可以使用[gulp.js](http://gulpjs.com)來自動化您 Pi 的範例應用程式的部署。 您也可以使用[的裝置-探索-cli](https://github.com/Azure/device-discovery-cli)擷取您 IoT 和裝置有關的網路資訊。

安裝`gulp`和`device-discovery-cli`終端機視窗中執行下列命令︰

```bash
sudo npm install -g device-discovery-cli gulp
```

如果您遇到問題上 macOS 安裝 Node.js 和這些其他開發工具，請參閱[疑難排解指南](iot-hub-raspberry-pi-kit-node-troubleshooting.md)常見問題的解決方案。

## <a name="126-install-visual-studio-code"></a>1.2.6 安裝 Visual Studio 程式碼

[下載](https://code.visualstudio.com/docs/setup/osx)並安裝 Visual Studio 程式碼。 Visual Studio 程式碼是 Windows、 Linux 和 macOS 輕量型但功能強大的來源程式碼編輯器。 您可以稍後教學課程中，使用這個編輯器來編輯程式碼範例。

## <a name="127-summary"></a>1.2.7 摘要

您已安裝必要的開發工具和軟體的第一個範例應用程式。 在下一個區段中，您可以建立、 部署及您 Pi 上執行的範例應用程式。

## <a name="next-steps"></a>後續步驟

[1.3 建立並部署閃爍範例應用程式](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)
