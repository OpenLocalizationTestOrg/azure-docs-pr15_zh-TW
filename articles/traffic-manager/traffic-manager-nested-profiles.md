<properties
    pageTitle="巢狀流量管理員設定檔 |Microsoft Azure"
    description="本文說明巢狀檔的 Azure 流量管理員功能"
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

# <a name="nested-traffic-manager-profiles"></a>巢狀的流量管理員設定檔

流量管理員包含傳輸路由的方法，可讓您控制流量管理員如何選擇哪個端點應從每位使用者收到流量的範圍。 如需詳細資訊，請參閱[流量管理員傳輸路由的方法](traffic-manager-routing-methods.md)。

每個流量管理員設定檔指定單一流量路由方法。 然而，有需要更複雜且流量路由比路由單一流量管理員設定檔所提供的案例。 您可以使用巢狀流量管理員設定檔，合併多個流量路由方法的優點。 巢狀的設定檔可讓您覆寫預設流量管理員行為，以支援更大和較複雜的應用程式部署。

下列範例會說明如何在各種情況中使用巢狀的流量管理員設定檔。

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>範例 1︰ 結合 '效能 」 和 「 加權' 路由流量

假設您部署下列 Azure 區域中的應用程式︰ 西、 西歐和中式地址。 您使用流量主管的 '效能' 流量路由方法將流量分散給使用者的區域。

![單一流量管理員設定檔][1]

現在，假設您想要再廣泛復原取出多測試您的服務的更新。 您想要使用 「 加權' 流量路由方法，將您測試的部署流量的一小部分。 您在西歐設定測試部署，同時現有生產部署。

您無法合併兩個 '加權 」 和 「 效能流量路由單一的設定檔中。 若要支援這種情況，您可以建立使用兩個西歐端點和 '加權' 流量路由方法流量管理員設定檔。 接下來，您將新增此 '子系' 設定檔為端點至 「 上層 」 的設定檔。 父項設定檔仍會使用效能流量路由方法，並包含為端點的其他通用部署。

下圖顯示此範例中︰

![巢狀的流量管理員設定檔][2]

在此組態中，透過上層設定檔導向流量分配流量區域以正常方式。 西歐，內巢狀的設定檔散佈流量生產和測試結束點，根據的權重。

