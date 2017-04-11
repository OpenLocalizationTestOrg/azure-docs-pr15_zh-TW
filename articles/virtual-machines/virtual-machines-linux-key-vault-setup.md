<properties
    pageTitle="虛擬機器中 Azure 資源管理員設定金鑰保存庫 |Microsoft Azure"
    description="如何設定金鑰保存庫用於 Azure 資源管理員虛擬機器。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="singhkay"/>

# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>設定金鑰保存庫虛擬機器中 Azure 資源管理員

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型

Azure 資源管理員堆疊的機密資料/憑證建立模型做為索引鍵保存庫資源者所提供的資源。 若要進一步瞭解 Azure 金鑰保存庫，請參閱[什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-whatis.md)

用來搭配 Azure 資源管理員虛擬機器金鑰保存庫順序] 鍵保存庫的*EnabledForDeployment*屬性必須設定為 true。 您可以在各種不同的用戶端。 」

## <a name="use-cli-to-set-up-key-vault"></a>使用 CLI 設定金鑰保存庫
若要使用的命令列介面 (CLI) 建立索引鍵保存庫，請參閱[管理金鑰保存庫使用 CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)。

CLI，您必須先建立索引鍵保存庫，才能指派部署原則。 您可以使用下列命令︰

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>使用範本，以設定金鑰保存庫
當您使用的範本時，您必須設定`enabledForDeployment`屬性設定為`true`金鑰保存庫資源。

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

當您使用的範本以建立關鍵保存庫時，您可以設定其他選項，請參閱[建立索引鍵保存庫](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)。
