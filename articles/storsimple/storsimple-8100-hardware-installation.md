<properties
   pageTitle="安裝 StorSimple 8100 裝置 |Microsoft Azure"
   description="說明如何解壓縮和建立機架連接，部署，並將軟體設定之前，纜線 StorSimple 8100 裝置。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>解壓縮，機架-裝載及纜線 StorSimple 8100 裝置

## <a name="overview"></a>概觀

您的 Microsoft Azure StorSimple 8100 是單一圍繞符號、 機架裝載的裝置。 本教學課程說明如何解壓縮，機架-裝載及纜線 StorSimple 8100 裝置硬體設定並部署 StorSimple 裝置之前。

## <a name="unpack-your-storsimple-8100-device"></a>解壓縮 StorSimple 8100 裝置

下列步驟提供有關如何解壓縮 StorSimple 8100 存放裝置的清除、 詳細說明。 此裝置隨附的單一中。

### <a name="prepare-to-unpack-your-device"></a>準備解壓縮您的裝置

解壓縮您的裝置前，請檢閱下列資訊。

![警告圖示](./media/storsimple-safety/IC740879.png)![高負載圖示](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png)**警告 ！**

1. 請確定您已有兩人可用於管理圍繞符號的粗細，如果您以手動方式處理。 完全設定的圍繞符號可以衡量最 32 公斤 （70 磅。）。
1. 將方塊一般、 層級的介面上。

接下來，請完成下列步驟，以解壓縮您的裝置。

#### <a name="to-unpack-your-device"></a>若要解壓縮您的裝置

1. 檢查方塊和包裝海裡 crushes、 剪下、 水產生的損害，或任何其他明顯損壞跡象。 如果方塊或包裝嚴重損毀，無法開啟的方塊。 請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)來協助您評估裝置是否運作正常。

2. 解壓縮] 方塊。 下圖顯示 StorSimple 裝置的 [解壓縮] 檢視。

     ![解壓縮存放裝置](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **存放裝置的 [解壓縮] 檢視**

     標籤 | 描述
     ----- | -------------
     1     | 封裝方塊
     2     | 底部海裡
     3     | 裝置
     4     | 上方海裡
     5     | 配件] 方塊


