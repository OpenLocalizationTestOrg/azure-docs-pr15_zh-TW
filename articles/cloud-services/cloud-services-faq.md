<properties
    pageTitle="雲端服務的常見問題集 |Microsoft Azure"
    description="常見問題集雲端服務。"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="adegeo"/>

# <a name="cloud-services-faq"></a>雲端服務的常見問題集
本文解答關於 Microsoft Azure 雲端服務的一些常見問題集的問題。 您也可以造訪[Azure 支援常見問題集](http://go.microsoft.com/fwlink/?LinkID=185083)的一般 Azure 價格和支援資訊。 您可以也資訊，請參閱[雲端服務虛擬記憶體大小頁面](cloud-services-sizes-specs.md)大小。

## <a name="certificates"></a>憑證

### <a name="where-should-i-install-my-certificate"></a>在應該安裝我的憑證？

- **我**  
使用私密金鑰的應用程式憑證 (\*.pfx， \*.p12)。

- **CA**  
在此儲存區 （原則和子 Ca） 中，移您所有的中繼憑證。

- **根**  
根的 CA 儲存，所以您主要的根的 CA 憑證應該請前往這裡。

### <a name="i-cant-remove-expired-certificate"></a>我無法移除過期的憑證

Azure 可讓您在使用時移除憑證。 您需要刪除會使用憑證，部署或更新部署不同或已更新的憑證。

### <a name="delete-an-expired-certificate"></a>刪除憑證過期

只要憑證不是使用，您可以使用[移除 AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) PowerShell cmdlet，若要移除的憑證。

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>我已過期憑證副檔名為命名 Windows Azure 服務管理

加入副檔名雲端服務，例如副檔名為遠端桌面時，會建立這些憑證。 這些憑證僅適用於加密和解密的副檔名為私人的設定。 並不重要這些憑證過期。 不會檢查到期日。

### <a name="certificates-i-have-deleted-keep-reappearing"></a>已經有已刪除的憑證

這些再次出現很可能因為您使用的，例如 Visual Studio 的工具。 當您重新連線的工具，使用的憑證時，它會再上傳至 Azure。

### <a name="my-certificates-keep-disappearing"></a>我的憑證保留消失

當回收的虛擬機器執行個體時，所有的本機變更將會遺失。 用於[啟動工作](cloud-services-startup-tasks.md)虛擬機器每次角色開始安裝的憑證。

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>我找不到我的管理憑證入口網站中

[管理憑證](..\azure-api-management-certs.md)僅可在 Azure 傳統入口網站。 目前的 Azure 入口網站並未使用管理憑證。 

### <a name="how-can-i-disable-a-management-certificate"></a>如何停用管理憑證？

[管理憑證](..\azure-api-management-certs.md)無法停用。 您將它們刪除透過 Azure 傳統入口網站時您不想再使用。

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>如何建立特定的 IP 位址的 SSL 憑證？

依照指示[建立的憑證教學課程](cloud-services-certs-create.md)。 做為 DNS 名稱中使用的 IP 位址。

## <a name="security"></a>安全性

### <a name="disable-ssl-30"></a>停用 SSL 3.0

若要停用 SSL 3.0，並使用 TLS 安全性，建立記錄啟動任務此部落格文章︰ https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>不按比例縮放雲端服務

### <a name="i-cannot-scale-beyond-x-instances"></a>我無法調整超出 X 執行個體

Azure 訂閱有的限制，您可以使用的核心數目。 縮放比例不適如果您已使用所有可用的核心。 例如，如果您有 100 核心的限制，這表示您可能會有 100 A1 大小虛擬機器執行個體雲端服務，或 50 A2 大小虛擬機器執行個體。

## <a name="troubleshooting"></a>疑難排解

### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>我無法保留多重 VIP 雲端服務中的 IP

首先，請確定已開啟您想要保留的 IP 的虛擬機器執行個體。 第二，請確定您正在使用保留 IPs 的麻煩執行與生產部署。 部署升級時，**請不要**變更設定。

