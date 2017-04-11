<properties
    pageTitle="使用資料流分析建置 IoT 方案 |Microsoft Azure"
    description="快速入門亭案例的資料流分析 IoT 解決方案的教學課程"
    keywords="iot 解決方案的視窗函數"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="build-an-iot-solution-by-using-stream-analytics"></a>使用資料流分析建置 IoT 方案

## <a name="introduction"></a>簡介

在本教學課程中，您將學習如何使用 Azure 資料流分析，取得即時的深入見解從您的資料。 開發人員歷程記錄或衍生商務獲得深入見解的參考資料，可以輕鬆地結合資料，按一下 [資料流、 記錄和裝置所產生的事件，例如資料的流。 為完整的管理、 即時資料流計算服務裝載於 Microsoft Azure，Azure 資料流分析提供內建恢復與低延遲，以取得您設定及執行以分鐘為單位的延展性。

完成本教學課程之後，您將可以︰

-   自我熟悉 Azure 資料流分析入口網站。
-   設定和部署串流工作。
-   清楚實際問題使用和解決這些資料流分析查詢語言。
-   開發的客戶的解決方案串流放心使用串流分析。
-   使用監視與記錄體驗問題進行疑難排解。

## <a name="prerequisites"></a>必要條件

您必須完成本教學課程下列先決條件︰

-   最新版的[PowerShell 的 Azure](../powershell-install-configure.md)
-   Visual Studio 2015 或免費的[Visual Studio 社群](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   [Azure 訂閱](https://azure.microsoft.com/pricing/free-trial/)
-   在電腦上的系統管理員權限
-   從 Microsoft 下載中心[TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip)的下載
-   可省略︰ 原始程式碼中[GitHub](https://aka.ms/azure-stream-analytics-toll-source) TollApp 事件產生器

## <a name="scenario-introduction-hello-toll"></a>案例簡介:"Hello，免付費電話 ！ 」


免付費電話站台是一般的現象。 您會遇到這些許多 expressways、 橋，以及通道世界。 每個免付費電話站台有多個免付費電話 booths。 手動 booths，在您停止支付免付費電話語音應答。 在自動 booths，每個攤位上方感應器會掃描您車輛的擋風玻璃當您將免付費電話攤位附 RFID 卡片。 很容易以視覺化方式顯示這些免付費電話站台透過車輛經過事件資料流哪些有趣可以執行的作業。

![在免付費電話 booths 汽車的圖片](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>內送的資料

本教學課程中搭配資料的兩個資料流。 安裝在進入及免付費電話站台的結束感應器產生的第一個資料流。 第二個資料流是靜態查閱資料集含有車輛註冊資料。

### <a name="entry-data-stream"></a>項目資料流

項目資料流包含，他們會進入免付費電話站台的汽車的相關資訊。


| TollID | EntryTime               | LicensePlate | 狀態 | 進行   | 模型   | VehicleType | VehicleWeight | 免付費電話 | 標記       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 2014 09 10 12:01:00.000 | JNB 7001     | NY    | Honda  | CRV     | 1           | 0             | 7    |           |
| 1      | 2014 09 10 12:02:00.000 | YXZ 1001     | NY    | 豐田 | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 2014 09 10 12:02:00.000 | ABC 1004     | CT    | 二十   | 羅斯  | 1           | 0             | 5    | 456789123 |
| 2      | 2014 09 10 12:03:00.000 | XYZ 1003     | CT    | 豐田 | Corolla | 1           | 0             | 4    |           |
| 1      | 2014 09 10 12:03:00.000 | BNJ 1007     | NY    | Honda  | CRV     | 1           | 0             | 5    | 789123456 |
| 2      | 2014 09 10 12:05:00.000 | CDE 1007     | NJ    | 豐田 | 4 x 4     | 1           | 0             | 6    | 321987654 |


以下是資料行的簡短描述︰

| 資料行 | 描述 |
|--------------|--------------------------------------------------------------------|
| TollID       | 可唯一識別免付費電話攤位免付費電話攤位 ID            |
| EntryTime    | 日期及時間至以 utc 表示免付費電話攤位車輛的項目 |
| LicensePlate | 量的車輛授權調色盤數字                            |
| 狀態        | 在 [美國狀態                                           |
| 進行         | 製造商的汽車                                 |
| 模型        | 模型數字的汽車                                 |
| VehicleType  | 1 旅客車輛或商業車輛 2       |
| WeightType   | 在 [噸; 車輛粗細旅客車輛 0                   |
| 免付費電話         | 免付費電話中的值美元                                              |
| 標記          | 付款; 會自動執行的汽車 e 標記手動做的付款是空白 |


### <a name="exit-data-stream"></a>結束資料流

結束資料流包含離開免付費電話站台的汽車的相關資訊。


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CDE 1007         |

以下是資料行的簡短描述︰


| 資料行 | 描述 |
|--------------|-----------------------------------------------------------------|
| TollID       | 可唯一識別免付費電話攤位免付費電話攤位 ID         |
| ExitTime     | 日期及時間的結束量的車輛從以 utc 表示免付費電話攤位 |
| LicensePlate | 量的車輛授權調色盤數字                         |

### <a name="commercial-vehicle-registration-data"></a>商業車輛註冊資料

教學課程使用靜態商業車輛註冊資料庫的快照。


| LicensePlate | RegistrationId | 過期 |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| BAC 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

以下是資料行的簡短描述︰


| 資料行 | 描述 |
|--------------|-----------------------------------------------------------------|
| LicensePlate | 量的車輛授權調色盤數字                         |
| RegistrationId     | 車輛註冊識別碼 |
| 過期 | 車輛的登錄狀態︰ 如果車輛註冊是作用中的 0 1 如果註冊已過期                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Azure 資料流分析設定環境

若要完成此教學課程中，您需要 Microsoft Azure 訂閱。 Microsoft 提供免費試用版的 Microsoft Azure 服務。

如果您沒有 Azure 帳戶，您可以[要求免費試用版](http://azure.microsoft.com/pricing/free-trial/)。

> [AZURE.NOTE] 若要註冊免費試用版，您會需要可接收文字訊息和有效的信用卡在行動裝置。

請務必，好讓您可以進行最佳的使用 $200 空閒 Azure 信用卡，請遵循本文結尾 「 清理 Azure 帳戶 」 一節中的步驟。

## <a name="provision-azure-resources-required-for-the-tutorial"></a>佈建 Azure 教學課程所需的資源

本教學課程需要兩個事件集線器接收*項目*]，*結束*資料流。 Azure SQL 資料庫輸出資料流分析作業的結果。 Azure 儲存區的相關車輛登錄的參考資料。

若要建立所有需要的資源，您可以在 GitHub TollApp 資料夾中使用 Setup.ps1 指令碼。 以時間，我們建議您執行。 如果您想要進一步瞭解如何設定這些資源 Azure 入口網站中，請參閱 「 設定 Azure 入口網站中的教學課程的資源 」 附錄。

下載並儲存支援[TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip)資料夾和檔案。

**Microsoft Azure PowerShell**視窗_以系統管理員身分_開啟。 如果您還沒有 PowerShell 的 Azure，請遵循[安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)中的指示進行安裝。

Windows 會自動封鎖.ps1、.dll 和.exe 檔，因為您需要設定執行原則之前執行指令碼。 請確定 PowerShell 的 Azure 視窗_以系統管理員身分_執行。 執行**設定 ExecutionPolicy 不受限制**。 出現提示時，請輸入**Y**。

![「 設定-ExecutionPolicy 不受限制的 「 PowerShell 的 Azure 視窗中執行的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

執行**取得 ExecutionPolicy** ，請確定命令正常運作。

![「 取得-ExecutionPolicy 」 PowerShell 的 Azure 視窗中執行的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

移至具有的指令碼和產生器應用程式的目錄。

![「 Cd.\TollApp\TollApp 」 PowerShell 的 Azure 視窗中執行的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

輸入**。\\Setup.ps1**若要設定您的 Azure 帳戶，建立所有必要的資源，並設定開始產生的事件。 指令碼隨機存挑選一個區域，來建立您的資源。 若要明確地指定區域，您可以將傳遞**-位置**參數，如下列範例所示︰

**.\\Setup.ps1-「 美國中部 」 的位置**

![Azure 登入頁面的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

指令碼會針對 Microsoft Azure 中開啟 [**登入**] 頁面。 輸入您的帳戶認證。

> [AZURE.NOTE] 如果您的帳戶有權存取的多個訂閱，系統會要求您輸入您想要使用教學課程的訂閱名稱。

指令碼可能需要幾分鐘的時間執行。 完成後，輸出看起來應該像下列的螢幕擷取畫面。

![輸出 PowerShell 的 Azure 視窗中的指令碼的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

您也會看到類似以下的螢幕擷取畫面的另一個視窗。 這個應用程式會傳送至 Azure 事件集線器，才能執行教學課程的事件。 因此，請勿停止應用程式或關閉視窗，直到您完成教學課程。

![「 傳送事件中樞資料 」 的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

您應該能立即看到 Azure 入口網站中建立的所有資源。 移至<https://manage.windowsazure.com>，並使用您的帳戶認證登入。

### <a name="azure-event-hubs"></a>Azure 事件集線器

按一下 [**服務匯流排**Azure 入口網站的左側，查看指令碼前一節中所建立的事件集線器]。

![服務匯流排](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

在您的訂閱，您會看到所有可用的命名空間。 按一下 [第一句是*tolldata*項目 (在本例中的為 tolldata4637388511)。 按一下 [**事件集線器**] 索引標籤。

![Azure 入口網站中的事件集線器] 索引標籤](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

您會看到兩個事件集線器命名*項目*，*結束*這個命名空間中建立。

![Azure 入口網站中的 「 進入 」 與 「 結束 」 事件集線器的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Azure 存放容器

1. 按一下 [**儲存**Azure 入口網站的左側，查看 Azure 儲存體容器教學課程中所使用。

    ![儲存空間] 功能表項目](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. 按一下 [開始*tolldata*項目 (在本例中的為 tolldata4637388511)。 按一下 [**容器**] 索引標籤，即可建立的容器。

    ![Azure 入口網站中的 [容器] 索引標籤](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. 按一下 [若要查看上傳的 JSON 檔案具有車輛註冊資料**tolldata**容器。

    ![Registration.json 檔案容器中的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Azure SQL 資料庫

1. 按一下 [ **SQL 資料庫**Azure 入口網站的左側，查看教學課程中將會使用 SQL 資料庫。

    ![SQL 資料庫](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. 按一下 [ **tolldatadb**]。

    ![建立 SQL 資料庫的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. 複製沒有連接埠號碼的伺服器名稱 (*伺服器名稱*。 database.windows.net，例如)。

## <a name="connect-to-the-database-from-visual-studio"></a>從 Visual Studio 連線至資料庫

使用 Visual Studio 存取輸出資料庫中的查詢結果。

從 Visual Studio 連線到 SQL 資料庫 （目的地）︰

1. 開啟 Visual Studio 中，然後再按一下 [**工具** > **連接至資料庫**。

2. 如果要求，請按一下 [ **Microsoft SQL Server**作為資料來源。

    ![變更資料來源] 對話方塊](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. 在 [**伺服器名稱**] 欄位中，貼上您在前一節中複製從 Azure 入口網站的名稱 (也就是*伺服器名稱*。 database.windows.net)。

4. 按一下 [**使用 SQL Server 驗證**]。

5. 在 [**使用者名稱**] 欄位中輸入**tolladmin**和**123toll ！** 在 [**密碼**] 欄位中。

6. 按一下**選取或輸入資料庫名稱**，然後選取**TollDataDB**為資料庫。

    ![新增連線] 對話方塊](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. 按一下**[確定]**。

8. 開啟伺服器檔案總管]。

    ![伺服器總管](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. 請參閱 TollDataDB 資料庫中的四個資料表。

    ![TollDataDB 資料庫中的資料表](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>事件產生器︰ TollApp 範例專案

若要使用 TollApp 範例應用程式的傳送事件自動啟動的 PowerShell 指令碼。 您不需要執行任何額外的步驟。

不過，如果您有興趣實作詳細資料，您可以尋找 TollApp 應用程式的原始程式碼 GitHub[範例/TollApp](https://aka.ms/azure-stream-analytics-toll-source)。

![在 Visual Studio 中顯示的範例程式碼的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>建立資料流分析工作

1. Azure 入口網站中，開啟 [串流分析然後按一下 [**新增**]，請在 [建立新的分析作業] 頁面的左下角。

    ![[新增] 按鈕](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. 按一下 [**快速建立**。 選取同一個位置的指令碼會建立其他資源的地區。

3. [**地區監控儲存帳戶**設定，選取 [**建立新的儲存空間帳戶**，並為其唯一的名稱。 Azure 資料流分析會儲存您所有的未來工作的監控資訊使用此帳戶。

4. 按一下 [**建立資料流分析工作**底部的頁面]。

    ![建立資料流分析作業選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>定義輸入的來源

1. 按一下 [在入口網站中建立的分析作業]。

    ![分析作業，在入口網站的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. 按一下 [**輸入**] 索引標籤定義的來源資料。

    ![[輸入] 索引標籤](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. 按一下 [**新增輸入**。

    ![[新增輸入選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. 按一下 [第一頁上的 [**資料流**]。

    ![[資料流] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. 按一下精靈的第二頁上的 [**事件] 中心**]。

    ![[事件] 中心] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. **輸入的別名**，輸入**EntryStream** 。

7. 按一下**事件中心**] 下拉式清單，然後選取 [第一句是 「 TollData 」 (例如，TollData9518658221) 的項目。

8. 選取**項目**為事件中心名稱，並選取 [**所有**事件中心原則名稱。

    您的設定看起來像︰

    ![事件中心設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. 在下一個頁面上，選取**事件序列化格式**和**UTF8** **JSON**的**編碼**。

    ![序列化設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. 按一下**[確定]**底部的頁面以完成精靈]。

    ![Azure 入口網站中的螢幕擷取畫面的 EntryStream 輸入](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    現在，您已建立的項目資料流，您將依照相同的步驟來建立結束資料流。 在精靈的第三個頁面上，請務必輸入值為下列的螢幕擷取畫面。

    ![設定結束串流](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    您已定義兩個輸入資料流時︰

    ![定義輸入資料流 Azure 入口網站中](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    接下來，您將加入 blob 檔案包含汽車登錄資料的參考資料輸入。

11. 按一下 [**新增輸入**]，然後按一下 [**參考資料**。

    ![[新增輸入] 選項已選取的參考資料](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. 在下一個頁面上，選取的儲存空間帳戶**tolldata**開頭。 容器名稱應**tolldata**，並在**路徑模式**] 底下 blob 名稱都必須是**registration.json**。 此檔案名稱會區分大小寫，應為小寫。

    ![部落格儲存設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. 選取在下一個頁面上下, 圖所示的值，然後按一下 [完成精靈的**[確定]** 。

    ![選取範圍的 JSON 」 甚至序列化格式 」 及 UTF8 的 「 編碼 」](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

現在被定義所有的輸入。

![三個已定義之輸入的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>定義輸出

1. 按一下 [**輸出**] 索引標籤，然後按一下 [**新增輸出**。

    ![輸出] 索引標籤和 [新增輸出] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. 按一下 [ **SQL 資料庫**]。

3. 選取本文的 「 連接至資料庫從 Visual Studio 」 一節中所使用的伺服器名稱。 資料庫名稱是**TollDataDB**。

4. 在 [**使用者名稱**] 欄位中，輸入**tolladmin** **123toll ！** 在 [**密碼**] 欄位和**TollDataRefJoin** **資料表**欄位中。

    ![SQL 資料庫設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Azure 資料流分析查詢

[**查詢**] 索引標籤包含 SQL 查詢轉換內送的資料。

![新增至 [查詢] 索引標籤查詢](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

本教學課程中嘗試回答幾個商務問題的相關資料和結構的資料流分析查詢可用於 Azure 資料流分析中相關的答案的免付費電話。

在啟動您的第一個資料流分析工作之前，現在就讓我們探索幾個案例與查詢語法。

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Azure 資料流分析查詢語言簡介
-----------------------------------------------------

例如，假設您需要的車輛輸入免付費電話攤位的字數。 因為這是事件的連續資料流時，您必須定義 」 的期間。 」 讓我們來修改為 「 多少車輛請輸入免付費電話攤位每三個分鐘？ 」 的問題。 這通常稱為解決計數。

我們來看看 Azure 資料流分析查詢回答這個問題︰

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

如您所見，Azure 資料流分析使用查詢語言，就像 SQL 並加入幾個副檔名指定時間方面有關的查詢。

如需詳細資訊，請閱讀關於 MSDN 從查詢中使用的[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)及[視窗化](https://msdn.microsoft.com/library/azure/dn835019.aspx)建構。

## <a name="testing-azure-stream-analytics-queries"></a>測試查詢 Azure 資料流分析

現在，您所撰寫的第一個 Azure 資料流分析查詢，就可以使用下列路徑中您 TollApp 資料夾中的範例資料檔案來加以測試︰

**..\\TollApp\\TollApp\\資料**

這個資料夾包含下列檔案︰

- Entry.json
- Exit.json
- Registration.json

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>第 1 題︰ 數字的車輛輸入免付費電話攤位

1. 開啟 [Azure 入口網站，然後移至您建立的 Azure 資料流分析工作。 按一下 [**查詢**] 索引標籤，並貼上一節的查詢。

    ![在 [查詢] 索引標籤中貼上的查詢](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. 若要驗證此查詢範例資料，按一下 [**測試**] 按鈕。 在開啟的對話方塊中，移至 Entry.json，含有**EntryTime**事件資料流範例資料檔案。

    ![Entry.json 檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. 驗證的查詢輸出會如預期般︰

    ![檢定的結果](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>第 2 題︰ 報表的總時間通過免付費電話攤位每個汽車

通過付費的汽車所需的平均時間協助評估程序的客戶體驗的效率。

若要尋找的總時間，您需要加入 EntryTime 資料流與 ExitTime 資料流。 您會加入 TollId 和 LicencePlate 資料行的資料流。 **加入**運算子需要您指定暫時保留描述可接受的時間之間的差異聯結的事件。 若要指定事件應該彼此不超過 15 分鐘，您會使用**DATEDIFF**函數。 若要結束**DATEDIFF**函數和計算買車花在免付費電話站台的實際時間的項目時間時，您也會套用。 使用在**SELECT**陳述式，而不是**加入**的條件時，請注意使用**DATEDIFF**的差異。

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. 若要測試此查詢，更新您的工作中的 [**查詢**] 索引標籤的查詢︰

    ![在 [查詢] 索引標籤中的更新的查詢](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. 按一下 [**測試]** ，然後指定 EntryTime] 和 [ExitTime 範例輸入的檔案。

    ![選取的輸入檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. 選取測試查詢並檢視輸出的核取方塊︰

    ![測試的輸出](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>第 3 題︰ 報告與過期註冊的所有商業車輛

Azure 資料流分析可以使用資料的靜態快照加入暫時資料流。 若要示範這項功能，請使用下列範例問題。

如果商業車輛在免付費電話公司註冊，請在不停止檢查的可以傳遞透過免付費電話攤位。 您會使用商業車輛註冊查閱表格來找出所有已過期登錄的商業車輛。

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

若要測試查詢，使用 [參考資料，您需要定義的參考資料、 您已經完成輸入的來源。

1. 若要測試此查詢，貼到 [**查詢**] 索引標籤的 [查詢**測試**，然後按一下指定兩個輸入的來源︰

    ![選取的輸入檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. 檢視查詢的輸出︰

    ![查詢輸出的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>啟動資料流分析作業


現在就來完成設定和啟動工作。 儲存從問題 3 查詢，其中會產生輸出符合**TollDataRefJoin**輸出表格的結構描述。

移至 [**儀表板**，該工作，然後按一下 [**開始]**。

![在工作儀表板中的 [開始] 按鈕的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

在 [開啟] 對話方塊中變更**啟動輸出**時間為**自訂時間**。 變更目前的前一個小時小時。 這項變更可確保自您開始在本教學課程的開頭產生的事件處理的所有事件，從 [事件] 中心。 現在按一下核取記號，以開始工作。

![自訂時間的選取範圍](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

開始工作，可能需要幾分鐘。 您可以在最上層的頁面上看到狀態串流分析。

![工作狀態的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>在 Visual Studio 中的檢查結果

1. 開啟 Visual Studio 伺服器總管] 中，並以滑鼠右鍵按一下**TollDataRefJoin**表格。
2. 按一下 [**顯示資料表資料**來查看工作的成果。

    ![伺服器總管] 中的 「 顯示表格資料 」 的選取範圍](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>不按比例縮放 Azure 資料流分析出工作

Azure 資料流分析被為了 elastically 小數位數，讓它可以處理大量資料。 Azure 資料流分析查詢可以使用**分割 BY**子句會告知系統的此步驟會擴充。 **PartitionId**是一個特殊的資料行，系統會新增到符合輸入 （事件中樞） 的資料分割識別碼。

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. 停止目前的工作，更新 [**查詢**] 索引標籤中的查詢並開啟 [**縮放比例**] 索引標籤。

    **串流單位**定義的工作可接收運算能力。

2. 將滑桿移到 6。

    ![選取 6 串流單位的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. 移至 [**輸出**] 索引標籤，然後變更**TollDataTumblingCountPartitioned**SQL 資料表的名稱。

如果您啟動工作現在 Azure 資料流分析可以分散更多的計算資源的工時及獲得更佳的處理量。 請注意 TollApp 應用程式也會傳送由 TollId 分割的事件。

## <a name="monitor"></a>監視器

[**監視器**] 索引標籤包含執行工作的相關統計資料。

![執行工作的相關統計資料的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

您可以從 [**儀表板**] 索引標籤，來存取**作業的記錄**。

![「 操作記錄] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![您可以在這裡看見的工作狀態的作業記錄檔的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

若要查看其他特定事件的詳細資訊，請按一下該事件，，然後按一下 [**詳細資料**] 按鈕。

![選取事件的相關詳細資料的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>結論

本教學課程介紹 Azure 資料流分析服務。 它示範如何設定的輸入與輸出資料流分析工作。 使用此免付費電話資料案例，教學課程說明常見的空間中移動及如何解決簡單類似 SQL 查詢中 Azure 資料流分析的資料中所發生的問題。 教學課程所述處理暫時資料的 SQL 副檔名結構。 說明如何加入資料流、 豐富資料流靜態參考資料以及擴充查詢以獲得更高的處理量。

雖然在此教學課程提供建議的簡介，即表示無法以任何方式完成。 您可以找到更多的查詢模式[的一般資料流分析使用模式的查詢範例](stream-analytics-stream-analytics-query-patterns.md)使用 SAQL 語言。
請參閱若要進一步瞭解 Azure 資料流分析[線上文件](https://azure.microsoft.com/documentation/services/stream-analytics/)。

## <a name="clean-up-your-azure-account"></a>清理 Azure 帳戶

1. 停止 Azure 入口網站中的資料流分析作業。

    Setup.ps1 指令碼建立兩個事件集線器和 SQL 資料庫。 以下指示可協助您清理資源教學課程結尾處。

2. 在 PowerShell 視窗中，輸入**。\\Cleanup.ps1**開始刪除資源教學課程中所用的指令碼。

    > [AZURE.NOTE] 資源名稱來識別。 請確定您確認移除之前請仔細檢閱每個項目。

    ![清除程序的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)
