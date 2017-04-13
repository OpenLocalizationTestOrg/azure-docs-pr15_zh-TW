<properties
   pageTitle="使用 Windows 的用戶端影像開發/測試案例 |Microsoft Azure"
   description="如何使用 Visual Studio 訂閱優惠部署 Windows 7/8/10 Azure 中針對開發/測試案例"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="iainfou"/>

# <a name="using-windows-client-in-azure-for-devtest-scenarios"></a>使用 Windows 用戶端 Azure 中，針對開發/測試案例

您可以使用 Windows 8、 Windows 7 或 Windows 10 裝置/測試案例 Azure 中提供您有適當的 Visual Studio (舊稱 MSDN) 訂閱。 本文概述 Azure 和使用 Azure 庫圖像中執行的 Windows 用戶端的資格需求。


## <a name="subscription-eligibility"></a>訂閱資格
Visual Studio 訂閱 （人員取得 Visual Studio 訂閱授權） 可以使用 Windows 用戶端開發和測試之用。 硬體和 Azure 虛擬機器執行任何類型的 Azure 訂閱，可以使用 Windows 的用戶端。 Windows 用戶端可能不會部署至或用於上 Azure 用於標準生產環境，或使用的不是作用中的 Visual Studio 訂閱者的人員。

您方便時觀看，我們有提供特定 Windows 10 圖像從 Azure] 庫中[符合資格的開發測試提供](#eligible-offers)。 [適當地作準備及建立](virtual-machines-windows-prepare-for-upload-vhd-image.md)64 位元 Windows 7、 Windows 8 或 Windows 10 圖像，然後[上傳至 Azure](virtual-machines-windows-upload-image.md)中提供的任何類型的 visual Studio 訂閱者也可以。 使用會保持限於作用中的 Visual Studio 訂閱者開發/測試。


## <a name="eligible-offers"></a>優惠資格
下列表格詳細說明優惠方案部署透過 Azure 庫 Windows 10 的識別碼。 在 Windows 10 圖像只看到下列優惠。 需要執行 Windows 用戶端在不同的方案類型 visual Studio 訂閱者要求您為[累積作準備及建立](virtual-machines-windows-prepare-for-upload-vhd-image.md)64 位元 Windows 7、 Windows 8 或 Windows 10 的圖像，[然後上傳至 Azure](virtual-machines-windows-upload-image.md)。

| 提供名稱 | 提供數字 | 可用的用戶端圖像 |
|:-----------|:------------:|:-----------------------:|
| [Pay-As-You-Go 開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)                          | 0023 P | 在 Windows 10 |
| [Visual Studio 企業 (MPN) 版訂閱](https://azure.microsoft.com/offers/ms-azr-0029p/)      | 0029 P | 在 Windows 10 |
| [Visual Studio 專業版訂閱](https://azure.microsoft.com/offers/ms-azr-0059p/)          | 0059 P | 在 Windows 10 |
| [Visual Studio 測試專業版訂閱](https://azure.microsoft.com/offers/ms-azr-0060p/)     | 0060 P | 在 Windows 10 |
| [Visual Studio 進階版的 MSDN （優勢）](https://azure.microsoft.com/offers/ms-azr-0061p/)       | 0061 P | 在 Windows 10 |
| [Visual Studio 企業版訂閱](https://azure.microsoft.com/offers/ms-azr-0063p/)            | 0063 P | 在 Windows 10 |
| [Visual Studio 企業 (BizSpark) 版訂閱](https://azure.microsoft.com/offers/ms-azr-0064p/) | 0064 P | 在 Windows 10 |
| [企業開發/測試](https://azure.microsoft.com/ofers/ms-azr-0148p/)                              | 0148 P | 在 Windows 10 |


## <a name="check-your-azure-subscription"></a>檢查您 Azure 的訂閱
如果您不知道您提供的識別碼，您可以透過 Azure 入口網站或入口網站帳戶取得它。

Azure 入口網站中的 '訂閱' 刀會加註的訂閱優惠識別碼︰

![從 Azure 入口網站提供識別碼詳細資料](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png) 

您也可以檢視從 Azure 帳戶入口網站的[[訂閱] 索引標籤](http://account.windowsazure.com/Subscriptions)的優惠識別碼︰

![從 Azure 帳戶入口網站提供識別碼詳細資料](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png) 


## <a name="next-steps"></a>後續步驟
您現在可以部署使用[PowerShell](virtual-machines-windows-ps-create.md)、[資源管理員範本](virtual-machines-windows-ps-template.md)或[Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)您 Vm。
