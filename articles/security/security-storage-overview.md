<properties
   pageTitle="Azure 儲存安全性概觀 |Microsoft Azure"
   description=" Azure 儲存體是雲端儲存空間方案的新式依賴持續性、 可用性和延展性以符合其客戶的需求的應用程式。 本文提供的核心概觀 Azure 安全性功能，可供 Azure 儲存體。 "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="terrylan"/>

# <a name="azure-storage-security-overview"></a>Azure 儲存安全性概觀

Azure 儲存體是雲端儲存空間方案的新式依賴持續性、 可用性和延展性以符合其客戶的需求的應用程式。 Azure 儲存體提供一組完整的安全性功能︰

- 儲存帳戶可以使用角色型存取控制和 Azure Active Directory 受到保護。
- 資料可以使用用戶端加密、 HTTPS 或中小企業 3.0 受到保護的應用程式和 Azure 之間傳送之用。
- 資料可以設定自動加密寫入 Azure 儲存體時使用的儲存空間服務加密。
- 虛擬機器所使用的作業系統與資料磁碟可以設定為使用 Azure 磁碟加密加密。
- Azure 儲存體中的資料物件的委派存取權授與使用共用的 Access 簽章。
- 使用儲存空間分析追蹤存取儲存時，由使用的驗證方法。

Azure 儲存體中的安全性的更多詳細檢視，請參閱[Azure 儲存體安全性指南](../storage/storage-security-guide.md)。 本指南可提供深度剖析 Azure 儲存體的安全性功能，例如儲存帳戶金鑰，將資料在傳輸時會和的其餘部分中，並儲存分析的加密。

本文提供可與 Azure 儲存體的 Azure 安全性功能的概觀。 如果文件，讓每項功能的詳細資料，您可以瞭解更多，的連結。

以下是本文涵蓋的核心功能︰

- 角色型存取控制
- 若要儲存物件委派的存取
- 在傳輸時會加密
- 加密其餘儲存服務加密
- Azure 磁碟加密
- Azure 鍵保存庫

## <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)

您可以保護您儲存的帳戶使用角色型存取控制 (RBAC)。 限制存取根據[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低權限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)的安全性原則是必要的組織想要強制執行存取資料的安全性原則。 將適當的 RBAC 角色指派給群組和應用程式在某個範圍授與這些存取權限。 您可以使用[內建 RBAC 角色](../active-directory/role-based-access-built-in-roles.md)，例如儲存帳戶參與者] 權限指派給使用者。

深入瞭解︰

- [Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>若要儲存物件委派的存取

共用的 access 簽章 (SA) 提供委派的存取您儲存的帳戶中的資源。 SA 表示您可以授與用戶端您儲存的帳戶中的物件權限限制在指定期間內的時間，以指定的權限。 您可以授與這些限制權限，而不必共用您的帳戶便捷鍵。 SA 是 URI，其查詢參數中包含所有經過驗證的存取儲存資源所需的資訊。 若要存取與 SA 儲存資源，用戶端只需要 SA 中傳送到適當的建構函式或方法。

深入瞭解︰

- [了解 SA 模型](../storage/storage-dotnet-shared-access-signature-part-1.md)
- [建立及使用 SA 與 Blob 儲存體](../storage/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>在傳輸時會加密
在傳輸時會加密是在網路上傳輸時，保護資料的機制。 您可以使用 Azure 儲存體來保護資料使用︰

- [傳輸層級加密](../storage/storage-security-guide.md#encryption-in-transit)，例如 HTTPS 當您將資料轉換成比例 Azure 儲存體。
- [線上加密](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares)，例如 Azure 檔案共用區中小企業 3.0 加密。
- [用戶端加密](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage)，來加密資料，才能傳送到存放以及解密傳輸不儲存後的資料。

深入瞭解用戶端加密︰

- [Microsoft Azure 儲存空間的用戶端加密](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [雲端安全性控制項數列︰ 在傳輸時會加密的資料](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>在其餘的加密

大多數組織會[在其餘的資料加密](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)是資料的隱私權、 規範和資料主權必要步驟。 有三個 Azure 功能，可提供資料位於 「 其他 」 的加密︰

- [儲存服務加密](../storage/storage-security-guide.md#encryption-at-rest)可讓您要求寫入 Azure 儲存體時，儲存服務自動加密資料。
- [用戶端加密](../storage/storage-security-guide.md#client-side-encryption)也會提供加密其餘的功能。
- [Azure 磁碟加密](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines)可讓您加密 OS 及資料磁碟 IaaS 虛擬機器使用。

進一步瞭解儲存服務加密︰

- [Azure 儲存服務加密](https://azure.microsoft.com/services/storage/)適用於[Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)。 如需其他 Azure 儲存空間類型的詳細資訊，請參閱[檔案](https://azure.microsoft.com/services/storage/files/)、[磁碟 （Premium 儲存區）](https://azure.microsoft.com/services/storage/premium-storage/)、[表格](https://azure.microsoft.com/services/storage/tables/)和[佇列](https://azure.microsoft.com/services/storage/queues/)。
- [Azure 儲存服務加密的其餘的資料](../storage/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure 磁碟加密

Azure 虛擬機器 (Vm) 的磁碟加密可協助您組織的地址安全性與規範要求加密 VM 磁碟 （包括開機和資料磁碟） 鍵與您控制[Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)中的原則。

磁碟加密 Vm 適合 Linux 與 Windows 作業系統。 它也可以使用金鑰保存庫可協助您保護、 管理及稽核磁碟加密金鑰的使用。 靜止已加密 VM 磁碟中的所有資料，使用業界標準加密技術，在您 Azure 儲存體帳戶。 在 Windows 版的磁碟加密解決方案根據[Microsoft BitLocker 磁碟機加密](https://technet.microsoft.com/library/cc732774.aspx)和 Linux 解決方案根據[dm 窖](https://en.wikipedia.org/wiki/Dm-crypt)。

深入瞭解︰

- [Azure 磁碟加密 for Windows 和 Linux IaaS 虛擬機器](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure 鍵保存庫

Azure 磁碟加密使用[Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)可協助您控制與管理磁碟加密金鑰和金鑰保存庫訂閱，並確保在您 Azure 儲存體中其餘的加密磁碟虛擬機器中的所有資料中的密碼。 您應該使用金鑰保存庫稽核索引鍵和原則使用方式。

深入瞭解︰

- [什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-whatis.md)
- [Azure 鍵保存庫快速入門](../key-vault/key-vault-get-started.md)
