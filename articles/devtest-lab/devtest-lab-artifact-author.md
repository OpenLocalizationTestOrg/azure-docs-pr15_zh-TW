<properties 
    pageTitle="建立自訂的成品您 DevTest 實驗室 VM |Microsoft Azure"
    description="瞭解如何製作您自己的成品用於 DevTest 實驗室"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

#<a name="create-custom-artifacts-for-your-devtest-labs-vm"></a>建立自訂的成品您 DevTest 實驗室 VM

> [AZURE.VIDEO how-to-author-custom-artifacts] 

## <a name="overview"></a>概觀
**成品**用來部署及 VM 已佈建後，設定您的應用程式。 成品組成的成品定義檔案及其他指令碼檔案儲存於給存放庫中的資料夾。 成品定義檔案包含 JSON 以及您可以使用，指定您要在 VM 上安裝的運算式。 例如，您可以定義成品、 命令，以執行及，可執行命令時的參數的名稱。 您可以參照其他成品定義檔案中的指令碼檔案名稱。

##<a name="artifact-definition-file-format"></a>成品定義檔案格式
下列範例顯示的建立基本結構定義檔案的區段。

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| 項目名稱 | 需要？ | 描述
| ------------ | --------- | -----------
| $schema      | 無        | 可在測試定義檔案的有效性 JSON 結構描述檔案的位置。
| 標題        | [是]       | 實驗室中顯示的成品的名稱。
| 描述  | [是]       | 實驗室中顯示的成品的描述。
| iconUri      | 無        | Uri 實驗室中顯示的圖示。
| targetOsType | [是]       | VM 成品將會安裝作業系統。 支援的選項︰ Windows 和 Linux。
| 參數   | 無        | 在電腦上執行的成品安裝命令時所提供的值。 這可協助自訂您的成品。
| runcommand] 巨集   | [是]       | 成品安裝 VM 上執行的命令。

###<a name="artifact-parameters"></a>成品參數

在 [定義檔案的 [參數] 區段中，您可以指定哪些安裝成品時，使用者可以輸入的值。 您可以參考以下成品安裝命令中的值。

定義的參數會下列結構。

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| 項目名稱 | 需要？ | 描述
| ------------ | --------- | -----------
| 類型         | [是]       | 輸入參數值]。 請參閱下方允許類型的清單︰
| 顯示名稱是       | 實驗室中的使用者顯示的參數的名稱。
| 描述  | [是]       | 會顯示在實驗室參數的描述。

允許的類型如下︰

- 字串 – 任何有效的 JSON 字串
- int – 任何有效的 JSON 整數
- bool – 任何有效 JSON 布林值
- 陣列 – 任何有效的 JSON 陣列

##<a name="artifact-expressions-and-functions"></a>成品運算式及函數

您可以使用運算式及函數來建構成品安裝命令。
使用括弧括住的運算式 （[和]），會評估已安裝的成品。 運算式可以出現 JSON 字串值中的任何位置，並傳回另一個 JSON 值。 如果您需要使用文字字串的開始使用括號 [，您必須使用兩個括號 [[。
一般而言，您使用的運算式函數來建構值。 就像在 JavaScript] 函數呼叫會格式化為 functionName(arg1,arg2,arg3)

下列清單會顯示常用的功能。

- parameters(parameterName)-傳回成品指令執行提供的參數值。
- concat 函數 （arg1 arg2 參數、 arg3，）-結合多個字串值。 此函數可採取任何引數的數字。

下列範例會示範如何使用運算式和函數來建構值。

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

##<a name="create-a-custom-artifact"></a>建立自訂的成品

建立自訂的成品遵循下列步驟︰

1. 安裝 JSON 編輯器-您必須使用成品定義檔案 JSON 編輯器。 我們建議使用[Visual Studio 程式碼](https://code.visualstudio.com/)，這適用於 Windows、 Linux 和 OS X。

1. 範例 artifactfile.json-取出，我們已在其中建立豐富的成品可協助您我們[GitHub 存放庫](https://github.com/Azure/azure-devtestlab)Azure DevTest 實驗室小組所建立的成品建立您自己的成品。 下載的成品定義檔案，並建立您自己的成品進行變更。

1. 請使用 IntelliSense-使用 IntelliSense，若要查看可以用來建構成品定義檔案的有效項目。 您也可以查看項目的值不同的選項。 例如，IntelliSense 顯示您的 Windows 或 Linux 的兩個選擇編輯**targetOsType**項目時。

1. 給存放庫中儲存的成品
    1. 建立另一個位置的目錄名稱是成品名稱相同的每個成品的目錄。
    1. 儲存您所建立的目錄中的成品定義檔案 (artifactfile.json)。
    1. 儲存被參考的成品安裝命令指令碼。

    以下是範例成品] 資料夾可能的外觀︰

    ![成品給 repo 範例](./media/devtest-lab-artifact-author/git-repo.png)

1. 新增成品存放庫實驗室来參照的文件[新增至實驗室給成品存放庫](devtest-lab-add-artifact-repo.md)。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關的部落格文章
- [如何疑難排解失敗 AzureDevTestLabs 的成品](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [加入 VM 至現有的 AD 網域 Azure 開發測試實驗中使用手錶範本](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>後續步驟

- 瞭解如何[新增至實驗室給成品存放庫](devtest-lab-add-artifact-repo.md)。
