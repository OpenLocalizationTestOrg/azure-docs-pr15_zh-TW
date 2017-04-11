<properties
    pageTitle="測試流量管理員設定 |Microsoft Azure"
    description="本文可協助您測試流量管理員設定"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="test-your-traffic-manager-settings"></a>測試您的資料傳輸管理員設定

若要測試您的資料傳輸管理員設定，您需要有多個用戶端在不同的位置，您可以執行您的測試。 然後，顯示下一次流量管理員設定檔中的端點。

* 設定 [DNS TTL] 值低，以便變更傳播快速 （例如，30 秒）。
* 知道您的 Azure 雲端服務及您正在測試設定檔中的網站的 IP 位址。
* 使用工具，可讓您的 DNS 名稱解析成 IP 位址，並顯示的地址。

會檢查 DNS 名稱解析到您的設定檔中的端點的 IP 位址。 名稱應解決與定義流量管理員設定檔中的傳輸路由方法一致的方式。 您可以使用的工具，例如**nslookup**或**深入**解析 DNS 名稱。

下列範例會協助您測試您的資料傳輸管理員設定檔。

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>核取 [使用中 Windows nslookup 和 ipconfig 流量管理員設定檔

1. 以管理員身分開啟命令或 Windows PowerShell 提示。
2. 輸入`ipconfig /flushdns`要清除 DNS 解析程式快取。
3. 輸入`nslookup <your Traffic Manager domain name>`。 例如，下列命令會檢查使用前置詞*myapp.contoso*的網域名稱

        nslookup myapp.contoso.trafficmanager.net

    一般的結果會顯示下列資訊︰

    * DNS 名稱及 IP 位址存取若要解決此流量管理員網域名稱的 DNS 伺服器。
    * 流量管理員網域名稱您輸入的命令列上 「 nslookup 」，流量管理員網域會解析的 IP 位址。 第二個 IP 位址是要檢查的重要項目。 它應該相符的公用虛擬 IP (VIP) 地址的其中一個雲端服務或您正在測試流量管理員設定檔中的網站。

## <a name="how-to-test-the-failover-traffic-routing-method"></a>如何測試容錯移轉流量路由方法

1. 設定讓所有的結束點。
2. 使用單一用戶端，要求您使用 nslookup 或類似公用程式的公司網域名稱的 DNS 解析。
3. 請確定解決的 IP 位址符合主要的結束點。
4. 關閉您的主要端點或移除監控檔案，以便該流量管理員認為應用程式當機。
5. 等待 DNS 時間存留 (TTL) 的流量管理員設定檔，加上其他兩分鐘。 例如，如果您的 DNS TTL 300 秒 （5 分鐘），您必須等到七分鐘。
6. 清除您的 DNS 用戶端快取及要求 DNS 解析使用 nslookup。 在 Windows 中，您可以清除 DNS 快取 ipconfig /flushdns，即可命令。
7. 請確定解決的 IP 位址符合您的第二個端點。
8. 重複此程序，依序停機每個端點。 確認 DNS 傳回清單中的下一個端點的 IP 位址。 關閉所有的結束點時，您應該再次取得主要端點的 IP 位址。

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>如何測試加權的流量路由方法

1. 設定讓所有的結束點。
2. 使用單一用戶端，要求您使用 nslookup 或類似公用程式的公司網域名稱的 DNS 解析。
3. 請確定解決的 IP 位址符合您的結束點的其中一個。
4. 清除您的 DNS 用戶端快取，然後重複步驟 2 和 3 的每個端點。 您應該會看到不同的每個端點傳回的 IP 位址。

## <a name="how-to-test-the-performance-traffic-routing-method"></a>如何測試效能流量路由方法

若要有效測試效能流量路由方式，您必須位於世界的不同部分的用戶端。 您可以建立用戶端在不同的 Azure 區域的可用來測試您的服務。 如果您有全域的網路，您就可以從遠端登入世界各地的其他部分中的用戶端，然後從該處執行測試。

或者，有空閒網頁 DNS 查閱挖掘並提供服務。 這些工具可讓您檢查 DNS 名稱解析來自世界各地的各種不同的位置。 執行 [DNS 查閱] 上搜尋的範例。 協力廠商服務，例如 Gomez 或 「 主題演說可用來確認您的設定檔分送傳輸，如預期般。

## <a name="next-steps"></a>後續步驟

* [關於流量管理員流量路由方法](traffic-manager-routing-methods.md)
* [流量管理員效能考量](traffic-manager-performance-considerations.md)
* [疑難排解流量管理員降低狀態](traffic-manager-troubleshooting-degraded.md)