當上層設定檔中使用 '效能' 流量路由方法時，每個端點必須被指派一個位置。 當您設定的結束點時，會被指派位置。 選擇 [部署至最接近的 Azure 地區]。 Azure 區域是受支援的網際網路延遲表格的位置值。 如需詳細資訊，請參閱[流量管理員 '效能' 流量路由方法](traffic-manager-routing-methods.md#performance-traffic-routing-method)。

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>範例 2︰ 監控巢狀的設定檔中的端點

流量管理員主動監控每個服務端點的狀況。 如果不佳端點，流量管理員會指導使用者替代的端點若要保留您的服務的可用性。 此端點的監控和容錯移轉行為適用於所有流量路由的方法。 如需詳細資訊，請參閱[流量管理員端點監控](traffic-manager-monitoring.md)。 結束點監視的運作方式會不同的巢狀的設定檔。 使用巢狀的設定檔，上層設定檔不會直接執行上兒童的狀況檢查。 不過，子設定檔的結束點的健康情況用來計算子設定檔的整體健康情況。 此狀況資訊會傳播巢狀的設定檔階層。 父項設定檔此彙總來決定是否要直接流量至子的設定檔的狀況。 狀況監視的巢狀的設定檔，請參閱這篇文章的完整詳細資料的[常見問題集](#faq)一節。

返回前一個範例，假設生產中的部署西歐失敗。 根據預設，'子系' 設定檔將導向所有流量測試部署。 如果測試部署也失敗，請上層設定檔會決定子設定檔應該不接收流量之後的所有子端點。 然後上層設定檔將流量分散到其他區域。

![巢狀的設定檔容錯移轉 （預設行為）][3]

您可能會滿意這樣的安排中。 或者，您可能會考慮所有流量西歐現在都要測試部署，而不是受限制的子集傳輸。 測試部署的狀況，無論您想要生產中的部署西歐失敗時，無法在其他區域。 若要啟用此容錯移轉，您可以指定 'MinChildEndpoints' 參數，設定為端點上層設定檔中的 [子設定檔時。 參數會決定可用的端點子設定檔中的最小的數字。 預設值是 '1'。 此案例中，您可以設定 MinChildEndpoints 值為 2。 下面這個閥值，上層設定檔會根據整個子設定檔，無法使用，並指示流量的其他端點。

下圖說明這項設定︰

![巢狀方式使用 「 MinChildEndpoints 」 的設定檔容錯移轉 = 2][4]

>[AZURE.NOTE]
>[優先順序] 流量路由方法散佈所有流量的單一端點。 因此有小用途 '1' 以外的 MinChildEndpoints 設定子設定檔。

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>範例 3︰ 選擇優先將容錯移轉地區 '效能' 流量路由

'效能' 流量路由方法的預設行為被為了避免覆載入 [最近的結束點的下一步和造成一連串的失敗次數。 當端點失敗時，所有的流量會導向至該端點是平均分散到其他結束點，在所有區域。

!['效能' 流量路由與預設容錯移轉][5]

不過，假設您偏好西美國，西歐流量容錯移轉，並只將其他區域流量導向兩端時無法使用。 您可以建立子設定檔使用 [優先順序] 流量路由方法此方案。

!['效能' 流量路由與優先容錯移轉][6]

因為西歐端點有較高的優先順序比西美國端點，所有的流量會傳送至西歐端點，兩個端點連線時。 如果西歐失敗，它的流量會導向西美國。 以巢狀的設定檔，流量會導向至中式地址，只有在同時西歐和西美國失敗時。

您可以在所有區域的重複這個模式。 取代上層設定檔中的所有三個端點三個子設定檔，每個提供優先順序容錯移轉順序。

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>範例 4︰ 控制 '效能' 相同的區域中的多個端點之間路由流量

假設傳輸路由的方法在設定檔中的特定區域包含一個以上的端點用 '效能'。 根據預設，流量導向至該區域平均分佈過該範圍中所有可用的結束點。

!['效能' 傳輸路由的區域流量分配 （預設行為）][7]

而非西歐中新增多個端點，這些端點會放在不同的子設定檔。 上層會新增子設定檔中西歐唯一的端點。 在子設定檔設定可以控制西歐流量分配啟用優先順序型或加權傳輸路由的區域內。

!['效能' 流量路由與自訂的區域流量分配][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>範例 5︰ 每個端點監控設定

假設您使用流量管理員順利移轉從舊版流量的內部部署到裝載於 Azure 中的新雲端版本的網站。 舊版的網站，您想要使用 [首頁] 頁面 URI 監控網站的健全狀況。 但您的新雲端的版本，實作自訂的監控頁面 (路徑 」 / monitor.aspx 」)，包括其他檢查項目。

![監控 （預設行為） 的流量管理員結束點][9]

流量管理員設定檔中的監控設定套用至所有單一的設定檔中的端點。 使用巢狀的設定檔，您使用不同的子設定檔每個網站定義不同的監控設定。

![監視使用每個端點設定流量管理員結束點][10]

## <a name="faq"></a>常見問題集

### <a name="how-do-i-configure-nested-profiles"></a>我要如何設定巢狀的設定檔？

您可以同時使用 Azure 資源管理員和傳統 Azure REST Api，Azure PowerShell cmdlet 和跨平台 Azure CLI 命令設定巢狀的流量管理員設定檔。 他們也支援透過新 Azure 入口網站。 在 [傳統] 入口網站中不支援他們。

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>支援多少的圖層的巢狀用途流量管理員嗎？

您可以使用巢狀最多 10 個層的設定檔。 不允許迴圈 」。

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>可以使用相同的資料傳輸管理員設定檔中的巢的狀子設定檔，混合其他結束點類型？

[是]。 沒有任何限制在您如何合併不同類型的設定檔中的端點。

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>付款的模型如何套用巢狀的設定檔呢？

有不負價格的使用巢狀的設定檔的影響。

流量管理員計費有兩個元件︰ 端點狀況檢查和數百萬 DNS 查詢

- 結束點狀況檢查︰ 子設定檔設定為父設定檔中的端點時不另外收費。 依平常的方式是向收費監視子設定檔中的端點。
- DNS 查詢︰ 每個查詢只會計算一次。 針對上層設定檔只計算對傳回端點從子設定檔中的上層設定檔的查詢。

完整的詳細資訊，請參閱[流量管理員價格] 頁面](https://azure.microsoft.com/pricing/details/traffic-manager/)。

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>有巢狀的設定檔的影響效能嗎？

[否]。 有任何使用巢狀的設定檔時所產生的效能影響。

流量管理員名稱伺服器內部往返設定檔階層，處理每個 DNS 查詢時。 DNS 查詢至上層的設定檔可以接收來自子設定檔的端點為 DNS 回應。 無論您使用單一的設定檔或巢狀的設定檔，則會使用單一的 CNAME 記錄。 有不需要建立階層圖中的每個設定檔的 CNAME 記錄。

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>流量管理員如何計算上層設定檔中的巢狀端點的狀況？

父項設定檔不會直接執行上的狀況檢查。 不過，子設定檔的結束點的健康情況用來計算子設定檔的整體健康情況。 這項資訊會傳播巢狀的設定檔階層來決定健康狀況的巢狀的結束點。 上層設定檔會使用此彙總的狀況，來判斷是否可以導向到子流量。

下表說明流量管理員 」 的狀況檢查巢狀端點的行為。

|子設定檔監視器狀態|父端點監視器狀態|備忘稿|
|---|---|---|
|停用。 子設定檔已停用。|停止|已停止上層端點狀態，不會停用。 停用狀態會保留來表示您已停用上層設定檔中的端點。|
|降低。 至少有一個子設定檔端點是降級狀態。| Online︰ 子設定檔中的線上結束點的數目至少是 MinChildEndpoints 的值。<BR>CheckingEndpoint︰ 子設定檔中的線上加上 CheckingEndpoint 結束點的數目至少是 MinChildEndpoints 的值。<BR>降低︰ 否則。|路由流量的狀態 CheckingEndpoint 端點。 如果 MinChildEndpoints 設得太高，永遠降低端點。|
|線上。 至少有一個子設定檔端點是線上狀態。 沒有端點是降級狀態。|請參閱上方。||
|CheckingEndpoints。 至少有一個子設定檔端點是 「 CheckingEndpoint 」。 沒有端點是 「 Online' 降低 」|與上面相同。||
|非使用中。 所有的子設定檔端點會停用或已停止]，或此設定檔中有不結束點。|停止||


## <a name="next-steps"></a>後續步驟

深入瞭解[流量管理員的運作方式](traffic-manager-how-traffic-manager-works.md)

瞭解如何[建立流量管理員設定檔](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

