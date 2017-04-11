<properties
   pageTitle="開始使用 SQL 資料庫動態資料 Masking （Azure 傳統入口網站）"
   description="如何開始使用 SQL 資料庫動態資料 Masking Azure 傳統入口網站中"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# <a name="get-started-with-sql-database-dynamic-data-masking-azure-classic-portal"></a>開始使用 SQL 資料庫動態資料 Masking （Azure 傳統入口網站）

> [AZURE.SELECTOR]
- [動態資料遮罩-Azure 入口網站](sql-database-dynamic-data-masking-get-started.md)

## <a name="overview"></a>概觀

SQL 資料庫動態資料 Masking 限制機密資料的風險，其偽裝不權限的使用者。 Azure SQL 資料庫的 V12 版本支援的動態資料遮罩。

動態資料遮罩可協助防止未經授權的存取機密資料，讓客戶指定多少機密的資料，以顯示應用程式層級上影響降至最低。 它是查詢的原則為基礎的安全性功能，隱藏大於指定的資料庫欄位結果集中的機密資料時不會變更資料庫中的資料。

例如，客服中心服務人員可能會識別來電者社會安全的信用卡卡號數數字，但這些資料的項目應不完全公開服務代表。 所有的遮罩但任何社會安全或信用卡卡號結果中的最後一個四位數字設定的任何查詢定義遮罩規則。 另一個範例，遮罩適當的資料可以定義保護個人識別資訊 (PII) 資料，以便開發人員可以查詢以進行疑難排解而不違反法規遵循法規生產環境。

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL 資料庫動態資料 Masking 基本概念

您設定 masking 原則 Azure 傳統入口網站資料庫的 [稽核及安全性] 索引標籤中的動態資料。


> [AZURE.NOTE] 若要設定 masking Azure 入口網站中的動態資料，請參閱[開始使用 SQL 資料庫動態資料 Masking （Azure 入口網站）](sql-database-dynamic-data-masking-get-started.md)。


### <a name="dynamic-data-masking-permissions"></a>動態資料遮罩權限

Azure 資料庫管理員、 伺服器管理員或安全性主管角色可以設定的動態資料遮罩。

### <a name="dynamic-data-masking-policy"></a>動態資料遮罩原則

* **SQL 使用者排除遮罩**-一組 SQL 使用者或 AAD 身分識別的 SQL 查詢結果中取得遮罩的資料。 請注意，系統管理員權限的使用者一律會排除遮罩，會看到沒有任何遮罩的原始資料。

* **Masking 規則**的一組被遮罩在指定的欄位及遮罩函數所定義的規則。 在指定的欄位，可以使用資料庫結構描述名稱、 表格和資料行名稱來定義。

* **Masking 函數**的一組控制資料的不同情況的公開的方法。

| Masking 函數 | Masking 邏輯 |
|----------|---------------|
| **預設值**  |**根據指定欄位的資料型別完整遮罩**<br/><br/>• 使用 XXXX 或較少的 x，如果欄位的大小小於 4 個字元字串資料類型 （nchar、 ntext、 nvarchar）。<br/>• 使用零值 （bigint、 位元，小、 int、 金錢、 數字、 小、 smallmoney、 tinyint、 浮動時間，即時） 的數字資料類型。<br/>• 使用 01-01-1900 日期/時間資料類型日期、 datetime2、 datetime、 datetimeoffset、 29.998 （時間）。<br/>• SQL 變數目前類型的預設值會使用。<br/>XML 文件的 •<masked/>使用。<br/>• 使用特殊的資料類型為空值 (時間戳記資料表、 hierarchyid，GUID 二進位、 圖像、 varbinary 空間類型)。
| **信用卡** |**Masking 的公開指定欄位的最後一個四個數字的方法**，並新增常數字串形式信用卡的開頭。<br/><br/>XXXX XXXX-XXXX 1234|
| **社會安全號碼** |**偽裝的公開指定欄位的最後一個四個數字的方法**，並在 [美國社會安全號碼的表單中的前置詞為新增常數的字串。<br/><br/>上述-XX 1234 |
| **電子郵件** | **Masking 的方法公開的第一個字母以及取代網域 XXX.com 搭配**使用的電子郵件地址的形式常數字串前置字元。<br/><br/>aXX@XXXX.com |
| **隨機數字** | 根據所選的界限及實際的資料類型， **masking 產生隨機數字的方法**。 如果指定的界限相等，則遮罩函數將會固定數字。<br/><br/>![功能窗格](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **自訂文字** | **偽裝的公開的第一個和最後一個字元的方法**，並新增自訂邊框之字串的中間。 如果原始字串小於公開的前置詞與後置字元，會使用只與邊框距離字串。<br/>[與邊框距離] 首碼尾碼<br/><br/>![功能窗格](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-classic-portal"></a>設定資料庫使用 Azure 傳統入口網站的動態資料遮罩

1. 啟動在[https://manage.windowsazure.com](https://manage.windowsazure.com)Azure 傳統的入口網站。

2. 按一下您想要遮罩的資料庫，然後按一下 [**稽核及安全性**] 索引標籤。

3. **動態資料 masking**] 下按一下 [**啟用**]，以啟用 masking 功能的動態資料]。  

4. 輸入 SQL 使用者或 AAD 身分識別，應該排除遮罩，且包含遮罩機密資料的存取權。 這應該是以分號分隔使用者清單。 請注意，系統管理員權限的使用者永遠原始遮罩資料的存取權。

    >[AZURE.TIP] 若要使應用程式層可以顯示機密資料應用程式的權限的使用者，新增 SQL 使用者或應用程式使用查詢資料庫 AAD 身分識別。 強烈建議這份清單，包含最小到最小化的機密資料揭露獲授限使用者的數字。

    ![功能窗格](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. 在功能表列中的頁面底部，按一下 [**新增的遮罩**開啟遮罩規則設定] 視窗。

6. 從下拉式清單，以定義會遮罩在指定的欄位中，選取**結構描述**、**表格**和**資料行**。

7. 從 masking 類別的機密資料的清單中選擇**MASKING 函數**。

    ![功能窗格](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. 資料 masking 更新的 masking masking 原則的動態資料中的規則集的 [規則] 視窗中，按一下**[確定]** 。

9. 按一下 [儲存新的或更新的遮罩原則的 [**儲存**]。


## <a name="set-up-dynamic-data-masking-for-your-database-using-transact-sql-statements"></a>設定資料庫使用 SQL 陳述式的遮罩的動態資料

請參閱[Masking 的動態資料](https://msdn.microsoft.com/library/mt130841.aspx)。

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>設定資料庫使用 Powershell cmdlet 的遮罩的動態資料

請參閱[Azure SQL 資料庫 Cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)。

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>設定使用 REST API 資料庫的動態資料遮罩

請參閱[Azure SQL 資料庫的作業](https://msdn.microsoft.com/library/dn505719.aspx)。
