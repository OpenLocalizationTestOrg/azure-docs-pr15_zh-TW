<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli-with-azure-resource-manager-arm"></a>使用 Azure CLI 與 Azure 資源管理員 (ARM)

您可以使用 Azure CLI 資源管理員命令與範本部署 Azure 資源及工作負載使用資源群組之前，您必須使用 Azure 帳戶 （當然）。 如果您沒有帳戶，您可以取得[這裡免費 Azure 試用版](https://azure.microsoft.com/pricing/free-trial/)。

> [AZURE.NOTE] 如果您還沒有 Azure 帳戶，但您需要訂閱 MSDN 的訂閱，您可以取得免費 Azure 信用額度來啟動您的[MSDN 訂閱者優點](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，或者您可以使用免費的帳戶。 [運作 Azure 存取。

### <a name="step-1-verify-the-azure-cli-version"></a>步驟 1︰ 確認 Azure CLI 版本

若要使用 Azure CLI 必要的命令和 ARM 範本，您需要至少具備版本 0.8.17。 若要確認您的版本，請輸入`azure --version`。 您應該會看到類似︰

    $ azure --version
    0.8.17 (node: 0.10.25)

如果您需要更新您的 Azure CLI 版本，請參閱[Azure CLI](https://github.com/Azure/azure-xplat-cli)。

### <a name="step-2-verify-you-are-using-a-work-or-school-identity-with-azure"></a>步驟 2︰ 確認您使用公司或學校身分識別與 Azure

如果您使用的[Azure Active Directory 租用戶](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)或[服務主要名稱](https://msdn.microsoft.com/library/azure/dn132633.aspx)，您可以只使用 ARM 命令模式。 （這也被稱為*組織識別碼*）。

若您有一個，請登入輸入`azure login`並使用您的公司或學校的使用者名稱和密碼出現提示時。 如果您有一個，您應該會看到如下所示的項目︰

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
  	|info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

如果您沒有看到此，您必須建立新的租用戶 （或主要的服務） 與您的 Microsoft 帳戶身分識別。 （這是通常與個人 MSDN 訂閱] 或 [免費試用版訂閱的情況）。若要建立的工作或學校從 Azure 帳戶建立 Microsoft id 的識別碼，請參閱[建立新的 Azure 訂閱 Azure AD 目錄之間的關聯](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)。 如果您認為您應該已經組織的識別碼，您可能需要洽詢為您建立帳戶的人員。

### <a name="step-3-choose-your-azure-subscription"></a>步驟 3︰ 選擇 Azure 訂閱

如果您只有一個訂閱 Azure 帳戶中，Azure CLI 關聯的訂閱預設。 如果您有多個訂閱，您需要選取的訂閱，您想要使用的方式輸入`azure account set <subscription id or name> true`_訂閱 id 或名稱_在哪裡訂閱 id 或您想要在目前的工作階段中使用的訂閱名稱。

您應該會看到如下所示的項目︰

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### <a name="step-4-place-your-azure-cli-in-the-arm-mode"></a>步驟 4︰ 將您 Azure CLI 置於 ARM 模式

若要使用 Azure CLI Azure 資源管理 (ARM) 模式，請輸入`azure config mode arm`。 您應該會看到如下所示的項目︰

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE] 您可以切換回使用 Azure 服務管理命令，只要輸入`azure config mode asm`。
