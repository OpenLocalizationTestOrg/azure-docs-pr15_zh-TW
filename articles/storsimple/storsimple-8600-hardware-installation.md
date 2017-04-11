<properties
   pageTitle="安裝 StorSimple 從 8600 裝置 |Microsoft Azure"
   description="說明如何解壓縮和建立機架連接，部署，並將軟體設定之前，纜線 StorSimple 從 8600 裝置。"
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
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>解壓縮，機架-裝載及纜線 StorSimple 從 8600 裝置

## <a name="overview"></a>概觀
Microsoft Azure StorSimple 從 8600 是雙圍繞符號的裝置，並由主要及 EBOD 圍繞符號。 本教學課程說明如何解壓縮、 機架連接之前, 纜線 StorSimple 從 8600 裝置硬體設定 StorSimple 軟體。

## <a name="unpack-your-storsimple-8600-device"></a>解壓縮 StorSimple 從 8600 裝置

下列步驟提供如何解壓縮 StorSimple 從 8600 存放裝置的清除、 詳細指示。 此裝置隨附的兩個方塊中，一個用於主要的圍繞符號，另一個用於 EBOD 圍繞符號。 這兩個方塊然後放置在單一方塊。

### <a name="prepare-to-unpack-your-device"></a>準備解壓縮您的裝置

解壓縮您的裝置前，請檢閱下列資訊。


![警告圖示](./media/storsimple-safety/IC740879.png)![高負載圖示](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)**警告 ！**

1. 請確定您已有兩人可用於管理裝置的粗細，如果您以手動方式處理。 完全設定的圍繞符號可以衡量最 32 公斤 （70 磅。）。
1. 將方塊一般、 層級的介面上。

接下來，請完成下列步驟，以解壓縮您的裝置。

#### <a name="to-unpack-your-device"></a>若要解壓縮您的裝置

1. 檢查方塊和包裝海裡 crushes、 剪下、 水產生的損害，或任何其他明顯損壞跡象。 如果方塊或包裝嚴重損毀，無法開啟的方塊。 請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)來協助您評估裝置是否運作正常。

