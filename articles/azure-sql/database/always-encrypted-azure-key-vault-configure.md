---
title: Azure Key Vault を使用した Always Encrypted の構成
description: このチュートリアルでは、SQL Server Management Studio の Always Encrypted ウィザードを使用して、Azure SQL Database 内のデータベースにある機密データをデータの暗号化により保護する方法について説明します。
keywords: データの暗号化, 暗号化キー, クラウドの暗号化
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 11/02/2020
ms.openlocfilehash: 257abf03994c7006b1c3789174f550515dcd309a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841463"
---
# <a name="configure-always-encrypted-by-using-azure-key-vault"></a>Azure Key Vault を使用した Always Encrypted の構成 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

この記事では、[SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms) の [Always Encrypted ウィザード](/sql/relational-databases/security/encryption/always-encrypted-wizard)を使用して、Azure SQL Database 内のデータベースにある機密データをデータの暗号化により保護する方法について説明します。 さらに、Azure Key Vault に各暗号化キーを格納する方法を示す手順についても説明します。

Always Encrypted はデータ暗号化テクノロジです。サーバーでの保存時、クライアントとサーバー間の移動中、およびデータの使用中も機密データを保護することができます。 Always Encrypted により、データベース システム内で機密データがプレーンテキストとして表示されることはありません。 データ暗号化の構成後に、プレーンテキスト データにアクセスできるのは、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーだけです。 詳細については、 [Always Encrypted (データベース エンジン) に関するページ](/sql/relational-databases/security/encryption/always-encrypted-database-engine)を参照してください。

Always Encrypted を使用するようデータベースを構成したら、Visual Studio を使って、暗号化されたデータを扱う C# クライアント アプリケーションを作成します。

この記事の手順に従って、Azure SQL Database または SQL Managed Instance 内のデータベースに Always Encrypted を設定する方法を学習しましょう。 この記事では、次のタスクを実行する方法を説明します。

- SSMS の Always Encrypted ウィザードを使用して [Always Encrypted キー](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3)を作成する。
  - [列マスター キー (CMK)](/sql/t-sql/statements/create-column-master-key-transact-sql)を作成する。
  - [列暗号化キー (CEK)](/sql/t-sql/statements/create-column-encryption-key-transact-sql)を作成する。
- データベース テーブルを作成して列を暗号化する。
- 暗号化された列のデータを挿入、選択、表示するアプリケーションを作成する。

## <a name="prerequisites"></a>前提条件


