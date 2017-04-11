<properties 
   pageTitle="開發 U SQL 使用者定義的運算子，Azure 資料湖分析工作 |Azure" 
   description="瞭解如何開發使用和重複使用資料湖分析工作的使用者定義運算子。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# <a name="develop-u-sql-user-defined-operators-for-azure-data-lake-analytics-jobs"></a>開發 U SQL 使用者定義的運算子，Azure 資料湖分析工作

瞭解如何開發使用和重複使用資料湖分析工作的使用者定義運算子。 您會開發自訂的運算子，以轉換國家/地區名稱。

##<a name="prerequisites"></a>必要條件

- Visual Studio 2015、 Visual Studio 2013 更新 4 或 Visual Studio 2012 Visual c + + 安裝 
- Microsoft Azure SDK.net 2.5 版或上方。  安裝並使用 Web 平台安裝程式。
- 以資料湖分析帳戶。  請參閱[開始使用 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-get-started-portal.md)。
- 移至 [[快速入門 Azure 資料湖分析 U SQL Studio](data-lake-analytics-u-sql-get-started.md)教學課程。
- 連線到 Azure 帳戶，請參閱[Azure 資料湖分析 U SQL Studio 快速入門](data-lake-analytics-u-sql-get-started.md#connect-to-azure)。 
- 上傳的來源資料，請參閱[Azure 資料湖分析 U SQL Studio 快速入門](data-lake-analytics-u-sql-get-started.md#upload-source-data-files)。 

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>定義及使用使用者定義運算子 U SQL 中

**建立及提交 U SQL 工作** 

1. 從 [**檔案**] 功能表中，按一下 [**新增**]，然後按一下**專案**。
2. 選取 [ **U SQL 專案**類型]。

    ![新的 U SQL Visual Studio 專案](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. 按一下**[確定]**。 Visual studio 建立 Script.usql 檔案的解決方案。
4. 從**方案總管**] 中，展開 Script.usql，，然後按兩下**Script.usql.cs**。
5. 下列程式碼貼入檔案︰

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;
        
        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };
        
                public override IRow Process(IRow input, IUpdatableRow output)
                {
        
                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");
        
                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);
        
                    return output.AsReadOnly();
                }
            }
        }

5. 開啟 Script.usql，並貼上下列的 U SQL 指令碼︰

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);
        
        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    
        
        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);

6. 從**方案總管**] 中，請以滑鼠右鍵按一下**Script.usql**，，，然後按一下 [**建立指令碼**。
6. 從**方案總管**] 中，請以滑鼠右鍵按一下**Script.usql**，，，然後按一下 [**送出指令碼**。
7. 如果您未連線至您的 Azure 訂閱，您會提示您 Azure 帳戶認證。
7. 按一下 [**送出**]。 送出的結果和工作連結時，可在 [結果] 視窗中送出已完成。
8. 您必須按一下 [重新整理] 按鈕，以查看最新的工作狀態，並重新整理畫面。

**若要查看工作輸出**

1. 從**伺服器總管**] 中，展開**Azure**、 展開**資料湖分析**、 展開資料湖分析帳戶、 展開**儲存帳戶**、 預設儲存空間，以滑鼠右鍵按一下，然後按一下**檔案總管**。 
2. 展開範例，展開輸出，然後按兩下**Drivers.csv**。


##<a name="see-also"></a>另請參閱

- [使用 PowerShell 資料湖分析快速入門](data-lake-analytics-get-started-powershell.md)
- [使用 [Azure 入口網站的資料湖分析快速入門](data-lake-analytics-get-started-portal.md)
- [使用 Visual Studio 開發 U SQL 應用程式的資料湖工具](data-lake-analytics-data-lake-tools-get-started.md)
