網域名稱系統 (DNS) 用來找出在網際網路上的項目。 例如，當您在瀏覽器中，輸入地址，或按一下 [在網頁上的連結，它會使用 DNS 將 IP 位址翻譯網域。 IP 位址就像街道地址，但不是非常人力易記。 例如，是很容易記住的 IP 位址，例如 192.168.1.88 或 2001:0:4137:1f67:24a2:3888:9cce:fea3 大於記住 DNS 名稱 （例如**contoso.com）** 。

DNS 系統會根據*記錄*。 記錄會特定*名稱*，例如**contoso.com**，關聯的 IP 位址或另一個 DNS 名稱。 時，請在應用程式，例如網頁瀏覽器中，尋找 DNS 中的名稱，它會找到記錄時，並使用上述程序指向為地址。 如果 IP 位址指向的值，在瀏覽器會使用該值。 如果它指向其他 DNS 名稱時，應用程式必須再次執行解析度。 最後，所有名稱解析會都結尾的 IP 位址。

當您建立 Azure 網站時，DNS 名稱會自動指派到網站。 此名稱的格式為**&lt;yoursitename&gt;。 azurewebsites.net**。 當您為 Azure 流量管理員端點新增您的網站時，您的網站是透過**&lt;yourtrafficmanagerprofile&gt;。 trafficmanager.net**網域。

> [AZURE.NOTE] 當您的網站設定為流量管理員端點時，您會使用**。 trafficmanager.net**位址時建立 DNS 記錄。

> 您可以只使用 CNAME 記錄與流量管理員

也有多種類型的記錄，兩者都有自己的功能和限制，但若要設定為流量管理員端點的網站，我們只想大約一;*CNAME*記錄。

###<a name="cname-or-alias-record"></a>CNAME 或別名記錄

CNAME 記錄會將*特定*DNS 名稱，例如**mail.contoso.com**或**www.contoso.com**，對應到另一個 （標準） 的網域名稱。 若是 Azure 使用流量管理員、 網站的標準的網域名稱是**&lt;myapp >。 trafficmanager.net**流量管理員設定檔的網域名稱。 CNAME 建立後，建立一個別名**&lt;myapp >。 trafficmanager.net**的網域名稱。 CNAME 項目會解析為的 IP 位址您**&lt;myapp >。 trafficmanager.net**網域名稱，如果變更網站的 IP 位址，您不必採取任何動作。

一旦流量到達流量管理員在其然後路由流量至您的網站，使用負載平衡其所設定的方法。 這是您的網站訪客完全透明。 就只會看到其瀏覽器中的自訂網域名稱。

> [AZURE.NOTE] 一些網域註冊機構只允許您將對應的子網域時使用的 CNAME 記錄，例如**www.contoso.com**，並不根名稱，例如**contoso.com**。 如需有關 CNAME 記錄的詳細資訊，請參閱提供您的註冊機構、<a href="http://en.wikipedia.org/wiki/CNAME_record">維基百科上的項目 CNAME 記錄</a>時或<a href="http://tools.ietf.org/html/rfc1035">IETF 網域名稱-實作與規格</a>文件的文件。
