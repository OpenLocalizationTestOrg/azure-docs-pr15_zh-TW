<properties 
   pageTitle="設定 CHAP StorSimple 裝置 |Microsoft Azure"
   description="說明如何設定 StorSimple 裝置上的挑戰信號驗證通訊協定 (CHAP)。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-chap-for-your-storsimple-device"></a>設定 CHAP StorSimple 裝置

本教學課程說明如何設定 CHAP StorSimple 裝置。 本文所述的程序適用於 StorSimple 8000 數列，以及 StorSimple 1200 裝置。

CHAP 代表挑戰交換驗證通訊協定。 是用來驗證其身分遠端用戶端伺服器驗證配置。 驗證根據共用的密碼或機密文件。 CHAP 可以是單向 （單向） 或共同 （雙向）。 單向 CHAP 時，目標會驗證啟動器。 相互或反向孩子，相反地，需要目標項目會驗證啟動器，然後起始者驗證目標。 可以不目標驗證實作啟動器驗證。 不過，也被實作啟動器驗證時，才可以實作目標驗證。 

最佳作法是我們建議您以增強 iSCSI 安全性使用 CHAP。

>[AZURE.NOTE] 請記住 IPSEC 目前不支援 StorSimple 裝置上。

CHAP StorSimple 裝置上可以設定下列方式︰

- 單向或單向驗證

- 雙向或相互或反向驗證

在每一個這種情況下，必須設定裝置及伺服器 iSCSI 啟動器軟體的入口網站。 此設定的詳細的步驟所述下列教學課程。

## <a name="unidirectional-or-one-way-authentication"></a>單向或單向驗證

在 [單向驗證目標會驗證啟動器。 此驗證會要求您設定 CHAP 啟動器設定 StorSimple 裝置和 iSCSI 啟動器軟體主機上。 如需您 StorSimple 裝置與 Windows 主機詳細的程序說明如下。

#### <a name="to-configure-your-device-for-one-way-authentication"></a>若要設定單向驗證您的裝置

1. Azure 傳統入口網站中，在 [**裝置**] 頁面上，按一下 [**設定**] 索引標籤。

    ![CHAP 啟動器](./media/storsimple-configure-chap/IC740943.png)

2. 向下捲動，在此頁面上，並在**CHAP 啟動器**] 區段中︰
                                                    
    1. 提供您 CHAP 啟動器的使用者名稱。

    2. 提供您 CHAP 啟動器密碼。

         > [AZURE.IMPORTANT] CHAP 使用者名稱必須包含少於 233 字元。 CHAP 密碼必須是 12 到 16 個字元。 較長的使用者名稱或密碼會導致 Windows 主機上驗證失敗。
    
    3. 確認密碼。

4. 按一下 [**儲存**]。 會顯示確認訊息。 按一下**[確定**] 儲存變更。

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>若要設定 Windows 主機伺服器上的單向驗證

1. 在 Windows host （主機） 伺服器上，開始 iSCSI 啟動器。

2. 在 [ **iSCSI 啟動器內容**] 視窗中，執行下列步驟︰
                                                    
    1. 按一下 [**探索**] 索引標籤。

        ![iSCSI 啟動器屬性](./media/storsimple-configure-chap/IC740944.png)

    2. 按一下 [**探索] 入口網站**]。

3. 在 [**探索目標入口網站**] 對話方塊中︰
                                                    
    1. 指定您的裝置的 IP 位址。

    3. 按一下 [**進階**]。

        ![探索目標入口網站](./media/storsimple-configure-chap/IC740945.png)

4. 在 [**進階設定**] 對話方塊中︰
                                                    
    1. 選取 [**啟用 CHAP 登入**] 核取方塊。

    2. 在 [**名稱**] 欄位中，提供您指定 CHAP 啟動器傳統入口網站中的使用者名稱。

    3. 在 [**目標密碼**] 欄位中，提供您指定 CHAP 啟動器傳統入口網站中的密碼。

    4. 按一下**[確定]**。

        ![一般的進階的設定](./media/storsimple-configure-chap/IC740946.png)

