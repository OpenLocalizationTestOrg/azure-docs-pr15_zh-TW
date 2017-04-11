<properties
   pageTitle="部署 StorSimple 虛擬陣列 3-設定檔案伺服器的虛擬裝置"
   description="此 StorSimple 虛擬陣列部署中的第三個教學課程指示您設定為檔案伺服器虛擬裝置。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>部署 StorSimple 虛擬陣列-設定設定檔伺服器

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>簡介 

本文適用於 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置） 執行 2016 年 3 月上市 (GA) 版本。 本文將說明如何執行初始設定、 註冊 StorSimple 檔案伺服器、 完成裝置設定]，並建立及連線至 SMB 共用。 這是完全部署虛擬陣列，以在檔案伺服器或 iSCSI 伺服器所需的部署教學課程系列中最後一個文件。

安裝及設定程序可以需要大約 10 分鐘的時間才能完成。


## <a name="setup-prerequisites"></a>設定的先決條件

設定並設定 StorSimple 虛擬裝置之前，請確定︰

-   您有佈建虛擬裝置，而且為詳細[佈建 HYPER-V StorSimple 虛擬陣列](storsimple-ova-deploy2-provision-hyperv.md)或[佈建 VMware StorSimple 虛擬陣列](storsimple-ova-deploy2-provision-vmware.md)中連線至該。

-   您有服務登錄機碼，從您要建立管理 StorSimple 虛擬裝置 StorSimple 管理員服務。 如需詳細資訊，請參閱[步驟 2︰ 取得服務登錄機碼](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)StorSimple 虛擬陣列。

-   如果這是第二或後續虛擬裝置註冊您現有的 StorSimple 管理員服務，您應該服務資料加密金鑰。 此按鍵產生時的第一個裝置已成功註冊此服務。 如果您遺失此鍵，請參閱[取得服務資料加密金鑰](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)StorSimple 虛擬陣列。

## <a name="step-by-step-setup"></a>逐步設定

使用下列的逐步指示安裝和設定 StorSimple 虛擬裝置。

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>步驟 1︰ 完成本機 web UI 設定和註冊您的裝置 


#### <a name="to-complete-the-setup-and-register-the-device"></a>若要完成的設定和註冊裝置

