<properties
   pageTitle="如何使用 「 大 data' 資料來源 |Microsoft Azure"
   description="醒目提示模式使用 '大 data' 資料來源，包括 Azure Blob 儲存體、 Azure 資料湖，Hadoop HDFS Azure 資料目錄的使用方法文章。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>如何使用 Azure 資料目錄] 中顯示較大的資料來源

## <a name="introduction"></a>簡介
**Microsoft Azure 資料目錄**] 是探索的登錄的完全受管理的雲端服務，可作為系統和企業資料來源系統。 換句話說， **Azure 資料目錄**是所有協助人員相關探索、 瞭解以及使用資料來源，並協助組織取得更多的值的現有資料來源，包括顯示較大的資料。

**Azure 資料目錄**支援註冊 Azure 部落格的儲存空間 blob 和目錄，以及 HDFS Hadoop 檔案及目錄。 半結構化資料來源提供更多的彈性，但也表示使用者必須考慮如何才能從註冊**Azure 資料目錄**充分組織資料來源。

## <a name="directories-as-logical-data-sets"></a>為邏輯資料集的目錄

組織大型資料來源的常見模式是視為邏輯資料集的目錄。 最上層目錄用來定義資料集，而子資料夾定義磁碟分割區，並包含的檔案儲存本身的資料。

可能的這個模式範例︰

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

在此範例中，vehicle_maintenance_events 和 location_tracking_events 代表邏輯資料集。 每個資料夾中含有資料檔案可根據 year 和 month 組織到子資料夾。 每個資料夾可能包含數百或數以千計的檔案。

在此模式中，可能是因為註冊**Azure 資料目錄**個別檔案意義。 不過，註冊代表資料集，所能使用的資料使用者有意義的目錄。

## <a name="reference-data-files"></a>參考資料檔案

互補模式是儲存為個別檔案的參考資料集。 這些資料集可能會被視為顯示較大的資料的 「 小 」 端，通常類似於分析的資料模型中的維度。 參考資料檔包含所提供的內容儲存在其他位置顯示較大的資料存放區中的資料檔案的記錄。

可能的這個模式範例︰

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

當分析師或資料科學家使用較大的目錄結構中的資料時，這些參照檔案中的資料可以用於提供的實體參照的只使用名稱或識別碼大型資料集的詳細的資訊。

在此模式中，這會使應註冊**Azure 資料目錄**的個別的參考資料檔案。 每個檔案代表資料集，而是註解及個別發現每個。

## <a name="alternate-patterns"></a>替代的圖樣

上述圖樣兩個可能的方式顯示較大的資料存放區可能會組織，但每個實作可能會不同。 無論您的資料來源結構方式，使用**Azure 資料目錄**註冊顯示較大的資料來源時，著重於註冊的檔案和代表會給其他人在您的組織內的值的資料集的目錄。 註冊所有檔案和目錄可以待過濾郵件目錄，難以使用者找到他們的需要。

## <a name="summary"></a>摘要
註冊**Azure 資料目錄**的資料來源進行比較容易探索並瞭解。 登錄及註解的大型資料檔案和代表邏輯資料集的目錄，您可以協助使用者找出並使用所需的大型資料來源。
