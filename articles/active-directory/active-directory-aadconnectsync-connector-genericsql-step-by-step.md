<properties
   pageTitle="一般步驟來步驟 SQL 連接器 |Microsoft Azure"
   description="本文會說明您逐步使用一般的 SQL 連接器的簡單 HR 系統。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-step-by-step"></a>逐步的一般 SQL 連接器
本主題是的逐步指南。 它會建立一個簡單的範例 HR 資料庫，並使用它來匯入一些使用者以及其群組成員資格。

## <a name="prepare-the-sample-database"></a>準備範例資料庫
在伺服器上執行 SQL Server，執行[附錄 A](#appendix-a)中找到的 SQL 指令碼。這個指令碼的名稱 GSQLDEMO 建立範例資料庫。 建立資料庫的物件模型看起來像圖片︰  
![物件模型](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

同時建立您要用來連線至資料庫的使用者。 在此逐步解說，使用者會 FABRIKAM\SQLUser 及位在網域]。

## <a name="create-the-odbc-connection-file"></a>建立 ODBC 連線檔案
一般的 SQL 連接器使用 ODBC 連線到遠端伺服器。 首先，我們需要建立檔案以 ODBC 連線資訊。

1. 在伺服器上啟動 ODBC 管理公用程式︰  
![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. 選取**檔案 DSN**] 索引標籤。 按一下 [**新增...**。
![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. 現成的驅動程式適用於要因此加以選取，然後按一下**下一步 >**。  
![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. 為檔案名稱，例如**GenericSQL**。  
![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. 按一下 [**完成**]。  
![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. 設定連線的時間。 為資料來源的建議的描述並提供執行 SQL Server 的伺服器名稱。  
![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. 選取驗證方法 SQL 的方式。 在此案例中，我們會使用 Windows 驗證。  
![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. 提供的範例資料庫， **GSQLDEMO**名稱。  
![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. 保留所有項目預設此畫面。 按一下 [**完成**]。  
![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. 若要確認一切運作正常，請按一下 [**測試資料來源**]。  
![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. 請確定沒有問題。  
![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. 現在應該顯示在檔案 DSN ODBC 設定檔。  
![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

現在，我們會有的檔案，我們需要就可以開始建立連接器。

## <a name="create-the-generic-sql-connector"></a>建立一般 SQL 連接器

1. 在同步處理服務管理員 ui 上，選取 [**連接器**並**建立**。 選取 [**一般 SQL (Microsoft)** ，並為其描述性的名稱。  
![Connector1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. 找出您建立前一節中的 DSN 檔案，然後上傳至伺服器。 提供的認證以連線至資料庫。  
![Connector2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. 在此逐步解說，我們會幫助您輕鬆我們，並假設有兩種物件類型，**使用者**和**群組**。
![Connector3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. 若要尋找的屬性，我們希望連接器，以查看資料表本身偵測這些屬性。 由於**使用者**是以 SQL 保留的字，我們需要提供中括弧裡面。  
![Connector4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. 若要定義錨點屬性和 DN 屬性的時間。 針對**使用者**，我們使用兩個屬性的使用者名稱和員工的組合。 ]**群組**中，我們使用群組名稱 (不真實在真實，但其運作方式此逐步解說)。
![Connector5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. 並非所有屬性類型可以都偵測 SQL 資料庫中。 特別無法參照屬性類型。 群組物件類型，我們需要變更 OwnerID 和 MemberID 參照。  
![Connector6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. 我們選取上一個步驟中的參照屬性需要的物件類型的屬性這些值的參照。 在此例中，輸入使用者物件。  
![Connector7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. 在全域參數頁面上，選取 [delta 策略為 [**浮水印**]。 在 [日期/時間格式**yyyy MM dd ss**中也輸入。
![Connector8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. **設定磁碟分割區和階層**在頁面上，選取兩個物件類型。
![Connector9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. 在 [**選取物件類型**及**選取屬性**中，選取 [物件類型] 及 [所有屬性。 **設定定位點**] 頁面上，按一下 [**完成]**。

## <a name="create-run-profiles"></a>建立執行的設定檔

1. 在同步處理服務管理員的 UI，選取 [**連接器**]，並**設定執行的設定檔**。 按一下 [**新的設定檔**]。 我們開始**完整匯入**。  
![Runprofile1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. 選取類型**（僅限階段） 完整匯入**]。  
![Runprofile2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. 選取的資料分割**物件 = 使用者**。  
![Runprofile3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. 選取**表格]** ，然後輸入**[使用者]**。 捲動到 [多重值的物件類型] 區段，然後輸入的資料，如如下圖所示。 選取 [**完成**] 以儲存的步驟。
![Runprofile4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png)  
![Runprofile4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. 選取**新的步驟**。 這次選取**物件 = 群組**。 在最後一個頁面上，使用如如下圖所示的設定。 按一下 [**完成**]。  
![Runprofile5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png)  
![Runprofile5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. 可省略︰ 如果您想要您可以設定執行的其他設定檔。 在此逐步解說，使用完整匯入。
7. 按一下**[確定]**以完成變更執行的設定檔]。

## <a name="add-some-test-data-and-test-the-import"></a>新增一些測試資料，以及測試匯入
填寫範例資料庫中的某些測試資料。 當您準備好時，選取 [**執行**] 並**完整匯入**]。

以下是使用者的兩個電話號碼與某些成員的群組。  
![cs1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png)  
![cs2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)  

## <a name="appendix-a"></a>附錄 A
**若要建立範例資料庫的 SQL 指令碼**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
