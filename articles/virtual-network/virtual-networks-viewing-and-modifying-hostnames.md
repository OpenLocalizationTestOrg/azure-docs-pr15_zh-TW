<properties 
   pageTitle="檢視及修改 Hostname |Microsoft Azure"
   description="如何檢視及變更 hostname Azure 虛擬機器、 網頁工作者的角色與名稱解析"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="viewing-and-modifying-hostnames"></a>檢視及修改主機名稱

若要允許您的主機名稱可以參考的角色執行個體，您必須在每一個角色的服務設定檔中設定主機名稱] 的值。 您執行這項作業，將所需的主機名稱新增至**角色**項目的**vmName**屬性。 **VmName**屬性的值是用來作為基礎的每一個角色執行個體主機名稱。 例如，如果**vmName** *webrole* ，有三個執行個體，該角色的執行個體主機名稱會*webrole0* *webrole1*，與*webrole2*。 您不需要在設定檔中，指定主機名稱的虛擬機器因為虛擬機器主機名稱會填入根據的虛擬機器名稱。 如需有關如何設定 Microsoft Azure 服務的詳細資訊，請參閱[Azure 服務設定結構描述 (.cscfg 檔案)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>檢視 [主機名稱

您可以使用下列工具的任何雲端服務中檢視虛擬機器和角色執行個體主機的名稱。

### <a name="azure-portal"></a>Azure 入口網站

若要檢視的虛擬機器概觀刀虛擬機器主機名稱，您可以使用[Azure 入口網站](http://portal.azure.com)。 請記住刀的顯示**名稱**] 和 [**主機名稱]**的值。 雖然就是一開始，變更主機名稱不會變更虛擬機器或角色執行個體的名稱。

角色執行個體也可以在 Azure 入口網站，來檢視，但如果您的清單執行個體雲端服務中的，不會顯示 [主機名稱]。 您會看到每個執行個體名稱，但該名稱不能代表主機名稱。

### <a name="service-configuration-file"></a>服務設定檔

您可以從 Azure 入口網站中的服務**設定**刀下載部署服務的服務設定檔。 然後您就可以查看**vmName**屬性，以查看主機名稱的**角色名稱**項目。 請記住，此主機名稱會用來作為的每一個角色執行個體主機名稱。 例如，如果**vmName** *webrole* ，有三個執行個體，該角色的執行個體主機名稱會*webrole0* *webrole1*，與*webrole2*。

### <a name="remote-desktop"></a>遠端桌面

啟用遠端桌面 (Windows)、 Windows PowerShell 遠端 (Windows) 或 SSH （Linux 及 Windows） 連線到您的虛擬機器或角色執行個體之後，您可以在各種不同的方式檢視從遠端桌面連線至作用中的主機名稱︰

- 在命令提示字元或 SSH 終端機中輸入主機名稱。

- 輸入 ipconfig/所有在命令提示 (僅限 Windows)。

- 在 [系統設定 (僅限 Windows) 中檢視的電腦名稱。

### <a name="azure-service-management-rest-api"></a>Azure 服務管理 REST API

從其他用戶端，請遵循以下指示︰

1. 請確定您已連線至 Azure 入口網站的用戶端憑證。 若要取得用戶端憑證，請遵循步驟呈現[如何︰ 下載並匯入發佈設定及訂閱資訊](https://msdn.microsoft.com/library/dn385850.aspx)。 

1. 設定命名 x-ms-版本與 2013年-11-01 值的標頭項目。

1. 傳送邀請以下列格式︰ https://management.core.windows.net/\<subscrition 識別碼\>/服務/hostedservices/\<服務名稱\>?embed 詳細資料 = true

1. 查看**主機名稱**項目的每個**RoleInstance**項目。

>[AZURE.WARNING] 您也可以檢視內部網域尾碼雲端服務從其餘通話回應檢查**InternalDnsSuffix**項目，或執行 ipconfig/所有的命令提示字元遠端桌面工作階段 (Windows)，或執行貓 /etc/resolv.conf 從 SSH 終端機 (Linux)。

## <a name="modifying-a-hostname"></a>修改 [主機名稱

上傳已修改的服務設定檔案，或重新命名的電腦從遠端桌面工作階段，您可以修改任何虛擬機器或角色執行個體的主機名稱。

## <a name="next-steps"></a>後續步驟

[名稱解析 (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure 服務設定結構描述 (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure 虛擬網路組態結構描述](http://go.microsoft.com/fwlink/?LinkId=248093)

[指定使用網路設定檔的 DNS 設定](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
