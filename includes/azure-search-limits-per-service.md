儲存空間限制磁碟空間，或索引或文件的*最大的數字*是固定限制，視何者先。 

資源|免費|基本|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
服務等級協定 (SLA)|沒有<sup>1</sup> |[是] |[是]  |[是] |[是] |[是]
每個資料分割的儲存空間|50 MB |2 GB|25 GB|100 GB|200 GB|200 GB
每個服務的磁碟分割區|N/A|1|12|12|12|3
分割大小|N/A|2 GB|25 GB|100 GB|200 GB |200 GB
複本|N/A|1|12|12|12|12
最大的索引|3|5|50|200|200|每個資料分割或每個服務的 3000 1000
最大的文件|10000|有 1 百萬個|每個資料分割 15 百萬或每個服務的 180 百萬 |每個資料分割 60 百萬或每個服務的 720 百萬 |每個資料分割成 120 百萬或每個服務的 1.4 億|每個索引有 1 百萬個或每個資料分割 200 百萬 |
秒 (QPS) 估計的查詢|N/A|~ 每個複本 3|~ 15 每複本|~ 60 每複本|~ 60 每複本|> 60 每複本

<sup>1</sup>免費，預覽 Sku 都不會與服務等級協定 (Sla)。 SKU 變成推出之後，會強制執行 Sla。