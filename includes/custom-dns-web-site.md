#<a name="configuring-a-custom-domain-name-for-an-azure-website"></a>設定的 Azure 網站的自訂網域名稱

當您建立網站，Azure 提供好記的子網域 azurewebsites.net 網域上，您的使用者能存取您的網站使用的 URL，例如 http://&lt;我的網站 >。 azurewebsites.net。 不過，如果您的 [共用] 或 [標準模式中設定您的網站，您可以將您的網站對應到您自己的網域名稱。

或者，您可以使用 Azure 流量管理員載入遞減連入流量至您的網站。 使用網站流量管理員的運作方式的詳細資訊，請參閱[控制 Azure 網站流量與 Azure 流量 Manager][trafficmanager]。

> [AZURE.NOTE] 這項工作的程序套用至 Azure 網站;如需雲端服務，請參閱<a href="/develop/net/common-tasks/custom-dns/">設定 Azure 中的自訂網域名稱</a>。

> [AZURE.NOTE] 此工作中的步驟會要求您針對共用] 或 [標準模式，可能會變更您訂閱的付款多少設定您的網站。 如需詳細資訊，請參閱<a href="/pricing/details/web-sites/">網站定價詳細資料</a>。

本文內容

-   [了解 CNAME 和 A 記錄](#understanding-records)
-   [設定網站的 [共用] 或 [標準模式](#bkmk_configsharedmode)
-   [新增網站至流量管理員](#trafficmanager)
-   [新增您的自訂網域的 CNAME](#bkmk_configurecname)
-   [新增 A 記錄以您的自訂網域](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>了解 CNAME 和 A 記錄</h2>

CNAME （或別名記錄） 和記錄這兩個允許您與網站的網域名稱，但各個運作方式會不同。

###<a name="cname-or-alias-record"></a>CNAME 或別名記錄

CNAME 記錄會將*特定*的網域，例如**contoso.com**或**www.contoso.com**，對應至標準的網域名稱。 在此情況下，標準的網域名稱可能是**&lt;myapp >。 azurewebsites.net** Azure 網站的網域名稱或**&lt;myapp >。 trafficmgr.com**流量管理員設定檔的網域名稱。 CNAME 建立後，建立一個別名**&lt;myapp >。 azurewebsites.net**或**&lt;myapp >。 trafficmgr.com**網域名稱。 CNAME 項目會解析為的 IP 位址您**&lt;myapp >。 azurewebsites.net**或**&lt;myapp >。 trafficmgr.com**網域名稱，如果變更網站的 IP 位址，您不必採取任何動作。

> [AZURE.NOTE] 一些網域註冊機構只允許您將對應的子網域時使用 CNAME 記錄，例如 www.contoso.com，並不根名稱，例如 contoso.com。 如需有關 CNAME 記錄的詳細資訊，請參閱提供您的註冊機構、<a href="http://en.wikipedia.org/wiki/CNAME_record">維基百科上的項目 CNAME 記錄</a>時或<a href="http://tools.ietf.org/html/rfc1035">IETF 網域名稱-實作與規格</a>文件的文件。

###<a name="a-record"></a>記錄

例如 A 記錄對應的網域，例如**contoso.com**或**www.contoso.com**，*或萬用字元網域* ** \*。 contoso.com**，IP 位址。 若是 Azure 網站，服務的虛擬 IP 或特定的 IP 位址您購買您的網站。 因此 A 記錄上一筆 CNAME 記錄的主要優點是，您可以讓項目，例如使用萬用字元， * **。 contoso.com**，這就例如處理多個子網域的要求* *mail.contoso.com**， * *login.contoso.com**，或* *www.contso.com**。

> [AZURE.NOTE] A 記錄] 已對應至的靜態 IP 位址，因為其無法自動解析變更您的網站的 IP 位址。 當您設定自訂網域名稱設定網站; 提供記錄搭配使用的 IP 位址不過，如果您要刪除和重新建立您的網站，或變更網站模式]，返回空閒，可能會變更這個值。

> [AZURE.NOTE] 記錄無法用於負載平衡與流量管理員。 如需詳細資訊，請參閱[控制 Azure 網站流量與 Azure 流量 Manager][trafficmanager]。

<a name="bkmk_configsharedmode"></a><h2>設定您的網站的 [共用] 或 [標準模式</h2>

將自訂網域名稱設定網站上是僅適用於共用和 Azure 網站的標準模式。 切換至共用的免費網站模式] 或 [標準網站模式的網站，您必須先移除才能費用大寫字就地網站訂閱。 如需有關共用及 [標準] 模式價格的詳細資訊，請參閱[定價詳細資料][PricingDetails]。

1. 在瀏覽器中開啟 [[管理入口網站][portal]。
2. 在 [**網站**] 索引標籤中，按一下您網站的名稱。

    ![][standardmode1]

3. 按一下 [**縮放比例**] 索引標籤。

    ![][standardmode2]


4. 在 [**一般**] 區段中，按一下 [**共用**設定網站模式。

    ![][standardmode3]

    > [AZURE.NOTE] 如果您要使用此網站使用流量管理員，您必須使用選取標準模式，而不是共用。

5. 按一下 [**儲存**]。
6. 出現提示時需共用模式 （或如果您選擇標準的標準模式） 成本增加，按一下**[是]**如果您同意的話。

    <!--![][standardmode4]-->

    **附註**<br />
   如果您收到 「 設定小數位數為網站的網站名稱失敗 」 錯誤，您可以使用 [詳細資料] 按鈕，以取得詳細資訊。

<a name="trafficmanager"></a><h2>（選用）新增您的網站至流量管理員</h2>

如果您想要使用您的網站與流量管理員，請執行下列步驟。

1. 如果您還沒有流量管理員設定檔，使用[建立使用快速建立流量管理員設定檔]資訊[createprofile]建立帳戶。 請注意**。 trafficmgr.com**流量管理員設定檔與相關聯的網域名稱。 這會用在接下來的步驟。

2. 使用中[新增或刪除端點]的資訊[addendpoint]可將您的網站新增為流量管理員設定檔中的端點。

    > [AZURE.NOTE] 如果沒有列出您的網站，新增端點時，請確認這設定為 [標準模式。 您必須搭配使用流量管理員才能使用網站的標準模式。

3. 登入您的 DNS 註冊機構的網站，然後移至 [管理 DNS 的頁面。 尋找連結或標示為 「**網域名稱**、 **DNS**或**名稱伺服器管理**網站的區域。

4. 現在尋找您可以在此選取或輸入 CNAME 記錄。 您可能必須選取的記錄類型從下拉向下或移至 [進階的設定] 頁面。 您應該看這幾個字**CNAME**，**別名**或**子網域**。

5. 您也必須提供 CNAME 網域或子網域別名。 例如， **www**如果您想要建立**www.customdomain.com**別名。

5. 您也必須提供是標準的網域名稱，這個 CNAME 別名主機名稱。 這是**。 trafficmgr.com**名稱為您的網站。

例如，下列 CNAME 記錄轉所有流量從**www.contoso.com** **contoso.trafficmgr.com**，網站的網域名稱︰

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias （別名）/主機名稱子網域</strong></td>
<td><strong>標準的網域</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

**Www.contoso.com**的訪客不會看到 true host （主機） (contoso.azurewebsite.net)，因此轉寄程序是看不到使用者。

> [AZURE.NOTE] 如果您使用流量管理員使用的網站，您不需要遵循下列各節，[**新增您的自訂網域的 CNAME**] 和 [**新增 A 記錄以您的自訂網域**] 中的步驟。 在上述步驟中建立 CNAME 記錄將內送將流量路由傳送至流量管理員，再將流量路由至網站端點。

<a name="bkmk_configurecname"></a><h2>新增您的自訂網域的 CNAME</h2>

若要建立 CNAME 記錄，您必須新增新的項目 DNS 資料表中的自訂網域使用您的註冊機構所提供的工具。 每個註冊機構有類似，但稍有不同的方法，指定一筆 CNAME 記錄，但是相同的概念。

1. 使用其中一個方法來尋找**。 azurewebsite.net**指派給您的網站的網域名稱。

    * 登入[管理入口網站 Azure][portal]、 選取您的網站，選取**儀表板**，，然後在 [**快速規則**] 區段中找到的**網站 URL**項目。

    * 安裝並設定[Powershell 的 Azure](/manage/install-and-configure-windows-powershell/)，然後使用下列命令︰

            get-azurewebsite yoursitename | select hostnames

    * 安裝並設定[Azure 命令列介面](/manage/install-and-configure-cli/)，然後使用下列命令︰

            azure site domain list yoursitename

    儲存此**。 azurewebsite.net**名稱，因為它會使用下列步驟。

3. 登入您的 DNS 註冊機構的網站，然後移至 [管理 DNS 的頁面。 尋找連結或標示為 「**網域名稱**、 **DNS**或**名稱伺服器管理**網站的區域。

4. 現在尋找您可以在此選取或輸入 CNAME 記錄。 您可能必須選取的記錄類型從下拉向下或移至 [進階的設定] 頁面。 您應該看這幾個字**CNAME**，**別名**或**子網域**。

5. 您也必須提供 CNAME 網域或子網域別名。 例如， **www**如果您想要建立**www.customdomain.com**別名。 如果您想要建立根網域別名，它可能會列為 '**@**「 您的註冊機構 DNS 工具] 中的符號。

5. 您也必須提供是標準的網域名稱，這個 CNAME 別名主機名稱。 這是**。 azurewebsite.net**名稱為您的網站。

例如，下列 CNAME 記錄轉所有流量從**www.contoso.com** **contoso.azurewebsite.net**，網站的網域名稱︰

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias （別名）/主機名稱子網域</strong></td>
<td><strong>標準的網域</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

**Www.contoso.com**的訪客不會看到 true 主機 (contoso.azurewebsite.net)，因此轉寄程序是看不到使用者。

> [AZURE.NOTE] 上述範例只適用於流量__www__子網域。 由於您無法使用萬用字元的 CNAME 記錄，您必須建立每個網域/子網域的一筆的 CNAME。 如果您想要直接從子網域，例如 *。 contoso.com，對 azurewebsite.net 地址，您可以在您的 DNS 設定，設定的__URL 重新導向__或__URL 轉寄__項目，或建立 A 記錄。

> [AZURE.NOTE] 可以需要一些時間，才會傳播透過 DNS 系統您 CNAME。 您無法設定為網站 CNAME，直到散佈 CNAME。 您可以使用的服務，例如<a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a>確認 CNAME 可以使用。

###<a name="add-the-domain-name-to-your-website"></a>將網域名稱新增至您的網站

已傳播的 CNAME 記錄的網域名稱之後，您必須將其關聯您的網站。 您可以新增 CNAME 記錄，您的網站定義，使用 [Azure 命令列介面 (Azure CLI) 或使用 [Azure 管理入口網站的自訂網域名稱。

**若要新增使用的命令列工具的網域名稱**

安裝並設定[Azure 命令列介面](/manage/install-and-configure-cli/)，然後使用下列命令︰

    azure site domain add customdomain yoursitename

例如，下列將新增**www.contoso.com**的自訂網域名稱至**contoso.azurewebsite.net**網站︰

    azure site domain add www.contoso.com contoso

您的自訂網域名稱已新增至網站，請使用下列命令確認︰

    azure site domain list yoursitename

這個命令傳回清單應包含自訂網域名稱，以及預設**。 azurewebsite.net**項目。

**若要新增使用 Azure 管理入口網站的網域名稱**

1. 在瀏覽器中開啟 [[管理入口網站 Azure][portal]。

2. 在 [**網站**] 索引標籤中，按一下您的網站名稱**儀表板**，，然後選取然後選取 [從頁面底部的 [**管理網域**。

    ![][setcname2]

6. 在**網域名稱**] 文字方塊中，輸入您所設定的網域名稱。

    ![][setcname3]

6. 按一下接受的網域名稱的核取記號。

完成設定後，自訂網域名稱會列在您的網站的 [**設定**] 頁面的 [**網域名稱**] 區段。

<a name="bkmk_configurearecord"></a><h2>新增 A 記錄以您的自訂網域</h2>

若要建立 A 記錄，您必須先找到您的網站的 IP 位址。 使用您的註冊機構提供的工具，然後在您的自訂網域的 DNS 表格中新增項目。 每個註冊機構有類似，但稍有不同的方法，指定 A 記錄，但是相同的概念。 除了建立 A 記錄，您必須同時建立 CNAME 記錄 Azure 用來確認 A 記錄。

1. 在瀏覽器中開啟 [[管理入口網站 Azure][portal]。

2. 在 [**網站**] 索引標籤中，按一下您的網站名稱**儀表板**，，然後選取然後從畫面底部選取 [**管理網域**。

    ![][setcname2]

5. 在 [**管理自訂網域**] 對話方塊中，找出**要設定記錄時，使用的 IP 位址**。 複製 IP 位址。 這會建立 A 記錄時使用。

5. 在 [**管理自訂網域**] 對話方塊中，注意] 對話方塊頂端的文字結尾處的 awverify 網域名稱。 您應該**awverify.mysite.azurewebsites.net** **我的網站**是指您的網站名稱的位置。 將複製，因此在建立驗證 CNAME 記錄時使用的網域名稱。

6. 登入您的 DNS 註冊機構的網站，然後移至 [管理 DNS 的頁面。 尋找連結或標示為 「**網域名稱**、 **DNS**或**名稱伺服器管理**網站的區域。

6. 尋找您可以在此選取或輸入 A 與 CNAME 記錄。 您可能必須選取的記錄類型從下拉向下或移至 [進階的設定] 頁面。

7. 執行下列步驟來建立 A 記錄︰

    1. 選取或輸入網域或會使用 A 記錄的子網域。 如果您想要建立**www.customdomain.com**別名，，例如，選取 [ **www** ]。 如果您想要建立的所有的子網域為萬用字元項目，請輸入 「__*__」。 這會包含所有的子網域，例如**mail.customdomain.com**、 **login.customdomain.com**及**www.customdomain.com**。

        如果您想要建立 A 記錄以根網域，可能會列出為 「**@**「 在您的註冊機構 DNS 工具] 中的符號。

    2. 提供的欄位中輸入您的雲端服務的 IP 位址。 這將關聯雲端服務部署的 IP 位址的 A 記錄中使用的網域項目。

        例如，下列記錄**contoso.com**所有流量應用都轉寄**137.135.70.239**，我們已部署的應用程式的 IP 位址︰

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>主機名稱子網域</strong></td>
        <td><strong>IP 位址</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        此範例會示範如何建立 A 記錄以根網域。 如果您想要建立萬用字元項目，包含所有的子網域，您可以輸入 「__*__' 為子網域。

7. 接下來，請建立具有**awverify**的別名和**awverify.mysite.azurewebsites.net**較舊版本取得的正式網域的 CNAME 記錄。

    > [AZURE.NOTE] 雖然 awverify 的別名可能適用於幾個註冊機構，其他人可能需要 awverify.www.customdomainname.com 或 awverify.customdomainname.com 別名完整網域名稱。

    例如，下列將會建立 Azure 可用來確認 A 記錄設定的 CNAME 記錄。

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>Alias （別名）/主機名稱子網域</strong></td>
    <td><strong>標準的網域</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.net</td>
    </tr>
    </table>

> [AZURE.NOTE] 可以需要 awverify CNAME 至整個 DNS 系統散佈了一些時間。 您無法設定直到散佈 awverify CNAME A 記錄，為網站定義的自訂網域名稱。 您可以使用的服務，例如<a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a>確認 CNAME 可以使用。

###<a name="add-the-domain-name-to-your-website"></a>將網域名稱新增至您的網站

散佈**awverify** CNAME 記錄的網域名稱之後，您可以將產生關聯的記錄，使用您的網站定義的自訂網域。 您可以新增定義 A 記錄，您的網站，藉由使用 Azure CLI 或使用 Azure 管理入口網站的自訂網域名稱。

**若要新增使用 Azure 命令列介面 (Azure CLI) 的網域名稱**

安裝並設定[Azure CLI](/manage/install-and-configure-cli/)，然後使用下列命令︰

    azure site domain add customdomain yoursitename

例如，下列將新增**contoso.com**的自訂網域名稱至**contoso.azurewebsite.net**網站︰

    azure site domain add contoso.com contoso

您的自訂網域名稱已新增至網站，請使用下列命令確認︰

    azure site domain list yoursitename

這個命令傳回清單應包含自訂網域名稱，以及預設**。 azurewebsite.net**項目。

**若要新增使用 Azure 管理入口網站的網域名稱**

1. 在瀏覽器中開啟 [[管理入口網站 Azure][portal]。

2. 在 [**網站**] 索引標籤中，按一下您的網站名稱**儀表板**，，然後選取然後選取 [從頁面底部的 [**管理網域**。

    ![][setcname2]

6. 在**網域名稱**] 文字方塊中，輸入您所設定的網域名稱。

    ![][setcname3]

6. 按一下接受的網域名稱的核取記號。

完成設定後，自訂網域名稱會列在您的網站的 [**設定**] 頁面的 [**網域名稱**] 區段。

> [AZURE.NOTE] 您已新增 A 記錄，您的網站定義的自訂網域名稱之後，您可以移除 awverify CNAME 記錄，您的註冊機構所提供的工具。 不過，如果您想要新增另一個 A 記錄，您就必須重新建立 awverify 定義的新記錄，以使用網站的記錄之前，您可以建立新的網域名稱的關聯。

## <a name="next-steps"></a>後續步驟

-   [如何管理網站](/manage/services/web-sites/how-to-manage-websites/)

-   [針對網站中設定 SSL 憑證](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
