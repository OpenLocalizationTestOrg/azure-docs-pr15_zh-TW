<properties 
   pageTitle="取代 StorSimple 裝置上的底座 |Microsoft Azure"
   description="說明如何移除並取代 StorSimple 主要圍繞符號或 EBOD 圍繞符號底座。"
   services="storsimple"
   documentationCenter=""
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

# <a name="replace-the-chassis-on-your-storsimple-device"></a>取代 StorSimple 裝置上底座

## <a name="overview"></a>概觀

本教學課程說明如何移除並取代底座 StorSimple 8000 數列裝置中。 StorSimple 8100 模型是以單一圍繞符號裝置 （一個底座），而從 8600 是雙圍繞符號的裝置 （兩個底座）。 如果從 8600 模型，有可能兩個底座裝置可能會失敗︰ 主要的圍繞符號或 EBOD 圍繞符號的底座底座。

在任一情況，Microsoft 隨附的替代底座是空的。 沒有 Power 及製冷的模組 (PCMs)，控制器模組實心狀態磁碟機 (SSDs)、 硬碟機 (HDDs) 或 EBOD 模組將會出現。

>[AZURE.IMPORTANT] 移除，取代底座之前檢閱[StorSimple 硬體元件替代](storsimple-hardware-component-replacement.md)的安全性資訊。

## <a name="remove-the-chassis"></a>移除底座

執行下列步驟，以移除底座 StorSimple 裝置上。

#### <a name="to-remove-a-chassis"></a>若要移除底座

1. 請確定已關閉 StorSimple 裝置，並中斷所有 power 來源。

2. 移除所有的網路和 SA 纜線，如果有的話。

3. 移除機架單位。

4. 移除每個磁碟機，請注意，移除其中的位置。 如需詳細資訊，請參閱[移除磁碟機](storsimple-disk-drive-replacement.md#remove-the-disk-drive)。

5. 在 EBOD 圍繞符號 （如果這是無法底座），移除 EBOD 控制器模組。 如需詳細資訊，請參閱[移除 EBOD 控制器](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller)。 

    在 [主要圍繞符號 （如果這是無法底座），移除控制器，並記下移除其中的位置。 如需詳細資訊，請參閱[移除控制器](storsimple-controller-replacement.md#remove-a-controller)。

## <a name="install-the-chassis"></a>安裝底座

執行下列步驟，以 StorSimple 裝置上安裝底座。

#### <a name="to-install-a-chassis"></a>若要安裝底座

1. 裝載在框架底座。 如需詳細資訊，請參閱[框架承載 StorSimple 8100 裝置](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device)或[框架承載 StorSimple 從 8600 裝置](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device)。

2. 底座裝載在框架之後，請在先前已安裝在同一個位置安裝控制器模組。

3. 安裝磁碟機中相同的位置和先前已安裝在的位置。

    >[AZURE.NOTE] 我們建議您先安裝 SSDs 位置，然後再安裝 [HDDs。

2. 機架和安裝元件裝載的裝置，您的裝置連接到適當的電源來源與裝置上開啟。 如需詳細資訊，請參閱[纜線 StorSimple 8100 裝置](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device)或[纜線 StorSimple 從 8600 裝置](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)。

## <a name="next-steps"></a>後續步驟

進一步瞭解[StorSimple 硬體元件取代](storsimple-hardware-component-replacement.md)。

