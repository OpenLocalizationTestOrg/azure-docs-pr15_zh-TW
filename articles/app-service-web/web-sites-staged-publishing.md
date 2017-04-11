<properties
    pageTitle="設定臨時環境中 Azure 應用程式服務的 web 應用程式"
    description="瞭解如何使用分段的發佈 web 應用程式中 Azure 應用程式服務。"
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/09/2016"
    ms.author="cephalin"/>

# <a name="set-up-staging-environments-for-web-apps-in-azure-app-service"></a>設定臨時環境中 Azure 應用程式服務的 web 應用程式
<a name="Overview"></a>

當您部署 web 應用程式至[應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)時，您可以部署至不同的部署位置，而不是預設生產位置在 [**標準**] 或 [**進階版**應用程式服務方案模式中執行時。 部署位置是使用自己的 hostname 實際即時 web 應用程式。 可以兩個部署位置，包括生產位置之間交換 web 應用程式的內容和設定項目。 應用程式部署位置部署具有下列優點︰

- 您可以驗證暫存的部署位置中變更 web 應用程式之前，交換和生產位置。

- 第一次將 web 應用程式部署到位置及交換其生產可確保所有位置的執行個體都接手之前生產一起移動。 當您部署 web 應用程式時，如此停機時間。 流量重新導向順暢，而不要求會遭到捨棄當做交換作業。 此整個工作流程可以設定[自動交換](#configure-auto-swap-for-your-web-app)時，不需要測試交換驗證來自動化。

- 交換之後, 的位置使用先前分段的 web 應用程式現在有先前生產 web 應用程式。 如果交換生產位置將所做的變更是不是如您預期地的時候，您可以執行相同的交換立即上手您 「 最後一個已知建議的網站 」 上一步。

每個應用程式服務方案模式支援不同的數字的部署的位置。 若要找出數位置 web 應用程式的模式支援，請參閱[應用程式服務價格](/pricing/details/app-service/)。

- 當您的 web 應用程式有多個的位置時，您無法變更模式。

- 縮放比例不適用於非生產位置。

- 連結的資源管理不支援的非生產位置。 在[Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)僅，您可以暫時移至不同的應用程式服務方案模式的非生產位置來避免對生產位置此可能影響。 請注意，非生產位置必須再次相同的模式與共用生產位置之前，您可以交換兩個位置。


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## <a name="add-a-deployment-slot-to-a-web-app"></a>新增 web 應用程式部署位置 ##

在 [**標準**] 或 [**進階**模式中，若要啟用多個部署位置，必須執行 web 應用程式。

1. 在[Azure 入口網站](https://portal.azure.com/)中，開啟您的 web 應用程式刀。
2. 按一下 [**設定**]，然後按一下 [**部署的位置**。 然後中**部署位置**刀中，按一下 [**新增位置**]。

    ![新增新的部署位置][QGAddNewDeploymentSlot]

    > [AZURE.NOTE]
    > 如果 web 應用程式尚未在 [**標準**] 或 [**進階**模式中，您會收到訊息，指出啟用分段的發佈的支援的模式。 此時，您可以選取**升級**，並瀏覽至您的 web 應用程式中的 [**縮放比例**] 索引標籤，在繼續之前選擇。

2. 在**新增位置**刀中，輸入位置的名稱，然後選取是否要複製從另一個現有的部署位置的 web 應用程式設定。 按一下 [核取記號，以繼續]。

    ![設定來源][ConfigurationSource1]

    第一次新增位置，您只會有兩個選擇︰ 從預設位置生產環境中，或完全無法複製設定。

    建立多個位置之後，您可以複製設定從生產環境中的項目以外的位置︰

    ![設定來源][MultipleConfigurationSources]

5. 在**部署位置**刀中，按一下 [部署位置，以開啟的位置，刀一組的指標以及就像其他任何 web 應用程式的設定]。 **your-web-app-name-deployment-slot-name**會出現在刀，提醒您正在檢視的部署位置的上方。

    ![部署位置標題][StagingTitle]

5. 按一下 [應用程式 URL 中的位置刀]。 請注意具有專屬 hostname 部署位置，同時也是即時應用程式。 若要限制公開存取權的部署位置，請參閱[應用程式服務 Web 應用程式 – 封鎖對非生產部署位置的網站存取權](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)。

部署位置建立之後，沒有任何內容。 您可以從不同的存放庫分支或全然不同的存放庫部署到的位置。 您也可以變更位置的設定。 使用內容更新的部署位置相關聯的發佈設定檔或部署認證。  例如，您可以[發佈給與給此位置](app-service-deploy-local-git.md)。

<a name="AboutConfiguration"></a>
## <a name="configuration-for-deployment-slots"></a>部署位置的設定 ##
當您複製設定從另一個部署位置時，都可以編輯複製的設定。 此外，某些設定項目會遵循內容跨交換 （不位置特定） 時其他組態項目交換 （特定位置） 之後，會保留在同一個位置。 下列清單顯示當您交換位置會變更的設定。

**交換的設定**︰

- 一般設定-例如架構的版本，32/64 位元 Web 通訊端
- （您可以設定讓位置） 的應用程式設定
- （您可以設定讓位置） 的連線字串
- 處理常式對應
- 監控和診斷設定
- WebJobs 內容

**設定的不交換**︰

- 發佈的端點
- 自訂網域名稱
- SSL 憑證和繫結
- 縮放設定
- WebJobs 排程器

若要設定的位置 （不交換） 如果您的應用程式設定或連線字串，存取**應用程式設定**刀的特定位置，然後選取應盡可能位置設定元素的**位置設定**方塊。 請注意，將標記設定項目為特定位置的效果的所有相關聯的 web 應用程式的部署位置上，建立做為不可切換的項目。

![設定位置][SlotSettings]

<a name="Swap"></a>
## <a name="to-swap-deployment-slots"></a>若要交換部署槽 ##

>[AZURE.IMPORTANT] 您將生產交換從部署位置的 web 應用程式之前，務必的所有非位置特定設定您想要讓它在交換目標一樣。

1. 若要交換部署的位置，請按一下命令列中的 web 應用程式或部署位置的命令列中的 [**交換**] 按鈕。 請確定交換來源與交換目標正確設定。 通常交換目標是實際執行位置。  

    ![交換] 按鈕][SwapButtonBar]

3. 按一下**[確定**] 以完成作業。 完成作業之後，已交換部署位置。

## <a name="configure-auto-swap-for-your-web-app"></a>設定 web 應用程式的 [自動切換 ##

自動交換會簡化回傳 DevOps 案例要持續部署的結束客戶的 web 應用程式的 [web 應用程式零低溫開始與零停機時間的位置。 當部署位置設定為自動交換生產，每次您推入您的程式碼的更新到該位置時，應用程式服務將會自動交換 web 應用程式生產之後，具有已接手的位置中。

>[AZURE.IMPORTANT] 當您啟用自動交換的位置時，請確定位置設定完全設定適用於目標位置 （通常是實際執行位置）。

設定位置的 [自動切換很容易。 請遵循下列步驟︰

1. 在**部署位置**刀中，選取非生產位置，按一下該位置刀的**所有設定**。  

    ![][Autoswap1]

2. 按一下 [**應用程式設定**]。 **自動交換****上**選取**自動切換位置**，選取所要的目標位置與命令列中按一下 [**儲存**。 請確定的位置設定完全設定適用於目標位置。

    [**通知**] 索引標籤會 flash 綠色**成功**，一旦完成作業。

    ![][Autoswap2]

    >[AZURE.NOTE] 若要測試自動交換 web 應用程式，請您第一次可以選取非生產目標位置中**自動交換位置**熟悉的功能。  

3. 執行的程式碼推入到該部署位置。 自動交換會發生在短時間後，更新會反映在您的目標位置的 URL。

<a name="Multi-Phase"></a>
## <a name="use-multi-phase-swap-for-your-web-app"></a>使用多重階段交換 web 應用程式 ##

多重階段交換位於簡化內容中的驗證您的位置，例如連接字串設計的設定項目。 在下列情況下，可能較為有用交換目標這類設定項目套用至交換來源和驗證之前交換實際才會生效。 一次交換目標設定項目就會套用至交換來源可用的動作是 [完成交換] 或 [還原成原始的設定，交換來源也有取消交換的效果。 Azure PowerShell cmdlet 部署位置區段中包含的 PowerShell 的 Azure 指令程式可用的多重階段交換的範例。

<a name="Rollback"></a>
## <a name="to-rollback-a-production-app-after-swap"></a>復原之後交換生產應用程式 ##

如果任何錯誤都會列在生產位置交換之後，復原位置其測試交換狀態立即調換相同的兩個位置。

<a name="Warm-up"></a>
## <a name="custom-warm-up-before-swap"></a>自訂交換前的準備 ##

有些應用程式可能會要求自訂準備動作。 Web.config applicationInitialization 設定項目可讓您指定自訂初始化之前收到要求時執行的動作。 若要完成此自訂準備等候交換作業。 以下是範例 web.config 片段。

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## <a name="to-delete-a-deployment-slot"></a>若要刪除的部署位置##

在部署位置的刀，按一下 [命令列中的 [**刪除**]。  

![刪除部署位置][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Azure 的 PowerShell 指令程式的部署位置

Azure PowerShell 是一個模組，可提供 cmdlet 來管理 Windows PowerShell，包括管理 web 應用程式部署位置 Azure 應用程式服務中的支援透過 Azure。

- 安裝及設定 PowerShell 的 Azure 及驗證 Azure PowerShell 的 Azure 訂閱資訊，請參閱[如何安裝和設定 Microsoft Azure PowerShell](../powershell-install-configure.md)。  

----------

### <a name="create-web-app"></a>建立 web 應用程式

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### <a name="create-a-deployment-slot-for-a-web-app"></a>建立 web 應用程式的部署位置

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### <a name="initiate-multi-phase-swap-and-apply-target-slot-configuration-to-source-slot"></a>啟動多階段交換，並將目標位置設定套用至來源位置

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="revert-the-first-phase-of-multi-phase-swap-and-restore-source-slot-configuration"></a>還原多重階段交換的第一個階段與還原來源位置設定

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### <a name="swap-deployment-slots"></a>交換部署槽

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="delete-deployment-slot"></a>刪除部署位置

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
##Azure 部署位置的命令列介面 (Azure CLI) 命令

Azure CLI 提供使用 Azure，包括管理 Web 應用程式部署位置的支援跨平台命令。

- 安裝及設定 Azure CLI，包括如何連線至您的 Azure 訂閱的 Azure CLI 的資訊上的指示請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md)。

-  若要在 Azure CLI Azure 應用程式服務] 清單中可用的命令，連絡`azure site -h`。

----------
### <a name="azure-site-list"></a>azure 網站清單
有關 web 應用程式中最新訂閱，通話**azure 網站清單**，如下列範例所示。

`azure site list webappslotstest`

----------
### <a name="azure-site-create"></a>azure 網站建立
若要建立部署位置，請撥打**azure 網站建立**並指定現有的 web 應用程式的名稱和位置，若要建立，如下列範例所示的名稱。

`azure site create webappslotstest --slot staging`

若要啟用來源控制新的位置，請使用 [ **-給**] 選項，如下列範例所示。

`azure site create --git webappslotstest --slot staging`

----------
### <a name="azure-site-swap"></a>azure 網站交換
若要更新的部署介面槽生產應用程式，使用**azure 網站交換**指令執行交換操作，如下列範例所示。 生產應用程式不會發生任何停機時間，也不會進行低溫開始。

`azure site swap webappslotstest`

----------
### <a name="azure-site-delete"></a>azure 網站刪除
若要刪除不再需要的部署位置，請使用 [ **azure 網站刪除**] 命令，如下列範例所示。

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="next-steps"></a>後續步驟 ##
[Azure 應用程式服務 Web 應用程式 – 封鎖對非生產部署位置的網站存取權](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 免費試用版](/pricing/free-trial/)

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 
