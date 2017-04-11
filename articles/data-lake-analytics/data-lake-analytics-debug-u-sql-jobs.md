<properties 
   pageTitle="偵錯 U SQL 作業 |Microsoft Azure" 
   description="瞭解如何偵錯 U SQL 失敗的頂點，使用 Visual Studio。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>



#<a name="debug-c-code-in-u-sql-for-data-lake-analytics-jobs"></a>偵錯 C# 程式碼中 U SQL 資料湖分析工作 

瞭解如何使用 Azure 資料湖 Visual Studio 工具偵錯失敗的 U SQL 工作，因為使用者程式碼中的錯誤。 

Visual Studio 工具可讓您從叢集來追蹤及偵錯失敗的工作下載編譯程式碼和必要的頂點資料。

大型資料系統通常會提供擴充模型透過語言，例如 Java、 C#、 Python 等等。許多這些系統提供有限的執行階段偵錯時，很難偵錯執行階段錯誤自訂程式碼中的資訊。 最新的 Visual Studio 工具隨附稱為 「 無法頂點偵錯 」 的功能。 使用此功能，您可以下載 runtime 資料從 Azure 至本機工作站，好讓您可以偵錯失敗自訂 C# 程式碼雲端使用相同的執行階段和確切的輸入的資料。  修正問題之後，您可以重新執行修訂的程式碼 Azure 中從工具]。

此功能的視訊的簡報，請參閱[偵錯 Azure 資料湖分析您的自訂程式碼](https://mix.office.com/watch/1bt17ibztohcb)。

>[AZURE.NOTE] Visual Studio 可能會停止回應或損毀，如果您沒有安裝下列兩個視窗升級︰ [Microsoft Visual c + + 2015年可轉散發套件更新 2](https://www.microsoft.com/download/details.aspx?id=51682)、[通用 C 執行階段 for Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0)。


##<a name="prerequisites"></a>必要條件
-   經過的[開始](data-lake-analytics-data-lake-tools-get-started.md)文件。

## <a name="create-and-configure-debug-projects"></a>建立及設定偵錯專案

當您在資料湖 Visual Studio 工具中開啟失敗的工作時，您會收到通知。 在 [錯誤] 索引標籤和黃色提醒列上方的視窗中，將會顯示詳細的錯誤資訊。 

![Azure 資料湖分析 U SQL 偵錯 visual studio 下載頂點](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**若要下載頂點，並建立的偵錯解決方案**

1.  Visual Studio 中開啟失敗的 U SQL 工作。
2.  按一下 [**下載**] 下載所有需要的資源及輸入資料流。 如果下載失敗，請按一下 [**重試**]。
3.  若要建立本機偵錯專案完成下載後，請按一下 [**開啟**]。 將會建立新的 Visual Studio 方案一部分**VertexDebug**稱為**LocalVertexHost**空的專案。

如果使用者定義的運算子 (Script.usql.cs) 後的 U SQL 程式碼中使用，您必須使用使用者定義的運算子程式碼，建立課程文件庫 C# 專案，並 VertexDebug 方案中加入專案。

如果您已經註冊.dll 組件至您的資料湖分析資料庫，您必須新增組件的原始程式碼 VertexDebug 方案。
 
如果您建立另一個 C# 類別庫 U SQL 程式碼和註冊的.dll 組件至您的資料湖分析資料庫時，您需要將來源 C# 專案的組件新增至 VertexDebug 解決方案。

在某些少見的情況下，您可以使用 [使用者定義的運算子 U SQL 程式碼後置中的原始方案 (Script.usql.cs) 檔案。 如果您想要使其運作，您需要建立 C# 文件庫包含原始碼及叢集登錄項目變更的組件名稱。 您可以取得叢集註冊，請核取您使用的執行叢集的指令碼的組件名稱。 您可以執行開啟 U SQL 工作，然後按一下 [工作] 面板中的 「 指令碼]。 

**若要設定方案**

1.  從方案總管中，以滑鼠右鍵按一下您剛建立的 C# 專案，然後按一下**屬性**。
2.  設定輸出路徑為 LocalVertexHost 專案工作的路徑。 您可以取得 LocalVertexHost 專案工作目錄路徑透過 LocalVertexHost 屬性。
3.  若要將.pdb 檔案放入 LocalVertexHost 專案工作的目錄，建立 C# 專案或您可以手動將.pdb 檔案複製到這個資料夾。
4.  在 [**例外狀況設定**] 中，核取常見的語言執行階段例外狀況︰

![Azure 資料湖分析 U SQL 偵錯 visual studio 設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##<a name="debug-the-job"></a>偵錯工作

您已建立偵錯解決方案下載頂點，並設定環境之後，您就可以開始偵錯 U SQL 程式碼。

1.  從方案總管中，以滑鼠右鍵按一下您剛才建立的**LocalVertexHost**專案欲**偵錯**，指向，再按一下**啟動新的執行個體**。 LocalVertexHost 必須設定為啟動專案]。 您可能會看到下列訊息，您可以略過的第一次。 可能需要一分鐘，移至 [偵錯] 畫面。
 
    ![Azure 資料湖分析 U SQL 偵錯 visual studio 警告](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.  使用 Visual Studio 依據偵錯體驗 （監看式、 變數等） 來解決問題。 
5.  您找出問題之後，修正的程式碼，然後再測試它，直到所有問題都的解決之前重建 C# 專案。 在 [偵錯之後已經順利完成，顯示下列訊息輸出視窗 

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##<a name="resubmit-the-job"></a>重新送出工作

當您完成偵錯 U SQL 程式碼後，您可以重新送出失敗的工作。

1. 註冊 ADLA 資料庫的新.dll 組件。

    1.  從伺服器總管/雲端總管資料湖 Visual Studio 工具中，展開 [**資料庫**] 節點 
    2.  以滑鼠右鍵按一下 Register 組件的組件。 
    3.  註冊新.dll 組件 ADLA 資料庫。
 
2.  或 C# 程式碼複製到 script.usql.cs-C# 程式碼後置檔案。
3.  重新提交您的工作。

##<a name="next-steps"></a>後續步驟

- [教學課程︰ 開始使用 Azure 資料湖分析 U SQL 語言](data-lake-analytics-u-sql-get-started.md)
- [教學課程︰ 開發 U SQL 指令碼的 Visual Studio 中使用資料湖工具](data-lake-analytics-data-lake-tools-get-started.md)
- [開發 U SQL 使用者定義的運算子，Azure 資料湖分析工作](data-lake-analytics-u-sql-develop-user-defined-operators.md)