1.  開啟瀏覽器視窗，然後連線到本機網站使用者介面。[類型]: 

    `https://<ip-address of network interface>`

    使用上一個步驟所述的連線 URL。 您會看到的錯誤訊息網站的安全性憑證問題。 按一下 [**繼續] 以這個網頁**]。

    ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  **StorSimpleAdmin**登入 web 虛擬裝置的使用者介面。 在步驟 3 中輸入您要變更的裝置系統管理員密碼︰[佈建 VMware StorSimple 虛擬陣列](storsimple-ova-deploy2-provision-vmware.md)或[佈建 HYPER-V StorSimple 虛擬陣列](storsimple-ova-deploy2-provision-hyperv.md)中啟動虛擬裝置。

    ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  您會移至 [**首頁**] 頁面。 本頁面說明來設定和註冊虛擬裝置 StorSimple 管理員服務所需的不同設定。 請注意，**網路設定**、 **Web proxy 設定**與**時間設定**選用。 只需要的設定的**裝置設定**和**雲端設定**。

    ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  在**網路介面**] 下的 [**網路設定**] 頁面資料 0 會自動設定您。 每個網路介面預設設定為自動取得 IP 位址 (DHCP)。 因此，IP 位址]、 [子網路] 及 [閘道器會自動指派 （適用於 IPv4 和 IPv6）。

    ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

    如果您的裝置提供時新增多個網路介面，您可以在這裡設定。 請注意，您可以設定您的網路介面為 IPv4 只或為 IPv4 和 IPv6。 不支援 IPv6 只設定。

1.  因為它們用名稱時設定為在檔案伺服器進行通訊的雲端儲存服務提供者或解決您的裝置，嘗試您的裝置時，所需的 DNS 伺服器。 在 [ **DNS 伺服器**底下的**網路設定**] 頁面︰

    1.  主要和次要 DNS 伺服器會自動設定。 如果您選擇設定靜態 IP 位址，您可以指定 DNS 伺服器。 高可用性，我們建議您設定的主要與次要 DNS 伺服器。

    2.  按一下 [**套用**]。 這將會套用，然後驗證網路設定。

2.  在 [**裝置設定**] 頁面︰

    1.  指定唯一**名稱**至您的裝置。 此名稱 1-15 個字元，而且可以包含字母、 數字和連字號。

    2.  按一下 [**檔案伺服器**圖示![](./media/storsimple-ova-deploy3-fs-setup/image6.png)您所建立的裝置**類型**。 在檔案伺服器可讓您建立共用的資料夾。

    3.  為您的裝置已在檔案伺服器，您必須加入網域的裝置。 輸入**網域名稱**。

    1.  按一下 [**套用**]。

2.  對話方塊會顯示。 輸入您的網域的認證以指定的格式。 按一下 [檢查] 圖示。 將確認網域認證。 如果是不正確的認證，您會看到一則錯誤訊息。

    ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  按一下 [**套用**]。 這將會套用，然後驗證裝置設定。

    ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

    > [AZURE.NOTE]
    > 
    > 確定您虛擬陣列是在自己的組織單位 (OU) 適用於 Active Directory 群組原則物件 (GPO) 會套用至或繼承。 群組原則可能 StorSimple 虛擬陣列上安裝的防毒軟體等應用程式。 安裝其他軟體不受支援，且可能會造成資料損毀。 

1.  （選擇性） 設定您的 web proxy 伺服器。 雖然 web proxy 設定是選擇性的請注意，如果您使用 web proxy，您可以只設定以下。

    ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

    在 [**網頁 proxy**頁面︰

    1.  提供此格式**proxy 網址**︰ *http://&lt;主機 IP 位址或 FDQN&gt;︰ 連接埠號碼*。 請注意 HTTPS Url 不受支援。

    2.  指定**驗證**與 [**基本**] 或 [**無]**。

    3.  如果使用的驗證，您也必須提供**使用者名稱**和**密碼**。

    4.  按一下 [**套用**]。 這會驗證並套用設定的網頁 proxy 設定。

1.  （選擇性） 設定您的裝置，例如時區和主要和次要 NTP 伺服器的時間設定。 因為您的裝置必須同步處理的時間，讓它可以驗證您的雲端服務提供者，就需要 NTP 伺服器。

    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

    在 [**時間設定**] 頁面︰

    1.  從下拉式清單中，選取 [根據部署裝置的地理位置**和時區**]。 預設時區，您的裝置是 PST。 您的裝置會使用這個時區所有排程作業。

    2.  指定您的裝置的**主索引 NTP 伺服器**或接受 time.windows.com 的預設值。 請確定您的網路，讓 NTP 流量，若要從您的資料中心傳遞至網際網路。

    3.  您也可以指定在您的裝置的**次要 NTP 伺服器**。

    4.  按一下 [**套用**]。 這會驗證並套用設定的時間設定。

1.  設定您的裝置的雲端設定。 在此步驟中，您就會完成本機裝置設定，然後再登錄 StorSimple 管理員服務的 [裝置。

    1.  輸入您從中取得**服務登錄機碼**[步驟 2︰ 取得服務登錄機碼](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)StorSimple 虛擬陣列。

    2.  如果這是您的第一個裝置註冊這項服務，請略過此步驟，移至下一個步驟。 如果這不是您要註冊此服務的第一個裝置，您必須提供**服務的資料加密金鑰**。 使用服務登錄機碼，其他裝置註冊 StorSimple 管理員服務需要此按鍵。 如需詳細資訊，請參閱取得[服務資料加密金鑰](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)，在您的本機網頁 UI 上。

    3.  按一下 [**註冊**]。 這會重新啟動裝置。 您可能需要 2-3 分鐘之後的裝置已成功註冊，請等候。 重新啟動裝置之後，您會被重新導向至登入頁面。

        ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
    

1.  返回 Azure 傳統入口網站。 在 [**裝置**] 頁面上確認裝置已成功連線到服務查看狀態。 裝置狀態] 應為**作用中**。

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>步驟 2︰ 完成所需的裝置設定

若要完成 StorSimple 裝置的裝置設定，您需要︰

-   選取儲存的帳戶與您的裝置。

-   選擇 [傳送至雲端的資料的加密設定]。

完成所需的裝置設定[Azure 傳統入口網站](https://manage.windowsazure.com/)中，執行下列步驟。

#### <a name="to-complete-the-minimum-device-setup"></a>若要完成的最小值的裝置設定

1.  從 [**裝置**] 頁面中，選取您剛剛建立的裝置。 此裝置會顯示為 [**作用中**。 按一下 [針對裝置名稱的箭號，然後按一下 [**快速入門**。

2.  按一下 [開始設定裝置精靈**完成裝置設定**]。

3.  在設定裝置精靈中**基本的設定**] 頁面上，執行下列動作︰

    1.  指定儲存帳戶以搭配您的裝置。 您可以在此訂閱，從下拉式清單中選取現有的儲存空間帳戶，或指定**新增多個**帳戶選擇不同的訂閱。

    2.  定義所有資料靜止 （AES 加密），將會傳送至雲端的加密設定。 若要加密資料，請核取**啟用雲端儲存空間加密金鑰**下拉式方塊。 輸入包含 32 個字元的雲端儲存加密。 重新輸入的金鑰以進行確認。 256 位元 AES 鍵會使用加密的使用者定義索引鍵。

    3.  按一下核取圖示![](./media/storsimple-ova-deploy3-fs-setup/image15.png)。

        ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

設定就會更新。 設定成功更新之後，完成的裝置設定] 按鈕會呈現灰色。 您會回到裝置的 [**快速入門**] 頁面。

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> 您可以隨時修改其他裝置設定，以存取 [**設定**] 頁面。

## <a name="step-3-add-a-share"></a>步驟 3︰ 新增共用

建立共用[Azure 傳統入口網站](https://manage.windowsazure.com/)中，執行下列步驟。

#### <a name="to-create-a-share"></a>建立共用

1.  在 [裝置**快速入門**] 頁面上按一下 [**新增共用**。 此舉會啟動 [新增共用] 精靈。

    ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  在 [**基本的設定**] 頁面上執行下列動作︰

    1.  指定您共用的唯一名稱。 名稱必須包含要 127 3 個字元的字串。

    2.  （選用）提供共用的描述。 描述可協助您識別共用擁有者。

    3.  選取 [使用類型的共用]。 使用類型可以**Tiered**或**本機釘選**，方式分層的預設值。 需要本機的保證與低延遲，較高的效能的工作量，選取 [**本機釘選**的共用]。 所有其他的資料中，選取 [ **Tiered**共用]。

    本機固定的共用 thickly 佈建後，並確保共用的主資料會保留本機裝置並不會不要至雲端。 另一方面分層的共用是狗熊提供。 當您建立的分層的共用時，10%的間距在本機層佈建後，90%的間距在雲端佈建後。 比方說，如果您佈建 1 TB 音量，100 GB 也可以位於本機的間距，而且 900 GB 用在雲端時資料層。 依序也就是說，如果您在裝置上執行所有本機的空間，您無法佈建分層的共用。

1.  指定提供您共用的容量。 請注意，指定的容量應該小於可用的容量。 如果使用的分層的共用，應該 500 GB 和 20 TB 之間共用大小。 本機固定的共用，請指定 50 GB 與 2 TB 之間共用大小。 佈建共用作為輔助線可用的容量。 如果 0 GB 的可用的本機容量，然後您就不能佈建本機或分層共用。

    ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  按一下箭號圖示![](./media/storsimple-ova-deploy3-fs-setup/image19.png)以移至下一個頁面。

1.  在 [**其他設定**] 頁面中，指定使用者或群組的存取此共用權限。 指定使用者或使用者群組中的名稱*<john@contoso.com>*格式。 我們建議您使用，讓存取這些共用的管理員權限的使用者群組 （而非單一使用者）。 您已指派的權限之後，您可以修改這些權限，然後使用 Windows 檔案總管]。

    ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  按一下核取圖示![](./media/storsimple-ova-deploy3-fs-setup/image21.png)。 共用資源將會建立與指定的設定。 根據預設，監控和備份啟用共用。

## <a name="step-4-connect-to-the-share"></a>步驟 4︰ 連線到共用

您現在必須連線至您在上一個步驟中建立共用。 在您的 Windows Server 主機上執行這些步驟。

#### <a name="to-connect-to-the-share"></a>連線至共用

1.  按下![](./media/storsimple-ova-deploy3-fs-setup/image22.png)+。在 [執行] 視窗中，指定*\\*的路徑，可透過使用您指定給您的檔案伺服器裝置名稱取代*檔案的伺服器名稱*。 按一下**[確定]**。

    ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  這會開啟檔案總管。 您現在應該可以看到您所建立的共用資料夾。 選取並按兩下以檢視內容的共用 （資料夾）。

    ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  您現在可以將檔案新增至這些共用，並進行備份。

![[視訊] 圖示](./media/storsimple-ova-deploy3-fs-setup/video_icon.png)**可用的視訊**

觀看影片，請參閱如何設定和註冊 StorSimple 虛擬的陣列為檔案伺服器。

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## <a name="next-steps"></a>後續步驟

瞭解如何使用本機網路使用者介面，來[管理您的 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。
