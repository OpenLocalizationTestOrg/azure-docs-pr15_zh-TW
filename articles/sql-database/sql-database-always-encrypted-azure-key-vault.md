<properties
    pageTitle="永遠加密︰ 保護機密資料的 Azure SQL 資料庫資料庫加密 |Microsoft Azure"
    description="保護機密 SQL 資料庫中的資料以分鐘為單位。"
    keywords="資料加密加密金鑰雲端加密"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="sstein"/>

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-azure-key-vault"></a>永遠加密︰ 保護機密 SQL 資料庫中的資料和 Azure 金鑰保存庫中儲存您的加密金鑰

> [AZURE.SELECTOR]
- [Azure 鍵保存庫](sql-database-always-encrypted-azure-key-vault.md)
- [Windows 憑證存放區](sql-database-always-encrypted.md)


本文將示範如何使用[SQL Server 管理 Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx)中使用[加密精靈](https://msdn.microsoft.com/library/mt459280.aspx)資料加密保護機密 SQL 資料庫中的資料。 還包含會告訴您如何 Azure 金鑰保存庫中儲存每個加密金鑰的指示。

永遠加密是 Azure SQL 資料庫並協助保護機密資料其餘的伺服器上，在用戶端與伺服器之間，及使用資料時所執行動作的 SQL Server 中的新資料的加密技術。 永遠加密可確保機密資料，永遠不會顯示為純文字內的資料庫系統。 設定資料加密之後，只有用戶端應用程式或的按鍵存取應用程式伺服器可以存取純文字資料。 詳細資訊，請參閱[永遠加密 （資料庫引擎）](https://msdn.microsoft.com/library/mt163865.aspx)。


設定用於永遠加密的資料庫後，您將建立的用戶端應用程式 C# 中使用 Visual Studio 使用加密的資料。

遵循本文中的步驟，並瞭解如何設定 [Azure SQL 資料庫的 [永遠加密。 在本文中，您將學習如何執行下列工作︰

- 使用永遠加密精靈中 SSMS 建立[永遠加密金鑰](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)。
    - 建立[資料行主索引鍵 (CMK)](https://msdn.microsoft.com/library/mt146393.aspx)。
    - 建立[欄加密金鑰 (CEK)](https://msdn.microsoft.com/library/mt146372.aspx)。
- 建立資料庫資料表，並將加密的資料行。
- 建立應用程式的插入、 選取，並顯示加密的資料行的資料。


## <a name="prerequisites"></a>必要條件

在此教學課程中，您需要︰

- Azure 帳戶與訂閱。 如果您還沒有，註冊[免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242 版本或更新版本。
- [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx)或更新版本 （用戶端電腦上）。
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)。
- [PowerShell 的 azure](../powershell-install-configure.md)1.0 或更新版本。 輸入**(取得模組 azure ListAvailable)。版本**若要查看您正在執行哪個版本的 PowerShell。



## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>啟用您的用戶端應用程式，以存取 SQL 資料庫服務

您必須啟用您存取 SQL 資料庫服務所設定的必要驗證並取得*ClientId*和*密碼*，您會需要驗證您的應用程式在下列程式碼中的用戶端應用程式。

1. 開啟[Azure 傳統入口網站](http://manage.windowsazure.com)。
2. 選取 [**作用中的目錄**，然後按一下 [應用程式會使用 Active Directory 執行個體。
3. 按一下 [**應用程式**，然後再按一下 [**新增**。
4. 輸入您的應用程式的名稱 (例如︰ *myClientApp*)、 選取**WEB 應用程式**，然後按一下箭號以繼續。
5. **登開啟 URL**以及**應用程式識別碼 URI**您可以輸入有效的 URL (例如， *http://myClientApp*)，然後繼續。
6. 按一下 [**設定**]。
7. 複製您的**用戶端識別碼**。 （您會需要此值程式碼中稍後。）
8. 在 [**索引鍵**] 區段中，請從**選取期間**] 下拉式清單中選取**1 年**。 （您就可以複製鍵儲存步驟 14 之後）。
11. 向下捲動並按一下 [**新增應用程式**。
12. 將**顯示**設為**Microsoft 應用程式**，然後選取 [ **Microsoft Azure 服務管理**。 按一下 [核取記號，以繼續]。
13. 從 [**委派的權限**] 下拉式清單中選取**存取 Azure 服務管理**。
14. 按一下 [**儲存**]。
15. 儲存] 完成之後，請**鍵**] 區段中複製的值。 （您會需要此值程式碼中稍後。）



## <a name="create-a-key-vault-to-store-your-keys"></a>建立索引鍵的保存庫來儲存索引鍵

現在，設定您的用戶端應用程式，並可讓您用戶端識別碼，就建立索引鍵保存庫，並設定其存取原則，因此您和您的應用程式可存取保存庫的機密資料 （一律加密索引鍵）。 *建立*、*取得*、*清單*、*標誌*、*驗證*、 *wrapKey*和*unwrapKey*權限所需建立新的資料行主索引鍵，並設定與 SQL Server Management Studio 加密的項目。

您可以執行下列指令碼，快速建立索引鍵保存庫。 這些 cmdlet 和建立及設定金鑰保存庫的相關詳細資訊的詳細說明，請參閱[開始使用 Azure 金鑰保存庫](../Key-Vault/key-vault-get-started.md)。



    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).SubscriptionId
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup –Name $resourceGroupName –Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>建立空白的 SQL 資料庫
1. [Azure 入口網站](https://portal.azure.com/)登入。
2. 移至**新** > **資料 + 的儲存空間** > **SQL 資料庫**。
3. 建立新的或現有的伺服器上命名**診所****空白**資料庫]。 如需有關如何建立資料庫 Azure 入口網站中的詳細指示，請參閱[建立以分鐘為單位的 SQL 資料庫](sql-database-get-started.md)。

    ![建立空白的資料庫](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

您需要連線字串稍後的教學課程，所以您建立的資料庫之後，請瀏覽至新的診所資料庫，複製連線字串。 您可以在任何時候取得連線字串，但它可以輕鬆地將其複製 Azure 入口網站中。

1. 移至 [ **SQL 資料庫** > **診所** > **顯示資料庫連線字串**。
2. **ADO.NET**的複製連線字串。

    ![複製連線字串](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>連線至 SSMS 資料庫

開啟 SSMS，並連線到與診所資料庫伺服器。


1. 開啟 SSMS。 (移至**連線** > 以開啟**連接至伺服器**] 視窗，如果不是開啟的**資料庫引擎**。)
2. 輸入您的伺服器名稱及認證。 可以在 SQL 資料庫刀找到的伺服器名稱，然後連線字串中您先前複製。 輸入完整的伺服器名稱，包括*database.windows.net*。

    ![複製連線字串](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

如果**新的防火牆規則**] 視窗隨即開啟，請登入 Azure，並讓 SSMS 為您建立新的防火牆規則。


## <a name="create-a-table"></a>建立表格

在此區段中，您將建立表格，按住病患的資料。 它不先加密，您將會設定加密下一節。

1. 展開 [**資料庫**]。
1. 以滑鼠右鍵按一下**診所**資料庫，然後按一下 [**新增查詢**。
2. 將下列 SQL (T SQL) 貼到新的 [查詢] 視窗，然後**執行**它。


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>加密資料行 （設定一律加密）

SSMS 提供的精靈，幫助您輕鬆地設定來設定資料行主索引鍵資料行加密金鑰，與加密的資料行為您的 [永遠加密。

1. 展開 [**資料庫** > **診所** > **資料表**。
2. 以滑鼠右鍵按一下 [**病人**] 表格，然後選取 [**加密的資料行**] 以開啟 [永遠加密精靈︰

    ![加密資料行](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

[永遠加密的精靈] 包含下列各節︰**資料行選取範圍**、**設定主索引鍵**、**驗證**及**摘要**。

### <a name="column-selection"></a>資料行選取範圍##

若要開啟 [**資料行選取範圍**] 頁面的 [**簡介**] 頁面上，按一下 [**下一步**]。 您會在此頁面上，選取您想要加密的資料行[加密的類型和哪些欄加密金鑰 (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2)使用。

加密的每個病人**SSN**和**出生日期**資訊。 [SSN] 欄會使用確定加密，可支援 」 等相等性查閱、 聯結與群組依據]。 [出生日期] 欄會使用隨機的加密，不支援作業。

設定 SSN 欄的**加密類型****決定性**和**Randomized**出生日期欄。 按一下 [**下一步**]。

![加密資料行](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>設定主索引鍵###

[**主索引鍵設定**] 頁面可讓您設定您的 CMK，選取金鑰存放區中的提供者 CMK 儲存的位置。 目前，您可以儲存 CMK Windows 憑證存放區、 Azure 金鑰保存庫，或硬體安全性模組 (HSM)。

本教學課程中會顯示如何 Azure 金鑰保存庫中儲存索引鍵。

1.     選取 [ **Azure 金鑰保存庫**]。
1.     從下拉式清單中選取所要的按鍵保存庫。
1.     按一下 [**下一步**]。

![設定主索引鍵](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)


### <a name="validation"></a>驗證###

您可以立即加密欄或儲存 PowerShell 指令碼，稍後再執行。 在此教學課程中，選取**前進到現在完成**，然後按一下 [**下一步**]。

### <a name="summary"></a>摘要 ###

確認設定所有修正，然後按一下 [**完成**] 以完成安裝，一定加密。


![摘要](./media/sql-database-always-encrypted-azure-key-vault/summary.png)


### <a name="verify-the-wizards-actions"></a>確認精靈的動作

完成精靈後，您的資料庫已設定為一律加密。 精靈會執行下列動作︰

- 建立欄的主索引鍵和儲存至 Azure 金鑰保存庫。
- 建立欄加密金鑰並儲存至 Azure 金鑰保存庫。
- 設定為加密選取的資料行。 病患表格目前不含的資料，但現在都會經過加密選取的資料行中任何現有的資料。

您可以展開**診所**驗證 SSMS 中的索引鍵的建立 > **安全性** > **加密金鑰**。


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>建立用戶端應用程式，可以使用加密的資料

現在，永遠加密設定妥當，您可以建立執行*插入*並*選取 [*加密的資料行的應用程式。  

> [AZURE.IMPORTANT] 您的應用程式時傳送純文字資料永遠加密資料行的伺服器，必須使用[它初始化](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx)物件。 傳遞常值，而不使用它初始化物件會造成例外狀況。

1. 開啟 Visual Studio 並建立新 C# 主控台應用程式。 請確定您的專案設定為 [ **.NET Framework 4.6**或更新版本。
2. 專案**AlwaysEncryptedConsoleAKVApp**的名稱，然後按一下**[確定**]。
![新主控台應用程式](./media/sql-database-always-encrypted-azure-key-vault/console-app.png)
3. 移至 [**工具**安裝下列 NuGet 封裝 > **NuGet 封裝管理員** > **封裝管理員主控台**。

在 [封裝管理員主控台執行這兩行程式碼。

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>修改您的連接字串，若要啟用永遠加密

本節說明如何啟用您的資料庫連線字串中的 [永遠加密。


若要啟用永遠加密，必須先將**欄加密設定**關鍵字新增至您的連接字串，並將其設為**啟用**。

您可以設定此直接在連接字串，或者您可以設定該公用網站使用[SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx)。 在下一節中的範例應用程式會顯示如何使用**SqlConnectionStringBuilder**。



### <a name="enable-always-encrypted-in-the-connection-string"></a>啟用 [連線字串中的 [永遠加密

將下列關鍵字新增至 [連接字串。

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>啟用一律使用 SqlConnectionStringBuilder 加密

下列程式碼會示範如何啟用永遠加密設定[啟用](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx) [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) 。

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>註冊 Azure 金鑰保存庫提供者

下列程式碼會顯示如何註冊 Azure 金鑰保存庫提供者 ADO.NET 驅動程式。

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(clientId, clientSecret);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## <a name="always-encrypted-sample-console-application"></a>永遠加密範例主控台應用程式

此範例會示範如何︰

- 修改您的連接字串，若要啟用永遠加密。
- 註冊 Azure 金鑰保存庫做為應用程式的金鑰存放區提供者。  
- 插入加密的資料行中的資料。
- 選取的記錄，來加密資料行中的特定值的篩選。

下列程式碼取代**Program.cs**的內容。 取代行直接之前的主要方法，使用您從 Azure 入口網站的有效的連接字串中的全域的連接字串變數中的連接字串。 這是唯一的變更，您需要進行此程式碼。

執行動作中查看 [永遠加密應用程式。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## <a name="verify-that-the-data-is-encrypted"></a>確認已加密資料

快速，您可以檢查在伺服器上的實際資料已加密來查詢病患資料與 SSMS （使用您目前連線位置**加密設定欄**為尚未啟用）。

請執行下列查詢診所資料庫。

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

您可以看到加密的資料行不包含任何純文字的資料。

   ![新主控台應用程式](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)


若要使用 SSMS 存取純文字資料，您可以新增*欄加密設定 = 啟用*連線參數。

1. 在 SSMS，以滑鼠右鍵按一下您的伺服器**物件檔案總管**中，選擇 [**中斷連線**。
2. 按一下 [**連線**] > **資料庫引擎**開啟 [**連線至伺服器**] 視窗，然後按一下 [**選項]**。
3. 按一下 [**連線的其他參數**，然後輸入**欄加密設定 = 啟用**。

    ![新主控台應用程式](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. 請執行下列查詢診所資料庫。

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     您現在可以看到純文字中的資料加密的資料行。


    ![新主控台應用程式](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>後續步驟
建立使用永遠加密的資料庫後，您可能會想要執行下列動作︰

- [旋轉及清理您的金鑰](https://msdn.microsoft.com/library/mt607048.aspx)。
- [將資料的已加密的永遠加密](https://msdn.microsoft.com/library/mt621539.aspx)。


## <a name="related-information"></a>相關的資訊

- [永遠加密 （用戶端開發）](https://msdn.microsoft.com/library/mt147923.aspx)
- [透明資料加密](https://msdn.microsoft.com/library/bb934049.aspx)
- [SQL Server 加密](https://msdn.microsoft.com/library/bb510663.aspx)
- [永遠加密精靈](https://msdn.microsoft.com/library/mt459280.aspx)
- [永遠加密的部落格](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
