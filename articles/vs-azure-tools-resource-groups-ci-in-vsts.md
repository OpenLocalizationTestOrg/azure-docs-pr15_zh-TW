<properties
    pageTitle="與小組服務使用 Azure 資源群組專案中的連續整合 |Microsoft Azure"
    description="說明如何在 Visual Studio 中使用 Azure 資源群組部署專案設定 Visual Studio 小組服務中的連續整合。"
    services="visual-studio-online"
    documentationCenter="na"
    authors="mlearned"
    manager="erickson-doug"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="mlearned" />

# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>使用 Azure 資源群組部署專案的 Visual Studio 小組服務中的連續整合

若要部署 Azure 的範本，您需要執行工作的不同階段瀏覽︰ 建立，測試，複製到] Azure （也稱為 「 臨時 」），及部署範本。  有兩種不同方式若要在 Visual Studio 小組服務 （與小組服務）。 這兩種方法提供相同的結果，因此，選擇最適合您的工作流程的項目。

-   將您建立的定義執行 Azure 資源群組部署專案 (部署 AzureResourceGroup.ps1) 中所包含的 PowerShell 指令碼的單一步驟。 指令碼複製成品，然後部署範本。
-   新增多個與小組服務建立步驟，每個執行工作階段。

本文將示範如何使用第一個選項 （使用執行的 PowerShell 指令碼組建定義）。 這個選項的其中一個優點是在 Visual Studio 開發人員使用的指令碼與小組服務會使用相同的指令碼。 此程序假設您已經有核取 [將與小組服務 Visual Studio 中部署專案。

## <a name="copy-artifacts-to-azure"></a>將成品複製到 Azure 

無論此案例中，如果您有任何所需的範本部署的成品您要為其授與 Azure 資源管理員存取。 這些成品例如可以包含檔案︰

-   巢狀的範本
-   設定及 DSC 指令碼
-   應用程式的二進位檔案

### <a name="nested-templates-and-configuration-scripts"></a>巢狀的範本與設定指令碼
當您使用 Visual Studio 所提供的範本 （或 [內建的 Visual Studio 程式碼片段），不僅階段的 PowerShell 指令碼的成品，也參數化不同部署資源的 URI。 指令碼，然後將成品複製到 [安全的容器 Azure 中，會建立該容器，Sa 權杖，然後將傳遞範本部署至該資訊。 請參閱[建立範本部署](https://msdn.microsoft.com/library/azure/dn790564.aspx)更深入的巢狀的範本。

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>與小組服務中的連續部署設定

若要在與小組服務呼叫的 PowerShell 指令碼，您需要更新您建立的定義。 簡而言之，步驟如下︰ 

1.  編輯組建定義。
1.  設定與小組服務中的 Azure 授權。
1.  新增參照 Azure 資源群組部署專案中的 PowerShell 指令碼 PowerShell 的 Azure 建置步驟。
1.  設定參數的值*-ArtifactsStagingDirectory*使用內建與團隊服務的專案。

### <a name="detailed-walkthrough"></a>詳細逐步解說

下列步驟會引導您完成設定連續的部署與小組服務中的必要步驟 

1.  編輯與小組服務建立定義，並新增 PowerShell 的 Azure 建置步驟。 選擇 [**建立定義**] 類別底下建立定義]，然後選擇 [**編輯**] 連結。

    ![][0]

1.  建立定義中加入新的**PowerShell 的 Azure**建置步驟，然後選擇 [**新增建置步驟...** ] 按鈕。

    ![][1]

1.  選擇**部署工作**類別、 選取**PowerShell 的 Azure**工作，然後選擇其**新增**] 按鈕。

    ![][2]

1.  選擇 [ **PowerShell 的 Azure**建立步驟，然後填入其值。

    1.  如果您已經新增至與小組服務 Azure 服務端點，請選擇 [訂閱]，請在 [**訂閱 Azure** ] 下拉式清單方塊中，然後直接跳到下一節。 

        如果您沒有安裝 Azure 服務端點與小組服務中，必須將其加入。 這個小節會帶領您完成程序。 如果您 Azure 帳戶是使用 Microsoft 帳戶 （例如 Hotmail)，您會需要採取下列步驟，以取得服務主要驗證。

    1.  選擇 [ **Azure 訂閱**] 旁的 [**管理**] 連結的下拉式清單方塊]。

        ![][3]

    1. **新的服務端點**] 下拉式清單方塊中選擇 [ **Azure** ]。

        ![][4]

    1.  在 [**新增 Azure 訂閱**] 對話方塊中，選取的**服務主要**選項。

        ![][5]

    1.  若要**新增 Azure 訂閱**] 對話方塊中新增 Azure 訂閱資訊。 您必須提供下列項目︰
        -   訂閱識別碼
        -   訂閱名稱
        -   服務主要識別碼
        -   服務主要鍵
        -   租用戶識別碼

    1.  將您所選擇的名稱新增至 [**訂閱**名稱] 方塊中。 此值會出現在**Azure 訂閱**下拉式清單與小組服務中的稍後部份中。 

    1.  如果您不知道您 Azure 訂閱的識別碼，您可以使用其中一個下列命令，將它。
        
        PowerShell 指令碼，請使用︰

        `Get-AzureRmSubscription`

        Azure CLI 使用︰

        `azure account show`
    

    1.  若要取得服務主要識別碼，服務主要金鑰]，然後租用戶 ID，請遵循[建立 Active Directory 應用程式和服務主要使用入口網站](resource-group-create-service-principal-portal.md)，或[驗證服務主要與 Azure 資源管理員](resource-group-authenticate-service-principal.md)中的程序。

    1.  將服務主要識別碼、 服務主要金鑰，以及租用戶識別碼值新增至 [**新增 Azure 訂閱**] 對話方塊，然後選擇 [**確定**] 按鈕。

        現在，您會擁有有效的服務主要用的 PowerShell 的 Azure 指令碼。

