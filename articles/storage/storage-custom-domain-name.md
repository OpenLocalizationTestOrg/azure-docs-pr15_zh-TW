<properties
    pageTitle="設定將網域名稱用於您 Blob 儲存體端點 |Microsoft Azure"
    description="瞭解如何將自訂使用者網域對應至 Azure 儲存體帳戶 Azure 傳統入口網站中的 Blob 儲存體端點。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>設定您 Blob 儲存體端點的自訂網域名稱

## <a name="overview"></a>概觀

您可以設定將自訂網域用於存取 blob Azure 儲存體帳戶中的資料。 Blob 儲存體的預設端點是`<storage-account-name>.blob.core.windows.net`。 如果您將對應的自訂網域和子網域，例如**www.contoso.com** blob 端點您儲存的帳戶，則您的使用者可以也 access blob 資料儲存帳戶使用該網域中。

>[AZURE.IMPORTANT] Azure 儲存體還不支援 HTTPS 的自訂網域。 我們會注意客戶感興趣這項功能，可在未來版本。

有兩種方式儲存帳戶 blob 端點指向您的自訂網域。 最簡單的方法是，建立一筆 CNAME 記錄，將您的自訂網域和子網域對應至 blob 結束點。 CNAME 記錄是對應至目的地網域的 [來源網域的 DNS 功能。 在此情況下，來源網域是您的自訂網域和子網域，請注意，子網域是永遠必要。 目的地網域是您 Blob 服務端點。

將您的自訂網域對應至您 blob 端點的程序，不過，導致短時間停機時間的網域時要註冊[Azure 傳統入口網站](https://manage.windowsazure.com)中該網域。 如果您的自訂網域目前支援的應用程式與服務等級協定 (SLA) 的要求沒有停機時間，然後您可以使用 Azure **asverify**子網域，讓使用者將能夠存取您的網域的 DNS 對應進行時提供中繼註冊步驟。

下表顯示樣本 Url 存取 blob 儲存體帳戶名稱為**mystorageaccount**的資料。 註冊的儲存空間帳戶是**www.contoso.com**的自訂網域︰

資源類型|URL 格式
---|---
儲存帳戶|**預設 URL:** http://mystorageaccount.blob.core.windows.net<p>**自訂網域 URL:** http://www.contoso.com</td>
Blob|**預設 URL:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**自訂網域 URL:** http://www.contoso.com/mycontainer/myblob
根容器|**預設的 URL:** http://mystorageaccount.blob.core.windows.net/myblob 或 http://mystorageaccount.blob.core.windows.net/$ 根/myblob<p>**自訂網域 URL:** http://www.contoso.com/myblob 或 http://www.contoso.com/$ 根/myblob

## <a name="register-a-custom-domain-for-your-storage-account"></a>註冊將自訂網域用於您儲存的帳戶

使用此程序註冊您的自訂網域，如果您沒有顧慮有網域，不可使用簡單的使用者，或如果您的自訂網域不目前主控應用程式。

如果您的自訂網域目前支援的應用程式，不能有任何停機時間，然後使用<a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">登錄將自訂網域用於您儲存的帳戶中繼 asverify 子網域</a>所述的程序。

若要設定自訂網域名稱，您必須與您的網域註冊機構建立新的 CNAME 記錄。 CNAME 記錄指定網域名稱; 別名在此情況下，將您的自訂網域的地址對應到您儲存的帳戶的 Blob 儲存體端點。

每個註冊機構有類似，但稍有不同的方法，指定一筆 CNAME 記錄，但概念是相同。 請注意許多基本的網域註冊封裝並不提供 DNS 設定，因此您可能需要升級您的網域註冊套件，才能建立 CNAME 記錄。

1.  在[Azure 傳統入口網站](https://manage.windowsazure.com)，瀏覽至 [**儲存**] 索引標籤。

2.  在 [**儲存**] 索引標籤中，按一下您要對應的自訂網域的儲存空間帳戶名稱。

3.  按一下 [**設定**] 索引標籤。

4.  在畫面底部按一下 [**管理網域**]，顯示 [**管理自訂網域**」] 對話方塊。 在文字] 對話方塊的頂端，您會看到如何建立 CNAME 記錄的資訊。 此程序略過的**asverify**子網域是指文字]。

5.  登入您的 DNS 註冊機構的網站，然後移至 [管理 DNS 的頁面。 您可能會發現此例如**網域名稱**、 **DNS**或**名稱伺服器管理**] 區段中。

6.  管理 CNAMEs 尋找區段。 您可能要移至 [進階的設定] 頁面，並尋找文字**CNAME**，**別名**或**子網域**。

7.  建立新的 CNAME 記錄，並提供子網域別名，例如**www**或**相片**。 然後提供主機名稱，這是您 Blob 服務端點，在 [格式**mystorageaccount.blob.core.windows.net** （ **mystorageaccount**是您儲存的帳戶的名稱）。 若要使用 [主機名稱會在 [**管理自訂網域**] 對話方塊的文字提供給您。

8.  您建立 CNAME 記錄之後，返回 [**管理自訂的網域**] 對話方塊，然後輸入您的自訂網域，包括子網域，在**自訂網域名稱**] 欄位的名稱。 如果您的網域是**contoso.com**子網域為**www**，例如，輸入**www.contoso.com**。如果您的子網域**相片**，請，輸入**photos.contoso.com**。 請注意，不需要的子網域。

9. 按一下 [註冊您的自訂網域的 [**註冊**] 按鈕。

    如果註冊成功，您會看到**您的自訂網域是作用中**的訊息。 使用者可以使用自訂網域，現在檢視 blob 資料，只要有適當的權限。

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>註冊使用中繼 asverify 子網域您儲存帳戶的自訂網域

使用此程序註冊您的自訂網域如果 SLA 所需的應用程式目前支援您的自訂網域有任何停機時間。 藉由建立指向 CNAME 從 asverify。&lt;子網域&gt;。&lt;customdomain&gt;至 asverify。&lt;storageaccount&gt;。 blob.core.windows.net，您可以預先註冊 Azure 的網域。 然後，您可以建立點從第二個 CNAME&lt;子網域&gt;。&lt;customdomain&gt;至&lt;storageaccount&gt;。 blob.core.windows.net，此時您的自訂網域的流量會導向至您 blob 結束點。

Asverify 子網域是選擇性的子網域辨識 Azure。 藉由預先規劃**asverify**到您自己的子網域，您必須允許 Azure 辨識您的自訂網域，而不需要修改的網域的 DNS 記錄。 一旦您修改的網域的 DNS 記錄，其會對應至不中斷的 blob 端點。

1.  在[Azure 傳統入口網站](https://manage.windowsazure.com)，瀏覽至 [**儲存**] 索引標籤。

2.  在 [**儲存**] 索引標籤中，按一下您要對應的自訂網域的儲存空間帳戶名稱。

3.  按一下 [**設定**] 索引標籤。

4.  在畫面底部按一下 [**管理網域**]，顯示 [**管理自訂網域**」] 對話方塊。 在文字] 對話方塊的頂端，您會看到如何建立使用**asverify**子網域的 CNAME 記錄的資訊。

5.  登入您的 DNS 註冊機構的網站，然後移至 [管理 DNS 的頁面。 您可能會發現此例如**網域名稱**、 **DNS**或**名稱伺服器管理**] 區段中。

6.  管理 CNAMEs 尋找區段。 您可能要移至 [進階的設定] 頁面，並尋找文字**CNAME**，**別名**或**子網域**。

7.  建立新的 CNAME 記錄，並提供資料的子網域別名包含 asverify 子網域。 例如，您所指定的子網域，就會在格式**asverify.www**或**asverify.photos**。 然後提供主機名稱，這是您 Blob 服務端點，在 [格式**asverify.mystorageaccount.blob.core.windows.net** （ **mystorageaccount**是您儲存的帳戶的名稱）。 若要使用 [主機名稱會在 [**管理自訂網域**] 對話方塊的文字提供給您。

8.  您建立 CNAME 記錄後，返回 [**管理自訂的網域**] 對話方塊中，並**自訂網域名稱**] 欄位中輸入您的自訂網域名稱。 如果您的網域是**contoso.com**子網域為**www**，例如，輸入**www.contoso.com**。如果您的子網域**相片**，請，輸入**photos.contoso.com**。 請注意，不需要的子網域。

9.  按一下核取方塊，指出**進階︰ 使用 「 asverify' 子網域登錄的自訂網域**。

10. 按一下 [**註冊**] 按鈕，步驟，註冊您的自訂網域。

    如果 preregistration 成功，您會看到**您的自訂網域是作用中**的訊息。

11. 此時，已經由 Azure，驗證您的自訂網域，但流量至您的網域不尚未傳遞到您儲存的帳戶。 若要完成此程序，返回您的 DNS 註冊機構的網站，並建立另一個對應至您 Blob 服務端點的子網域的 CNAME 記錄。 例如，指定子網域為**www**或**相片**及主機名稱為**mystorageaccount.blob.core.windows.net** （ **mystorageaccount**是您儲存的帳戶的名稱）。 此步驟中，使用您的自訂網域註冊已完成。

12. 最後，您可以刪除使用**asverify**，您建立 CNAME 記錄時，必須只能作為中間的步驟。

使用者可以使用自訂網域，現在檢視 blob 資料，只要有適當的權限。

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>驗證自訂網域參照您 Blob 服務端點

若要確認您的自訂網域已確實對應至您 Blob 服務端點，請建立 blob 容器中將公用內您儲存的帳戶。 然後，在網頁瀏覽器中使用 URI 以下列格式來存取 blob:

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

例如，您可能會使用下列 URI 存取透過**photos.contoso.com**自訂子網域，對應到您**myforms**容器中 blob web 表單︰

-   http://photos.contoso.com/myforms/applicationform.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>移除註冊您儲存的帳戶的自訂網域

若要移除註冊的自訂網域，請遵循下列步驟︰ 

1. [Azure 傳統入口網站](https://manage.windowsazure.com)登入。 

2. 在功能窗格] 中按一下 [**儲存**]。 

3. 在 [**儲存**] 頁面中，按一下以顯示儀表板的儲存空間帳戶名稱。 

5. 在功能區中，按一下 [**管理網域**。 

6. 在 [**管理自訂網域**] 對話方塊中，按一下 [**取消註冊**]。 


## <a name="additional-resources"></a>其他資源

-   [如何將自訂網域對應至內容傳遞網路 」 (CDN) 端點](../cdn/cdn-map-content-to-custom-domain.md)
