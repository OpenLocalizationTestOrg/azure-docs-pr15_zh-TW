<properties
   pageTitle="新增 Azure 自動化 runbooks 復原方案 |Microsoft Azure"
   description="本文將說明如何 Azure 網站復原現在可讓您擴充修復計劃使用 Azure 自動化期間 Azure 復原完成複雜的工作"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Azure 自動化 runbooks 加入修復計劃


本教學課程說明 Azure 網站復原如何與 Azure 自動化提供復原方案的擴充功能整合。 修復計劃可以協調復原的虛擬機器複寫次要雲端和 Azure 案例複寫使用 Azure 網站復原保護。 他們也有助於進行修復**一致正確**、**可重複**和**自動化**。 如果您是透過至 Azure 虛擬機器，與 Azure 自動化整合延伸修復計劃，並可讓您執行 runbooks，因此可允許功能強大的自動化工作的功能。

如果您有不聽 Azure 自動化尚未，登入，[在這裡](https://azure.microsoft.com/services/automation/)，並下載其指令碼範例[以下](https://azure.microsoft.com/documentation/scripts/)。 閱讀更多關於[Azure 網站修復](https://azure.microsoft.com/services/site-recovery/)，以及如何協調復原 Azure 使用修復計劃，[在這裡](https://azure.microsoft.com/blog/?p=166264)。

在本教學課程中，我們會看看如何您可以將 Azure 自動化 runbooks 整合修復計劃。 我們會稍早所需手動操作的簡單工作自動化，並瞭解如何將多個步驟復原轉換成單一按一下復原動作。 我們也會看看如何疑難排解簡單的指令碼，如果它發生錯誤。

## <a name="customize-the-recovery-plan"></a>自訂復原計劃

1. 讓我們開始 operning 復原計劃的資源刀。 您可以查看復原計劃有兩個復原新增的虛擬機器。 

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
---------------------

2. 按一下 [自訂] 按鈕，若要開始新增 runbook。 這會開啟復原計劃自訂刀。


    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


3. 以滑鼠右鍵按一下開始群組 1，然後選取 [若要新增的 「 新增文章動作 」。

4. 選取 [選擇新的刀指令碼。

5. 命名的指令碼 ' Hello World 」。

6. 選擇 [自動化帳戶名稱]。 這是 Azure 自動化帳戶。 請注意，但必須在同一份訂閱為網站復原保存庫中任何 Azure 的地理位置會此帳戶。

7. 選取 runbook 自動化帳戶。 這是執行復原計劃的第一個群組復原之後時將執行的指令碼。

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)


8. 選取 [確定] 儲存指令碼。 這會將指令碼新增至文章動作] 群組中的群組 1︰ 開始。


    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>新增指令碼的明顯點為單位

1. 您可以以滑鼠右鍵按一下指令碼，並選擇 [刪除步驟] 或 [更新指令碼]。

2. 指令碼可以在上執行時容錯移轉 Azure 來自內部部署至 Azure，並可以執行 Azure 為主要端指令碼之前關閉，內部部署 azure 回復期間。

3. 指令碼執行時，它會插入復原計劃內容。

以下是範例內容變數所呈現的外觀。

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


下表包含的內容中的每個變數名稱和描述。

**變數名稱** | **描述**
---|---
RecoveryPlanName | 正在執行的方案名稱。 可協助您根據使用相同的指令碼的名稱採取動作
FailoverType | 指定是否容錯移轉是測試計劃，或尚未計劃。
FailoverDirection | 指定復原是否為主要或次要
識別碼 | 找出計劃正在執行復原計劃中的群組編號
VmMap | 在群組中的所有虛擬機器的陣列
VMMap 鍵 | 針對每個 VM 的唯一識別碼 (GUID)。 是的虛擬機器 VMM 識別碼相同適用之處。
角色名稱 | 在要復原 Azure VM 的名稱
CloudServiceName | Azure 虛擬機器會建立在其下的雲端服務名稱。
CloudServiceName （在資源管理員部署模型） | 建立虛擬機器時 azure 資源群組名稱。


## <a name="using-complex-variables-per-recovery-plan"></a>使用複雜變數每復原計劃

有時候，runbook 需要比只 RecoveryPlanContext 的詳細資訊。 沒有將參數傳遞到 runbook 第一本課程機制。 不過，如果您想要使用相同的指令碼，透過多個修復計劃您可以使用復原規劃內容變數 'RecoveryPlanName' 但下方 runbook 中使用 Azure 自動化複數變數實驗技巧。 下列範例會顯示如何建立三個不同的複雜的變數資產，並在 runbook 根據復原計劃的名稱中使用它們。

請考慮您想要在 runbook 中使用 3 的其他參數。 讓我們將它們編碼 JSON 表單 {」 Var1 」: 「 testautomation 「，」 Var2 」: 「 尚未計劃 」、 「 Var3 」: 「 PrimaryToSecondary 」}

若要建立新的自動化資產使用[AA 複雜的變數](../automation/automation-variables.md#variable-types Complex variable)。
命名為變數<RecoveryPlanName>-參數。
您可以使用的參考建立[複雜的變數](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396)。

不同的復原方案命名為變數

1. recoveryPlanName1 >-參數
2. recoveryPlanName2 >-參數
3. recoveryPlanName3 >-參數

現在，指令碼中參照為參數

1. 從 $rpname 取得資源點數名稱 = $Recoveryplancontext 變數
2. 取得資產的 $paramValue = 」 $($rpname) 參數 」
3. 使用此為複數變數復原計劃，則可電話取得 AzureAutomationVariable [-AutomationAccountName] <String> -命名 $paramValue。

作為範例，若要取得 SharepointApp 復原計劃複數變數/參數，來建立 Azure 自動化複數變數稱為 「 SharepointApp 參數 」。

從使用陳述式取得 AzureAutomationVariable 資產擷取變數復原計劃中使用 [-AutomationAccountName] <String> [集名稱] $paramValue。 [此參照的更多詳細資料](https://msdn.microsoft.com/library/dn913772.aspx)

相同的指令碼這種方式可以用於不同的復原計劃，先將計劃特定複數變數儲存在資產。

## <a name="sample-scripts"></a>指令碼範例

您可以直接匯入至您的自動化帳戶的指令碼存放庫，請參閱[指令碼 Kristian Nese OMS 存放庫](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

指令碼是 Azure 資源管理員範本，會將所有部署下方指令碼

* NSG

NSG runbook 會指派復原計劃 」 中的每個 vm 的公用 IP 位址，並將其虛擬網路介面卡附加至網路安全性群組，讓預設通訊

* PublicIP

公用 IP runbook 會指派給復原計劃 」 中的每個 VM 的公用 IP 位址。 電腦和應用程式的存取權而定中每個來賓的防火牆設定


* CustomScript

CustomScript runbook 會指派復原計劃 」 中的每個 vm 的公用 IP 位址，並安裝將會提取您部署的範本時參照的指令碼自訂指令碼副檔名

* NSGwithCustomScript

Runbook 會指派的公用 IP 位址中復原計劃，安裝自訂指令碼使用副檔名，並將虛擬網路介面卡連線至 NSG，讓每個 vm NSGwithCustomScript 預設遠端存取的傳入和傳出通訊

## <a name="additional-resources"></a>其他資源

[為帳戶執行 azure 自動化服務](../automation/automation-sec-configure-azure-runas-account.md)

[Azure 自動化概觀](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 自動化概觀")

[Azure 自動化指令碼範例](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Azure 自動化指令碼範例")
