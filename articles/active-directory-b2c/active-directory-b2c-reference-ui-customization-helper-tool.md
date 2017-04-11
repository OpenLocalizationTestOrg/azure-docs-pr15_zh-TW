<properties
    pageTitle="Azure Active Directory B2C︰ 頁面 UI 自訂協助工具 |Microsoft Azure"
    description="用來說明頁面 UI 自訂中的功能 Azure Active Directory B2C 的協助工具"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C︰ 用來說明頁面使用者介面 (UI) 的自訂功能的協助工具

本文是[主要使用者介面自訂文章](active-directory-b2c-reference-ui-customization.md)中 Azure Active Directory (Azure AD) B2C 小幫手]。 下列步驟說明如何使用我們的範例 [HTML] 及 [CSS 內容練習頁面 UI 自訂功能。

## <a name="get-an-azure-ad-b2c-tenant"></a>取得 Azure AD B2C 租用戶

您可以自訂項目之前，您必須以[取得 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)如果您還沒有一個。

## <a name="create-a-sign-up-or-sign-in-policy"></a>建立註冊或登入原則

我們提供的範例內容可[註冊或登入原則](active-directory-b2c-reference-policies.md)中的 [自訂兩個頁面︰[整合的登入頁面](active-directory-b2c-reference-ui-customization.md)] 和 [[自我聲明的屬性頁面](active-directory-b2c-reference-ui-customization.md)。 當[建立註冊或登入原則](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy)，將本機帳戶 （電子郵件地址）、 Facebook、 Google 和 Microsoft 新增為**身分識別提供者**。 以下是我們的範例 HTML 內容就會接受只 IDPs。  如果您想要您也可以新增這些 IDPs 子集。

## <a name="register-an-application"></a>登錄應用程式

您必須在您可以用來執行您的原則的 B2C 租用戶中登錄[應用程式](active-directory-b2c-app-registration.md)。 在註冊之後您的應用程式，您有幾個選項可供您實際執行您註冊原則︰

- 建立 [快速啟動應用程式列的開始] 區段中 Azure AD B2C 其中[登入設定，並登入您的應用程式中的使用者皆](active-directory-b2c-overview.md#getting-started)。
- 使用預先建立的[Azure AD B2C 遊樂場](https://aadb2cplayground.azurewebsites.net)應用程式。 如果您選擇使用遊樂場，您必須使用**重新導向 URI**您 B2C 租用戶中登錄應用程式`https://aadb2cplayground.azurewebsites.net/`。
- 在您的原則[Azure 入口網站](https://portal.azure.com/)中使用 [**立即執行**] 按鈕。

## <a name="customize-your-policy"></a>自訂您的原則

若要自訂原則的外觀與風格，您需要先建立 HTML 和 CSS 檔案使用 Azure AD B2C 的特定慣例。 您可以再上傳您靜態內容可公開使用位置讓 Azure AD B2C 可以存取的地方。 這可能是您自己專屬的網頁伺服器、 Azure Blob 儲存體、 Azure 內容傳遞網路或任何其他靜態資源主機服務提供者。 僅限需求是您的內容是透過 HTTPS，並可使用 CORS 存取。 一旦您已公開網頁靜態內容，您可以編輯您的原則，指向此位置內容並進行簡報的客戶。 [主要使用者介面自訂文章](active-directory-b2c-reference-ui-customization.md)描述的 Azure AD B2C 自訂功能的運作方式的詳細資料。

進行本教學課程，我們已已經建立一些範例內容，其裝載於 Azure Blob 儲存體。 範例內容是我們虛構公司 」 Wingtip 玩具 」 的基本自訂佈景主題。 試試看在您自己的原則，請依照下列步驟執行︰

1. 登入您的租用戶[Azure 入口網站](https://portal.azure.com/)上，然後瀏覽至 B2C 功能刀。
2. 按一下**註冊或登入的原則**，然後按一下 [您的原則 (例如，「 b2c\_1\_登\_設定\_登\_中 」)。
3. 按一下 [**頁面 UI 自訂**以及然後**整合註冊或登入頁面**]。
4. 切換**使用自訂頁面**切換為 [**是]**。 在 [**自訂頁面 URI** ] 欄位中，輸入`https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`。 按一下**[確定]**。
5. 按一下 [**本機帳戶註冊頁面**。 切換**使用自訂範本**切換為 [**是]**。 在 [**自訂頁面 URI** ] 欄位中，輸入`https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`。
5. 重複相同步驟**社交帳戶註冊] 頁面**。
 按一下**[確定]** ，關閉 UI 自訂刀。
6. 按一下 [**儲存**]。

現在您可以嘗試您的自訂原則。 如果您想要但也只要按一下 [**立即執行**] 命令原則刀，您可以使用您自己的應用程式或 Azure AD B2C 遊樂場。 在下拉式方塊中選取您的應用程式，然後選擇適當的重新導向 URI]。 按一下 [**立即執行**] 按鈕。 隨即會開啟新的瀏覽器索引標籤，您可以執行透過使用者體驗的註冊您的應用程式，以新的內容中的位置 ！

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>上傳範例內容至 Azure Blob 儲存體

如果您想要使用 Azure Blob 儲存體裝載您的頁面內容，您可以建立您自己的儲存空間帳戶，並使用我們的 B2C 協助工具來上傳檔案。

### <a name="create-a-storage-account"></a>建立儲存的帳戶

1. [Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下 [ **+ 新增** > **資料 + 的儲存空間** > **儲存的帳戶**。 您必須建立 Azure Blob 儲存體帳戶 Azure 訂閱。 您可以註冊免費試用版[Azure 網站](https://azure.microsoft.com/pricing/free-trial/)。
3. 提供儲存帳戶 (例如 「 contoso 」) 的**名稱**，然後選擇**價格層**、**資源群組**和**訂閱**的適當選項。 請確定您已核取 [**固定至 Startboard**選項。 按一下 [**建立**]。
4. 回到 Startboard，並按一下您剛才建立的儲存空間帳戶。
5. 在 [**摘要**] 區段中，按一下**容器**，，然後按一下 [ **+ 新增**。
6. 提供容器 (例如，「 b2c 」) 的**名稱**，然後選取**Blob**的**存取類型**。 按一下**[確定]**。
7. 您建立的容器會出現在**Blob**刀上的清單。 寫下的容器; URL例如，它看起來應該像`https://contoso.blob.core.windows.net/b2c`。 關閉**Blob**刀。
8. 在儲存帳戶刀中，按一下 [**索引鍵**，寫下的**儲存體帳戶名稱**] 和 [ **Access 的主索引鍵**欄位的值。

1. [Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下 [ **+ 新增** > **資料 + 的儲存空間** > **儲存的帳戶**。 您必須建立 Azure Blob 儲存體帳戶 Azure 訂閱。 您可以註冊免費試用版[Azure 網站](https://azure.microsoft.com/pricing/free-trial/)。
3. 選取 [ **Blob 儲存體****帳戶類型**下, 然後保留其他的值為預設值。  如果您想要您可以編輯 [資源群組及位置。  按一下 [**建立**]。
4. 回到 Startboard，並按一下您剛才建立的儲存空間帳戶。
5. 在 [**摘要**] 區段中，按一下 [ **+ 容器**。
6. 提供容器 (例如，「 b2c 」) 的**名稱**，然後選取**Blob**的**存取類型**。 按一下**[確定]**。
7. 開啟 [容器]**屬性**，並記下容器; 的 URL例如，它看起來應該像`https://contoso.blob.core.windows.net/b2c`。 關閉容器刀。
8. 在儲存帳戶刀中，按一下**索引鍵] 圖示**，寫下的**儲存體帳戶名稱**] 和 [ **Access 的主索引鍵**欄位的值。

> [AZURE.NOTE]
    **Access 的主索引鍵**是重要的安全性認證。

### <a name="download-the-helper-tool-and-sample-files"></a>下載的協助工具] 及 [範例檔案

您可以下載[Azure Blob 儲存體協助工具] 及 [範例檔案另存為.zip 檔案](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)，或從 GitHub 複製︰

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

此存放庫包含`sample_templates\wingtip`目錄，其中包含範例 HTML、 CSS 和圖像。 若要參照 Azure Blob 儲存體帳戶這些範本，您必須編輯 HTML 檔案。 開啟`unified.html`和`selfasserted.html`及取代的任何執行個體`https://localhost`上述步驟中寫下您自己容器的 url。 您必須使用絕對的 HTML 檔案的路徑，因為 HTML 在此情況下，將由 Azure AD 下的 domain `https://login.microsoftonline.com`。

### <a name="upload-the-sample-files"></a>上傳範例檔案

在同一個存放庫中，將它解壓縮`B2CAzureStorageClient.zip`及執行`B2CAzureStorageClient.exe`檔案中。 此程式會直接上傳您儲存的帳戶，您指定的目錄中的所有檔案，並啟用 CORS 存取這些檔案。 如果您依照上述步驟，那麼的 HTML 和 CSS 檔案會立即指向您儲存的帳戶。 請注意，您儲存體帳戶名稱前面的組件`blob.core.windows.net`;例如， `contoso`。 您可以驗證的內容已上傳正確嘗試存取`https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html`瀏覽器上。 請確定該內容現在會啟用 CORS 也使用[http://test-cors.org/](http://test-cors.org/) 。 (尋找 「 XHR 狀態︰ 200 」 結果中。)

### <a name="customize-your-policy-again"></a>自訂您的原則，一次

既然您已上傳範例內容，儲存帳戶，您必須編輯參照您註冊原則。 使用您自己的儲存空間帳戶 Url 這次重複上述的[[自訂您的原則]](#customize-your-policy)區段中的步驟。 例如的位置，您`unified.html`檔案就會`<url-of-your-container>/wingtip/unified.html`。

現在您可以使用 [**立即執行**] 按鈕或您自己的應用程式至您的原則再執行一次。 結果看起來應該幾乎一樣，您可以使用相同的範例 HTML 和 CSS 這兩種情況。 不過，您原則現在會參照您自己的執行個體的 Azure Blob 儲存體，且您可用來編輯及上傳一次為您的檔案時，請。 如需有關自訂的 HTML 和 CSS 的詳細資訊，請參閱[主要使用者介面自訂文章](active-directory-b2c-reference-ui-customization.md)。
