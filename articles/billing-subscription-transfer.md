<properties
   pageTitle="轉接 Azure 訂閱的擁有權 |Microsoft Azure"
   description="如何將 Azure 訂閱轉移到另一個使用者，以及一些常見問題集 (faq) 瞭解程序"
   services=""
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor=""
   tags="billing,top-support-issue"/>

<tags
   ms.service="billing"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/10/2016"
   ms.author="genli"/>

# <a name="transferring-ownership-of-an-azure-subscription"></a>轉接 Azure 訂閱的擁有權

執行您︰

- 需要交出帳單給其他人 Azure 訂閱的擁有權？
- 想要變更註冊 Azure 所用的帳戶？ 或許您要使用您的 Microsoft 帳戶，但是用來使用您的工作或學校帳戶，請改為嗎？
- 要將 Azure 訂閱移到另一個目錄嗎？
- 有 Azure 和 Office 365 中不同的租用戶，而想要合併彙算嗎？

現在您可以輕鬆地在 Microsoft Azure 帳戶中心-Pay-As-You-Go、 MSDN、 動作套件或 BizSpark 訂閱。  我們已加入您的訂閱傳輸至另一個使用者的功能。 換句話說，您現在可以變更帳戶任何 Pay-As-You-Go、 MSDN、 動作套件或 BizSpark 訂閱管理員的您自己，哪些國家/地區無論您運作。 現在，我們支援 Azure 服務商場購買的傳送這些訂閱類型。

> [AZURE.NOTE] 若要變更您的訂閱不同的方案，請參閱[切換至另一個優惠 Azure 訂閱](billing-how-to-switch-azure-offer.md)如需詳細資訊。 如果您需要更多協助，這份文件中的任何點，請[連絡支援部門](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以取得您的問題已解決快速。

## <a name="how-to-transfer-ownership-of-an-azure-subscription"></a>如何移轉 Azure 訂閱的擁有權

> [AZURE.VIDEO transfer-an-azure-subscription]

1.  在<https://account.windowsazure.com/Subscriptions>登入。 您必須是帳戶管理員，才能執行擁有權傳輸。 如需有關如何找出誰是訂閱的帳戶管理員的詳細資訊，請參閱[常見問題集](#faq)。

2.  選取要轉接訂閱]。

3.  按一下 [**轉接訂閱**] 選項。

    ![Azure 帳戶訂閱] 索引標籤](./media/billing-subscription-transfer/image1.png)

4.  遵循提示，指定收件者。

    ![傳送 [訂閱] 對話方塊](./media/billing-subscription-transfer/image2.PNG)

5.  收件者都會自動收到接受連結的電子郵件。

    ![訂閱傳送電子郵件給收件者](./media/billing-subscription-transfer/image3.png)

6.  收件者按一下的連結，並遵循指示，包括輸入他們的付款資訊。

    ![第一個訂閱傳輸網頁](./media/billing-subscription-transfer/image4.png)

    ![第二個訂閱傳輸網頁](./media/billing-subscription-transfer/image5.png)

7. 成功 ！ 訂閱會立即傳送。

<a id="faq"></a>
## <a name="frequently-asked-questions-faq"></a>常見問題 (集 FAQ)

-   **如何知道訂閱的帳戶管理員是誰？**

    您可以確認誰是訂閱的帳戶管理員，如下所示︰

    1. [Azure 入口網站](https://portal.azure.com)登入。
    2. 在中樞] 功能表中，選取 [**訂閱**]。
    3. 選取您想要檢查的訂閱，然後選取 [**設定**。
    4. 選取 [**內容**]。 在 [**帳戶管理員**] 方塊中，將會顯示訂閱的帳戶管理員。  

-   **訂閱傳輸會造成任何服務停擺？**

    也不會影響服務。 此有效取消訂閱，在目前的帳戶管理員及建立新的收件者的帳戶] 下，但基礎 Azure 服務關聯新的訂閱。 訂閱識別碼保持不變。

-   **如何使用此機制變更訂閱的目錄中？**-   
    Azure 的訂閱會建立在帳戶管理員所屬的目錄。 因此，若要變更的目錄，只要傳輸訂閱目標目錄中的使用者帳戶。 當使用者完成接受傳輸，訂閱會自動移動到目標目錄的步驟。

