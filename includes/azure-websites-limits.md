資源|免費|共用 （預覽版本）|基本|標準|進階版 （預覽版本）</th>
---|---|---|---|---|---
[網頁，[行動電話或 API 應用程式](https://azure.microsoft.com/services/app-service/)每個[應用程式服務方案](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup>|10|100|不受限制的<sup>2</sup>|無限制的<sup>2</sup>|無限制的<sup>2</sup>
每個[應用程式服務方案](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)[邏輯應用程式](https://azure.microsoft.com/services/app-service/logic/)</a><sup>1</sup>|10|10|10|每個核心 20|每個核心 20
[應用程式服務計劃](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|1 每個地區|每個資源群組 10|100，每個資源群組|100，每個資源群組|100，每個資源群組
計算執行個體類型|共用|共用|專用的<sup>3</sup>|專用的<sup>3</sup>|專用的<sup>3</sup></p>
[擴充](../articles/app-service-web/web-sites-scale.md)（最大的執行個體）|1 共用|1 共用|3 專用的<sup>3</sup>|10 專用的<sup>3</sup>|20 專用 (50 中 ASE)<sup>3、 4</sup>
儲存<sup>5</sup>|1 GB<sup>5</sup>|1 GB<sup>5</sup>|10 GB<sup>5</sup>|50 GB<sup>5</sup>|500 GB<sup>4,5</sup></p>
CPU 時間 （簡稱）<sup>6</sup>|3 分鐘|3 分鐘|無限制，標準[工資率](https://azure.microsoft.com/pricing/details/app-service/)支付</a>|無限制，在 [標準工資率支付|無限制，在 [標準工資率支付
CPU 時間 （日）<sup>6</sup>|60 分鐘的時間|240 分鐘|無限制，標準[工資率](https://azure.microsoft.com/pricing/details/app-service/)支付</a>|無限制，在 [標準工資率支付|無限制，在 [標準工資率支付
記憶體 （1 小時）|1024 MB 每個應用程式服務計劃|1024 MB 每個應用程式|N/A|N/A|N/A
頻寬|165 MB|沒有限制，[資料傳輸速度](https://azure.microsoft.com/pricing/details/data-transfers/)套用|無限制，費率套用的資料傳輸|無限制，費率套用的資料傳輸|無限制，費率套用的資料傳輸
應用程式結構|32 位元|32 位元|32 位元/64 位元|32 位元/64 位元|32 位元/64 位元
每個執行個體<sup>7</sup>的 web 通訊端|5|35|350|無限制|無限制
每個應用程式的同時[偵錯工具連線](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)|1|1|1|5|5
[FTP/S 與 SSL azurewebsites.net 子網域](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
[自訂網域](../articles/app-service-web/web-sites-custom-domain-name.md)支援||X|X|X|X
[SSL 支援](../articles/app-service-web/web-sites-configure-ssl-certificate.md)的自訂網域|||無限制|無限制 5 SNI SSL 與包含 1 IP SSL 連線|無限制 5 SNI SSL 與包含 1 IP SSL 連線
整合式的負載平衡器||X|X|X|X
[永遠開啟](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[排定的備份](../articles/app-service-web/web-sites-backup.md)||||一次每日|一次每個 5 分鐘<sup>8</sup>
[[自動調整大小](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md)<sup>9</sup>|X|X|X|X|X
[Azure 排程器](https://azure.microsoft.com/services/scheduler/)支援||X|X|X|X
[監控端點](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[執行位置 （預覽版本）](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
每個應用程式的自訂網域</a>||500|500|500|500
SLA||<p>|99.9%|99.95<sup>10</sup>|99.95<sup>10</sup>

<sup>1</sup>應用程式，並儲存配額是每個應用程式服務方案除非明。  
<sup>2</sup>您可以主控這些電腦的應用程式的實際次數，取決於應用程式的活動、 電腦執行個體和對應的資源使用量的大小。  
<sup>3</sup>不同大小的可專用的執行個體。 如需詳細資訊，請參閱[應用程式服務價格](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/)。  
<sup>4</sup>進階版層可讓最 50 計算 （愛爾蘭可用性） 的執行個體和 500 GB 的磁碟空間時使用應用程式服務環境中，並且 20 可計算執行個體，250 GB 的儲存空間否則。  
<sup>5</sup>儲存空間限制位於內容大小總計過所有應用程式的相同的應用程式服務方案。 更多儲存空間的選項可在[應用程式服務環境](../articles/app-service-web/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>這些資源受到實際資源專用的執行個體 （執行個體大小和執行個體數目）。  
<sup>7</sup>如果您在兩個執行個體基本層縮放應用程式，您會有兩個執行個體的每個 350 同時連線。  
<sup>8</sup>進階版層可讓備份的時間間隔下最每 5 分鐘，使用應用程式服務環境時, 和 50 次每日否則。  
<sup>9</sup>視需要處理的排程，或持續在您的應用程式服務執行個體背景工作執行自訂可執行檔及/或指令碼。 一律在時需要連續 WebJobs 執行。 Azure 項排程器免費或標準是必要的排程 WebJobs。 在應用程式服務執行個體，可以執行的 WebJobs 數目沒有預先定義限制，但取決於應用程式碼嘗試執行的實際限制。   
<sup>10</sup>使用多個執行個體以 Azure 流量管理員的部署提供 99.95%的 SLA 設定容錯移轉。  
