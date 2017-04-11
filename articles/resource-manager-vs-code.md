<properties
   pageTitle="使用與程式碼資源管理員範本 |Microsoft Azure"
   description="顯示如何建立 Azure 資源管理員範本設定 Visual Studio 程式碼。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="chmatsk;tomfitz"/>

# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>使用 Visual Studio 程式碼中的 Azure 資源管理員範本

Azure 資源管理員範本是描述資源和相關的相依性的 JSON 檔案。 這些檔案有時很大，因此務必工具支援複雜。 Visual Studio 程式碼是新精簡、 開啟來源、 跨平台的程式碼編輯器。 支援建立和編輯資源管理員範本透過[新的副檔名](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)。 與程式碼會執行任何位置，並不需要存取網際網路，除非您也想要部署資源管理員範本。

如果您還沒有與程式碼，您可以在[https://code.visualstudio.com/](https://code.visualstudio.com/)來進行安裝。

## <a name="install-the-resource-manager-extension"></a>安裝副檔名為資源管理員

若要使用 JSON 範本與程式碼中，您必須安裝副檔名。 下列步驟下載並安裝的語言支援資源管理員 JSON 範本︰

1. 啟動與程式碼 
2. 開啟快速開啟 (Ctrl + P) 
3. 執行下列命令︰ 

        ext install azurerm-vscode-tools

4. 重新啟動與程式碼當畫面提示您啟用延伸模組。 

 完成的工作 ！

## <a name="set-up-resource-manager-snippets"></a>設定資源管理員的程式碼片段

先前的步驟安裝工具的支援，但現在要設定與程式碼，若要使用 JSON 範本片段。

1. 從[azure xplat 雲端世界-工具](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json)存放庫的檔案的內容複製到剪貼簿中。
2. 啟動與程式碼 
3. 與程式碼中，您可以開啟 JSON 程式碼片段檔案，[瀏覽至**檔案** -> **喜好設定** -> **使用者程式碼片段** -> **JSON**，或您可以選取**F1** ，輸入**喜好設定**，直到您可以選取**喜好設定︰ 程式碼片段**。

    ![喜好設定程式碼片段](./media/resource-manager-vs-code/preferences-snippets.png)

    從選項中，選取 [ **JSON**]。

    ![選取 json](./media/resource-manager-vs-code/select-json.png)

4. 貼上在步驟 1 到使用者的程式碼片段檔案之前最終檔案的內容 」} 」 
5. 請確定 JSON 正確無誤，而且沒有不規則曲線任何一處。 
6. 儲存並關閉使用者的程式碼片段檔案。

這是需要即可開始使用的資源管理員程式碼片段。 接下來，我們會將此設定放到測試。

## <a name="work-with-template-in-vs-code"></a>使用範本與程式碼

若要開始使用範本的最簡單方法是其中一個快速開始可用的範本上[Github](https://github.com/Azure/azure-quickstart-templates) ，或使用您自己。 您可以輕鬆地[匯出範本](resource-manager-export-template.md)的資源群組透過入口網站。 

1. 如果您從 [資源群組匯出為範本，請與程式碼中開啟檔案。

    ![顯示的檔案](./media/resource-manager-vs-code/show-files.png)

2. 開啟 template.json 檔案，讓您可以編輯並新增一些額外的資源。 之後**「 資源 」: [**按下 enter，若要開始新的一行。 如果您輸入**arm**，就會看到的選項清單。 這些選項是您安裝的範本程式碼片段。 它看起來應該像這樣︰ 

    ![顯示的程式碼片段](./media/resource-manager-vs-code/type-snippets.png)

3. 選擇您想要的程式碼片段。 在本文中，選擇要建立新的公用 IP 位址**手臂 ip** 。 右方括弧後面加上逗號 」} 「 新建立的資源，以確定您的範本的語法不正確。

     ![新增逗號](./media/resource-manager-vs-code/add-comma.png)

4. 與程式碼具有內建的 IntelliSense。 當您編輯您的範本，與程式碼會建議可用的值。 例如，若要新增您的範本變數] 區段，新增**」 「** （兩個雙引號），然後選取這些報價之間的**Ctrl + 空格鍵**。 您會看到選項包括**變數**。

    ![新增變數](./media/resource-manager-vs-code/add-variables.png)

5. 可用的值或函數，也可以使用建議 IntelliSense。 若要設定參數值] 屬性，請使用**「 」**和**Ctrl + 空格鍵**建立運算式。 您可以開始輸入的函數名稱。 當您找到想要的函數，請選取 [**索引標籤**。

    ![新增參數](./media/resource-manager-vs-code/select-parameters.png)

6. 函數，查看您的範本中可用的參數的清單中，再次選取**Ctrl + 空格鍵**。

    ![新增參數](./media/resource-manager-vs-code/select-avail-parameters.png)

7. 如果您在範本中有任何結構描述驗證問題，您會看到編輯器] 中的很熟悉不規則曲線。 您可以輸入**Ctrl + Shift + M**或選取較低左側的狀態列中的 [圖像檢視錯誤及警告的清單。

    ![錯誤](./media/resource-manager-vs-code/errors.png)

    驗證您的範本，可協助您偵測語法問題;不過，您可能也會看到您可以忽略的錯誤。 在某些情況下，編輯器] 中會比較您的範本不是最新版本，因此報告錯誤，即使您知道是正確的結構描述。 例如，假設函數最近已新增到資源管理員，但尚未更新結構描述。 編輯器報表函數正常運作時部署儘管錯誤。

    ![錯誤訊息](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>部署新的資源

當您的範本時，您可以部署新的資源，下列指示︰ 

### <a name="windows"></a>Windows

1. 開啟 PowerShell 命令提示字元 
2. 登入類型︰ 

        Login-AzureRmAccount 

3. 如果您有多個訂閱，取得與訂閱的清單︰

        Get-AzureRmSubscription

    然後選取要使用的訂閱。
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. 更新 parameters.json 檔案中的參數
5. 執行部署上 Azure 範本 Deploy.ps1

### <a name="osxlinux"></a>OSX/Linux

1. 開啟終端機視窗 
2. 登入類型︰

        azure login 

3. 如果您有多個訂閱，請選取右訂閱︰

        azure account set <subscriptionNameOrId> 

4. 更新 parameters.json 檔案中的參數。
5. 若要部署範本，請執行︰

        azure group deployment create -f <PathToTemplate> 

## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解範本，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
- 若要瞭解範本函數，請參閱[Azure 資源管理員範本函數](resource-group-template-functions.md)。
- 更多範例使用 Visual Studio 程式碼的詳細資訊，請參閱從[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 連線[示範](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)[建立雲端應用程式搭配使用 Visual Studio 程式碼](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code)。 從 HealthClinic.biz 示範更多的快速入門，請參閱[Azure 開發人員工具快速入門](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)。