-   **如果我接手帳單的另一個組織訂閱的擁有權，他們仍有權存取我的資源？**

    如果訂閱轉移到另一個租用戶，與上一個租用戶相關聯的使用者將會遺失的存取權的訂閱。 即使使用者不是服務管理員或共同管理員不再，他們還是可以存取透過其他安全性機制訂閱。 這些功能包括︰
    - 管理資源訂閱的管理員權限授與使用者的憑證。 如需詳細資訊，請參閱[建立和上傳 Azure 管理憑證](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -   便捷鍵服務，例如儲存空間。 如需詳細資訊，請參閱[檢視、 複製與重新產生的儲存空間便捷鍵](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -   遠端存取認證服務等 Azure 虛擬機器

    這是不完整清單。 收件者必須考量更新如果需要限制存取他們的資源，與服務相關的任何機密資料。 大部分的資源可更新，如下所示︰

    1.   移至 [Azure 入口網站︰ [ *https://portal.azure.com*](https://portal.azure.com)

    2.    按一下 [瀏覽所有-&gt;所有資源

    3.    選取 [資源]。 這會開啟資源刀。

    4.    在資源刀中，按一下 [**設定**]。 這裡，您可以檢視及更新現有的密碼。


-   **如果我傳送的計費週期中間訂閱時，會整個計費收件者工資循環嗎？**

    寄件者會負責付款的任何已完成的傳送到報告的使用方式。 收件者會負責從這時候開始傳輸的時間報告的使用方式。 可能會發生轉接之前，但之後，報告的一些使用方式。 這將會包含在收件者的帳單。

-   **收件者有使用情況和帳單記錄的存取權嗎？**

    此時，透露給收件者的唯一資訊是量的最後一個帳單 （或如果訂閱已傳輸之前的第一份帳單所產生的目前平衡）。 使用情況和帳單記錄其他無法傳輸與訂閱。

-   **可以在傳送變更優惠？**

    優惠必須保持相同。 若要變更您的提議，您必須[連絡支援人員](http://go.microsoft.com/fwlink/?LinkID=619338)。

-   **我可以將訂閱移轉到另一個國家或地區中的使用者帳戶嗎？**

    否，這次是不受支援。 收件者的使用者帳戶必須使用相同的國家/地區。

-   **收件者可以使用不同的付款機制嗎？**

    [是]。 有以下的限制︰ 現在訂閱帳單記錄分成兩個帳戶。 但的優點是，您可以不需[連絡支援人員](http://go.microsoft.com/fwlink/?LinkID=619338)。

-   **將我傳輸 Azure 訂閱之後受影響的付款方法？**

    若要接受訂閱傳輸，請信用卡或類似的付款方法，必須提供支付訂閱。 例如，如果百勝將訂閱轉移到陳陳接受傳輸，陳也必須提供她會用來支付訂閱的付款方法。 完成之後，百勝不再需要付費的訂閱，他轉移到陳。

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>後續步驟後接受訂閱的擁有權

1. 現在您已經帳戶管理員。 檢視並更新服務系統管理員和共同管理員。 管理管理員[Azure 傳統入口網站](https://manage.windowsazure.com)中的移至 [設定]。 [進一步瞭解](http://go.microsoft.com/fwlink/?LinkID=533293)。
2. 您也可以使用角色型存取控制 (RBAC)，您的訂閱和服務。 請造訪[Azure 入口網站](https://portal.azure.com)[深入瞭解 RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. 更新與此訂閱服務相關聯的認證。 這些功能包括︰
    - 管理資源訂閱的管理員權限授與使用者的憑證。 如需詳細資訊，請參閱[建立和上傳管理憑證的 Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -   便捷鍵服務，例如儲存空間。 如需詳細資訊，請參閱[檢視、 複製與重新產生的儲存空間便捷鍵](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -   遠端存取認證服務等 Azure 虛擬機器
4. 更新此訂閱]，在[Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)[進一步瞭解](http://go.microsoft.com/fwlink/?LinkID=533292)帳單通知  
5.  如果您與合作夥伴合作，請考慮更新此訂閱的合作夥伴識別碼。 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)] 中，您可以執行這個動作。

> [AZURE.NOTE] 如果您仍有更進一步的問題，請[連絡支援部門](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以取得您的問題已解決快速。
