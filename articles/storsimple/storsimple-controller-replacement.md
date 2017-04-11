<properties 
   pageTitle="取代 StorSimple 裝置控制器 |Microsoft Azure"
   description="說明如何移除並取代 StorSimple 裝置上的一或兩個控制器模組。"
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

# <a name="replace-a-controller-module-on-your-storsimple-device"></a>取代控制器模組 StorSimple 裝置上

## <a name="overview"></a>概觀

本教學課程說明如何移除並取代 StorSimple 裝置中的一或兩個控制器模組。 同時也討論的單一及雙控制器取代案例的基礎邏輯。

>[AZURE.NOTE] 之前執行控制器替代方案，建議您隨時更新為最新版本的控制器韌體。
>
>若要避免 StorSimple 裝置產生的損害，請勿退出控制器直到 Led 會顯示為下列其中一項︰
>
>- 所有指示燈都會關閉。
>- LED 3![綠色核取記號圖示](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png)，及![紅色交叉圖示](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png)閃爍，且 LED 0 和 LED 7 中**開啟]**。

下表顯示支援的控制器取代案例。

|大小寫|取代案例|適用的程序|
|:---|:-------------------|:-------------------|
|1|一個控制站位於失敗的狀態，另一個控制站健全與作用中。|[單一控制器取代](#replace-a-single-controller)，它描述[單一控制器取代的邏輯](#single-controller-replacement-logic)，以及[取代項目的步驟](#single-controller-replacement-steps)。|
|2|兩個控制站已失敗，並要求取代。 底座磁碟，and.disk 圍繞符號是健全。|[雙重控制器取代](#replace-both-controllers)，說明[雙重控制器取代的邏輯](#dual-controller-replacement-logic)，以及[取代項目的步驟](#dual-controller-replacement-steps)。 |
|3|交換控制站從一部裝置或不同的裝置。 底座、 磁碟和磁碟隨函附件是健全。|會顯示位置不相符通知訊息。|
|4|一個控制站遺失，而其他控制器失敗。|[雙重控制器取代](#replace-both-controllers)，說明[雙重控制器取代的邏輯](#dual-controller-replacement-logic)，以及[取代項目的步驟](#dual-controller-replacement-steps)。|
|5|一個或兩個控制站會失敗。 您無法透過循序主控台或 Windows PowerShell 遠端存取裝置。|手動控制站取代程序[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)。|
|6|控制站有不同的建置版本，這可能是因為︰<ul><li>控制站有不同的軟體版本。</li><li>控制站有不同的韌體版本。</li></ul>|如果控制器軟體版本不同，取代邏輯偵測到的並更新取代控制器上的軟體版本。<br><br>如果控制器韌體版本有不同，且舊韌體版本是**不**會自動升級，一則警示訊息會出現在 Azure 傳統入口網站。 您應該掃描更新並安裝韌體更新。</br></br>如果控制器韌體版本有不同，韌體舊版可以自動升級控制器取代邏輯會偵測，並控制站開始之後，會自動更新韌體。|

您需要移除控制器模組，如果未能。 將一或兩個控制器模組可能會失敗，導致在單一控制器取代或雙控制器取代。 取代程序及後面的邏輯，請參閱下列動作︰

- [取代單一控制器](#replace-a-single-controller)
- [取代兩個控制站](#replace-both-controllers)
- [移除控制器](#remove-a-controller)
- [插入控制器](#insert-a-controller)
- [找出您的裝置上使用中控制器](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT] 移除，取代控制器之前檢閱安全中的資訊[StorSimple 硬體元件取代](storsimple-hardware-component-replacement.md)。

## <a name="replace-a-single-controller"></a>取代單一控制器

當 Microsoft Azure StorSimple 裝置上的兩個控制站的其中一個失敗、 有問題，或遺失時，您需要取代單一控制器。 

### <a name="single-controller-replacement-logic"></a>單一控制器取代邏輯

在單一控制器替代方案，您應該先移除失敗的控制器。 （剩餘控制器裝置中的是作用中的控制器）。當您插入的替代控制站，會發生下列動作︰

1. 取代控制器會立即開始與 StorSimple 裝置通訊。

2. 取代控制器上複製的虛擬硬碟 (VHD) 的作用中的控制站的快照。

3. 讓取代控制器從啟動時此 VHD，才可辨識為備用控制站修改快照集。

4. 修改完成後，取代控制器就會開始為備用控制站。

5. 當正在執行兩個控制站時，叢集上線

### <a name="single-controller-replacement-steps"></a>單一控制器取代步驟

如果您的 Microsoft Azure StorSimple 裝置中的控制站的其中一個失敗，請完成下列步驟。 （另一個控制站必須使用中並執行。 如果這兩個控制站失敗或發生問題，請移至[雙重控制器取代項目的步驟](#dual-controller-replacement-steps)。）

>[AZURE.NOTE] 可能需要重新啟動，完全從單一控制器取代程序復原控制器 30-45 分鐘。 整個程序的總時間，包括附加纜線，是大約 2 個小時。

#### <a name="to-remove-a-single-failed-controller-module"></a>若要移除單一失敗的控制器模組

1. 在 Azure 傳統的入口網站中，移至 StorSimple 管理員服務，按一下 [**裝置**] 索引標籤，然後按一下您想要監視裝置的名稱。

2. 移至 [**進行的維修作業 > 硬體狀態**。 控制站 0 或控制器 1 的狀態應該紅色、 失敗表示。

    >[AZURE.NOTE] 在單一控制器取代失敗的控制器一律是備用控制站。

3. 若要找出失敗的控制器模組中使用圖 1] 和 [下表。  

    ![後擋板的裝置主要的圍繞符號模組](./media/storsimple-controller-replacement/IC740994.png)

    **圖 1**背面 StorSimple 裝置

  	|標籤|描述|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|控制站 0|
  	|4|控制站 1|

4. 失敗的控制站，移除 [所有連線的網路纜線從資料連接埠。 如果您使用的 8600 的模型，也會移除控制器 EBOD 控制站的 SA 纜線。

5. 依照[移除控制器](#remove-a-controller)移除失敗的控制器] 中的步驟。 

6. 移除失敗控制站的相同位置中安裝工廠取代。 這樣會觸發單一控制器取代邏輯。 如需詳細資訊，請參閱[單一控制器取代邏輯](#single-controller-replacement-logic)。

7. 單一控制器取代邏輯進行背景，同時重新連線纜線。 請注意連線的纜線完全相同的方式他們曾經連線之前取代。

8. 控制站重新啟動後，檢查**控制器狀態**及**叢集狀態**Azure 傳統入口網站中驗證控制器回健全狀態，並在備用模式。

>[AZURE.NOTE] 如果您正在監視透過循序主控台裝置，您可能會看到多個重新啟動時控制器復原取代程序。 當呈現循序主控台] 功能表時，然後就會知道取代已完成。 如果沒有出現的功能表啟動控制器替代的兩個小時內，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

## <a name="replace-both-controllers"></a>取代兩個控制站

當 Microsoft Azure StorSimple 裝置上的兩個控制站已失敗，發生故障或遺失時，您需要取代兩個控制站。 

### <a name="dual-controller-replacement-logic"></a>雙重控制器取代邏輯

在雙重控制器替代方案，您可以先移除兩個失敗控制站，然後插入 [檢查的取代文字。 當插入兩個取代控制站時，會發生下列動作︰

1. 取代中的控制站位置 0 會檢查下列各項︰
 
   1. 正在使用目前版本的韌體和軟體？

   2. 就可以叢集部分嗎？

   3. 執行的對等控制站，而是群組直條圖？
                            
    如果沒有上述情況，則為 true 時，請控制器會尋找最新的每日備份 （位於**nonDOMstorage**磁碟機上 S）。 控制站從備份複製 VHD 的最新的快照。

2. 在位置 0 控制器使用快照圖像本身。

3. 同時，在位置 1 控制器等待控制器 0 完成影像，並開始。

4. 控制站 0 開始之後，控制站 1 偵測到叢集控制器 0，建立單一控制器取代邏輯觸發程序。 如需詳細資訊，請參閱[單一控制器取代邏輯](#single-controller-replacement-logic)。

5. 之後，會執行兩個控制站和叢集會重新連線。

>[AZURE.IMPORTANT] 雙重控制器取代，下列 StorSimple 裝置設定之後，請務必讓您執行手動備份裝置。 24 小時後，每日裝置設定備份不會觸發為止。 使用[Microsoft 支援服務](storsimple-contact-microsoft-support.md)進行手動備份您的裝置。

### <a name="dual-controller-replacement-steps"></a>雙重控制器取代步驟

在您的 Microsoft Azure StorSimple 裝置的控制站其中失敗時，需要此工作流程。 在資料中心的冷卻系統停止運作，並在一段時間內的兩個控制站失敗的結果，可能造成此問題。 根據 StorSimple 裝置是否已開啟或關閉，而且您是否使用從 8600 或 8100 模型，是必要的一組不同的步驟。

>[AZURE.IMPORTANT] 為 1 小時重新啟動，完全從雙重控制器取代程序復原控制器需要 45 分鐘。 總時間為整個程序，包括附加纜線，是大約 2.5 小時。

#### <a name="to-replace-both-controller-modules"></a>若要取代這兩個控制器模組

1. 如果使用的裝置已關閉，略過此步驟，然後繼續進行下一個步驟。 如果使用的裝置已開啟，請關閉裝置。
                                        
    1. 如果您使用的從 8600 模型，先關閉主要的圍繞符號，，然後關閉 [EBOD 圍繞符號。

    2. 請等待裝置已完全關閉。 所有背面裝置的 Led 會關閉。

2. 移除所有的網路纜線連接到資料連接埠。 如果您使用的 8600 的模型，也會移除 SA 纜線連接 EBOD 圍繞符號主要的圍繞符號。

3. 兩個控制站移除 StorSimple 裝置。 如需詳細資訊，請參閱[移除控制器](#remove-a-controller)。

4. 首先，請插入控制器 0 工廠取代，然後插入控制器 1。 如需詳細資訊，請參閱[插入控制器](#insert-a-controller)。 這樣會觸發雙重控制器取代邏輯。 如需詳細資訊，請參閱[雙重控制器取代邏輯](#dual-controller-replacement-logic)。

5. 雖然控制器取代邏輯進行背景，重新連線纜線。 請注意連線的纜線完全相同的方式他們曾經連線之前取代。 請參閱[您 StorSimple 8100 裝置上安裝](storsimple-8100-hardware-installation.md)或[安裝 StorSimple 從 8600 裝置](storsimple-8600-hardware-installation.md)的模型纜線您的裝置] 區段的詳細的指示。

6. 開啟 StorSimple 裝置。 如果您使用的從 8600 模型︰

    1. 請確定是否已開機 EBOD 圍繞符號第一個。

    2. 請等待執行 EBOD 圍繞符號。

    3. 開啟 [主要的圍繞符號]。

    4. 第一個控制器重新啟動，並在健全狀態之後，系統就會執行。

    >[AZURE.NOTE] 如果您正在監視透過循序主控台裝置，您可能會看到多個重新啟動時控制器復原取代程序。 序列主控台] 功能表出現時，您知道取代已完成。 如果沒有出現的功能表啟動控制器替代的 2.5 小時內，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

## <a name="remove-a-controller"></a>移除控制器

若要從 StorSimple 裝置上移除有錯誤的控制器模組中使用下列程序。

>[AZURE.NOTE] 下列說明適用於控制站 0。 控制站 1，這些會還原。

#### <a name="to-remove-a-controller-module"></a>若要移除控制器模組

1. 掌握您的縮圖和食指之間模組鎖定。

2. 輕輕擠您捲動方塊和食指放在一起以釋放控制站鎖定。

    ![釋放控制站鎖定](./media/storsimple-controller-replacement/IC741047.png)

    **圖 2**釋放控制站鎖定

2. 投影片不在底座控制器作為控點鎖定。

    ![不在底座滑動控制器](./media/storsimple-controller-replacement/IC741048.png)

    **圖 3**滑動 [不在底座控制器

## <a name="insert-a-controller"></a>插入控制器

您可以使用下列程序來安裝工廠提供控制器模組之後您從 StorSimple 裝置上移除有錯誤的模組。

#### <a name="to-install-a-controller-module"></a>若要安裝控制器模組

1. 請檢查是否有任何損害介面連接線。 如果任何的連接器 pin 損毀或彎曲無法安裝模組。

2. 雖然完全放開鎖定，則您可以回復底座控制器模組。 

    ![將底座滑動控制器](./media/storsimple-controller-replacement/IC741053.png)

    **圖 4**將底座滑動控制器

3. 插入控制器模組，開始將底座推控制器模組，同時關閉鎖定。 鎖定會加入到定位引導控制器。

    ![關閉控制器鎖定](./media/storsimple-controller-replacement/IC741054.png)

    **圖 5**關閉控制器鎖定

4. 完成時鎖定貼齊位置。 **確定**LED 現在應該上。  

    >[AZURE.NOTE] 可能需要控制器和啟動 LED 最多 5 分鐘。

5. 若要確認取代項目的成功 Azure 傳統入口網站中，移至 [**裝置** > **進行的維修作業** > **硬體狀態**，並確定控制器 0 和 1 控制器是狀況良好 （狀態為綠色）。

## <a name="identify-the-active-controller-on-your-device"></a>找出您的裝置上使用中控制器

有許多情況下，第一次裝置註冊控制器取代或，例如，必須找出作用中的控制站 StorSimple 裝置上。 作用中的控制器處理所有的磁碟韌體和網路作業。 您可以使用下列方法之一來識別作用中的控制器︰

- [若要識別的作用中的控制站使用 Azure 傳統入口網站](#use-the-azure-classic-portal-to-identify-the-active-controller)

- [使用 Windows PowerShell 的 StorSimple 識別作用中的控制站](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [核取要識別的作用中的控制站的實體裝置](#check-the-physical-device-to-identify-the-active-controller)

下一步被描述每個這些程序。

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>若要識別的作用中的控制站使用 Azure 傳統入口網站

在 Azure 傳統的入口網站中，瀏覽至**裝置** > **進行的維修作業**]，然後捲動到**控制器**] 區段。 這裡，您可以驗證哪個控制站是作用中。

![識別作用中的控制站 Azure 傳統入口網站中](./media/storsimple-controller-replacement/IC752072.png)

**圖 6**顯示 [作用中的控制站的 azure 傳統入口網站

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>使用 Windows PowerShell 的 StorSimple 識別作用中的控制站

當您透過循序主控台存取您的裝置時，則會顯示橫幅訊息。 橫幅郵件包含基本裝置資訊，例如模型、 名稱、 已安裝的版本和您要存取控制站的狀態。 下圖顯示橫幅訊息範例︰

![序列橫幅訊息](./media/storsimple-controller-replacement/IC741098.png)

**圖 7**橫幅為使用中的郵件顯示控制器 0

若要判斷您已連線到控制器是作用中或被動式，您可以使用橫幅訊息。

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>核取要識別的作用中的控制站的實體裝置

若要找出您的裝置上的使用中控制站，找出藍色主導上方 5 的連接埠背面的主要的圍繞符號。

如果此 LED 閃爍，控制器作用中，而其他控制器位於備用模式。 使用下列的圖表和表格作為輔助工具。

![使用 dataports 裝置主要的圍繞符號後擋板](./media/storsimple-controller-replacement/IC741055.png)

**圖 8**背面資料連接埠與監控 Led 主要圍繞符號

|標籤|描述|
|:----|:----------|
|1-6|資料 0-5 的網路連接埠|
|7|藍色 LED|


## <a name="next-steps"></a>後續步驟

進一步瞭解[StorSimple 硬體元件取代](storsimple-hardware-component-replacement.md)。
