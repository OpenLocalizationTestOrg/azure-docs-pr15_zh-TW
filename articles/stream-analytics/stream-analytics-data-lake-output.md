<properties
    pageTitle="資料流分析資料湖存放輸出 |Microsoft Azure"
    description="驗證設定和 Azure 資料湖儲存區中的資料流分析工作的授權"
    keywords=""
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="stream-analytics-data-lake-store-output"></a>資料流分析資料 Lake 存放輸出

資料流分析工作支援有數種輸出方法，一個是[Azure 資料湖存放區](https://azure.microsoft.com/services/data-lake-store/)。 Azure 資料湖存放是顯示較大的資料分析負載企業超比例存放庫。 資料湖存放區可讓您儲存的任何大小、 類型及 ingestion 速度作業與探分析的資料。

## <a name="authorize-a-data-lake-store-account"></a>授權資料 Lake 存放區帳戶

1.  作為輸出 Azure 管理入口網站中選取資料湖存放區時，系統會提示您現有的資料湖存放區的使用方式的授權或要求存取資料湖市集預覽透過 Azure 傳統入口網站。

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  

2.  如果您已經有 access 資料湖存放區，請按一下 [立即授權]，然後簡短時間的頁面會顯示指出 「 重新導向 」 授權...」。 頁面會自動關閉，您將會看到可讓您設定的資料湖存放輸出的網頁。

如果您有無法註冊資料湖市集預覽，您可以依循 「 立即註冊] 連結，進行要求，或依照 [[快速入門的指示進行](../data-lake-store/data-lake-store-get-started-portal.md)。

## <a name="configure-the-data-lake-store-output-properties"></a>設定資料 Lake 存放輸出屬性

驗證資料湖存放帳戶之後，您可以設定屬性的資料湖存放輸出。 下表是屬性名稱及設定您的資料湖存放輸出其描述的清單。

<table>
<tbody>
<tr>
<td><B>屬性名稱</B></td>
<td><B>描述</B></td>
</tr>
<tr>
<td>輸出別名</td>
<td>這是在查詢中使用，將此資料湖存放查詢輸出記的名稱。</td>
</tr>
<tr>
<td>資料湖存放區帳戶</td>
<td>傳送您的輸出儲存體帳戶名稱。 您會看到的下拉式清單的資料湖存放帳戶的登入入口網站的使用者擁有的存取權。</td>
</tr>
<tr>
<td>[<I>選擇性</I>] 路徑加上字首圖樣</td>
<td>用來撰寫您的檔案中指定的資料湖儲存帳戶檔案路徑。 <BR>{日期}，{time}<BR>範例 1︰ 資料夾 1/記錄 / {日期} / {時間}<BR>範例 2︰ 資料夾 1/記錄 / {日期}</td>
</tr>
<tr>
<td>[<I>選擇性</I>] 的日期格式</td>
<td>如果前置詞路徑中使用日期權杖，您可以選取組織檔案的 [日期] 格式。 範例︰ YYYY/MM/DD</td>
</tr>
<tr>
<td>[<I>選擇性</I>] 時間格式</td>
<td>如果時間權杖使用前置詞路徑中，指定組織檔案的時間格式。 目前唯一支援的值是 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>輸出資料序列化格式。 支援 JSON、 CSV 和 Avro。</td>
</tr>
<tr>
<td>編碼</td>
<td>如果 CSV 或 JSON 設定格式，請編碼必須指定。 Utf-8 這次是只支援編碼格式。</td>
</tr>
<tr>
<td>分隔符號</td>
<td>只適用於 CSV 序列化。 資料流分析支援序列化 CSV 資料常見的分隔符號的數字。 支援的值為逗號、 分號、 空格、] 索引標籤及垂直線。</td>
</tr>
<tr>
<td>格式</td>
<td>只適用於 JSON 序列化。 線條分隔指定的輸出格式化由以新的一行分隔每個 JSON 物件。 陣列指定的輸出格式化為 JSON 物件的陣列。</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>更新資料 Lake 存放授權

目前，沒有限制位置的 「 驗證 」 token 需要手動重新整理的資料湖存放輸出的所有工作的每個 90 天。 您也必須重新驗證您的資料湖存放帳戶，如果您已變更您的密碼，因為建立或最後驗證您的工作。 此問題的症狀是沒有工作輸出並指出需要重新授權作業記錄中的錯誤。

若要解決此問題，請停止您執行的工作並移至您的資料 Lake 存放輸出。 按一下 [更新授權] 連結，然後簡短時間的頁面會顯示指出 「 授權重新導向...」。 這個頁面會自動關閉，並且如果成功，即表示 「 授權已經順利更新 」。 您然後需要底部的頁面上，按一下 [儲存]，來重新啟動您的工作，以避免資料遺失的最後一個停止時間可以進行。

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)
