<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli"></a>使用 Azure CLI

下列步驟可協助您輕鬆地使用 Azure CLI，最新版本與適當的訂閱。 如果您需要安裝 Azure CLI，先將其連線至您的帳戶，請參閱[Azure 命令列介面 (Azure CLI)](xplat-cli-install.md)。

### <a name="step-1-update-azure-cli-version"></a>步驟 1︰ 更新 Azure CLI 版本

若要使用 Azure CLI 服務管理模式的必要的命令，您應該為新的版本如果可以的話。 若要確認您的版本，請輸入`azure --version`。 您應該會看到類似︰

    $ azure --version
    0.8.17 (node: 0.10.25)

如果您想要更新您的 Azure CLI 版本，請參閱[Azure CLI](https://github.com/Azure/azure-xplat-cli)。

### <a name="step-2-set-the-azure-account-and-subscription"></a>步驟 2︰ 設定 Azure 帳戶和訂閱

一旦您 Azure CLI 連線與您想要使用的帳戶後，您可能需要多個訂閱。 如果您執行時，您應該輸入您的帳戶檢閱可用的訂閱`azure account list`，然後選取您想要使用的方式輸入訂閱`azure account set <subscription id or name> true`_訂閱 id 或名稱_在哪裡訂閱 id 或您想要在目前的工作階段中使用的訂閱名稱。 您應該會看到如下所示的項目︰

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] 如果您還沒有 Azure 帳戶，但您需要訂閱 MSDN 的訂閱，您可以取得免費 Azure 信用額度來啟動您的[MSDN 訂閱者優點](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，或者您可以使用免費的帳戶。 [運作 Azure 存取。