2. 開啟 [外部] 方塊，然後踏出對應至主要和 EBOD 隨函附件的兩個方塊。 您現在可以解壓縮的主要及 EBOD 隨函附件。 下圖顯示未解壓縮的隨函附件的其中一個檢視。

    ![解壓縮存放裝置](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

    **存放裝置的 [解壓縮] 檢視**

     標籤 | 描述
     ----- | -------------
     1     | 封裝方塊
     2     | SA 纜線 （在 [附屬應用程式和纜線匣）
     3     | 底部海裡
     4     | 裝置
     5     | 上方海裡
     6     | 配件] 方塊

3. 解壓縮後的兩個方塊中，確定您有︰

  - 1 （主要的圍繞符號和 EBOD 圍繞符號是以個別的兩個方塊） 的主要圍繞符號
  - 1 EBOD 圍繞符號
  - 4 的電源線，每個方塊中的 2
  - 2 SA 纜線 （連接主要的圍繞符號到 EBOD 圍繞符號）
  - 1 交叉乙太網路纜線
  - 2 循序主控台纜線
  - 序列存取 1 循序 USB 轉換程式
  - 4 QSFP-至-SFP + 10 GbE 網路介面搭配介面卡
  - 2 建立機架裝載套件 （4 邊滑軌與裝載硬體，2 每個主要的圍繞符號和 EBOD 圍繞符號），1 每個方塊中
  - 快速入門的文件

    如果您沒有收到任何項目列於上方，[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。  

下一步是機架裝載您的裝置。

## <a name="rack-mount-your-storsimple-8600-device"></a>機架裝載 StorSimple 從 8600 裝置

依照安裝 StorSimple 從 8600 存放裝置在標準 19 英吋機架前端與後文章中的下一個步驟。 此裝置隨附兩個附件︰ 主要的圍繞符號和 EBOD 圍繞符號。 這兩種都需要會機架裝載。

安裝包含多個步驟，下列程序中所提及的每一種。

> [AZURE.IMPORTANT]
機架裝載能正常運作時，必須 StorSimple 裝置。



### <a name="site-preparation"></a>網站準備

在標準 19 英吋機架具有最上層和後的文章，必須安裝隨函附件。 使用下列程序機架安裝的準備。

#### <a name="to-prepare-the-site-for-rack-installation"></a>準備機架安裝的網站

1. 請確認主要及 EBOD 隨函附件休止安全地在一般、 穩定，和層級工時曲面圖 （或類似）。

2. 請確認您要設定的網站標準電源從獨立的來源或機架 Power 分配單位 (PDU) 與不斷電 （畫面）。

3. 請確定該一個 4U (2 X 2U) 位置會提供您要連接的隨函附件機架。

![警告圖示](./media/storsimple-safety/IC740879.png)![高負載圖示](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)**警告 ！**

 請確定您已有兩人可用於管理粗細，當您以手動方式處理裝置設定。 完全設定的圍繞符號可以衡量最 32 公斤 （70 磅。）。

### <a name="rack-prerequisites"></a>機架先決條件

附件是安裝在標準 19 英吋機架櫃中的設計︰

- 從機架張貼 27.84 英吋為單位的最小深度
- 32 公斤裝置的最大重量
- 最大的返回壓力的 5 Pascal （0.5 mm 水量測）

### <a name="rack-mounting-rail-kit"></a>機架裝載 rail 損毀套件

用於 19 英吋機架封包，將會提供一組裝載滑軌。 測試滑軌以控點的最大的圍繞符號粗細。 這些滑軌也可讓安裝的多個附件，而不會遺失機架內的空間。 第一次安裝 EBOD 圍繞符號。

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>若要在機殼安裝 EBOD 圍繞符號

2. 只有當您的裝置上未安裝內部滑軌，請執行此步驟。 一般而言，在工廠安裝內部滑軌。 如果未安裝滑軌，然後安裝左邊界] 和 [右邊界的投影片的圍繞符號底座旁邊。 他們附加使用六個公制螺絲每一邊。 為了協助方向， **LH – 最上層**和**RH – 最上層**，，則標示 rail 損毀投影片，並根據的圍繞符號後方貼附結束具有錐形的結束。

    ![圍繞符號底座附加 rail 損毀投影片](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **附加的圍繞符號旁邊的 rail 損毀的投影片**

    標籤 | 描述
    ----- | -----------
    1     | M 3 x 4] 按鈕不對螺絲
    2     | 底座投影片

3. 機架封包垂直成員附加的左邊的界和右邊界的組件。 角括弧標示**LH**、 **RH**，且**設定此側**引導您完成正確的方向。

4. 找出在最上層和 rail 損毀組件的後方 rail 損毀的 pin。 延伸 rail 損毀，配合之間機架文章，然後插入的正面及後方機架文章垂直成員文章的 pin。 請務必 rail 損毀組件的層級。

5. 藉由使用兩個提供公制螺絲保護機架 rail 損毀組件垂直的成員。 使用一個螺絲前面，而另一個在後方。

6. 其他 rail 損毀組件重複這些步驟。

     ![機架封包附加 rail 損毀投影片](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **機架附加 rail 損毀的組件**

     標籤 | 描述
     ----- | -----------
     1     | 鉗制螺絲
     2     | 方形漏洞前端機架文章螺絲
     3     | 向左前端 rail 損毀位置 pin 碼
     4     | 鉗制螺絲
     5     | 左後 rail 損毀位置 pin 碼

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>裝載在框架 EBOD 圍繞符號

使用只安裝機架滑軌，執行下列步驟來裝載在框架 EBOD 圍繞符號。

#### <a name="to-mount-the-ebod-enclosure"></a>裝載 EBOD 圍繞符號

1. 小幫手]，請拿起圍繞符號與對齊機架滑軌。

2. 謹慎滑軌，插入圍繞符號，然後再推它完全到機架封包。

    ![機架中插入裝置](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **裝載在框架圍繞符號**

3. 移除左、 右前端法蘭大寫字抽取免費大寫字。 法蘭大寫字只要貼齊到 flanges。

4. 將機架，即可安裝一個提供的 Phillips 不對螺絲透過每個法蘭左右保護圍繞符號。

4. 藉由按這些到適當的位置，並進行對齊到定位安裝法蘭大寫字。

     ![安裝法蘭大寫字](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **安裝法蘭大寫字**

     標籤 | 描述
     ----- | -----------
     1     | 圍繞符號繫牢螺絲


### <a name="mounting-the-primary-enclosure-in-the-rack"></a>裝載在框架主要的圍繞符號

當您完成裝載 EBOD 圍繞符號之後，您必須裝載主要的圍繞符號的相同步驟進行。

> [AZURE.NOTE]
>
> - 就可以在主要的圍繞符號與 EBOD 圍繞符號之間機架幾個有空的位置。
> - 使用提供 2 m SA 纜線連接至 EBOD 圍繞符號的主要的圍繞符號。
> - 有無限制 EBOD 單位標頭單位的相對位置。 因此，主要的圍繞符號放置在上方的位置和下列 EBOD 圍繞符號，或反向操作。

下一步是纜線、 網路和序列存取您的裝置。

## <a name="cable-your-storsimple-8600-device"></a>纜線 StorSimple 從 8600 裝置

下列程序說明如何纜線您 StorSimple 從 8600 的裝置、 網路和序列連線。

### <a name="prerequisites"></a>必要條件

若要纜線您的裝置之前，您需要︰

- 您主要的圍繞符號和 EBOD 圍繞符號] 完全解壓縮
- 4 的電源線 (2 每個主要和 EBOD 圍繞符號) 裝置隨附的
- 提供的裝置連接至主要的圍繞符號的 EBOD 圍繞符號 2 SA 纜線
- Access 2 Power 分配單位 (PDUs) （建議使用）
- 網路纜線
- 提供序列纜線
- 循序 USB 轉換程式 （如有需要），在您的電腦上已安裝適當驅動程式
- 提供 4 QSFP-至-SFP + 10 GbE 網路介面搭配介面卡
- [支援硬體 StorSimple 裝置上之 10 GbE 網路介面](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SA 及 Power 纜線

您的裝置有主要的圍繞符號] 與 [EBOD 圍繞符號。 需要會放在一起的循序附加 SCSI (SA) 連線與 power 妥的單位。

時設定第一次此裝置，請執行步驟的第一次 SA 纜線，然後完成 [power 纜線的步驟。

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>網路纜線

您的裝置已在作用中待命設定︰ 在任何時候一個控制器模組作用中，而處理所有的磁碟和網路作業時的控制站模組會準備就緒。 如果發生控制器失敗，備用控制器立即啟動，並持續所有的磁碟和網路作業。

若要支援此多餘控制器容錯移轉，必須先纜線您裝置的網路，如下所示。

#### <a name="to-cable-for-network-connection"></a>網路連線纜線

1. 您的裝置有每個控制器上的六個網路介面︰ 四個 1 Gbps 和兩個 10 Gbps 乙太網路連接埠。 請參閱下列圖例識別資料上的連接埠後擋板您的裝置。

     ![後擋板 8600 裝置的](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **返回您的裝置顯示資料連接埠**

     標籤   | 描述
     ------- | -----------
     0,1,4,5 |  1 GbE 網路介面
     2，3     | 10 GbE 網路介面
     6       | 序列連接埠



1. 請參閱下列網路纜線的圖表。 （藍色實線會顯示最小的網路設定。 高可用性和效能，所需的其他設定會顯示虛線。）

![纜線 4U 裝置的網路](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**網路纜線您為裝置**

標籤 | 描述
----- | -----------
A    | 透過網際網路存取的區域網路
B    | 控制站 0
C    | PCM 0
D    | 控制站 1
E    | PCM 1
F    | EBOD 控制器 0
G    | EBOD 控制站 1
H、 我  | 主機 （例如檔案伺服器）
0-5  | 網路介面
6    | 主要的圍繞符號
7    | EBOD 圍繞符號

纜線裝置時的最小設定的需求︰


- 網路介面至少兩個連線使用一個雲端存取，一個用於 iSCSI 每個控制器上。 資料 0 自動啟用和設定裝置的循序主控台透過連接埠。 除了資料 0，另一個資料連接埠也必須透過 Azure 傳統入口網站設定。 在此情況下，將 0 的資料連線到主要區域網路 （網路存取網際網路） 連接埠。 其他資料連接埠可以連線到舊/iSCSI 區域網路 （虛擬區域網路） 網路區段中，根據預定的角色。

- 每個控制器上相同的介面連線到相同的網路，以確保可用性發生控制器容錯移轉。 例如，如果您選擇將資料 0 的其中一個控制站的資料 3，您需要連線其他控制器上的對應資料 0 和資料 3。

請注意，高可用性和效能︰


- 可能的話，請設定網路介面雲端存取 (1 GbE) 的一組與另一組 iscsi (建議使用 10 GbE) 每個控制器上。

- 可能的話，連接網路介面每個控制站兩個不同的選項，以確保針對切換失敗的可用性。 下圖說明 10 的兩個 GbE 網路介面，資料 2 和 3 的資料從每個控制器連接到兩個不同的選項。 如需詳細資訊，請參閱下[可用性 StorSimple 裝置的需求](storsimple-system-requirements.md#high-availability-requirements-for-storsimple)**網路介面**。

>[AZURE.NOTE] 如果使用您的 10 個 GbE 網路介面 SFP + 收發，請使用提供的 QSFP-SFP + 介面卡。 如需詳細資訊，請前往[支援硬體 StorSimple 裝置上之 10 GbE 網路介面](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)。

### <a name="serial-port-cabling"></a>序列連接埠纜線

執行下列步驟，以纜線序列連接埠。

#### <a name="to-cable-for-serial-connection"></a>序列連線纜線

1. 您的裝置有扳手的符號圖示來識別每個控制器上序列連接埠。 若要找出序列連接埠，請參閱圖例的資料連接埠顯示您的裝置上。

2. 找出在您的裝置後擋板控制器作用中。 閃爍的藍色 LED 表示控制器是作用中。

3. 使用提供序列纜線 （如有需要您的膝上型電腦的 USB 循序轉換程式），並將您的主控台或電腦 （終端機裝置模擬） 連線至作用中的控制站的循序連接埠。

4. 在您的電腦上安裝 （隨附於裝置） 的循序 USB 驅動程式。

5. 設定序列連線，如下所示︰
   - 115200 傳輸
   - 8 資料位元
   - 1 停止位元
   - 任何不一致性致歉
   - 設定為 [**無**流量控制

6. 驗證連線正常運作的主控台上的 enter。 序列主控台功能表應該會出現。

> [AZURE.NOTE] **幕後管理︰**裝置已安裝在遠端資料中心或在電腦的有限存取權的聊天室，請確定兩個控制站的循序連線會永遠連線到序列主控台切換或類似設備。 這個選項可讓-頻遠端控制 」 和 「 若網路中斷或非預期的失敗的支援作業。

您已完成纜線連接您的裝置、 網路存取，請和序列連線。下一步是在您的裝置上設定軟體。

## <a name="next-steps"></a>後續步驟

您已準備好要[部署及設定您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough-u2.md)。
