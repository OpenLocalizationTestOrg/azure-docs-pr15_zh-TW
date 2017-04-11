<properties
   pageTitle="技術建立的虛擬機器圖像 Azure Marketplace 的必要條件 |Microsoft Azure"
   description="瞭解建立及部署其他人的 [Azure Marketplace] 以購買的虛擬機器圖像的需求。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="04/29/2016"
  ms.author="hascipio; v-divte"/>

# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>技術建立的虛擬機器圖像 Azure Marketplace 的先決條件
閱讀前開頭的程序並瞭解位置及原因執行每一個步驟。 盡可能，您應準備您的公司資訊及其他資料，下載所需的工具，及/或開頭優惠建立程序之前，先建立技術的元件。 這些項目應該檢閱這份文件中的 [清除。  

## <a name="download-needed-tools--applications"></a>下載所需的工具及應用程式
您應該準備好開始此程序之前的下列項目︰

- 根據您的目標的作業系統，安裝[Azure PowerShell cmdlet](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids)或[Linux 命令列介面工具](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409)從[Azure 下載](https://azure.microsoft.com/downloads/)頁面。
- 從 CodePlex 安裝 Azure 儲存檔案總管。
- 下載並安裝的 Azure 認證的憑證測試工具︰
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913)。 您需要的 windows 電腦執行 [憑證] 工具。 如果您沒有可用的 Windows 電腦，您可以執行使用 Windows 型 VM Azure 中的工具。

## <a name="platforms-supported"></a>支援的平台
您可以開發 Windows 或 Linux 的 Azure 基礎 Vm。 發佈程序，例如建立 Azure 相容虛擬硬碟 (VHD)-使用不同的工具和步驟視作業系統而定，您正在使用的某些項目︰  

- 如果您使用的 Linux，請參閱 「 建立 （Linux 型） Azure 相容 VHD 」 一節的[虛擬機器圖像發佈指南](marketplace-publishing-vm-image-creation.md)。
- 如果您使用的 Windows，請參閱 「 建立 (windows) Azure 相容 VHD 」 一節的[虛擬機器圖像發佈指南](marketplace-publishing-vm-image-creation.md)。

> [AZURE.NOTE] 您需要 windows 電腦存取︰
- 執行憑證驗證工具。
- 建立 VHD 憑證送出的 VHD 共用 access 簽章 URL。

## <a name="develop-your-vhd"></a>開發您 VHD
您可以開發 Azure Vhd 內部部署或雲端中︰

- 雲端開發表示上存放在 Azure VHD 遠端執行所有步驟。
- 內部部署開發需要下載 VHD，以及開發使用內部部署基礎結構。 雖然這可能，但我們不建議使用。 請注意，針對 Windows] 或 [SQL 開發內部部署要求您擁有內部部署的相關授權產品金鑰。 您無法加入或建立 VM 之後安裝 SQL Server。 您也必須將您的提議依據核准 SQL 圖像從 Azure 入口網站中。 如果您決定要開發內部部署，您必須不同如果您要開發雲端中執行一些步驟。 您可以[建立內部部署 VM 圖像](marketplace-publishing-vm-image-creation-on-premise.md)中找到的相關資訊。

## <a name="next-steps"></a>後續步驟
現在，您可以檢閱必要條件，完成的必要工作，您可以建立虛擬機器圖像提供為詳細[虛擬機器圖像發佈指南](marketplace-publishing-vm-image-creation.md)中向前移動。

## <a name="see-also"></a>另請參閱
- [快速入門︰ 如何將提供發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)
- [建立虛擬機器執行 Windows Azure 預覽入口網站中](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