3. 之後解壓縮] 方塊中，請確定您有︰

   - 1 的單一圍繞符號裝置
   - 2 電源線
   - 1 交叉乙太網路纜線
   - 2 循序主控台纜線
   - 序列存取 1 循序 USB 轉換程式
   - 1 竄改 T10 螺絲起子
   - 4 QSFP-至-SFP + 10 GbE 網路介面搭配介面卡
   - 1 框架承載套件 （2 邊滑軌與裝載硬體）
   - 快速入門文件

    如果您沒有收到任何項目列於上方，[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

下一步是機架裝載您的裝置。

## <a name="rack-mount-your-storsimple-8100-device"></a>機架裝載 StorSimple 8100 裝置

依照安裝 StorSimple 8100 存放裝置在標準 19 英吋機架前端與後文章中的下一個步驟。 StorSimple 8100 裝置有單一主要圍繞符號。

安裝包含多個步驟，下列程序中所提及的每一種。

> [AZURE.IMPORTANT]
機架裝載能正常運作時，必須 StorSimple 裝置。

### <a name="prepare-the-site"></a>準備網站

在標準 19 英吋機架具有最上層和後的文章，必須安裝裝置。 使用下列程序機架安裝的準備。

#### <a name="to-prepare-the-site-for-rack-installation"></a>準備機架安裝的網站

1. 請確定該裝置將安全地停留在一般、 穩定，和層級的工作，運用 （或類似）。

2. 請確認您要設定的網站標準電源從獨立的來源或機架 power 分配單位 (PDU 與不斷電 （畫面）)。

3. 請確定該一個 2U 位置才會提供您打算裝載裝置機架。

![警告圖示](./media/storsimple-safety/IC740879.png)![高負載圖示](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png)**警告 ！**

請確定您已有兩人可用於管理粗細，當您以手動方式處理裝置設定。 完全設定的圍繞符號可以衡量最 32 公斤 （70 磅。）。

### <a name="rack-prerequisites"></a>機架先決條件

在標準 19 英吋機架櫥櫃安裝的是設計用 8100 圍繞符號︰

- 從機架張貼 27.84 英吋的最小深度。
- 32 公斤裝置的最大重量
- 5 Pascal （0.5 mm 水量測） 的最大的返回壓力。

### <a name="rack-mounting-rail-kit"></a>機架裝載 rail 損毀套件

提供一組裝載滑軌用於 19 英吋機架封包。 測試滑軌以控點的最大的圍繞符號粗細。 這些滑軌也可讓安裝的多個附件，而不會遺失任何機架內的空間。


#### <a name="to-install-the-device-on-the-rails"></a>若要在機殼安裝裝置

2. 只有當您的裝置上未安裝內部滑軌，請執行此步驟。 一般而言，在工廠安裝內部滑軌。 如果未安裝滑軌，然後安裝左邊界] 和 [右邊界的投影片的圍繞符號底座旁邊。 他們附加使用六個公制螺絲每一邊。 為了協助方向， **LH – 最上層**和**RH – 最上層**，，則標示 rail 損毀投影片，並根據的圍繞符號後方貼附結束具有錐形的結束。<br/>

    ![圍繞符號底座附加 rail 損毀的投影片](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
    **附加內部 rail 損毀的端圍繞符號的投影片**

       標籤 | 描述
       ----- | -----------
       1     | M 3 x 4] 按鈕不對螺絲
       2     | 底座投影片

3. 附加到機架封包垂直成員的外部的左邊的界和外部的右邊界的組件。 角括弧標示**LH**、 **RH**，且**設定此側**引導您完成正確的方向。

4. 找出在最上層和 rail 損毀組件的後方 rail 損毀的 pin。 延伸 rail 損毀，配合之間機架文章，然後插入的正面及後機架文章垂直成員文章的 pin。 請務必 rail 損毀組件的層級。

5. 請使用提供的公制螺絲的兩個垂直成員的安全性機架 rail 損毀組件。 使用一個螺絲前面，而另一個在後方。

6. 其他 rail 損毀組件重複這些步驟。<br/>

     ![機架封包附加 rail 損毀投影片](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **機架附加外部 rail 損毀的組件**

     標籤 | 描述
     ----- | -----------
     1     | 鉗制螺絲
     2     | 方形漏洞前端機架文章螺絲
     3     | 左 rail 損毀前端位置 pin 碼
     4     | 鉗制螺絲
     5     | 左 rail 損毀後位置 pin 碼


### <a name="mounting-the-device-in-the-rack"></a>裝載在框架的裝置

使用機架滑軌只安裝，請執行下列步驟裝載在框架的裝置。

#### <a name="to-mount-the-device"></a>裝載裝置

1. 小幫手]，請拿起圍繞符號與對齊機架滑軌。

2. 謹慎裝置插入至滑軌，然後再推裝置完全到機架封包。<br/>

    ![機架中插入裝置](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **裝載在框架的裝置**


3. 移除左、 右前端法蘭大寫字抽取免費大寫字。 法蘭大寫字只要貼齊到 flanges。

5. 安裝一個提供的 Phillips 不對螺絲透過每個法蘭左右來保護在框架圍繞符號。

4. 按這些到適當的位置，再貼齊其位置中安裝法蘭大寫字。<br/>

     ![安裝法蘭大寫字](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)

    **安裝法蘭大寫字**

     標籤 | 描述
     ----- | -----------
     1     | 圍繞符號繫牢螺絲

下一步是纜線、 網路和序列存取您的裝置。

## <a name="cable-your-storsimple-8100-device"></a>纜線 StorSimple 8100 裝置

下列程序說明如何纜線您 StorSimple 8100 的裝置、 網路和序列連線。

### <a name="prerequisites"></a>必要條件

您的裝置纜線連接之前，您需要︰

- 您的儲存裝置中完全解壓縮和機架裝載。

- 裝置隨附的 2 power 纜線

- Access 2 Power 分配單位 （建議使用）。

- 網路纜線

- 提供序列纜線

- 序列 USB 轉換程式 （如有需要），在您的電腦上已安裝適當驅動程式

- 提供 4 QSFP-至-SFP + 10 GbE 網路介面搭配介面卡

- [支援硬體 StorSimple 裝置上之 10 GbE 網路介面](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### <a name="power-cabling"></a>Power 纜線

您的裝置包含重複和製冷模組 (PCMs)。 這兩個 PCMs 必須安裝並連線到不同的電源來源，以確保高可用性。

執行下列步驟，以纜線 power 您的裝置。

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>網路纜線

您的裝置是作用中待命的設定︰ 在任何時候一個控制器模組作用中，而處理所有的磁碟和網路作業時的控制站模組會準備就緒。 如果控制器失敗，請備用控制器就會立即啟動，並持續所有磁碟和網路作業。

若要支援此多餘控制器容錯移轉，必須先纜線您裝置的網路，如以下步驟所述。

#### <a name="to-cable-for-network-connection"></a>網路連線纜線

1. 您的裝置有每個控制器上的六個網路介面︰ 四個 1 Gbps，以及兩個 10 Gbps 乙太網路連接埠。 找出您的裝置後擋板的各種資料連接埠。

    ![後擋板 8100 裝置的](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **返回裝置的顯示資料的連接埠**

     標籤   | 描述
     ------- | -----------
     0,1,4,5 |  1 GbE 網路介面
     2，3     | 10 GbE 網路介面
     6       | 序列連接埠

2. 請參閱下列網路纜線的圖表。 （藍色實線會顯示最小的網路設定。 可用性及效能所需的額外設定會顯示虛線。）


    ![纜線 2U 裝置的網路](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **網路纜線您為裝置**


  	|標籤 | 描述 |
  	|----- | ----------- |
  	| A    | 透過網際網路存取的區域網路 |
  	| B    | 控制站 0 |
  	| C    | PCM 0 |
  	| D    | 控制站 1 |
  	| E    | PCM 1 |
  	| F、 G | 主機 |
  	| 0-5  | 網路介面 |



纜線裝置時的最小設定的需求︰


- 網路介面至少兩個連線使用一個雲端存取，一個用於 iSCSI 每個控制器上。 資料 0 自動啟用和設定裝置的循序主控台透過連接埠。 除了資料 0，另一個資料連接埠也必須透過 Azure 傳統入口網站設定。 在此情況下，將 0 的資料連線到主要區域網路 （網路存取網際網路） 連接埠。 其他資料連接埠可以連線到舊/iSCSI 區域網路 （虛擬區域網路） 網路區段中，根據預定的角色。

- 每個控制器上相同的介面連線到相同的網路，以確保可用性發生控制器容錯移轉。 例如，如果您選擇將資料 0 的其中一個控制站的資料 3，您需要連線其他控制器上的對應資料 0 和資料 3。

請注意，高可用性和效能︰


- 可能的話，請設定網路介面雲端存取 (1 GbE) 的一組與另一組 iscsi (建議使用 10 GbE) 每個控制器上。

- 可能的話，連接網路介面每個控制站兩個不同的選項，以確保針對切換失敗的可用性。 下圖說明 10 的兩個 GbE 網路介面，資料 2 和 3 的資料從每個控制器連接到兩個不同的選項。

如需詳細資訊，請參閱下[可用性 StorSimple 裝置的需求](storsimple-system-requirements.md#high-availability-requirements-for-storsimple)**網路介面**。

>[AZURE.NOTE] 如果您使用您 10 GbE 網路介面 SFP + 收發，請使用提供的 QSFP-SFP + 介面卡。 如需詳細資訊，請前往[支援硬體 StorSimple 裝置上之 10 GbE 網路介面](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)。



### <a name="serial-port-cabling"></a>序列連接埠纜線

執行下列步驟，以纜線序列連接埠。

#### <a name="to-cable-for-serial-connection"></a>序列連線纜線

1. 您的裝置有扳手的符號圖示來識別每個控制器上序列連接埠。 請參考，找出序列的連接埠後擋板您的裝置上的 [[網路纜線](#network-cabling)] 區段中的圖例。

2. 找出在您的裝置後擋板控制器作用中。 閃爍的藍色 LED 表示控制器是作用中。

3. 使用提供序列纜線 （如有需要您的膝上型電腦的 USB 循序轉換程式），並將您的主控台或電腦 （終端機裝置模擬） 連線至作用中的控制站的循序連接埠。

4. 在您的電腦上安裝 （隨附於裝置） 的循序 USB 驅動程式。

5. 序列的連線設定，如下所示︰ 115200 傳輸、 8 資料位元、 1 停止位元，沒有不一致性致歉及流量控制設定為 [無]。

6. 驗證連線正常運作的主控台上的 enter。 序列主控台功能表應該會出現。

>[AZURE.NOTE] **幕後管理**︰ 裝置已安裝在遠端資料中心或在電腦的有限存取權的聊天室，請確定兩個控制站的循序連線會永遠連線到序列主控台切換或類似設備。 這個選項可讓-頻遠端控制和支援作業有網路中斷或非預期的失敗次數。

您的裝置是現在妥用於 power 網路存取與循序連線。 下一步是設定軟體及部署您的裝置。

## <a name="next-steps"></a>後續步驟

瞭解如何[部署及設定您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。
