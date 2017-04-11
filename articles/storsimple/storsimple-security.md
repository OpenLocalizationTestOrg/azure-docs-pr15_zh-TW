<properties 
   pageTitle="StorSimple 安全性 |Microsoft Azure" 
   description="說明保護 StorSimple 服務、 裝置和內部部署和雲端中的資料的安全性與隱私權功能。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="05/03/2016"
   ms.author="v-sharos"/>

# <a name="storsimple-security-and-data-protection"></a>StorSimple 安全性與資料保護

## <a name="overview"></a>概觀

安全性是任何人都可以採用新技術，尤其是技術機密或專屬的資料使用的主要考量。 當您評估不同技術，您必須考慮風險提高和保護資料的成本。 Microsoft Azure StorSimple 提供安全性和隱私權解決方案資料保護，協助確保︰ 

- **機密性**– 僅授權的實體可以檢視您的資料。 
- **完整性**– 只允許經授權的實體可以修改或刪除您的資料。

Microsoft Azure StorSimple 方案包含四個彼此互動的主要元件︰

- **裝載於 Microsoft Azure StorSimple 管理員服務**– 您用來設定和佈建 StorSimple 裝置管理服務。
- **StorSimple 裝置**– 安裝在您的資料中心的實體裝置。 所有 [主辦城市] 和 [產生資料的用戶端連線至 StorSimple 裝置和裝置管理資料，並將其移至 Azure 雲端視。
- **用戶端/主機連線到裝置**– 基礎結構中的用戶端連接到 StorSimple 裝置，並產生受保護的資料。
- **雲端儲存空間**– 在 Azure 雲端儲存資料的位置。

下列各節說明 StorSimple 安全性功能，協助您保護每個元件和上儲存的資料。 它也包含有關 Microsoft Azure StorSimple 安全性及相對應的答案，您可能會遇到的問題清單。

## <a name="storsimple-manager-service-protection"></a>StorSimple 管理員服務保護

StorSimple 管理員服務是管理服務裝載於 Microsoft Azure 中，用來管理您的組織有採購的所有 StorSimple 裝置。 您可以使用組織認證登入 Azure 傳統的入口網站，透過網頁瀏覽器存取 StorSimple 管理員服務。 

StorSimple 管理員服務的存取要求您的組織有包含 StorSimple Azure 訂閱。 您的訂閱會決定您可以存取 Azure 傳統入口網站中的功能。 如果貴組織沒有 Azure 的訂閱，您想要瞭解更多相關資訊，請參閱[註冊 Azure 做為組織](../active-directory/sign-up-organization.md)。 

因為 StorSimple 管理員服務會裝載於 Azure，其受 Azure 的安全性功能。 如需 Microsoft Azure 所提供的安全性功能的詳細資訊，請移至[Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/security/)。

## <a name="storsimple-device-protection"></a>StorSimple 裝置保護

StorSimple 裝置是內部部署混合式存放裝置含有實心狀態磁碟機 (SSDs) 與硬碟機 (HDDs)，加上多餘的控制站和自動容錯移轉功能。 控制站管理 tiering、 目前使用 （或快速鍵） 將資料放入本機的儲存空間 （在 [StorSimple 裝置或內部部署伺服器）] 移動至雲端的較少經常使用的資料時的儲存空間。

只允許經授權的 StorSimple 裝置允許加入 Azure 訂閱中所建立的 StorSimple 管理員服務。 若要授權的裝置，您必須將其以註冊 StorSimple 管理員服務提供服務登錄機碼。 服務登錄機碼會產生 Azure 傳統入口網站中的是 128 位元隨機金鑰。 

![服務登錄機碼](./media/storsimple-security/ServiceRegistrationKey.png)

若要瞭解如何取得 [服務註冊鍵，請移至 [[步驟 2︰ 取得服務登錄機碼](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key)。

服務登錄機碼是包含 100 + 字元長索引鍵。 您可以複製索引鍵，並將其儲存在安全的位置中的文字檔案，以便您可以使用該授權所需的其他裝置。 如果您在註冊您的第一個裝置後，會遺失服務登錄機碼，您可以從 StorSimple 管理員服務來產生新的金鑰。 不會影響現有裝置的作業。 

註冊裝置之後，它會使用權杖與 Microsoft Azure 通訊。 裝置註冊後，不會使用服務登錄機碼。

> [AZURE.NOTE] 我們建議您在每次使用後重新產生服務登錄機碼。

## <a name="protect-your-storsimple-solution-via-passwords"></a>保護您的 StorSimple 方案，透過密碼

密碼的重要的電腦的安全性及常用於 StorSimple 解決方案以確保您的資料是授權的使用者存取。 StorSimple 可讓您設定下列密碼︰

- StorSimple 裝置系統管理員密碼
- 挑戰交換驗證通訊協定 (CHAP) 啟動器和目標的密碼
- StorSimple 快照管理員密碼

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell StorSimple 和 StorSimple 裝置系統管理員的密碼

Windows PowerShell 的 StorSimple 是您可以用來管理 StorSimple 裝置的命令列介面。 Windows PowerShell 的 StorSimple 有功能，讓您以註冊您的裝置，您的裝置上設定的網路介面、 安裝特定類型的更新，存取支援工作階段，即可疑難排解在您的裝置，並變更裝置狀態。 您可以連線到裝置上的循序主控台或使用 Windows PowerShell 遠端存取 StorSimple Windows PowerShell。

遠端 PowerShell 可以透過 HTTPS 或 HTTP 完成。 如果啟用遠端管理透過 HTTPS，則您必須遠端管理憑證從裝置上下載及安裝該遠端用戶端。 如需 PowerShell 遠端處理的詳細資訊，移至[遠端到 StorSimple 裝置的連線](storsimple-remote-connect.md)。

您使用 Windows PowerShell 的 StorSimple 裝置連線之後，您必須提供裝置系統管理員密碼以登入裝置。

![裝置系統管理員密碼](./media/storsimple-security/DeviceAdminPW.png)

請注意下列最佳作法︰

- 根據預設，遠端管理會關閉。 若要啟用它，您可以使用 「 StorSimple 管理員服務。 安全性最佳作法是，應該只在實際所需的時間內啟用遠端存取。
- 如果您變更密碼時，請務必通知遠端存取的所有使用者，使其不會發生未預期的連線遺失。
- StorSimple 管理員服務無法擷取現有的密碼:，才能只重設密碼。 我們建議您在安全的地方儲存所有的密碼，以便您沒有重設密碼，如果忘了。 如果您需要重設密碼，請務必之前重設通知所有使用者。 

您可以使用循序連線到裝置來存取 Windows PowerShell 介面。 您也可以使用 HTTP 或 HTTPS，提供額外的安全性來遠端加以存取。 HTTPS 提供較高的安全性比循序或 HTTP 連線。 不過，若要使用 HTTPS，您必須先安裝憑證的存取裝置，將用戶端電腦上。 您可以下載遠端存取憑證從 StorSimple 管理員服務中的 [裝置設定] 頁面。 遠端存取的憑證遺失時，您必須下載新的憑證，並傳播至所有已獲授權使用遠端管理的用戶端。

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>挑戰交換驗證通訊協定 (CHAP) 啟動器和目標的密碼

CHAP 是用來驗證其身分遠端用戶端 StorSimple 裝置驗證配置。 驗證根據共用的密碼。 CHAP 可以是單向 （單向） 或共同 （雙向）。 單向孩子，目標 （StorSimple 裝置） 會驗證啟動器 （主機）。 相互或反向 CHAP 需要目標驗證啟動器，然後啟動器驗證目標。 若要使用任何一種方法可以設定您 StorSimple。

當您設定 CHAP，則請注意下列動作︰

- CHAP 使用者名稱必須包含少於 233 字元。
- CHAP 密碼必須是 12 到 16 個字元。 嘗試使用較長的使用者名稱或密碼會導致 Windows 主機上驗證失敗。
- 您無法使用相同的密碼 CHAP 啟動器和 CHAP 目標兩者均適用。
- 您設定密碼後，可以對其進行變更，但無法擷取。 如果變更密碼時，請務必通知遠端存取的所有使用者，讓他們可以成功連線至 StorSimple 裝置。

如需 CHAP，以及如何將它設定為 StorSimple 方案的詳細資訊，請移至[設定 CHAP StorSimple 裝置](storsimple-configure-chap.md)。

### <a name="storsimple-snapshot-manager-password"></a>StorSimple 快照管理員密碼

StorSimple 快照管理員是 Microsoft 管理主控台 (MMC) 嵌入式管理單元用以大量群組和 Windows 區陰影複製服務產生應用程式一致的備份。 此外，您可以使用 StorSimple 快照管理員建立備份的排程和複製或還原區。

當您設定的裝置使用 StorSimple 快照管理員時，您都必須提供 StorSimple 快照管理員的密碼。 這是第一次設定密碼在 Windows PowerShell 中 StorSimple 註冊期間。 密碼也會設定而變更 StorSimple 管理員服務。 此密碼驗證裝置使用 StorSimple 快照管理員。

![StorSimple 快照管理員密碼](./media/storsimple-security/SnapshotMgrPassword.png)

StorSimple 快照管理員密碼必須 14 到 15 個字元，而且必須包含 3 或多個大寫、 小寫字母、 數字及特殊字元的組合。 設定 StorSimple 快照管理員密碼後，可以對其進行變更，但無法擷取。 如果您變更密碼時，請務必通知遠端的所有使用者。

如需關於 StorSimple 快照管理員，請移至[什麼是 StorSimple 快照管理員？](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>密碼最佳作法

我們建議您以確保 StorSimple 密碼的強式和良好的保護，使用下列方針︰

- 變更您的密碼每三個月。 變更密碼的執行計算。
- 使用強式密碼。 如需詳細資訊，移至[建立更嚴密的密碼和保護](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/)。
- 一律使用不同的密碼不同的存取機制。每個您指定的密碼應該是唯一的。
- 不使用未獲授權存取 StorSimple 裝置的任何人共用的密碼。
- 不需密碼前面其他人的 [讀出或在格式密碼的提示。
- 如果您懷疑的客戶或密碼已洩露，，報告的事件至您的資訊安全性部門。
- 所有的密碼視為機密、 機密資訊。 

## <a name="storsimple-data-protection"></a>StorSimple 資料保護

本節說明保護資料在傳輸時會和儲存的資料的 StorSimple 安全性功能。

其他節所述，將會用於密碼授權的使用者，驗證他們可以存取您的 StorSimple 解決方案之前。 其他的安全性考量時，傳送存放系統與之間被儲存時從未經授權的使用者保護資料。 下列各節說明 StorSimple 所提供的資料保護功能。

> [AZURE.NOTE] Deduplication 提供其他保護 StorSimple 裝置上和 Microsoft Azure 儲存體儲存的資料。 當資料 deduplicated 時，與用於對應，以及存取他們的中繼資料分開儲存的資料物件︰ 沒有可用儲存空間層級內容進行重建根據大量結構]、 [檔案系統] 或 [檔案名稱的資料。

## <a name="protect-data-flowing-through-the-service"></a>保護通過服務的資料

StorSimple 管理員服務的主要目的是可用於管理及設定 StorSimple 裝置。 在 Microsoft Azure 中，執行 「 StorSimple 管理員服務。 您可以使用 Azure 傳統入口網站輸入裝置設定資料，並 Microsoft Azure 使用 StorSimple 管理員服務傳送資料至裝置。 StorSimple 使用非對稱式金鑰組的系統，以確保 Azure 服務危害不會導致危害儲存的資訊。 

![中的資料加密](./media/storsimple-security/DataEncryption.png)

非對稱式金鑰系統協助保護資料流動服務，如下所示︰

1. 使用非對稱公開及私密金鑰金鑰組產生的裝置上，用來保護資料的資料加密憑證。 第一個裝置註冊時，會產生金鑰。 
2. 資料加密憑證金鑰會匯出到受服務資料加密金鑰，也就是強式的是 128 位元索引鍵的隨機產生註冊期間的第一個裝置的個人資訊交換 (.pfx) 檔案。
3. 公開金鑰的憑證安全地供 StorSimple 管理員服務，及私密金鑰保留與裝置。
4. 輸入服務的資料已加密使用確保 Azure 服務無法解密傳送至裝置的資料公用解密使用私密金鑰儲存在裝置上的按鍵。

僅限登錄服務的第一個裝置上則會產生服務資料加密金鑰。 所有後續的裝置，包含已註冊服務必須使用相同的服務資料加密金鑰。 

> [AZURE.IMPORTANT] 
> 
> 務必讓服務資料加密金鑰的複本，並將它儲存在安全的位置。 一份服務資料加密金鑰應該儲存方式，可由授權的人員存取，而且可以輕鬆地傳達給裝置管理員。
>
> 如果服務資料加密金鑰遺失時，Microsoft 支援人員可協助您擷取它所提供的線上狀態中有一個以上的裝置。 我們建議您在擷取之後，變更服務資料加密金鑰。 如需相關指示，請前往[變更服務資料加密金鑰](storsimple-service-dashboard.md#change-the-service-data-encryption-key)。

您可以選取 [服務儀表板上的 [**變更服務資料加密金鑰**] 選項，來變更服務資料加密金鑰和對應的資料加密憑證。 若要確保資料的安全性不會遭到盜用，您必須使用實體 StorSimple 裝置，若要變更的服務資料加密金鑰。 變更加密金鑰需要新的金鑰以更新的所有裝置。 因此，建議您在所有裝置連線時變更鍵。 如果裝置是離線，就可以變更其金鑰在不同的時間。 含有過期的鍵裝置仍然可以執行備份，但無法在更新的索引鍵，直到還原資料。 如需詳細資訊，請參閱[StorSimple 管理員服務儀表板](storsimple-service-dashboard.md)。

服務資料加密金鑰及資料加密憑證不會過期。 不過，我們建議您變更每年以協助防範金鑰洩漏服務資料加密金鑰。

## <a name="protect-data-at-rest"></a>保護其餘的資料

StorSimple 裝置，藉此將其儲存至層在雲端，使用頻率根據本機及管理資料。 連線至裝置的所有主機機器會都傳送資料至裝置，然後移至雲端，適當的資料。 資料會從裝置至雲端安全地透過網際網路傳送。 每個裝置有該裝置會呈現所有共用的區的其中一個 iSCSI 目標。 所有的資料已加密，再將其傳送至雲端儲存空間。 

![雲端儲存空間加密金鑰](./media/storsimple-security/CloudStorageEncryption.png)

若要確保的安全性和移動至雲端的資料的完整性，StorSimple 可讓您定義雲端儲存空間加密金鑰，如下所示︰

- 當您建立大量容器時，您可以指定雲端儲存空間加密金鑰。 索引鍵無法修改，或稍後加入。 
- 大量容器中的所有磁碟區共用相同的加密金鑰。 如果您要的特定的大量加密不同的表單，我們建議您建立新的大量容器裝載的音量。
- 當您輸入的雲端儲存空間加密金鑰 StorSimple 管理員服務時，使用服務的資料加密金鑰公用部分，然後傳送到裝置加密金鑰。
- 雲端儲存空間加密金鑰不會儲存在服務中，只有知道裝置。
- 指定一個雲端儲存空間的加密金鑰是選擇性的。 您可以傳送加密裝置主機上的資料。

### <a name="additional-security-best-practices"></a>額外的安全性的最佳作法

- 分割流量︰ 隔離在公司網路的使用者流量您 iSCSI 舊部署完全分隔的網路，並使用虛擬其中實體隔離不其中一個選項。 針對 iSCSI 存放專用的網路保證的安全與重要資料的效能。 透過公司 LAN 混合儲存和使用者流量不建議您可以增加延遲，並會導致網路失敗。

- Host （主機） 端網路的安全性，使用支援 TCP/IP 卸載引擎 （腳） 的網路介面。 出色的網路介面卡處理 TCP 減少 CPU 載入。

## <a name="protect-data-via-storage-accounts"></a>保護透過儲存帳戶的資料

每個 Microsoft Azure 訂閱可以建立一或多個儲存帳戶。 （儲存帳戶會提供唯一的命名空間使用儲存在 Azure 雲端的資料）。存取儲存的帳戶是由該儲存帳戶相關聯的訂閱] 與 [存取鍵控制。 

當您建立的儲存空間帳戶時，Microsoft Azure 會產生兩個 512 位元儲存便捷鍵，其中會用於驗證 StorSimple 裝置存取儲存帳戶時。 請注意，只有一個這些機碼中使用。 [其他] 鍵會保留，可讓您定期旋轉按鍵。 要旋轉鍵，您可以進行的次要索引鍵，並刪除主索引鍵。 然後，您可以建立新的金鑰用於期間的下一個旋轉。 （基於安全性理由，許多資料中心需要金鑰旋轉）。 

我們建議您遵循這些按鍵旋轉的最佳作法︰

- 您應該旋轉儲存帳戶金鑰定期以確保未經授權的使用者無法存取您儲存的帳戶。
- 定期 Azure 系統管理員應變更或重新產生主要或次要金鑰，以便直接存取儲存帳戶使用 Azure 傳統入口網站的 [儲存] 區段。


## <a name="protect-data-via-encryption"></a>保護透過加密的資料

StorSimple 會使用下列的加密演算法來保護中儲存的資料或出差元件之間的 StorSimple 方案。

| 演算法 | 索引鍵的長度 | 註解通訊協定/應用程式 |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | Azure 傳統入口網站用 RSA PKCS 1 v1.5 來加密會傳送至裝置的設定資料︰ 例如，儲存帳戶認證，StorSimple 裝置設定]，然後雲端儲存空間加密金鑰。 |
| AES       | 256        | 使用 CBC AES 用來加密服務資料加密金鑰公用部分 Azure 傳統入口網站傳送從 StorSimple 裝置之前。 它也可供 StorSimple 裝置來加密資料之前傳送資料至雲端儲存空間帳戶。 |


## <a name="storsimple-virtual-device-security"></a>StorSimple 虛擬裝置安全性

[AZURE.INCLUDE [storsimple virtual device security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>常見問題 (集 FAQ)

以下是一些問與答的安全性與 Microsoft Azure StorSimple。

**Q:**受到我的服務。 應該是我的下一個步驟的什麼？

**A:**您應該立即變更服務資料加密金鑰和儲存帳戶的按鍵時所使用的 tiering 資料的儲存空間帳戶。 如需相關指示，請移至︰ 

- [變更服務資料加密金鑰](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [儲存帳戶的重要的旋轉角度](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:**有新的 StorSimple 裝置的服務登錄機碼。 如何擷取呢？

**A:**當您第一次建立 StorSimple 管理員服務時，就被建立此按鍵。 當您使用的 StorSimple 管理員服務連線到裝置時，您可以使用 [服務快速入門] 頁面，檢視或重新產生服務登錄機碼。 產生新的服務登錄機碼並不會影響現有註冊的裝置。 如需相關指示，請移至︰

- [檢視或重新產生服務登錄機碼](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**Q:**我的服務資料加密金鑰遺失。 我該怎麼做？

**A:**連絡 Microsoft 支援服務。 在您的裝置與說明您擷取金鑰 （如果是在線上至少有一個裝置） 上的支援工作階段，他們可以登入。 立即取得服務資料加密金鑰之後，您應該變更，以確保您稱為新的金鑰。 如需相關指示，請移至︰

- [變更服務資料加密金鑰](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**Q:** 我授權裝置的服務資料加密金鑰變更，但未啟動金鑰變更程序。 我該怎麼辦？

**A:**如果逾時期間已過期，您必須重新授權的服務資料加密金鑰變更裝置，並重新啟動程序。

**Q:** 變更服務資料加密金鑰，但我找不到更新 4 小時內的其他裝置。 我是否必須重新啟動？

**A:**4 小時的時間週期只適用於起始變更。 授權 StorSimple 裝置上啟動更新程序之後，授權無效，直到所有裝置的都更新。

**Q:**我們 StorSimple 系統管理員已離開公司。 我該怎麼辦？

**A:**變更重設密碼，讓存取 StorSimple 裝置，並變更服務資料加密金鑰以確保新的資訊不會知道未經授權的人員。 如需相關指示，請移至︰

- [若要變更您 storsimple 密碼使用 StorSimple 管理員服務](storsimple-change-passwords.md)
- [變更服務資料加密金鑰](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [設定 CHAP StorSimple 裝置](storsimple-configure-chap.md)

**Q:**我要連線到 StorSimple 裝置，主機提供 StorSimple 快照管理員的密碼，但不使用密碼。 該怎麼辦？

**A:**如果您忘了密碼，您應該建立新的項目。 然後，請務必通知所有現有使用者的密碼已變更，並要求他們應該更新用戶端，若要使用的新密碼。 如需相關指示，請移至︰

- [變更 StorSimple 快照管理員密碼](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
- [驗證裝置](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:**在裝置上已變更的 StorSimple Windows PowerShell 的遠端存取的憑證。 我要如何更新我的遠端存取用戶端？

**A:**您可以從 StorSimple 管理員服務，下載新的憑證，，然後提供其安裝在您遠端存取的用戶端的憑證存放區。 如需相關指示，請移至︰

- [匯入憑證 cmdlet](https://technet.microsoft.com/library/hh848630.aspx)

**Q:**是我的資料受保護的 if StorSimple 管理員服務會遭到盜用？

**A:**當您在網頁瀏覽器中檢視時，服務設定資料一律會使用公開金鑰加密。 服務沒有私密金鑰的存取權，因為服務將無法看到的任何資料。 如果受到 StorSimple 管理員服務，也不會影響，因為沒有儲存在 StorSimple 管理員服務機碼。

**Q:**如果有人取得資料加密憑證，將我的資料會遭到盜用？

**A:**Microsoft Azure 加密格式儲存客戶的資料加密金鑰 （.pfx 檔）。 因為.pfx 檔已加密並 StorSimple 服務沒有服務資料加密金鑰解密.pfx 檔，即可快速存取.pfx 檔將不會揭露任何機密。

**Q:**如果我的資料政府的實體要求 Microsoft，會發生什麼情況？

**A:**所有的資料已加密服務私密金鑰會保持與裝置，因為政府實體尋求客戶的資料。 

## <a name="next-steps"></a>後續步驟

[部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。
 
