<properties 
pageTitle="部署上 Azure VM S/4 HANA 或 BW/4 HANA |Microsoft Azure" 
description="部署 S/4 HANA 或 BW/4 HANA Azure VM 上" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/> 
<tags 
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/> 


# <a name="deploying-s4-hana-or-bw4-hana-on-microsoft-azure"></a>部署 S/4 HANA 或在 Microsoft Azure BW/4 HANA 

本文將說明如何部署 S/4 HANA SAP 雲端應用程式庫 3.0 透過 Microsoft Azure 上。
螢幕擷取畫面顯示程序逐步解說。 部署其他 SAP HANA 型解決方案等 BW/4 HANA 是相同的程序觀點。 其中一個只要有選取不同的方案。

開始使用 SAP 雲端應用程式庫 (SAP CAL) 到[這裡](https://cal.sap.com/)。 有關於新的[SAP 雲端應用程式庫 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)SAP 從部落格。 


以下螢幕擷取畫面顯示如何部署上 Microsoft Azure S/4 HANA 逐步。 程序其他方案 likeBW/4 HANA 的一樣。


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

第一張圖片會顯示可用的 Microsoft Azure 所有 SAP CAL HANA 為基礎的解決方案。
Exemplarily 」 SAP S/4 HANA 內部部署版本 」 （在螢幕擷取畫面下方的解決方案） 選擇到完成程序。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

首先必須建立新的 SAP CAL 帳戶。 目前有兩個選項 Azure-的標準 Azure 和 Azure 合作夥伴 21Vianet 所營運的 china （中國） 大陸上。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

然後，其中一個有輸入位於 Azure 入口網站-也請參閱進一步下如何取得 Azure 訂閱 ID。 之後 Azure 管理憑證必須下載。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

在新的 Azure 入口網站的項目尋找的項目 」 訂閱 「 左側。 按一下以顯示所有的作用中訂閱您的使用者。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

選取其中一個訂閱，然後選擇 [「 管理憑證 」 有說明的是新的概念新 Azure 資源管理員模型中使用 「 服務原則]。
SAP CAL 不尚未調整為這個新的模型，並仍需要 「 傳統 「 模型和前者 Azure 入口網站，使用 [管理憑證。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

以下其中一個可以看到離職 Azure 入口網站。 管理認證的上傳可提供 SAP CAL 建立虛擬機器中的客戶訂閱的權限。 [訂閱] 底下，其中一個索引標籤可以尋找具有輸入 SAP CAL 入口網站中的訂閱識別碼。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

在第二個索引標籤，則再上傳管理憑證從 SAP CAL 之前已下載的。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

小] 對話方塊會顯示以選取下載的憑證檔案。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

憑證已上傳後可以內 SAP CAl 測試 SAP CAL 與客戶 Azure 訂閱之間的連線。 這就是告訴連線是有效應該出現小的訊息。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

帳戶安裝之後，其中一個有選取應該部署，並建立執行個體的解決方案。
使用 「 基本 」 模式時，就很重要。 輸入 [執行個體名稱]，選擇 [Azure 地區然後定義解決方案的主要密碼。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

根據大小和複雜的解決方案 （估計就會提供 SAP CAL） 段時間後會顯示為 「 使用中 」，並可供使用。 就是非常簡單。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

查看一方案的一些詳細資料，可以看到何種類型的 Vm 部署。 在此情況下建立不同的大小和用途的三個 Azure Vm。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Azure 入口網站中，虛擬機器可以找到相同的執行個體名稱所提供的 SAP CAL 開始。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

現在就可以連線至的方案，透過 SAP CAL 入口網站中的 [連線] 按鈕。 [小] 對話方塊包含說明方案所使用的所有預設認證使用者指南的連結。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

另一個選項是用戶端 Windows VM 登入並開始，例如預先設定的 SAP GUI。







