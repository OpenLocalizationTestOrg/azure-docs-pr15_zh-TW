<properties
    pageTitle="重新部署 Azure 堆疊 |Microsoft Azure"
    description="重新部署 Azure 堆疊。"
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="erikje"/>

# <a name="redeploy-azure-stack"></a>重新部署 Azure 堆疊

若要重新部署 Azure 堆疊，您必須從頭開始從頭如下所述。

## <a name="steps-to-redeploy-azure-stack"></a>若要重新部署 Azure 堆疊的步驟

1. 重新主機原始作業系統 （不包裝金屬安裝）。 這是不在 [啟動] 功能表中的預設設定，因此您必須使用 KVM 或本機主控台加以選取重開機 （您在安裝期間，名為 「 開機從 VHD 」 OS 以 「 AzureStack TP2 」，這有助於識別 OS 是其中）。

    您不需要移除現有開機項目 （新支援的指令碼 「 PrepareBootFromVHD.ps1 」 會為您處理。）

2. 如果您沒有 KVM，或想要選擇開機 OS 之前重新啟動︰
    
    1. 找出指令碼.\BootMenuNoKVM.ps1。 此檔案有提供此建立以及其他支援指令碼。
    
    2. 提高權限的權限執行指令碼。 選取原始的主機作業系統的名稱。 這會將原始主機 OS 開機 host （主機），而不需要 KVM access。
    
    3. 指令碼完成後，系統會要求您確認重新開機。

    - 如果有其他使用者身分登入，此命令將會失敗。

    - 請執行下列命令︰ 重新啟動電腦-強制 
 
3. 刪除 CloudBuilder.vhdx 檔案做為之前的部署的一部分使用。

    您不需要從先前的 TP2 部署刪除現有的儲存集區。 部署指令碼偵測到清除現有，然後建立新。

5. 重新部署複製新複本的 CloudBuilder.vhdx，開機，以將其等。

## <a name="next-steps"></a>後續步驟

[連線至 Azure 堆疊](azure-stack-connect-azure-stack.md)
