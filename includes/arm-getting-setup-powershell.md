## <a name="setting-up-powershell-for-resource-manager-templates"></a>資源管理員範本 PowerShell 設定

您可以使用 PowerShell 的 Azure 與資源管理員之前，您必須有權限在 Windows PowerShell 和 PowerShell 的 Azure 版本。

### <a name="verify-powershell-versions"></a>驗證 PowerShell 版本

確認您有 Windows PowerShell 3.0 或 4.0。 若要尋找的 Windows PowerShell 的版本，請在 Windows PowerShell 命令提示字元輸入此命令。

    $PSVersionTable

您會收到下列類型的資訊︰

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


確認**PSVersion**的值是 3.0 或 4.0。 如果沒有的話，請參閱[Windows 管理 Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595)或[Windows 管理架構 4.0](http://www.microsoft.com/download/details.aspx?id=40855)。

### <a name="set-your-azure-account-and-subscription"></a>設定您的 Azure 帳戶及訂閱

如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊設定[免費試用](https://azure.microsoft.com/pricing/free-trial/)版。

開啟命令提示字元中 PowerShell 的 Azure 並登入 Azure 使用此命令。

    Login-AzureRmAccount

如果您有多個 Azure 訂閱時，您可以列出您 Azure 的訂閱，此命令。

    Get-AzureRmSubscription

您會收到下列類型的資訊︰

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

您可以設定目前的 Azure 訂閱的 PowerShell 的 Azure 命令提示字元中執行這些命令。 報價，包括內的所有項目取代 < 與 > 字元，請以正確的名稱。

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

如需有關 Azure 訂閱與帳戶的詳細資訊，請參閱[如何︰ 連線至您的訂閱](powershell-install-configure.md#Connect)。
