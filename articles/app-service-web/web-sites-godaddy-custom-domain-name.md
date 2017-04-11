<properties
    pageTitle="Azure 應用程式服務 (GoDaddy) 中設定自訂網域名稱"
    description="瞭解如何使用 Azure Web 應用程式從 GoDaddy 網域名稱"
    services="app-service"
    documentationCenter=""
    authors="erikre"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="cephalin"/>

# <a name="configure-a-custom-domain-name-in-azure-app-service-purchased-directly-from-godaddy"></a>Azure 應用程式服務 （直接從 GoDaddy 購買） 中設定自訂網域名稱

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

如果您購買網域透過 Azure 應用程式服務 Web 應用程式則參考[購買網域名稱用於 Web 應用程式](custom-dns-web-site-buydomains-web-app.md)的最後一個步驟。

本文提供指示使用的直接向[GoDaddy](https://godaddy.com) [應用程式服務 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)與購買的自訂網域名稱。

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
##<a name="understanding-dns-records"></a>瞭解 DNS 記錄

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

<a name="bkmk_configurecname"></a>
## <a name="add-a-dns-record-for-your-custom-domain"></a>新增您的自訂網域的 DNS 記錄

應用程式服務中的 web 應用程式中與您的自訂網域，您必須新增新的項目 DNS 資料表中的自訂網域使用 GoDaddy 所提供的工具。 使用下列步驟，在 GoDaddy.com 尋找 [DNS 工具

1. 登入您的帳戶與 GoDaddy.com，並**我的帳戶**依序選取 [**管理我的網域**。 最後，選取您想要使用 Azure web 應用程式，並選取 [**管理 DNS**的網域名稱下拉式功能表。

    ![GoDaddy 的 [自訂的網域] 頁面](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. 從 [**網域詳細資料**] 頁面上，捲動至**DNS 區域檔案**] 索引標籤。 這是用來新增及修改您的網域名稱的 DNS 記錄] 區段。

    ![DNS 區域檔案] 索引標籤](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

    選取 [**新增記錄**以新增現有記錄]。

    若要**編輯**現有記錄，請選取記錄旁的 [畫筆及紙張] 圖示。

    > [AZURE.NOTE] 再新增新的記錄，請注意 GoDaddy 已經建立常用功能的子網域 （在編輯器中，稱為**主機**） 等**電子郵件**、**檔案**、**郵件**及其他人的 DNS 記錄。 如果您想要已使用的名稱，修改而非建立一個新的現有記錄。

4. 新增一筆記錄，您必須先選取的記錄類型。

    ![選取 [記錄類型](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

    接下來，您必須提供**host （主機）** （的自訂網域或的子網域） 和其**指向**。

    ![新增區域記錄](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

    * 時新增**（主機） 記錄**-您必須將**主機**欄位設為 [ **@** （這表示根網域名稱，例如**contoso.com**，） *（萬用字元符合多個的子網域） 或您想要使用的子網域 (例如， * *www**。)您必須設定**指向** Azure web 應用程式的 IP 位址] 欄位。

    * 新增**CNAME （別名） 記錄**-您必須設定**主機**欄位至您想要使用的子網域。 例如， **www**。 您必須設定 [**指向**] 欄位為**。 azurewebsites.net** Azure web 應用程式的網域名稱。 例如， **contoso.azurewebsites.net**。

5. 按一下 [**新增另一個**。
6. 選取 [ **TXT** ] 作為記錄類型，然後指定**host （主機）**值為**@**]，**指向**值是**&lt;yourwebappname&gt;。 azurewebsites.net**。

    > [AZURE.NOTE] 此 TXT 記錄是由 Azure 以驗證您擁有網域所描述的 A 記錄或第一個的 TXT 記錄。 一旦網域具有已對應至 Azure 入口網站中的 web 應用程式，您可以移除此 TXT 記錄的項目。

5. 當您完成新增或修改記錄，請按一下 [**完成**] 以儲存變更。

<a name="enabledomain"></a>
## <a name="enable-the-domain-name-on-your-web-app"></a>啟用 web 應用程式中的網域名稱

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)
