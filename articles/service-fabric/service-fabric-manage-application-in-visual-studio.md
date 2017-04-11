<properties
   pageTitle="管理您的應用程式，在 Visual Studio |Microsoft Azure"
   description="使用 Visual Studio 建立、 開發、 封裝、 部署及偵錯服務布料的轉印圖樣應用程式和服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>使用 Visual Studio 簡化撰寫及管理服務布料的轉印圖樣應用程式

您可以管理 Azure 服務布料的轉印圖樣應用程式和服務，透過 Visual Studio。 一旦您[設定您的開發環境](service-fabric-get-started.md)，您可以使用 Visual Studio 建立服務布料的轉印圖樣應用程式、 新增服務] 或封裝、 register 及部署在您的本機開發叢集的應用程式。

## <a name="deploy-your-service-fabric-application"></a>部署服務布料的轉印圖樣應用程式

根據預設，部署應用程式會合併到一個簡單的操作下列步驟︰

1. 建立應用程式套件
2. 將應用程式套件上傳至圖像存放區
3. 註冊應用程式類型
4. 移除任何執行應用程式的執行個體
5. 建立新的應用程式執行個體

在 Visual Studio 中，按**f5 鍵**將也部署應用程式，並將偵錯工具附加至所有應用程式執行個體。 您可以使用**Ctrl + F5**偵錯部署應用程式，或者您可以使用 [發佈] 設定檔發佈至本機或遠端叢集。 如需詳細資訊，請參閱[發佈至遠端叢集使用 Visual Studio 應用程式](service-fabric-publish-app-remote-cluster.md)。

### <a name="application-debug-mode"></a>應用程式偵錯模式

根據預設，Visual Studio 移除現有的執行個體您的應用程式類型當您停止偵錯或 （如果您部署應用程式不附加偵錯工具），當您重新部署應用程式。 在此情況下，會移除所有應用程式的資料。 本機偵錯時可能會想要保留您已經建立測試應用程式的新版本時的資料，您想要保留的應用程式執行，或您想要升級應用程式的後續偵錯工作階段。 Visual Studio 服務布料的轉印圖樣工具提供稱為**應用程式偵錯模式**的控制項是否**F5**必須解除安裝應用程式，讓執行後結束偵錯工作階段，或讓升級在後續的偵錯工作階段，而不移除並重新部署應用程式的應用程式的屬性。

#### <a name="to-set-the-application-debug-mode-property"></a>若要將應用程式偵錯模式] 屬性設定

1. 在應用程式專案的快顯功能表中，選擇 [**屬性**] （或按**F4**鍵）。
2. 在 [**屬性**] 視窗中，設定**應用程式偵錯模式**屬性。

    ![設定應用程式偵錯模式屬性][debugmodeproperty]

以下是可用的**應用程式偵錯模式**選項。

1. **自動升級**︰ 應用程式時繼續執行偵錯工作階段]: 結束。 下一步**F5**會視為部署升級快速附加日期字串升級至新版的應用程式使用監控的自動模式。 升級程序會保留您在先前的偵錯工作階段中輸入的任何資料。

2. **將應用程式**︰ 偵錯工作階段結束時，應用程式會保留在叢集執行。 在下一個**F5**將會移除應用程式，新建立的應用程式部署至叢集。

3. **移除應用程式**會使偵錯工作階段結束時移除應用程式。

**自動升級**的資料會保留套用服務結構的應用程式升級功能，但它所調整最佳化效能，而不是安全。 如需有關如何升級應用程式，您可能會如何執行升級實數的環境中的詳細資訊，請參閱[升級服務布料的轉印圖樣應用程式](service-fabric-application-upgrade.md)。

![新的應用程式版本，加上日期的範例][preservedata]

>[AZURE.NOTE] Visual studio，此屬性不存在之前版本 1.1 （英文） 的服務布料的轉印圖樣工具。 之前 1.1 （英文），請使用 [**保留資料啟動**屬性來達成相同的行為。 「 保留應用程式] 選項已採用的 Visual Studio 1.2 版服務布料的轉印圖樣工具。

## <a name="add-a-service-to-your-service-fabric-application"></a>新增服務應用程式服務布料的轉印圖樣

您可以新增新的服務應用程式來擴充功能。  若要確保的應用程式套件中包含的服務，請新增的服務，透過**新布料的轉印圖樣服務...** ] 功能表項目。

![新增布料的轉印圖樣服務應用程式][newservice]

選取要新增至您的應用程式服務布料的轉印圖樣專案類型，並指定服務的名稱。  請參閱[選擇您的服務的架構](service-fabric-choose-framework.md)，協助您決定要使用的服務類型。

![選取要新增至您的應用程式布料的轉印圖樣服務專案類型][addserviceproject]

新服務將會新增至您的方案或現有的應用程式套件。 服務參照與預設服務的執行個體將會新增至應用程式顯示。 服務會建立並開始的下次部署應用程式。

![新服務將會新增至您的應用程式資訊清單][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>封裝服務布料的轉印圖樣應用程式

若要為叢集部署應用程式和服務，您需要建立應用程式套件。  封裝組織應用程式顯示服務 manifest(s) 與其他必要的檔案是特定的版面配置中。  Visual Studio 設定及管理應用程式專案的資料夾，請在 「 版本' 目錄中的套件。  從**應用程式**內容功能表中按一下 [**套件**建立或更新應用程式套件。  若要這麼做，如果您使用自訂的 PowerShell 指令碼部署應用程式。

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>移除應用程式和使用雲端的檔案總管的應用程式類型

您可以執行 Visual Studio 中使用雲端 Explorer，您可以在啟動 [**檢視**] 功能表中的基本叢集管理的作業。 例如，您可以刪除應用程式，並取消提供本機或遠端叢集上的應用程式類型。

![移除應用程式](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] 更豐富的叢集管理功能，請參閱[視覺化叢集服務布料的轉印圖樣檔案總管](service-fabric-visualizing-your-cluster.md)]。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

- [服務布料的轉印圖樣應用程式模型](service-fabric-application-model.md)
- [服務布料的轉印圖樣的應用程式部署](service-fabric-deploy-remove-applications.md)
- [管理多個環境的應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)
- [應用程式服務布料的轉印圖樣偵錯](service-fabric-debugging-your-application.md)
- [使用服務布料的轉印圖樣總管視覺化叢集](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
