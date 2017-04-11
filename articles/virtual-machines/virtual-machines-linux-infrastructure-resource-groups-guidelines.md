<properties
    pageTitle="資源群組指導方針 |Microsoft Azure"
    description="深入了解重要的設計及實作方針部署 Azure 基礎結構服務中的 [資源群組。"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="azure-resource-group-guidelines"></a>Azure 資源群組指導方針

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

本文著重於了解如何邏輯建置您的環境及群組資源] 群組中的所有元件。


## <a name="implementation-guidelines-for-resource-groups"></a>資源群組的實作指導方針

決策︰

- 您要建置資源群組的核心基礎結構元件，或完整的應用程式部署嗎？
- 您是否要限制使用角色型存取控制資源群組的存取？

工作︰

- 定義核心基礎結構元件和專用的資源群組，您需要的項目。
- 檢閱如何實作一致、 重現問題部署資源管理員範本。
- 定義控制資源群組的存取，您需要何種使用者存取角色。
- 建立一組的資源群組使用您的命名慣例。 您可以使用 Azure CLI 或入口網站。


## <a name="resource-groups"></a>資源群組

Azure 中邏輯群組相關的資源，例如儲存帳戶、 虛擬網路及部署、 管理及維護它們當作單一實體的虛擬機器 (Vm)。 這個方法可讓您更容易部署應用程式時將管理觀點放在一起的所有相關的資源，或授與其他人的存取權的資源的群組。 資源群組的更完整的認識，您可以參閱[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。

資源群組的主要功能是建立您使用的宣告儲存空間，網路、 JSON 檔案的環境及計算資源的能力。 您也可以定義任何相關的自訂指令碼或若要套用的設定。 藉由使用這些 JSON 範本，您可以建立一致的重現部署您的應用程式。 這種方法可讓您建立的開發環境，然後建立生產部署中，使用相同的範本或反向操作。 進一步了解使用範本，可讀[的範本逐步解說](../resource-manager-template-walkthrough.md)，逐步引導您完成建置配置 JSON 範本的每個步驟。

有兩種設計您的環境的資源群組時，您可以採取的不同方法︰

- 資源群組，結合儲存帳戶、 虛擬網路和子網路 Vm，每個應用程式部署負載平衡器等。
- 集中式的資源群組含有您核心虛擬網路和子網路或儲存的帳戶。 您的應用程式就會進入自己只包含 Vm 負載平衡器、 網路介面等的資源群組。

當您小數位數，建立您的虛擬網路和子網路集中的資源群組可讓您更容易建立跨內部部署的網路連線的混合式連線選項。 另一種方法為每個應用程式會有自己的虛擬網路，需要設定並進行的維修作業。 [角色型存取控制](../active-directory/role-based-access-control-what-is.md)提供漸層的方法來控制存取權給資源群組。 生產應用程式，您可以控制使用者可以存取這些資源，或為核心基礎結構資源，您可以限制只有基礎結構工程師使用。 您的應用程式擁有者只能存取其資源群組和不核心 Azure 基礎結構的環境中的應用程式元件。 在設計您的環境，請考慮要求存取資源，因此設計資源群組的使用者。 


## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 