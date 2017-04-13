<properties 
pageTitle="Microsoft Azure SAP ERP 6.0 的部署 SAP IDE EHP7 SP3 |Microsoft Azure" 
description="Microsoft Azure SAP ERP 6.0 的部署 SAP IDE EHP7 SP3" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/> 
<tags 
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/> 


# <a name="deploying-sap-ides-ehp7-sp3-for-sap-erp-60-on-microsoft-azure"></a>Microsoft Azure SAP ERP 6.0 的部署 SAP IDE EHP7 SP3 

本文將說明如何部署 SAP IDE Microsoft Azure 上執行 SQL Server 與 Windows OS 透過 SAP 雲端應用程式庫 3.0。 螢幕擷取畫面顯示程序逐步解說。 部署清單中的其他方案是相同的程序觀點。 其中一個只要有選取不同的方案。

開始使用 SAP 雲端應用程式庫 (SAP CAL) 到[這裡](https://cal.sap.com/)。 有從 SAP 關於新的[SAP 雲端應用程式庫 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)部落格。 


以下螢幕擷取畫面顯示如何部署上 Microsoft Azure SAP IDE 逐步。 程序其他方案的相同的方式。


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

第一張圖片會顯示所有可用的 Microsoft Azure 的解決方案。 醒目提示才 Azure 上可用的 Windows 型 SAP IDE 解決方案選擇到完成程序。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

首先必須建立新的 SAP CAL 帳戶。 目前有兩個選項 Azure-的標準 Azure 和 Azure 合作夥伴 21Vianet 所營運的 china （中國） 大陸上。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

然後，其中一個有輸入位於 Azure 入口網站-也請參閱進一步下如何取得 Azure 訂閱 ID。 之後 Azure 管理憑證必須下載。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

在新的 Azure 入口網站的項目尋找的項目 」 訂閱 「 左側。 按一下以顯示所有的作用中訂閱您的使用者。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

選取其中一個訂閱，然後選擇 [「 管理憑證 」 有說明的是新的概念新 Azure 資源管理員模型中使用 「 服務原則]。
SAP CAL 不尚未調整為這個新的模型，並仍需要 「 傳統 「 模型和離職 Azure 入口網站，使用 [管理憑證。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

以下其中一個可以看到離職 Azure 入口網站。 管理認證的上傳可提供 SAP CAL 建立虛擬機器中的客戶訂閱的權限。 [訂閱] 底下，其中一個索引標籤可以尋找具有輸入 SAP CAL 入口網站中的訂閱識別碼。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

在第二個索引標籤，則再上傳管理憑證從 SAP CAL 之前已下載的。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

小] 對話方塊會顯示以選取下載的憑證檔案。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

憑證已上傳後可以內 SAP CAl 測試 SAP CAL 與客戶 Azure 訂閱之間的連線。 這就是告訴連線是有效應該出現小的訊息。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

帳戶安裝之後，其中一個有選取應該部署，並建立執行個體的解決方案。
使用 「 基本 」 模式時，就很重要。 輸入 [執行個體名稱]，選擇 [Azure 地區然後定義解決方案的主要密碼。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

根據大小和複雜的解決方案 （估計就會提供 SAP CAL） 段時間後會顯示為 「 使用中 」，並可供使用。 就是非常簡單。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

查看一方案的一些詳細資料，可以看到何種類型的 Vm 部署。 在此情況下也有一個單一 Azure VM 大小 D12 SAP CAL 所建立。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

Azure 入口網站中，虛擬機器可以找到相同的執行個體名稱所提供的 SAP CAL 開始。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

現在就可以連線至的方案，透過 SAP CAL 入口網站中的 [連線] 按鈕。 [小] 對話方塊包含說明方案所使用的所有預設認證使用者指南的連結。
[以下](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf)是 IDE 方案指南的連結。

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

另一個選項是登入 Windows VM 並開始，例如預先設定的 SAP GUI。