- Azure アカウントとサブスクリプション。 お持ちでない場合は、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
- [Azure SQL Database](single-database-create-quickstart.md) または [Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md) 内のデータベース。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) バージョン 13.0.700.242 以降。
- [.NET framework 4.6](/dotnet/framework/) 以降 (クライアント コンピューター上)。
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/) または [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-client-application-access"></a>クライアント アプリケーションへのアクセスを有効にする

Azure Active Directory (Azure AD) アプリケーションを設定し、アプリケーションを認証するために必要な "*アプリケーション ID*" と "*キー*" をコピーして、クライアント アプリケーションから SQL Database 内のデータベースにアクセスできるようにする必要があります。

"*アプリケーション ID*" と "*キー*" を取得するには、[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページの手順に従ってください。

## <a name="create-a-key-vault-to-store-your-keys"></a>キーを格納する Key Vault を作成する

これで、クライアント アプリの構成が完了したので、アプリケーション ID の Key Vault を作成し、ユーザーおよびアプリケーションが資格情報コンテナーの機密情報 (Always Encrypted キー) にアクセスすることを許可するアクセス ポリシーを構成できます。 新しい列のマスター キーを作成したり、SQL Server Management Studio で暗号化を設定したりするには、*create* *get* *list* *sign* *verify* *wrapKey*、および *unwrapKey* 権限が必要です。

次のスクリプトを実行して、Key Vault をすばやく作成できます。 これらのコマンドの詳細、およびキー コンテナーの作成と構成の詳細については、「[Azure Key Vault とは](../../key-vault/general/overview.md)」をご覧ください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your database in Azure SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --spn $applicationId
```

---

## <a name="connect-with-ssms"></a>SSMS との接続

SQL Server Management Studio (SSMS) を開き、データベースがあるサーバーまたはマネージドに接続します。

1. SSMS を開きます。 ( **[サーバーへの接続]** ウィンドウを開いていない場合は、 **[接続]**  >  **[データベース エンジン]** の順に移動して開きます)。

2. サーバー名またはインスタンス名と、資格情報を入力します。 

    ![接続文字列のコピー](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

**[新しいファイアウォール規則]** ウィンドウが表示された場合は、Azure にサインインして、SSMS で自動的に新しいファイアウォール規則を作成します。

## <a name="create-a-table"></a>テーブルを作成する

このセクションでは、患者データを保持するテーブルを作成します。 これは最初は通常のテーブルで、次のセクションで暗号化を構成します。

1. **[データベース]** を展開します。
2. データベースを右クリックして、 **[新しいクエリ]** をクリックします。
3. [新しいクエリ] ウィンドウに次の Transact-SQL (T-SQL) を貼り付けて、 **実行** します。

```sql
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
```

## <a name="encrypt-columns-configure-always-encrypted"></a>列を暗号化する (Always Encrypted を構成する)

SSMS に用意されているウィザードを使用すると、列マスター キー、列暗号化キー、および暗号化する列を設定するだけで簡単に Always Encrypted を構成できます。

1. **[データベース]**  > **空の** >  **[テーブル]** を使用して、SQL データベース内の機密データを保護する方法について説明します。
2. **Patients** テーブルを右クリックして **[列の暗号化]** を選択すると、Always Encrypted ウィザードが起動します。

    ![[列の暗号化] メニュー オプションが強調表示されているスクリーンショット。](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

Always Encrypted ウィザードには、 **[列の選択]** 、 **[マスター キー構成]** 、 **[検証]** 、および **[まとめ]** のセクションがあります。

### <a name="column-selection"></a>列の選択

**[説明]** ページの **[次へ]** をクリックして、 **[列の選択]** ページを開きます。 このページで、暗号化する列、 [暗号化の種類、使用する列暗号化キー (CEK)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) を選択します。

各患者の **SSN** と **BirthDate** 情報を暗号化します。 SSN 列では決定論的な暗号化を使用します。この場合、等値のルックアップ、結合、グループ化を実行できます。 BirthDate 列ではランダム化された暗号化を使用します。この場合、操作は実行できません。

**[暗号化の種類]** として、SSN 列には **[決定論的]** を、BirthDate 列には **[ランダム化]** を選択します。 **[次へ]** をクリックします。

![[列の暗号化]](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>マスター キー構成

**[マスター キーの構成]** ページでは、CMK を設定し、その CMK を格納するキー ストア プロバイダーを選択します。 現時点では、Windows 証明書ストア、Azure Key Vault、またはハードウェア セキュリティ モジュール (HSM) に格納できます。

このチュートリアルでは、Azure Key Vault にキーを格納する方法を説明します。

1. **[Azure Key Vault]** を選択します。
2. ドロップダウン リストから必要な Key Vault を選択します。
3. **[次へ]** をクリックします。

![マスター キー構成](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>検証

列の暗号化はすぐに実行することも、PowerShell スクリプトを保存して後から実行することもできます。 このチュートリアルでは、 **[今すぐ続行して完了]** を選択して **[次へ]** をクリックします。

### <a name="summary"></a>まとめ

設定がすべて正しいことを確認し、 **[完了]** をクリックすれば、Always Encrypted の設定は完了です。

![タスクが成功としてマークされている [結果] ページを示すスクリーンショット。](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>ウィザードのアクションの確認

ウィザードが完了すると、データベースに Always Encrypted が設定されています。 ウィザードでは、次の操作が実行されました。

- 列マスター キーを作成し、Azure Key Vault に格納しました。
- 列暗号化キーを作成し、Azure Key Vault に格納しました。
- 選択した列の暗号化の構成 Patients テーブルにはまだデータがありませんが、選択した列にデータが存在していれば、この段階で暗号化されています。

SSMS でキーが生成されていることを確認するには、 **[Clinic]**  >  **[セキュリティ]**  >  **[Always Encrypted キー]** の順に展開します。

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>暗号化されたデータを扱うクライアント アプリケーションを作成する

Always Encrypted を設定したので、暗号化された列に対して、*inserts* や *selects* を実行するアプリケーションを構築できます。  

> [!IMPORTANT]
> Always Encrypted 列を構成したサーバーにプレーンテキスト データを渡す場合は、 [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) オブジェクトを使用する必要があります。 SqlParameter オブジェクトを使用せずにリテラル値を渡すと、例外が発生します。

1. Visual Studio を開き、新しい C# **コンソール アプリケーション** (Visual Studio 2015 以前) または **コンソール アプリケーション (.NET Framework)** (Visual Studio 2017 以降) を作成します。 プロジェクトは必ず **.NET Framework 4.6** 以降に設定してください。
2. プロジェクトに **AlwaysEncryptedConsoleAKVApp** という名前を付けて、 **[OK]** をクリックします。
3. **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に進んで、次の NuGet のパッケージをインストールします。

パッケージ マネージャー コンソールで、次の 2 行のコードを実行します。

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>接続文字列を変更して Always Encrypted を有効にする

このセクションでは、データベース接続文字列で Always Encrypted を有効にする方法を説明します。

Always Encrypted を有効にするには、接続文字列に **Column Encryption Setting** キーワードを追加し、**Enabled** に設定します。

接続文字列で直接設定することも、 [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder)を使用して設定することもできます。 **SqlConnectionStringBuilder** を使用する方法については、次のセクションでサンプル アプリケーションを使って説明します。

### <a name="enable-always-encrypted-in-the-connection-string"></a>接続文字列で Always Encrypted を有効にする

接続文字列に次のキーワードを追加します。

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>SqlConnectionStringBuilder を使って Always Encrypted を有効にする

次のコードは、[SqlConnectionStringBuilder.ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) を [Enabled](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting) に設定して Always Encrypted を有効にする方法を示しています。

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Azure Key Vault プロバイダーを登録する
次のコードは、Azure Key Vault プロバイダーを ADO.NET ドライバーに登録する方法を示しています。

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted サンプル コンソール アプリケーション

このサンプルでは次の操作を行います。

- 接続文字列を変更して Always Encrypted を有効にする。
- アプリケーションのキー ストア プロバイダーとして、Azure Key Vault を登録します。  
- 暗号化された列にデータを挿入する。
- 暗号化された列をフィルター処理して、特定の値を持つレコードを選択する。

*Program.cs* のコンテンツを次のコードに置き換えます。 Main メソッドのすぐ前の行にある connectionString のグローバル変数の接続文字列を、Azure ポータルから取得した有効な接続文字列に置き換えます。 コードに対する変更はこれだけです。

アプリケーションを実行して、Always Encrypted の動作を見てみましょう。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
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

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
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

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
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


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>データが暗号化されていることを確認する

サーバー上の実際のデータが暗号化されていることを簡単に確認するには、SSMS で Patients データをクエリします ( **Column Encryption Setting** がまだ有効になっていない現在の接続を使用します)。

Clinic データベースで次のクエリを実行します。

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

暗号化された列にプレーンテキスト データが含まれていないことがわかります。

   ![暗号化された列にプレーンテキスト データが含まれていないことを示すスクリーンショット。](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

プレーンテキスト データにアクセスする SSMS を使用するには、まず、ユーザーが Azure Key Vault への適切なアクセス許可、*get*、*unwrapKey*、および *verify* を持っていることを確認する必要があります。 詳細については、「[列マスター キーを作成して保存する (Always Encrypted)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted)」を参照してください。

次に、接続中に *Column Encryption Setting=enabled* パラメーターを追加します。

1. SSMS の **オブジェクト エクスプローラー** でサーバーを右クリックし、 **[切断]** を選択します。
2. **[接続]**  >  **[データベース エンジン]** の順にクリックして **[サーバーへの接続]** ウィンドウを開き、 **[オプション]** をクリックします。
3. **[追加の接続パラメーター]** をクリックし、「**Column Encryption Setting=enabled**」と入力します。

    ![[追加の修正パラメーター] タブが表示されているスクリーンショット。](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. Clinic データベースで次のクエリを実行します。

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

   暗号化された列のプレーンテキスト データを確認できます。
   
   ![新しいコンソール アプリケーション](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>次のステップ

Always Encrypted を使用するようにデータベースを構成した後、次の操作を行うことができます。

- [キーのローテーションとクリーンアップを行う](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio)。
- [Always Encrypted で既に暗号化されているデータを移行する。](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted)

## <a name="related-information"></a>関連情報

- [Always Encrypted (クライアント開発)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
- [透過的なデータ暗号化](/sql/relational-databases/security/encryption/transparent-data-encryption)
- [SQL Server の暗号化](/sql/relational-databases/security/encryption/sql-server-encryption)
- [Always Encrypted ウィザード](/sql/relational-databases/security/encryption/always-encrypted-wizard)
- [Always Encrypted 関連のブログ](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)