1.  編輯組建定義，並選擇 [ **PowerShell 的 Azure**建立步驟。 在 [**訂閱 Azure** ] 下拉式清單方塊中選取 [訂閱]。 （如果訂閱沒有出現，請選擇 [**重新整理**] 按鈕下一步 [**管理**] 連結）。 

    ![][8]

1.  提供的部署 AzureResourceGroup.ps1 PowerShell 指令碼的路徑。 若要這麼做，請選擇 [**指令碼路徑**] 方塊旁的省略符號 （...）] 按鈕，瀏覽至您的專案的**指令碼**資料夾中的部署 AzureResourceGroup.ps1 PowerShell 指令碼、 加以選取，，然後選擇**[確定**] 按鈕。 

    ![][9]

1. 選取 [指令碼後，更新路徑指令碼，讓執行時從 Build.StagingDirectory （ *ArtifactsLocation*設為相同目錄）。 您可以藉由新增 「 $(Build.StagingDirectory)/ 」 到指令碼路徑的開頭。

    ![][10]

1.  在 [**指令碼引數**] 方塊中，輸入下列參數 （在一條單線）。 當您在 Visual Studio 中執行指令碼時，您可以看到與**輸出**視窗中使用參數的方式。 您可以開始使用此設定參數值，在您建立的步驟。

  	| 參數 | 描述|
  	|---|---|
  	| -ResourceGroupLocation           | 資源群組的位置，例如**eastus**或**「 東亞美國 '**地理位置值。 （加入單引號括住如果名稱中有一個空格時）。如需詳細資訊，請參閱[Azure 區域](https://azure.microsoft.com/en-us/regions/)。|                                                                                                                                                                                                                              |
  	| -ResourceGroupName               | 使用此部署資源群組的名稱。|                                                                                                                                                                                                                                                                                                                                                                                                                |
  	| -UploadArtifacts                 | 這個參數時，會指定成品需要上傳至 Azure 從本機系統。 您只需要設定此參數，如果您的範本部署需要額外您想要使用 PowerShell 指令碼 （例如設定指令碼或巢狀的範本） 的階段的成品。                                                                                                                                                                 |
  	| -StorageAccountName              | 此部署所用階段成品的儲存體帳戶名稱。 如果您複製 Azure 成品需要此參數。 此儲存帳戶將不會自動建立的部署，必須已經存在。|                                                                                                                                                                                                                     |
  	| -StorageAccountResourceGroupName | 儲存帳戶相關聯的 [資源] 群組的名稱。 如果您複製 Azure 成品需要此參數。|                                                                                                                                                                                                                                                                                                                               |
  	| -TemplateFile                    | Azure 資源群組部署專案中的範本檔案路徑。 若要增強的彈性，使用此參數的是，而不是絕對路徑的 PowerShell 指令碼的位置的相對路徑。|
  	| -TemplateParametersFile          | Azure 資源群組部署專案中的參數檔案路徑。 若要增強的彈性，使用此參數是相對，而不是絕對路徑的 PowerShell 指令碼的位置的路徑。|
  	| -ArtifactStagingDirectory        | 這個參數可讓 PowerShell 指令碼從複製專案的二進位檔案的地方知道的資料夾。 此值會覆寫使用 PowerShell 指令碼的預設值。 與小組服務使用的設定值為:-ArtifactStagingDirectory $(Build.StagingDirectory)                                                                                                                                                                                              |

    以下是指令碼引數的範例 （列中斷，提高可讀性）︰

    ``` 
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)' 
    ```

    完成後，**指令碼引數**中應如下所示。

    ![][11]

1.  您已新增至 Azure 所有必要的項目建立步驟之後，請選擇 [**佇列中**建置] 按鈕，來建立專案]。 [**建立**] 畫面會顯示的輸出的 PowerShell 指令碼。

## <a name="next-steps"></a>後續步驟

閱讀[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)，瞭解 Azure 資源管理員和 Azure 資源群組的詳細資訊。


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
