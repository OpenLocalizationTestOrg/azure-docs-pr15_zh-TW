<properties 
    pageTitle="存取與電腦學習 Python 用戶端文件庫的資料集 |Microsoft Azure" 
    description="安裝並使用 Python 用戶端文件庫來存取並從本機 Python 環境安全地管理 Azure 電腦學習資料。" 
    services="machine-learning" 
    documentationCenter="python" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="huvalo;bradsev" />


#<a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>使用 Python 使用 Azure 電腦學習 Python 用戶端文件庫的存取資料集 

Microsoft Azure 電腦學習 Python 用戶端文件庫的預覽可以啟用您 Azure 電腦學習資料集的安全存取從本機 Python 環境，並可以建立及管理工作區中的資料集。

本主題說明如何︰

* 安裝的電腦學習 Python 用戶端文件庫 
* 存取及上傳資料集，包括如何取得授權，才能存取 Azure 電腦學習資料集從本機 Python 環境中的指示
*  從實驗存取中繼資料集
*  使用 Python 用戶端文件庫來列舉資料集，存取中繼資料、 讀取資料集的內容，建立新的資料集和更新現有的資料集

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>必要條件

在下列環境經過 Python 用戶端文件庫︰

 - Windows、 Mac 和 Linux
 - Python 2.7，3.3 和 3.4

它會相依於下列封裝時︰

 - 要求
 - python dateutil
 - 熊

