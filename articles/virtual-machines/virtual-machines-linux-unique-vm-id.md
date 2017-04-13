<properties
   pageTitle="存取 VM 識別碼"
   description="說明存取和使用 Azure VM 唯一識別碼"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="accessing-and-using-azure-vm-unique-id"></a>存取和使用 Azure VM 唯一識別碼

Azure VM 唯一識別碼是 128 位元識別碼編碼和儲存所有 Azure IaaS VM 的 SMBIOS，並可以目前用讀取平台 BIOS 命令。

Azure VM 唯一識別碼是唯讀的屬性。 Azure 唯一 VM 識別碼不會在重新啟動關機變更 ([規劃尚未計劃)，開始/停止解除配置、 服務治療或還原的位置。 不過，如果 VM 快照，若要建立新的執行個體複製新 Azure VM ID 設定。

> [AZURE.NOTE] 如果您有較舊 Vm 建立和執行，因為這項新功能有導入 (2014 年 9 月 18 年) 請重新啟動您的 VM 自動收到 Azure 唯一識別碼。


若要存取 Azure 唯一 VM 識別碼從 VM 中︰


## <a name="create-a-vm"></a>建立 VM
 

如需詳細資訊，請參閱[建立虛擬機器](virtual-machines-linux-creation-choices.md)


## <a name="connect-to-the-vm"></a>連線至 VM
 

如需詳細資訊，請參閱[從 Linux SSH](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="query-vm-unique-id"></a>查詢 VM 唯一識別碼

命令 （範例使用**Ubuntu**）︰

    sudo dmidecode | grep UUID
    
範例預期的結果︰

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
顯示較大位元組位元排序，因為實際的唯一 VM 識別碼在此情況下會是︰

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
Azure VM 唯一識別碼可以用於不同情況是否 VM Azure 上執行或內部部署與可協助您可能需要在您 Azure IaaS 部署授權、 報表或一般追蹤需求。 有許多獨立軟體廠商建置應用程式，這些認證 Azure 上可能會要求識別生命週期 Azure VM 並告知上 Azure 是否正在執行 VM 內部部署或其他雲端提供者上。 此平台識別項可以例如協助偵測軟體是否已正確地授權關聯的來源，例如任何 VM 資料，協助您設定正確的平台右側矩陣和來追蹤及建立這些計量內的其他用途之間的關聯。
