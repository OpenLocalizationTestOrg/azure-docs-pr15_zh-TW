<properties
    pageTitle="將應用程式發佈到遠端對 Visual Studio 叢集 |Microsoft Azure"
    description="瞭解如何使用 Visual Studio 發佈到遠端服務布料的轉印圖樣叢集應用程式。"
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>使用 Visual Studio 發佈到遠端叢集應用程式

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Visual studio 副檔名為 Azure 服務布料的轉印圖樣提供輕鬆地與重複編寫指令碼的方式發佈至服務布料的轉印圖樣叢集應用程式。

## <a name="the-artifacts-required-for-publishing"></a>發佈所需的成品

### <a name="deploy-fabricapplicationps1"></a>部署 FabricApplication.ps1

這是使用發佈的設定檔路徑做為參數，發佈服務布料的轉印圖樣應用程式的 PowerShell 指令碼。 由於此指令碼應用程式的一部分，您可以加以修改視您的應用程式歡迎使用。

### <a name="publish-profiles"></a>發佈設定檔

稱為**PublishProfiles**服務布料的轉印圖樣應用程式專案中的資料夾包含儲存發佈應用程式，例如的基本資訊的 XML 檔案︰

- 服務布料的轉印圖樣叢集連線參數
- 應用程式參數檔案路徑
- 升級設定

根據預設，您的應用程式會包含兩個發佈設定檔︰ Local.xml 和 Cloud.xml。 您可以複製並貼上的預設檔案，以新增更多設定檔。

### <a name="application-parameter-files"></a>應用程式參數檔案

稱為**ApplicationParameters**服務布料的轉印圖樣應用程式專案中的資料夾包含使用者指定的應用程式資訊清單的參數值的 XML 檔案。 應用程式資訊清單檔案可以參數化，讓您可以使用部署設定不同的值。 若要進一步瞭解參數化您的應用程式，請參閱[管理多個環境中服務布料的轉印圖樣](service-fabric-manage-multiple-environment-app-configuration.md)。

>[AZURE.NOTE] 如需動作項目服務，您應該建立第一次之前嘗試編輯檔案在編輯器中，或透過 [發佈] 對話方塊的專案。 這是因為建置期間會產生資訊清單檔案的一部分。

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>發佈應用程式，使用 [發佈服務布料的轉印圖樣應用程式] 對話方塊

下列步驟說明如何使用 [**發佈服務布料的轉印圖樣應用程式**] 對話方塊，提供的 Visual Studio 服務布料的轉印圖樣的工具來發佈應用程式。

1. 服務布料的轉印圖樣應用程式專案的快顯功能表，選擇 [**發佈...** 若要檢視**發佈服務布料的轉印圖樣應用程式**] 對話方塊。

    ![* * 發佈服務布料的轉印圖樣應用程式 * * 對話方塊][0]

    **目標設定檔**的下拉式清單方塊中選取的檔案時的設定]，除了**資訊清單版本**，所有儲存的位置。 您可以重複使用現有的設定檔，或建立新的**目標設定檔**的下拉式清單方塊中選擇**< 管理設定檔...>** 。 當您選擇發佈設定檔時，其內容會顯示] 對話方塊中的相對應的欄位。 若要隨時儲存您的變更，請選擇 [**儲存設定檔**連結]。    

2. 在 [**連線的結束點**] 區段中，指定本機或遠端服務布料的轉印圖樣叢集的發佈結束點。 若要新增或變更連線端點，請按一下 [**連線的結束點**] 下拉式清單。 清單會顯示可用的服務布料的轉印圖樣叢集連線端點您可以發佈根據您 Azure 訂閱。 請注意，是否您並未已登入 Visual Studio，系統會提示執行此作業。

    若要選擇從一組可用的訂閱及叢集使用 [叢集選取範圍] 對話方塊。

    ![* * 選取服務布料的轉印圖樣叢集 * * 對話方塊][1]

    >[AZURE.NOTE] 如果您想要發佈至任意端點 （例如廠商叢集），請參閱下方的**任意叢集端點發佈**一節。

    一旦您選擇端點，Visual Studio 驗證所選的服務布料的轉印圖樣叢集連線。 如果叢集不安全，Visual Studio 可以立即連線到它。 不過，如果安全叢集，必須安裝之前的本機電腦上的憑證。 如需詳細資訊，請參閱[如何設定安全連線](service-fabric-visualstudio-configure-secure-connections.md)。 當您完成時，選擇**[確定**] 按鈕。 選取的叢集會出現在 [**發佈服務布料的轉印圖樣應用程式**] 對話方塊。

