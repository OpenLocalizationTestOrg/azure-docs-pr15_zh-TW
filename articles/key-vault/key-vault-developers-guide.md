<properties
   pageTitle="主要儲藏室開發人員指南 |Microsoft Azure"
   description="開發人員可用來管理 Microsoft Azure 環境中的密碼編譯金鑰 Azure 機碼存放庫。 "
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/03/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-developers-guide"></a>Azure 主要儲藏室開發人員指南
使用機碼存放庫，您將能夠安全地存取機密資訊在您的應用程式內如此：

- 將不必自行撰寫程式碼保護機碼和機密資料與您將能夠輕易地使用它們從應用程式。
- 您將能夠有您自己的客戶與因此您可以專注於提供核心軟體功能管理自己的機碼。 如此一來您的應用程式不會擁有的責任或潛在客戶的租用戶金鑰及機密資料的責任。
- 您的應用程式可以使用金鑰進行簽署和加密尚未持續金鑰管理您的應用程式從外部如此解決方案是合適的地理位置分散的應用程式。

- 機碼存放庫的年 9 月 2016年版本，使用您的應用程式可以立即利用的機碼存放庫的憑證。 如需詳細資訊，請參閱**關於機碼、 機密、 和憑證**本文中[其餘參考 （英文）](https://msdn.microsoft.com/library/azure/dn903623.aspx)。

如需一般 Azure 機碼存放庫的詳細資訊，請參閱[什麼是機碼存放庫](key-vault-whatis.md)。

## <a name="videos"></a>影片
這段影片顯示如何建立您自己的主要存放庫以及如何使用 「 Hello 機碼存放庫 」 範例應用程式中。

[AZURE.VIDEO azure-key-vault-developer-quick-start]

視訊中提及的資源連結：
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure 主要儲藏室範例程式碼](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

若要了解更多您可以遵循[金鑰儲藏室部落格](http://aka.ms/kvblog)並參與[金鑰儲藏室論壇 （英文）](http://aka.ms/kvforum)。

## <a name="creating-and-managing-key-vaults"></a>建立及管理重要保存庫

之前在您的程式碼中使用 Azure 機碼存放庫，您可建立並管理透過 REST、 資源管理員範本、 PowerShell 或 CLI、 保存庫中的下列文章所述：

- [建立及管理與其餘的重要保存庫](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [建立並管理重要保存庫 powershell](key-vault-get-started.md)
- [建立並管理重要保存庫與 CLI](key-vault-manage-with-cli.md)
- [建立主要儲藏室並新增 Azure 資源管理員範本透過密碼](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] 針對主要保存庫作業透過 AAD 驗證及授權透過個別儲藏室所定義的機碼存放庫的專屬存取原則。

## <a name="coding-with-key-vault"></a>使用主要存放庫撰寫程式碼

程式設計人員的機碼存放庫管理系統包含數個介面，以作為 foundation REST[金鑰儲藏室 REST API 參考資料](https://msdn.microsoft.com/library/azure/dn903609.aspx)。

您可以隨時成功授權，執行下列動作：

- 管理密碼編譯金鑰使用[建立](https://msdn.microsoft.com/library/azure/dn903634.aspx)、[匯入](https://msdn.microsoft.com/library/azure/dn903626.aspx)、[更新](https://msdn.microsoft.com/library/azure/dn903616.aspx)、[刪除](https://msdn.microsoft.com/library/azure/dn903611.aspx)及其他作業

- 管理使用[取得](https://msdn.microsoft.com/library/azure/dn903633.aspx)、[更新](https://msdn.microsoft.com/library/azure/dn986818.aspx)、[刪除](https://msdn.microsoft.com/library/azure/dn903613.aspx)及其他作業的機密資料

- 使用密碼編譯金鑰搭配[登](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx)、 [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx)及[加密](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[解密](https://msdn.microsoft.com/library/azure/dn878097.aspx)作業

下列 Sdk 都可供使用機碼存放庫：

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[.NET SDK 文件](https://msdn.microsoft.com/library/mt765854.aspx)|[Node.js SDK 文件](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[在 Nuget.NET sdk （英文） 套件](http://www.nuget.org/packages/Microsoft.Azure.KeyVault)|[Node.js sdk （英文） 套件](https://www.npmjs.com/package/azure-keyvault)|

針對.NET SDK 2.x 版本的詳細資訊，請參閱[版本資訊](key-vault-dotnet2api-release-notes.md)。

## <a name="example-code"></a>範例程式碼
使用您的應用程式中的機碼存放庫完成的範例，請參閱：

- .NET 範例應用程式*HelloKeyVault*及 Azure web 服務範例。 [Azure 機碼存放庫的程式碼範例 （英文)](http://www.microsoft.com/download/details.aspx?id=45343)
- 教學課程中可協助您了解如何從 web 應用程式在 Azure 中使用 Azure 機碼存放庫。 [使用 Azure 從 Web 應用程式的主要存放庫](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>使用說明

下列文章及案例提供任務的使用 Azure 機碼存放庫的特定指示：

- [變更主要儲藏室租用戶識別碼訂閱移動後](key-vault-subscription-move-fix.md)-當您移 Azure 訂閱租用戶的租用戶 B、 您現有的主要保存庫都無法存取的租用戶 B.修正此使用本指南中的主體 （使用者和應用程式）。
- [存取防火牆後方的機碼存放庫](key-vault-access-behind-firewall.md)-存取重要儲藏室用戶端應用程式必須能夠存取多個的結束點的各種功能鍵存放庫。

- [如何產生及 Azure 機碼存放庫的 Transfer HSM-Protected 機碼](key-vault-hsm-protected-keys.md)-這可協助您規劃、 產生，然後傳輸自行 HSM 保護機碼來搭配 Azure 機碼存放庫。
- [如何將傳遞安全值 （如密碼） 部署期間](../resource-manager-keyvault-parameter.md)-做為參數傳遞的安全的值 （如密碼） 部署期間在需要時，就可以儲存該值為 Azure 機碼存放庫和參考其他資源管理員 」 範本中的值 （英文） 中的密碼。
- [如何使用機碼存放庫的可延伸以 SQL Server 的金鑰管理](https://msdn.microsoft.com/library/dn198405.aspx)Azure 機碼存放庫的 SQL Server 連接器可讓 SQL Server 與 SQL-在-a-VM 利用 Azure 金鑰儲藏室服務做為保護應用程式的連結 ； 其加密金鑰的可延伸金鑰管理 (EKM) 提供者透明資料加密、 備份加密和資料行層級的加密。
- [部署憑證給 Vm 機碼存放庫的方式](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/)在 Azure 虛擬機器中執行的雲端應用程式需要的憑證。 您如何取得這個憑證將此虛擬機器今天？
- [如何將安裝程式與端對端主要旋轉] 及 [稽核機碼存放庫](key-vault-key-rotation-log-monitoring.md)-此查核行程透過如何設定主要旋轉] 及 [稽核與 Azure 機碼存放庫。

針對整合和機碼保存庫使用 Azure 上更多特定任務的指引，請參閱[Ryan Jones Azure 資源管理員範本範例的機碼存放庫](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。

## <a name="integrated-with-key-vault"></a>與主要存放庫整合

這些文章是有關其他案例和服務，讓我們的或整合機碼存放庫。

- [Azure 磁碟加密](../security/azure-security-disk-encryption.md)如何運用的業界標準[BitLocker](https://technet.microsoft.com/library/cc732774.aspx)功能的 Windows 和 Linux 提供 OS 和資料磁碟磁碟區加密[性資料採擷窖](https://en.wikipedia.org/wiki/Dm-crypt)功能。 Azure 機碼存放庫可協助您控制及管理的磁碟加密金鑰與您的主要儲藏室訂閱，同時確保虛擬機器的磁碟中的所有資料會都加密在 Azure 的存放裝置中的其餘部分中的機密資料整合的解決方案。


## <a name="supporting-libraries"></a>支援文件庫

- [Microsoft Azure 機碼存放庫核心文件庫](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)提供`IKey`和`IKeyResolver`尋找從識別碼的機碼並執行作業與符號的介面。

- [Microsoft Azure 機碼存放庫 Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) Azure 機碼存放庫提供延伸的功能。

## <a name="other-key-vault-resources"></a>機碼存放庫的其他資源
- [主要儲藏室部落格](http://aka.ms/kvblog)
- [主要儲藏室論壇 （英文)](http://aka.ms/kvforum)
