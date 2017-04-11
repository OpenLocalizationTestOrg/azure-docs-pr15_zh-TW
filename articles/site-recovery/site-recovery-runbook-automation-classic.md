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


# <a name="add-azure-automation-runbooks-to-recovery-plans---classic"></a>Azure 自動化 runbooks 加入修復計劃-傳統


本教學課程說明 Azure 網站復原如何與 Azure 自動化提供復原方案的擴充功能整合。 修復計劃可以協調復原的虛擬機器複寫次要雲端和 Azure 案例複寫使用 Azure 網站復原保護。 他們也有助於進行修復**一致正確**、**可重複**和**自動化**。 如果您是透過至 Azure 虛擬機器，與 Azure 自動化整合延伸修復計劃，並可讓您執行 runbooks，因此可允許功能強大的自動化工作的功能。

如果您有不聽 Azure 自動化尚未，登入，[在這裡](https://azure.microsoft.com/services/automation/)，並下載其指令碼範例[以下](https://azure.microsoft.com/documentation/scripts/)。 閱讀更多關於[Azure 網站修復](https://azure.microsoft.com/services/site-recovery/)，以及如何協調復原 Azure 使用修復計劃，[在這裡](https://azure.microsoft.com/blog/?p=166264)。

在此簡短教學課程中，我們會查看您可以將 Azure 自動化 runbooks 整合至修復計劃的方式。 我們會稍早所需手動操作的簡單工作自動化，並瞭解如何將多重步驟復原轉換成單一按一下復原動作。 我們也會看看如何疑難排解簡單的指令碼，如果它發生錯誤。

## <a name="protect-the-application-to-azure"></a>保護 Azure 應用程式

讓我們開始包含兩個虛擬機器簡單的應用程式。 在這裡，我們會有 Fabrikam HRweb 應用的程式。 主選 HRweb Fabrikam 單和後 Hrweb Fabrikam 端都是兩個受保護的以 Azure 虛擬機器使用 Azure 網站復原。 若要保護虛擬機器使用 Azure 網站修復，請遵循下列步驟。

1.  啟用保護您的虛擬機器。

2.  確定虛擬機器已完成初始複寫複寫。

3.  先等初始複寫完成和複寫的狀態是 [受保護。

![](media/site-recovery-runbook-automation/01.png)
---------------------

在本教學課程中，我們會建立容錯移轉應用程式，以 Azure Fabrikam HRweb 應用程式的復原計劃。 然後，我們會整合會建立端點失敗 runbook 透過 Azure 虛擬機器做連接埠 80 的網頁。

首先，讓我們來建立應用程式修復計劃。

## <a name="create-the-recovery-plan"></a>建立復原計劃

若要復原 Azure 應用程式，必須先建立一個修復計劃。
使用 [復原計劃，您可以指定的虛擬機器復原的順序。 虛擬機器置於群組 1 會復原開始，並會依照虛擬機器 2] 群組中。

建立看起來像下面復原計劃。

![](media/site-recovery-runbook-automation/12.png)

若要閱讀更多關於復原方案，請閱讀文件[以下](https://msdn.microsoft.com/library/azure/dn788799.aspx "以下")。

接下來，請在 Azure 自動化建立所需的成品。

## <a name="create-the-automation-account-and-its-assets"></a>建立自動化帳戶和其資產

您需要建立 runbooks Azure 自動化帳戶。 如果您還沒有帳戶，瀏覽至 Azure 自動化] 索引標籤以![](media/site-recovery-runbook-automation/02.png)並建立新的帳戶。

1.  為帳戶命名找出。

2.  指定您要放置帳戶地理區域。

建議您將 ASR 保存庫為相同的區域中的帳戶。

![](media/site-recovery-runbook-automation/03.png)

接下來，請在 [帳戶建立下列資產。

### <a name="add-a-subscription-name-as-asset"></a>新增為資產的訂閱名稱

1.  加入新的設定![](media/site-recovery-runbook-automation/04.png)Azure 自動化資產和若要選取![](media/site-recovery-runbook-automation/05.png)

2.  選取**的字串**變數類型

3.  指定**AzureSubscriptionName**變數名稱

    ![](media/site-recovery-runbook-automation/06.png)

4.  指定您實際的 Azure 訂閱名稱做為變數的值。

    ![](media/site-recovery-runbook-automation/07_1.png)

您可以找出您的訂閱，從您的帳戶 Azure 入口網站上的 [設定] 頁面的名稱。

### <a name="add-an-azure-login-credential-as-asset"></a>新增資產 Azure 登入認證

Azure 自動化使用 PowerShell 的 Azure 連線到訂閱，並在那里的成品上的運作方式。 此，您必須以驗證您的 Microsoft 帳戶或公司或學校帳戶。
您可以在資產 runbook 安全地使用中儲存的帳戶認證。

1.  加入新的設定![](media/site-recovery-runbook-automation/04.png)Azure 自動化資產和選取![](media/site-recovery-runbook-automation/09.png)

2.  **Windows PowerShell 認證**中選取憑證類型

3.  指定名稱為**AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  指定使用者名稱和密碼以登入。

現在這兩個這些設定，可在您的資產。

![](media/site-recovery-runbook-automation/11.png)

詳細資訊，了解如何連線至您的訂閱，透過 PowerShell 提供[以下](../powershell-install-configure.md)。

接下來，您會建立 runbook 中可以新增前端虛擬機器端點後移轉後的 Azure 自動化。

## <a name="azure-automation-context"></a>Azure 自動化內容

ASR 會將內容變數傳遞給可協助您撰寫確定指令碼 runbook。 可以說雲端服務的虛擬機器名稱預測，但不是永遠程式特定案例︰ 其中一種情況位置的虛擬機器名稱可能會因為 Azure 中不支援的字元已變更，會發生情況。 因此這項資訊會傳遞給 ASR 復原計劃，做為*內容*的一部分。

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


若要找出 VmMap 中的索引鍵的內容，您也可以前往 VM 屬性] 頁面中 ASR，看看 VM GUID 屬性。

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>作者自動化 runbook

現在建立開啟連接埠 80 前端虛擬機器上的 runbook。

1.  在 [名稱**OpenPort80** Azure 自動化帳戶中建立新的 runbook

    ![](media/site-recovery-runbook-automation/14.png)

2.  瀏覽至 runbook 的 [作者] 檢視，然後輸入 [草稿模式。

3.  第一次指定要作為復原計劃內容的變數

    ```
        param (
            [Object]$RecoveryPlanContext
        )

    ```

4.  下一次連線到使用的認證與訂閱名稱的訂閱

    ```
        $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

        # Connect to Azure
        $AzureAccount = Add-AzureAccount -Credential $Cred
        $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
        Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    ```

    請注意，您會使用 Azure 資產 – **AzureCredential**和以下**AzureSubscriptionName** 。

5.  現在指定結束點詳細資料] 與 [您要的方式公開端點的虛擬機器 GUID。 在這種情況下前端虛擬機器。

    ```
        # Specify the parameters to be used by the script
        $AEProtocol = "TCP"
        $AELocalPort = 80
        $AEPublicPort = 80
        $AEName = "Port 80 for HTTP"
        $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
    ```

    這會指定 Azure 端點通訊協定、 VM 上的本機連接埠和其對應的公用連接埠。 這些變數是所需的結束點加入 Vm Azure 命令參數。 VMGUID 保留您需要所在的虛擬機器 GUID。

6.  指令碼現在會指定 VM GUID 的擷取內容，並建立參照，虛擬機器上的端點。

    ```
        #Read the VM GUID from the context
        $VM = $RecoveryPlanContext.VmMap.$VMGUID

        if ($VM -ne $null)
        {
            # Invoke pipeline commands within an InlineScript

            $EndpointStatus = InlineScript {
                # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
                # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

                $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                    Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                    Update-AzureVM
                Write-Output $Status
            }
        }
    ```

7. 此完成之後，按下發佈![](media/site-recovery-runbook-automation/20.png)允許您可執行的指令碼。

以下提供完整的指令碼供您參考

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>將指令碼新增至復原計劃

指令碼後，您可以將其新增至您先前建立的復原計劃。

1.  在您建立復原計劃的情況下，選擇將指令碼新增後群組 2。 ![](media/site-recovery-runbook-automation/15.png)

2.  指定的指令碼的名稱。 這是復原計劃中顯示的這個指令碼只易記的名稱。

3.  在 [在容錯移轉至 Azure 指令碼 – 選取 Azure 自動化帳戶名稱。

4.  在 Azure Runbooks 中，選取您所撰寫 runbook。

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>主端指令碼

當您執行 Azure 容錯移轉時，您也可以選擇執行主端指令碼。 這些指令碼會在伺服器上執行 VMM 在容錯移轉。
主端指令碼只僅適用於關機前，張貼關機階段。 這是因為我們預期，通常是無法使用時，可達到損毀的主要網站。
期間意外的容錯移轉，只有當您選擇的主站台作業，它將會嘗試執行主端指令碼。 如果不是連線到或逾時，容錯移轉會繼續復原虛擬機器。
主端指令碼可取消 VMware/實體/hyper-v 網站不受保護的 Azure-以時您容錯移轉至 Azure VMM。
不過，當您回復 Azure 從內部部署，主端指令碼 (Runbooks) 可用於 VMware 以外的所有目標。

## <a name="test-the-recovery-plan"></a>測試復原計劃

一旦您新增 runbook 至的方案，您可以進行測試容錯移轉，並在 [動作] 中檢視。 建議執行測試容錯移轉來測試您的應用程式，以及復原計劃以確保沒有任何錯誤。

1.  選取 [復原計劃，然後開始測試容錯移轉]。

2.  在方案執行時，您可以查看是否已執行 runbook 或不透過其狀態。

    ![](media/site-recovery-runbook-automation/17.png)

3.  您也可以查看詳細的 runbook 執行狀態 runbook Azure 自動化工作頁面上。

    ![](media/site-recovery-runbook-automation/18.png)

4.  Runbook 執行結果，除了容錯移轉完成後，您可以看到執行是否成功或不造訪 Azure 虛擬機器頁面，尋找在結束點。

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>指令碼範例

雖然我們個案自動化一經常使用加入 Azure 虛擬機器中的端點，在此教學課程中的工作，您可能會使用 Azure 自動化其他功能強大的自動化工作的數字。 Microsoft Azure 自動化社群提供範例 runbooks 可協助您開始建立您自己的解決方案，與公用程式 runbooks，您可以使用建置組塊為較大的自動化工作。 開始使用這些從圖庫，並建立功能強大的單鍵復原方案，您使用的應用程式 Azure 網站復原。

## <a name="additional-resources"></a>其他資源

[Azure 自動化概觀](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 自動化概觀")

[Azure 自動化指令碼範例](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Azure 自動化指令碼範例")
