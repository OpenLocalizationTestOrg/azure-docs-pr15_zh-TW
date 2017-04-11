<properties
    pageTitle="Azure 政府版服務 |Microsoft Azure"
    description="管理您的訂閱中 Azure 政府版的詳細資訊"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/21/2016"
    ms.author="zakramer" />


#  <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>管理並連線到您的訂閱中 Azure 政府版

Azure 政府都有唯一的 Url 與管理您的環境的結束點。 請務必使用正確的連線] 來管理您的環境入口網站，或是 PowerShell。 一旦您連線到 Azure 政府版環境，管理服務的正常作業適用於已部署元件。

## <a name="connecting-via-the-portal"></a>透過入口網站的連線
入口網站是多數人連線至 Azure 政府版的主要方式。  若要連線，瀏覽[https://portal.azure.us](https://portal.azure.us)在入口網站。  Azure 入口網站的舊版本可透過[https://manage.windowsazure.us](https://manage.windowsazure.us)加以存取。

訂閱可以用來連線到[https://account.windowsazure.us](https://account.windowsazure.us)建立您的帳戶。

## <a name="connecting-via-powershell"></a>透過 PowerShell 連線

您是否使用 PowerShell 的 Azure 以管理大型的訂閱，透過指令碼] 或 [存取目前不提供您需要連線到 Azure 政府版，而不是 Azure 公用 Azure 入口網站中的功能。  如果您已使用 PowerShell 的 Azure 公用，這是大多相同。  Azure 政府版的差異如下︰

+ 連接您的帳戶
+ 地區名稱

>[AZURE.NOTE] 如果您還沒有使用 PowerShell，請參閱[PowerShell 的 Azure 簡介](../powershell-install-configure.md)。

當您啟動 PowerShell 時，您必須判斷 Azure PowerShell 連線到 Azure 政府指定環境參數。  參數可確保 PowerShell 連線到正確的端點。  當您連線到您的帳戶的登入決定結束點的集合。  不同的 Api 需要不同版本的環境切換︰

連接類型 | ] 命令
---|----
[服務管理](https://msdn.microsoft.com/library/dn708504.aspx)] 命令 | `Add-AzureAccount -Environment AzureUSGovernment`
[資源管理](https://msdn.microsoft.com/library/mt125356.aspx)] 命令 | `Add-AzureRmAccount -EnvironmentName AzureUSGovernment`
[Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx)命令 | `Connect-MsolService -AzureEnvironment UsGovernment`
[Azure Active Directory 命令 v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) | `Connect-AzureAD -AzureEnvironmentName AzureUSGovernment`

您也可能會使用環境切換，連線至新增 AzureStorageContext 儲存帳戶時，並指定 AzureUSGovernment。

### <a name="determining-region"></a>決定區域

您連線之後，有一個差別其他 – 目標服務所使用的區域。  每個 Azure 雲端具有不同區域。  您可以看到所列的 [服務可用性]。  您通常會使用命令的位置參數中的區域。

有一個攔截。  Azure 政府區域需要不同的常見的名稱格式︰

常見的名稱 | ] 命令
---|----
美國 Gov 維吉尼亞州 | USGov 維吉尼亞州
美國 Gov 稍後 | USGov 稍後

>[AZURE.NOTE] 使用位置參數時，有美國和 Gov 之間沒有空格。

如果您想要驗證 Azure 政府版中可用的區域，您可以執行下列命令，然後列印目前的清單︰

    Get-AzureLocation

如果您不想使用的環境的相關透過 Azure，您可以執行︰

    Get-AzureEnvironment

## <a name="next-steps"></a>後續步驟

如果您正在尋找的詳細資訊，您可以查看︰

+ [PowerShell GitHub 上的文件](https://github.com/Azure/azure-powershell)
+ [連線到資源管理的逐步指示](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
+ [MSDN 上的 azure PowerShell 文件](https://msdn.microsoft.com/library/mt619274.aspx)

補充資訊和更新訂閱 [Microsoft Azure 政府部落格] (https://blogs.msdn.microsoft.com/azuregov/)
