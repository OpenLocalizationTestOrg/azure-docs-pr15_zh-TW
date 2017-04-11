下表列出 PolicyBased 與 RouteBased VPN 閘道器的需求。 此表格適用於資源管理員] 及 [傳統部署模型。 對於傳統的模型，PolicyBased VPN 閘道完全一樣靜態閘道器，且路由為基礎的閘道器動態閘道相同。


|   | **PolicyBased Basic VPN 閘道器** | **RouteBased Basic VPN 閘道器** | **RouteBased 標準 VPN 閘道器**   | **RouteBased 高效能 VPN 閘道器** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **網站-連線 (S2S)**  | PolicyBased VPN 設定        | RouteBased VPN 設定  | RouteBased VPN 設定     | RouteBased VPN 設定    |
| **點-網站連線 (P2S**)      | 不支援   | 支援 （可以同時存在 S2S）  | 支援 （可以同時存在 S2S）  | 支援 （可以同時存在 S2S） |
| **驗證方法**                 |    預先共用索引鍵  | 預先共用的金鑰 S2S 連線，P2S 連線的憑證 | 預先共用的金鑰 S2S 連線，P2S 連線的憑證 | 預先共用的金鑰 S2S 連線，P2S 連線的憑證 |
| **S2S 連線數目上限**       | 1                              | 10                                                                    | 10                                | 30                               |
| **P2S 連線數目上限**       | 不支援                  | 128                                                                   | 128                               | 128                              |
|**作用中的路由支援 (BGP)**           | 不支援                  | 不支援                                                         | 支援                     | 支援                   |
 