5. 在**iSCSI 啟動器屬性**視窗的 [**目標**] 索引標籤上裝置狀態應該會顯示為 [**已連線**。 如果您使用的 StorSimple 1200 裝置，然後每個大量會裝載為 iSCSI 目標如下所示。 因此，需要步驟 3-4，重複每一個磁碟。

    ![使連接成個別的目標](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] 如果您變更 iSCSI 名稱時，會使用新名稱，針對新 iSCSI 工作階段。 新的設定不再次用於現有工作階段，直到您登出和登入。

在 Windows 主機伺服器設定 CHAP 相關的詳細資訊，請移至[其他考量](#additional-considerations)。


## <a name="bidirectional-or-mutual-authentication"></a>雙向] 或 [共同驗證

在雙向驗證目標會驗證啟動器，然後啟動器送給目標。 需要使用者設定 CHAP 啟動器設定，以及反向 CHAP 設定裝置及 iSCSI 啟動器軟體主機上。 下列程序說明設定相互驗證，在裝置上與 Windows 主機上的步驟。

#### <a name="to-configure-your-device-for-mutual-authentication"></a>若要設定相互驗證您的裝置

1. Azure 傳統入口網站中，在 [**裝置**] 頁面上，按一下 [**設定**] 索引標籤。

    ![CHAP 目標](./media/storsimple-configure-chap/IC740948.png)

2. 向下捲動，在此頁面上，並在**CHAP 目標**] 區段中︰
                                                    
    1. 您為裝置提供**反向 CHAP 使用者名稱**。

    2. 提供**反向 CHAP 密碼**，您的裝置。

    3. 確認密碼。

3. 在 [ **CHAP 啟動器**] 區段中︰
                                                
    1. 您為裝置提供**使用者名稱**。

    1. 您的裝置提供的**密碼**。

    3. 確認密碼。

4. 按一下 [**儲存**]。 會顯示確認訊息。 按一下**[確定**] 儲存變更。

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>若要設定 Windows 主機伺服器上的雙向驗證

1. 在 Windows host （主機） 伺服器上，開始 iSCSI 啟動器。

2. 在 [ **iSCSI 啟動器內容**] 視窗中，按一下 [**設定**] 索引標籤。

3. 按一下 [ **CHAP**]。

4. 在 [ **iSCSI 啟動器相互 CHAP 密碼**] 對話方塊中︰
                                                    
    1. 輸入您在 Azure 傳統入口網站中設定的**反向 CHAP 密碼**。

    2. 按一下**[確定]**。

        ![iSCSI 啟動器相互 CHAP 密碼](./media/storsimple-configure-chap/IC740949.png)

5. 按一下 [**目標**] 索引標籤。

6. 按一下 [**連線**] 按鈕。 

7. 在 [**連線至目標**] 對話方塊中，按一下 [**進階]**。

8. 在 [**進階摘要資訊**] 對話方塊中︰
                                                    
    1. 選取 [**啟用 CHAP 登入**] 核取方塊。

    2. 在 [**名稱**] 欄位中，提供您指定 CHAP 啟動器傳統入口網站中的使用者名稱。

    3. 在 [**目標密碼**] 欄位中，提供您指定 CHAP 啟動器傳統入口網站中的密碼。

    4. 選取 [**執行相互驗證**核取方塊。

        ![進階的設定相互驗證](./media/storsimple-configure-chap/IC740950.png)

    5. 按一下**[確定**] 完成 CHAP 設定
     
在 Windows 主機伺服器設定 CHAP 相關的詳細資訊，請移至[其他考量](#additional-considerations)。

## <a name="additional-considerations"></a>其他考量

[**快速連線**的功能不支援之 CHAP 啟用的連線。 啟用 CHAP 時，請確定您使用的連線到目標的**目標**] 索引標籤上 [**連線**] 按鈕。

![連線到目標](./media/storsimple-configure-chap/IC740947.png)

顯示 [**連線至目標**] 對話方塊中選取 [**新增至清單中最愛的目標此連線**] 核取方塊。 這樣的每次重新啟動電腦，嘗試還原 iSCSI 最愛目標的連線。

## <a name="errors-during-configuration"></a>在設定時的錯誤

如果您 CHAP 設定不正確，即表示您可能會看到的**驗證失敗**」 錯誤訊息。

## <a name="verification-of-chap-configuration"></a>驗證的 CHAP 設定

您可以驗證的 CHAP 正在使用下列步驟。

#### <a name="to-verify-your-chap-configuration"></a>若要確認您 CHAP 設定

1. 按一下 [**最愛的目標**]。

2. 選取您啟用驗證的目標。

3. 按一下 [**詳細資料**。

    ![iSCSI 啟動器屬性最愛的目標](./media/storsimple-configure-chap/IC740951.png)

4. 在 [**最愛的目標詳細資料**] 對話方塊中，請注意 [**驗證**] 欄位中的項目。 如果設定成功，它應該**CHAP**。

    ![最愛的目標詳細資料](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>後續步驟

- 進一步瞭解[StorSimple 安全性](storsimple-security.md)。

- 瞭解更多關於[使用 StorSimple 管理員服務來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
