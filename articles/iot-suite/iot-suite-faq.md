<properties
  pageTitle="Azure IoT 套件的常見問題集 |Microsoft Azure"
  description="常見問題集 IoT 套件"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="09/26/2016"
  ms.author="araguila"/>
   
# <a name="frequently-asked-questions-for-iot-suite"></a>常見問題集 IoT 套件

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>刪除 Azure 入口網站中的 [資源] 群組，並按一下 [刪除的預先設定的方案中 azureiotsuite.com 之間的差異為何？

- 如果您刪除預先設定的解決方案中[azureiotsuite.com][lnk-azureiotsuite]，刪除您建立預先設定的解決方案; 時所佈建後的所有資源如果您在 [資源] 群組中加入額外的資源，也會一併刪除這些。 

- 如果您刪除[Azure 入口網站]中的 [資源] 群組[lnk-azure-portal]，您只會刪除資源群組; 中的資源您也需要刪除[Azure 傳統入口網站]中預先設定的解決方案相關聯的 Azure Active Directory 應用程式[lnk-classic-portal]。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>在訂閱中可以提供 IoT 中心執行個體數目？ 

十個。 您可以建立[Azure 支援票證][link-azuresupportticket]將這項限制，但根據預設，您可以只提供每個訂閱的十個 IoT 集線器[Azure 訂閱限制]中所述[link-azuresublimits]。 如此一來，新 IoT 中樞佈建新的每個預先設定的方案，因為您可以只提供最多 10 個預先設定的解決方案中指定的訂閱。 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>在訂閱中可以提供 DocumentDB 執行個體數目？

為。 您可以建立[Azure 支援票證][link-azuresupportticket]將這項限制，但根據預設，您可以只提供為每個訂閱的 DocumentDB 執行個體。 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>在訂閱中可以提供多少免費的 Bing 地圖服務 Api？

兩個。 您可以建立只有兩個內部交易階層 1 Bing 地圖服務企業方案的 Azure 的訂閱中。 遠端監視的解決方案是佈建後，預設的內部交易第 1 層計劃。 如此一來，您可以只提供兩個遠端監控解決方案在訂閱中的不需要修改。

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>有的靜態地圖的遠端監控解決方案部署，如何新增互動式 Bing 地圖？ 
1. 您的 Bing 地圖服務 API 取得從[Azure 入口網站]的企業 QueryKey[lnk-azure-portal]: 
 1. 瀏覽至您的 Bing 地圖服務 API，企業版中[Azure 入口網站]在哪裡資源群組[lnk-azure-portal]。
 2. 按一下 [所有設定]，然後金鑰管理]。 
 3. 您會注意到兩個機碼︰ 主要金鑰和 QueryKey。 複製 QueryKey 值。

     > [AZURE.NOTE] 沒有 Bing 地圖服務 API，企業版帳戶？ [Azure 入口網站]中建立[lnk-azure-portal]，按一下 [+ 新增]，搜尋 Bing 地圖服務 API，企業版，然後依照提示若要建立。

2. 從[Azure IoT-遠端-監視]下拉式功能表的最新的程式碼[lnk-remote-monitoring-github]。

3. 執行本機或雲端部署追蹤存放庫中的 [/docs/] 資料夾中的命令列部署指南。 

4. 您已執行本機或雲端部署中，尋找您的根資料夾中之後 *。 user.config 部署期間所建立的檔案。 在文字編輯器中開啟此檔案。 

5. 變更包含您從您 QueryKey 複製值的下列行︰ 
   
  `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>如果我擁有 Microsoft Azure DreamSpark 可以建立預先設定的方案嗎？
此時，您無法建立預先設定的解決方案與[Microsoft Azure 的 DreamSpark] [lnk-dreamspark]帳戶。 不過，您可以在其中建立[免費試用版 Azure 帳戶][lnk-30daytrial]在幾分鐘，讓您建立的預先設定的解決方案。

### <a name="how-do-i-delete-an-aad-tenant"></a>我要如何刪除 AAD 租用戶？

請參閱 Eric Golpe 部落格文章[的刪除 Azure AD 租用戶逐步解說][lnk-delete-aad-tennant]。

## <a name="next-steps"></a>後續步驟

您也可以瀏覽的一些其他功能和預先設定的 IoT 套件解決方案的功能︰

- [預先設定的預測維護解決方案概觀][lnk-predictive-overview]
- [從頭 IoT 安全性][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
