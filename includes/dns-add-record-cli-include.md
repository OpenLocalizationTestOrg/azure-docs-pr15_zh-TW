#### <a name="create-an-a-record-set-with-single-record"></a>建立 A 記錄以單一記錄設定

若要建立的記錄組，請使用`azure network dns record-set create`。 指定 [資源] 群組中，區域名稱記錄設定相對的名稱、 記錄類型和 live (TTL) 的時間。

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

之後建立 A 記錄設定、 IPv4 位址新增到記錄以設定`azure network dns record-set add-record`。

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>建立一筆記錄以設定 AAAA 記錄

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>建立一筆記錄以設定一筆 CNAME 記錄

CNAME 記錄只允許一串值。


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>建立一筆記錄以設定 MX 記錄

在此範例中，我們使用記錄集名稱"@"區域 apex 在建立 MX 記錄 (在此情況下，「 contoso.com 」)。 這是常見 MX 記錄。

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>建立 NS 記錄設定的單筆記錄

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>建立一筆記錄以設定 PTR 記錄  
在此情況下 ' 我-arpa-zone.com' 代表代表您的 IP 範圍的 ARPA 區域。  設定此區域中的每個 PTR 記錄會對應到這個 IP 範圍內的 IP 位址。    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>建立一筆記錄以設定一筆 SRV 記錄

如果您正在建立一筆 SRV 記錄的區域根目錄中，您可以指定 「 _service 」 和 「 _protocol 」 的記錄名稱。 包含不需要"@"的記錄名稱。


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>建立 TXT 記錄以單一記錄設定

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"
