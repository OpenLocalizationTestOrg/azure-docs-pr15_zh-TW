<properties
   pageTitle="若要建立 VM Marketplace 的設定 PowerShell |Microsoft Azure"
   description="設定 PowerShell 的 Azure，並使用它為選用的程序指示流程建立部署，VM 圖像，以及銷售上 Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>設定，建立 Azure Marketplace 提供的 PowerShell 的 Azure
如需如何設定 Azure 中的 PowerShell 的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。 一個簡單的方法是使用的憑證方法下載並匯入所需的驗證的憑證。 若要取得所需的憑證，請使用**取得 AzurePublishSettingsFile**指令程式。 當您收到提示時，請儲存檔案。 若要匯入憑證的 PowerShell 工作階段，使用**匯入 AzurePublishSettingsFile**指令程式。

若要設定並儲存的常見的 Microsoft Azure 訂閱設定 PowerShell 工作階段，使用 [**設定 AzureSubscription**和**選取 AzureSubscription** cmdlet:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

與訂閱 （在某些 VM 佈建作業所需） 的第一個命令產生關聯的預設儲存帳戶。  第二個可訂閱 （辨識其他 cmdlet） 的目前這一。

## <a name="see-also"></a>另請參閱
- [快速入門︰ 如何將提供發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)
- [建立服務商場的虛擬機器圖像](marketplace-publishing-vm-image-creation.md)