我們建議使用 Python 通訊群組，例如[Anaconda](http://continuum.io/downloads#all)或[繁星](https://store.enthought.com/downloads/)，其中隨附 Python IPython 與三個封包上述安裝。 雖然 IPython 不是必要的是很好的環境，處理及視覺化資料互動的方式。


###<a name="installation"></a>如何安裝 Azure 電腦學習 Python 用戶端文件庫

Azure 電腦學習 Python 用戶端文件庫，則也必須安裝完成本主題中所述的工作。 使用[Python 套件索引](https://pypi.python.org/pypi/azureml)。 若要將其安裝在您 Python 環境中，請從您的本機 Python 環境中執行下列命令︰

    pip install azureml

或者，您可以從下載並安裝[github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)來源。

    python setup.py install

如果您有給您的電腦上安裝時，您可以使用 pip 直接從給存放庫安裝︰

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>若要存取資料集使用 Studio 程式碼片段

Python 用戶端文件庫可讓您以程式設計方式存取您現有的資料集從已執行的實驗。

從 Studio web 介面，您可以產生包含所有必要的資訊，若要下載並還原為您的位置上熊 DataFrame 物件的序列化資料集的程式碼片段。

### <a name="security"></a>存取資料的安全性

使用與 Python 用戶端文件庫包含您的工作區識別碼和授權的 Studio 所提供的程式碼片段 token。 這些提供您的工作區的完整功能，必須受到保護，例如密碼。

基於安全性理由，程式碼片段功能僅適用於其角色的工作區設定為 [**擁有者**的使用者。 在 [**設定**底下的 [**使用者**] 頁面上，您的角色會顯示在 Azure 電腦學習 Studio。

![安全性][security]

如果您的角色不會設定為 [**擁有者**，您可以 reinvited 以擁有者，或要求工作區的擁有者提供給您的程式碼片段任一要求。

若要取得授權權杖，您可以執行下列其中一項︰



- 從 [擁有者要求取得憑證。 擁有人可以存取他們的授權權杖 Studio 中的工作區的 [設定] 頁面。 選取 [**設定**] 的左窗格，然後按一下 [若要查看主要和次要權杖**授權權杖**。  雖然 [主要] 或 [副授權權杖，都可以使用的程式碼片段，建議擁有者只能共用次要授權權杖。

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

- 要求您升級成擁有者的角色。  若要這麼做，工作區中的目前擁有者必須先移除您從工作區，然後重新邀請您將其以擁有者。

開發人員取得的工作區識別碼與授權後權杖，才能夠存取工作區中使用的程式碼片段，不論他們的角色。

在 [**設定****授權權杖**頁面上管理授權權杖。 您可以重新產生，但此程序撤銷存取權的上一個 token。

### <a name="accessingDatasets"></a>從本機 Python 應用程式存取資料集

1. 在電腦學習 Studio 中，按一下 [在左側導覽列中的 [**資料集**]。

2. 選取您想要存取資料集。 從 [**我的資料集**] 清單，或從 [**範例**] 清單中，您可以選取任何資料集。

3. 從 [底部] 工具列上，按一下 [**產生的資料存取程式碼**]。 如果資料是 Python 用戶端文件庫與不相容的格式，此按鈕會停用。

    ![資料集][datasets]

4. 從出現，並將其複製到剪貼簿的視窗中選取的程式碼片段。

    ![存取碼][dataset-access-code]

5. 將程式碼貼到您的本機 Python 應用程式的筆記本。

    ![筆記本][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Access 從電腦學習實驗的中繼資料集

在電腦學習 Studio 中執行實驗之後，可能是要從模組的輸出節點存取的中繼資料集。 中繼資料集是已建立及模型工具執行時所使用的中繼步驟的資料。

資料格式化為相容於 Python 用戶端文件庫時，您可以存取中繼資料集。

支援下列格式 (這些常數位於`azureml.DataTypeIds`課程):

 - 純文字
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

您可以決定格式的游標暫留在模組輸出節點。 會顯示在工具提示中的節點名稱，以及。

一些模組，例如[分割][split]模組，輸出格式名為`Dataset`，它不支援 Python 用戶端文件庫。

![資料集的格式][dataset-format]

您需要使用轉換模組，例如[轉換成 CSV][convert-to-csv]，以取得輸出為支援的格式。

![GenericCSV 格式][csv-format]

下列步驟顯示的範例會建立實驗、 執行及存取中繼資料集。

1. 建立新的實驗。

2. 插入**限制級人口普查收入二進位分類資料集**模組。

3. 插入[分割][split]模組，並將其輸入至資料集的模組輸出連線。

4. 插入[轉換成 CSV] [convert-to-csv]模組和連線到其中一個[分割]其輸入[split]模組輸出。

5. 儲存實驗、 執行，並等待，完成執行。

6. 按一下 [[轉換成 CSV]輸出節點[convert-to-csv]模組。

7. 出現快顯功能表中，選取 [**產生資料存取碼**。

    ![操作功能表][experiment]

8. 選取 [程式碼片段，並將其複製到剪貼簿中，從出現的視窗。

    ![存取碼][intermediate-dataset-access-code]

9. 在您的筆記本中，貼上程式碼。

    ![筆記本][ipython-intermediate-dataset]

10. 您可以使用 matplotlib 的資料視覺化方式顯示。 這會顯示在 [年齡] 欄的直方圖︰

    ![直方圖][ipython-histogram]


##<a name="clientApis"></a>若要存取、 讀取、 建立及管理資料集使用電腦學習 Python 用戶端文件庫

### <a name="workspace"></a>工作區

工作區是進入點 Python 用戶端文件庫。 提供`Workspace`課程使用您的工作區識別碼與授權權杖建立一個執行個體︰

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>列舉資料集

列舉指定的工作區中的所有資料集︰

    for ds in ws.datasets:
        print(ds.name)

列舉只使用者建立資料集︰

    for ds in ws.user_datasets:
        print(ds.name)

列舉只的範例資料集︰

    for ds in ws.example_datasets:
        print(ds.name)

您可以依名稱 （這是區分大小寫） 來存取資料集︰

    ds = ws.datasets['my dataset name']

或者，您可以存取索引︰

    ds = ws.datasets[0]


### <a name="metadata"></a>中繼資料

資料集有中繼資料，除了內容。 （中繼資料集此規則的例外狀況而不需要任何中繼資料。）

部分中繼資料值建立時間指派使用者︰

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

其他人是由 Azure 毫升指派的值︰

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

請參閱`SourceDataset`如需可用的中繼資料的類別。


### <a name="read-contents"></a>讀取內容

自動電腦學習 Studio 所提供的程式碼片段下載，並還原序列化熊 DataFrame 物件的資料集。 這是與`to_dataframe`方法︰

    frame = ds.to_dataframe()

如果您想要下載的原始資料，並執行還原序列化自己，這是一個選項。 此時，這是格式，例如 「 ARFF 」，其無法還原序列化 Python 用戶端文件庫的唯一選項。

若要閱讀為文字的內容︰

    text_data = ds.read_as_text()

若要為二進位讀取內容︰

    binary_data = ds.read_as_binary()

您也可以開啟資料流內容︰

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>建立新的資料集

Python 用戶端文件庫，可讓您上傳從 Python 程式的資料集。 這些資料集就可在您的工作區中使用。

如果您有熊 DataFrame 中的資料，請使用下列程式碼︰

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

如果資料已經，您可以使用︰

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Python 用戶端文件庫就可以序列化熊 DataFrame 為下列格式 (這些常數位於`azureml.DataTypeIds`課程):

 - 純文字
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### <a name="update-an-existing-dataset"></a>更新現有的資料集

如果您嘗試要上傳新的資料集，以符合現有的資料集的名稱，您應該取得衝突錯誤。

若要更新現有的資料集，您必須先取得現有的資料集的參照︰

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

然後使用`update_from_dataframe`序列化和取代 Azure 的資料集內的內容︰

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

如果您想要序列化資料以不同的格式，指定選擇性的值`data_type_id`參數。

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

您可以選擇指定的值來設定新的描述`description`參數。

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

您可以選擇指定的值來設定新的名稱`name`參數。 從現在起，您會擷取資料集使用新名稱。 下列程式碼更新資料、 名稱和描述。

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

`data_type_id`，`name`和`description`的選擇性參數，而且預設為先前的值。 `dataframe`參數永遠是必要。

如果資料已經，使用`update_from_raw_data`而不是`update_from_dataframe`。 如果您只要傳入`raw_data`而不是`dataframe`，其運作方式類似的方式。



<!-- Images -->
[security]:./media/machine-learning-python-data-access/security.png
[dataset-format]:./media/machine-learning-python-data-access/dataset-format.png
[csv-format]:./media/machine-learning-python-data-access/csv-format.png
[datasets]:./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]:./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]:./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]:./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