3. 在**應用程式參數檔案**下拉式清單方塊中，瀏覽至應用程式參數檔案。 應用程式參數檔案保留應用程式資訊清單檔案中的使用者指定參數的值。 若要新增或變更參數，請選擇 [**編輯**] 按鈕。 輸入或變更在 [**參數**] 方格中的參數值。 完成後，選擇 [**儲存**] 按鈕。

    ![* * 編輯參數 * * 對話方塊][2]

4. 使用**升級應用程式**] 核取方塊，指定是否這發佈動作會升級。 升級發佈動作異標準發佈動作。 差異的清單，請參閱[升級應用程式服務布料的轉印圖樣](service-fabric-application-upgrade.md)。 若要設定的升級設定，請選擇 [**設定升級設定**連結]。 升級的參數編輯器] 隨即出現。 請參閱[設定服務布料的轉印圖樣應用程式的升級](service-fabric-visualstudio-configure-upgrade.md)，若要進一步瞭解升級參數。

5. 選擇**資訊清單版本...** 若要檢視**編輯版本**] 對話方塊] 按鈕。 您需要更新應用程式和服務版本升級的位置。 請參閱[服務布料的轉印圖樣應用程式升級教學課程](service-fabric-application-upgrade-tutorial.md)，瞭解如何在應用程式和服務資訊清單版本影響升級程序。

    ![* * 編輯版本 * * 對話方塊][3]

    如果應用程式和服務版本使用語意的版本設定，例如 1.0.0 或數值 1.0.0.0 的格式，請選取 [**自動更新應用程式及服務版本**] 選項。 當您選擇此選項，服務和程式碼，設定或資料封裝版本時，會自動更新應用程式版本號碼會更新。 如果您想要手動編輯版本，請清除 [停用此功能的核取方塊。

    >[AZURE.NOTE] 顯示動作項目專案的所有套件項目，第一次都建立專案產生服務資訊清單的檔案中的項目。

6. 當您完成指定的所有必要的設定，選擇 [發佈至所選的服務布料的轉印圖樣叢集應用程式的 [**發佈**] 按鈕。 您所指定的設定會套用至發佈程序。

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>發佈至任意叢集端點 （包括廠商叢集）

發佈體驗 Visual Studio 已發佈至其中的 Azure 訂閱相關聯的遠端叢集而最佳化。 不過，就可以發佈至任意結束點 （例如服務布料的轉印圖樣廠商叢集） 直接編輯的發佈設定檔 XML。 如上所述，兩個發佈設定檔所提供的預設-**Local.xml**和**Cloud.xml**，但歡迎建立不同環境中的其他設定檔。 例如，您可能會想要建立發佈至廠商叢集，可能是名為**Party.xml**的設定檔。

如果您連線至不安全叢集，只需要是叢集連線端點，例如`partycluster1.eastus.cloudapp.azure.com:19000`。 在此情況下，發佈設定檔中的連接端點會看起來像這樣︰

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  如果您連線到安全叢集，您也必須提供用戶端憑證驗證使用本機存放區中的詳細資料。 如需詳細資訊，請參閱[設定安全連線至服務布料的轉印圖樣叢集](service-fabric-visualstudio-configure-secure-connections.md)。

  一旦您發佈的設定檔設定妥當，您可以發佈] 對話方塊中參考它，如下所示。

  ![新增發佈的設定檔中發佈] 對話方塊][4]

  請注意，在此情況下，新的發佈設定檔指向其中一個預設應用程式參數檔案。 這是適用於您想要將相同的應用程式設定發佈至環境的數字。 相反地，在您要有不同的設定，您想要發佈至每個環境的情況下，它會使建立對應的應用程式參數檔案。

## <a name="next-steps"></a>後續步驟

若要瞭解如何自動連續整合環境中的發佈程序，請參閱[設定服務布料的轉印圖樣連續整合](service-fabric-set-up-continuous-integration.md)。


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
