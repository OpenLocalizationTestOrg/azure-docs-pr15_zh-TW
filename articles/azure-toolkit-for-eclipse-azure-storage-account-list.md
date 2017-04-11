<properties
    pageTitle="Azure 儲存體帳戶] 清單中"
    description="管理您的儲存空間的帳戶設定使用蝕 Azure 工具組"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# <a name="azure-storage-account-list"></a>Azure 儲存體帳戶] 清單中 #

Azure 儲存體帳戶啟用 JDK、 應用程式伺服器和任意元件，以及儲存狀態，使用快取時使用的下載位置。 蝕維護已知的儲存空間帳戶可使用您的專案蝕工作區中的清單。 若要開啟**儲存的帳戶**] 對話方塊可用於管理蝕，該清單，按一下 [**視窗**]，按一下 [**喜好設定**、 展開**Azure**，然後按一下**儲存帳戶**。

以下範例顯示**儲存帳戶**] 對話方塊。

![][ic719496]

也可以開啟這個對話方塊從使用儲存的帳戶，如下所示的對話方塊上的 [**帳戶**] 連結︰

* [**伺服器設定**] 對話方塊的 [ **JDK** ] 索引標籤。
* [**伺服器設定**] 對話方塊的 [**伺服器**] 索引標籤。
* [**新增元件**] 對話方塊。
* [**快取**屬性] 對話方塊。

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>若要匯入您儲存帳戶使用發佈設定檔案 ##

1. 按一下 [**儲存帳戶**] 對話方塊中，**從發佈設定檔案匯入**。
2. （略過此步驟如果您已發佈設定檔案儲存到本機電腦。）在 [**匯入訂閱資訊**] 對話方塊中，按一下 [**下載發佈設定檔案**。 如果您還沒有登入 Azure 帳戶，系統會提示您登入。 然後您會提示您儲存 Azure 發佈的設定檔。 （您可以略過的結果的指示，顯示您在登入頁面-他們所提供的 Azure 入口網站和供 Visual Studio 使用者。）將其儲存到本機電腦。
3. 仍在 [**匯入訂閱資訊**] 對話方塊中，按一下 [**瀏覽**] 按鈕，選取您儲存在本機先前，發佈設定檔案，然後按一下**開啟**。
4. 按一下**[確定**] 關閉 [**匯入訂閱資訊**] 對話方塊。

## <a name="to-create-a-new-storage-account"></a>若要建立新的儲存空間帳戶 ##

1. **儲存帳戶**] 對話方塊中按一下 [**新增**]。
2. 在 [**新增儲存空間帳戶**] 對話方塊中，按一下 [**新增**]。
3. 在 [**新的儲存空間帳戶**] 對話方塊中，指定下列的值︰
    * 儲存體帳戶名稱。
    * 儲存帳戶的位置。
    * 儲存帳戶的描述。
    * 儲存帳戶所屬訂閱。
4. 按一下**[確定**] 關閉 [**新儲存的帳戶**] 對話方塊。

可能需要幾分鐘的時間為您建立的儲存空間帳戶。 建立之後，請按一下**[確定**] 關閉 [**新增儲存空間帳戶**] 對話方塊中，和您的新儲存帳戶將會新增至清單中可用儲存帳戶。

## <a name="to-add-an-existing-storage-account-to-the-list"></a>若要將現有的儲存空間帳戶新增至清單 ##

1. 如果您還沒有 Azure 儲存體帳戶，建立一個中**建立新的儲存空間帳戶區段**上方所列的步驟，即可。 （或者，您可以建立新的儲存空間帳戶在[Azure 管理入口網站][]）。
2. **儲存帳戶**] 對話方塊中按一下 [**新增**]。
3. 在 [**新增儲存空間帳戶**] 對話方塊中，輸入**名稱**] 和 [**便捷鍵**的值。 帳戶名稱和存取金鑰必須針對現有 Azure 儲存體帳戶。 若要檢視您儲存體帳戶名稱與索引鍵使用[Azure 管理入口網站][]的 [**儲存**] 區段。 您**新增儲存空間帳戶**] 對話方塊看起來類似下列項目。

    ![][ic719497]

4. 按一下**[確定**] 關閉 [**新增儲存空間帳戶**] 對話方塊。

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>若要修改的儲存空間帳戶，以使用新的便捷鍵 ##

1. **儲存帳戶**] 對話方塊中，按一下您想要編輯，然後按一下 [**編輯**的儲存空間帳戶。
2. 在 [**編輯的儲存空間帳戶存取索引鍵**] 對話方塊中修改**便捷鍵**的值。
3. 按一下**[確定**] 關閉 [**編輯的儲存空間帳戶存取索引鍵**] 對話方塊。

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>若要移除維護蝕中的清單中的儲存空間帳戶 ##

1. **儲存帳戶**] 對話方塊中，按一下您想要編輯，然後按一下 [**移除**的儲存空間帳戶。
2. 按一下**[確定]**若要移除的儲存空間帳戶出現提示時。

>[AZURE.NOTE] 移除**儲存帳戶**] 對話方塊儲存帳號，只會從儲存帳戶內蝕可檢視的清單。 它不會從 Azure 訂閱移除儲存帳戶。 此外，儲存帳戶可能會再次清單中顯示後蝕重新載入您的訂閱的詳細資料。

## <a name="see-also"></a>另請參閱 ##

[針對蝕 azure 工具組][]

[安裝蝕 Azure 工具組][] 

[建立 Azure 中蝕認識全球應用程式][]

如需有關使用 Java Azure 的詳細資訊，請參閱[Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[針對蝕 azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure 管理入口網站]: http://go.microsoft.com/fwlink/?LinkID=512959
[建立 Azure 中蝕認識全球應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[安裝蝕 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png
