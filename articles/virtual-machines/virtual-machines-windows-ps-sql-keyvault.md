<properties
    pageTitle="設定用於 SQL Server Azure 金鑰保存庫整合 Azure Vm （資源管理員）"
    description="瞭解如何將自動化的 SQL Server 加密用於 Azure 金鑰保存庫設定。 本主題說明如何使用 SQL Server 建立與資源管理員的虛擬機器中的 Azure 金鑰保存庫整合。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>設定用於 SQL Server Azure 金鑰保存庫整合 Azure Vm （資源管理員）

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-ps-sql-keyvault.md)
- [傳統](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>概觀
有多個 SQL Server 加密功能，例如[透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[資料行層級的加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)和[備份加密](https://msdn.microsoft.com/library/dn449489.aspx)。 這些表單的加密要求您管理並儲存您使用加密加密金鑰。 Azure 鍵保存庫 (AKV) 服務被為了改善的安全性與管理這些按鍵安全和高度可用的位置。 [SQL Server 連接器](http://www.microsoft.com/download/details.aspx?id=45344)可讓 SQL Server 使用從 Azure 金鑰保存庫這些按鍵。

如果那里執行 SQL Server，內部部署與您的電腦上，[您可以存取您內部部署的 SQL Server 電腦從 Azure 金鑰保存庫遵循](https://msdn.microsoft.com/library/dn198405.aspx)的步驟。 但的 SQL Server Azure Vm 中，您可以使用*Azure 金鑰保存庫整合*功能來節省時間。

啟用此功能時，它會自動安裝 SQL Server 連接器、 設定 EKM 提供者，若要存取 Azure 金鑰保存庫，並建立可讓您存取您保存庫認證。 如果您看先前所述的內部部署文件中的步驟，就可以看見此功能會自動執行步驟 2 和 3。 您仍需要手動執行的唯一項目是，建立關鍵保存庫與索引鍵。 從這裡被自動您 SQL VM 整個安裝。 這項功能已完成此設定，您可以執行 T SQL 陳述式以開始加密您的資料庫或備份，亦即。

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>啟用和設定 AKV 整合
您可以啟用期間佈建 AKV 整合，或將它設定為現有 Vm。

### <a name="new-vms"></a>新的 Vm
如果您在提供新的 SQL Server 虛擬機器與資源管理員，Azure 入口網站提供的步驟來啟用 Azure 金鑰保存庫整合。 Azure 鍵保存庫功能僅適用於企業版、 開發人員或的 SQL Server 評估版本。

![SQL Azure 金鑰保存庫整合](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

佈建的詳細逐步解說，請參閱[佈建 Azure 入口網站中的 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

### <a name="existing-vms"></a>現有 Vm
針對現有的 SQL Server 虛擬機器中，選取 [SQL Server 虛擬機器]。 然後選取 [ **SQL Server 組態**] 區段中的**設定**刀。

![針對現有 Vm SQL AKV 整合](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

在**SQL Server 組態**刀中，按一下 [自動金鑰保存庫整合] 區段中的 [**編輯**] 按鈕。

![針對現有 Vm 設定 SQL AKV 整合](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

完成時，按一下 [**確定**] 按鈕下方的**SQL Server 組態**刀，以儲存變更。

>[AZURE.NOTE] 您也可以設定 AKV 整合使用的範本。 如需詳細資訊，請參閱[Azure 金鑰保存庫整合 Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)。

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
