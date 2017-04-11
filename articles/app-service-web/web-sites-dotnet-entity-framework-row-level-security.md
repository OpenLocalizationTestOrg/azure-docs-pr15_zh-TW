<properties
    pageTitle="教學課程︰ 使用多租用戶資料庫使用實體架構與列層級安全性的 Web 應用程式"
    description="瞭解如何開發多的租用戶 SQL 資料庫 backent，使用實體架構與列層級安全性的 ASP.NET MVC 5 web 應用程式。"
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
    services="app-service\web"
    documentationCenter=".net"
    manager="jeffreyg"
  authors="tmullaney"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="04/25/2016"
    ms.author="thmullan"/>

# <a name="tutorial-web-app-with-a-multi-tenant-database-using-entity-framework-and-row-level-security"></a>教學課程︰ 使用多租用戶資料庫使用實體架構與列層級安全性的 Web 應用程式

本教學課程中會顯示如何建立多租用戶 web app 的 「[共用的資料庫、 共用的結構描述](https://msdn.microsoft.com/library/aa479086.aspx)」 tenancy 模型，使用實體架構與[列層級安全性](https://msdn.microsoft.com/library/dn765131.aspx)。 此模型的單一資料庫包含資料的許多租用戶，並在每個資料表中的每個資料列關聯 」 租用戶識別碼 」 資料列層級安全性 (RLS)，Azure SQL 資料庫的新功能用來存取其他人的資料時，防止租用戶。 這需要只單一，小型變更應用程式。 集中在資料庫中的租用戶存取邏輯，RLS 簡化的應用程式碼，並降低意外的資料外洩之間的租用戶的風險。

現在就讓我們開始[建立 ASP.NET MVP 應用程式授權與 SQL 資料庫並部署至 Azure 應用程式服務](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)從簡單的連絡管理員應用程式。 向右應用程式現在，讓所有的使用者 （租用戶） 若要查看所有的連絡人︰

![請連絡系統管理員啟用 RLS 之前的應用程式](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

只需要幾個小的變更，我們會將支援多重 tenancy，，讓使用者能看到其所屬的連絡人。

## <a name="step-1-add-an-interceptor-class-in-the-application-to-set-the-sessioncontext"></a>步驟 1︰ 若要設定 SESSION_CONTEXT 應用程式中新增攔截器類別

有一個我們需要進行的應用程式變更。 所有的應用程式使用者連線至使用相同的連接字串 （亦即相同 SQL 登入） 的資料庫，因為目前有不知道它應該篩選哪些使用者 RLS 原則。 這個方法最常見的 web 應用程式，因為它可以讓有效的連接共用，但這表示我們需要找出目前的應用程式使用者在資料庫中的另一種方法。 解決方法是將應用程式之後，立即執行任何查詢之前，開啟一個連線，將關鍵值組設定[SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806)中目前的使用者識別碼。 SESSION_CONTEXT 工作階段範圍關鍵值存放區，而我們 RLS 原則會使用儲存在使用者識別碼來識別目前的使用者。

我們會將[攔截器](https://msdn.microsoft.com/data/dn469464.aspx)（尤其是[DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor)），在實體架構 (EF) 6，若要自動設定目前的使用者識別碼中 SESSION_CONTEXT EF 開啟連線時執行 T SQL 陳述式的新功能。

1.  Visual Studio 中開啟 ContactManager 專案。
2.  以滑鼠右鍵按一下方案總管] 中的 [模型] 資料夾，然後選擇 [新增] > 類別。
3.  命名新課程 「 SessionContextInterceptor.cs 」，然後按一下 [新增]。
4.  下列程式碼取代 SessionContextInterceptor.cs 的內容。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
            // Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        
        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

這是唯一的應用程式將必要的變更。 請繼續進行並建立並發佈] 應用程式。

## <a name="step-2-add-a-userid-column-to-the-database-schema"></a>步驟 2︰ 新增使用者識別碼欄資料庫結構描述

接下來，我們需要將使用者識別碼欄新增至每個資料列與使用者 （租用戶） 的 [連絡人] 資料表。 讓我們沒有我們 EF 資料模型中包含此欄位，我們將會改變直接在資料庫中的結構描述。

連線至資料庫直接使用 SQL Server Management Studio 或 Visual Studio 中，內，然後執行下列 T SQL:

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

如此會使用者識別碼欄新增至 [連絡人] 資料表。 我們使用 nvarchar(128) 資料類型以符合 UserIds 儲存於 AspNetUsers] 資料表中，我們建立一個預設限制式，將會自動設定移到新插入的資料列的使用者識別碼，目前儲存在 SESSION_CONTEXT 使用者識別碼。

現在表格看起來像這樣︰

![SSMS 連絡人] 資料表](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

建立新的連絡人之後，他們會自動被指派正確的使用者識別碼。 為了示範，然而，現在就讓我們指派這些現有的連絡人的幾個現有使用者。

如果您已在應用程式中已建立一些使用者 (例如，使用本機、 Google 或 Facebook 帳戶)，您會看到這些 AspNetUsers 資料表中。 在以下的螢幕擷取畫面，有只有一位使用者為止。

![SSMS AspNetUsers 表格](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

複製的識別碼user1@contoso.com,並貼到下 T SQL 陳述式。 執行此陳述式之連絡人的三個關聯此使用者識別碼。

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## <a name="step-3-create-a-row-level-security-policy-in-the-database"></a>步驟 3︰ 建立資料庫中的資料列層級安全性原則

最後一個步驟是建立安全性原則，會使用使用者識別碼中 SESSION_CONTEXT 來自動篩選查詢所傳回的結果。

時仍能連線到資料庫，請執行下列 T SQL:

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

將此程式碼會三個項目。 首先，它會建立新的結構描述集中和限制存取 RLS 物件的最佳做法。 接下來，它會建立一列的使用者識別碼符合 SESSION_CONTEXT 中的使用者識別碼時，會傳回 '1' 述詞函數。 最後，它會建立在 [連絡人] 資料表的篩選 」 和 「 區塊述詞為新增此函數的安全性原則。 篩選述詞會使查詢傳回目前的使用者，屬於資料列並封鎖述詞做為保護來防止應用程式時不小心插入一列錯誤的使用者。

現在執行應用程式，並以登入user1@contoso.com。 此使用者看到我們先前指派給此使用者識別碼連絡人︰

![請連絡系統管理員啟用 RLS 之前的應用程式](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

若要進一步驗證此，請嘗試登錄新使用者。 因為沒有任何已指派給他們，就會看到任何連絡人。 如果其建立新的連絡人，它會指派給他們，並只他們能夠看到此。

## <a name="next-steps"></a>後續步驟

這樣就可以 ！ 簡單的連絡管理員 web 應用程式已經轉換成多租用戶位置每位使用者都有它自己的連絡人清單的其中一個。 藉由使用列層級安全性，我們已避免提高複雜度強制應用程式碼中的租用戶存取邏輯。 此透明度聚焦於手邊，實數商務問題的應用程式並也會減少的不小心洩漏資料為應用程式的程式碼基底風險成長。

本教學課程中具有只大略的 RLS 性。 例如，可能有更複雜的或精細存取邏輯，並的可能儲存 SESSION_CONTEXT 不只是目前的使用者識別碼。 您也可將[整合 RLS 彈性的資料庫工具用戶端的文件庫與](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md)支援多重租用戶擊碎擴充資料層。

除了這些可能性，我們也正在進行 RLS 更好。 如果您有任何問題，想法或您想要查看的項目，請讓我們知道註解中。 感謝您的意見反應 ！
