---
title: 'Always Encrypted: Azure SQL Database - Windows 証明書ストア | Microsoft Docs'
description: この記事では、SQL Server Management Studio (SSMS) の Always Encrypted ウィザードを使って、SQL Database 内の機密データを保護する方法について説明します。 さらに、Windows 証明書ストアにキーを格納する方法も説明します。
keywords: データの暗号化, sql 暗号化, データベースの暗号化, 機密データ, Always Encrypted
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
manager: craigg
ms.date: 03/08/2019
ms.openlocfilehash: 5226ec05af95cf305008968cf945070532274ee5
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726942"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Always Encrypted: 機密データを保護し、Windows 証明書ストアに暗号化キーを格納する

この記事では、[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx) の [Always Encrypted ウィザード](https://msdn.microsoft.com/library/mt459280.aspx)を使用して、SQL Database 内の機密データを保護する方法について説明します。 さらに、Windows 証明書ストアにキーを格納する方法も説明します。

Always Encrypted は Azure SQL Database と SQL Server の新しいデータ暗号化テクノロジです。常に暗号化した状態でデータを扱うので、データの使用中だけでなく、クライアント/サーバー間の移動中も機密データを保護することができます。データベース システム内で機密データがプレーンテキストとして表示されることはありません。 データの暗号化後に、プレーンテキスト データにアクセスできるのは、キーへのアクセス権を持つクライアント アプリケーションとアプリケーション サーバーだけです。 詳細については、 [Always Encrypted (データベース エンジン) に関するページ](https://msdn.microsoft.com/library/mt163865.aspx)を参照してください。

Always Encrypted を使用するようデータベースを構成したら、Visual Studio を使って、暗号化されたデータを扱う C# クライアント アプリケーションを作成します。

この記事の手順に従って、Azure SQL Database に Always Encrypted を設定する方法を学習します。 この記事では、次のタスクを実行する方法を説明します。

* SSMS の Always Encrypted ウィザードを使用して [Always Encrypted キー](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)を作成する。
  * [列マスター キー (CMK)](https://msdn.microsoft.com/library/mt146393.aspx)を作成する。
  * [列暗号化キー (CEK)](https://msdn.microsoft.com/library/mt146372.aspx)を作成する。
* データベース テーブルを作成して列を暗号化する。
* 暗号化された列のデータを挿入、選択、表示するアプリケーションを作成する。

## <a name="prerequisites"></a>前提条件

このチュートリアルには次のものが必要です。

* Azure アカウントとサブスクリプション。 お持ちでない場合は、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) バージョン 13.0.700.242 以降。
* [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) 以降 (クライアント コンピューター上)。
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)。

## <a name="create-a-blank-sql-database"></a>空の SQL データベースを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** > **[データ + ストレージ]** > **[SQL データベース]** をクリックします。
3. 新規または既存のサーバーに **Clinic** という名前の**空の**データベースを作成します。 Azure Portal でデータベースを作成する詳しい手順については、「[初めての Azure SQL Database](sql-database-single-database-get-started.md)」を参照してください。

    ![空のデータベースの作成](./media/sql-database-always-encrypted/create-database.png)

このチュートリアルでは、後で接続文字列が必要になります。 データベースの作成後、新しい Clinic データベースに移動し、接続文字列をコピーします。 いつでも接続文字列を取得できますが、Azure ポータルで、簡単にそれをコピーできます。

1. **[SQL Database]** > **[Clinic]** > **[データベース接続文字列の表示]** の順にクリックします。
2. **ADO.NET**の接続文字列をコピーします。

    ![接続文字列のコピー](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>SSMS を使用してデータベースに接続する

SSMS を開き、Clinic データベースを作成したサーバーに接続します。

1. SSMS を開きます。 (**[サーバーへの接続]** ウィンドウを開いていない場合は、**[接続]** > **[データベース エンジン]** の順にクリックして開きます)。
2. サーバー名と資格情報を入力します。 サーバー名がわからない場合は、[SQL Database] ブレードか、先ほどコピーした接続文字列で確認できます。 *database.windows.net*を含む完全なサーバー名を入力してください。

    ![接続文字列のコピー](./media/sql-database-always-encrypted/ssms-connect.png)

**[新しいファイアウォール規則]** ウィンドウが表示された場合は、Azure にサインインして、SSMS で自動的に新しいファイアウォール規則を作成します。

## <a name="create-a-table"></a>テーブルを作成する

このセクションでは、患者データを保持するテーブルを作成します。 これは最初は通常のテーブルで、次のセクションで暗号化を構成します。

1. **[データベース]** を展開します。
2. **Clinic** データベースを右クリックして、**[新しいクエリ]** をクリックします。
3. [新しいクエリ] ウィンドウに次の Transact-SQL (T-SQL) を貼り付けて、 **実行** します。

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

## <a name="encrypt-columns-configure-always-encrypted"></a>列を暗号化する (Always Encrypted を構成する)

SSMS に用意されているウィザードを使用すると、CMK、CEK、および暗号化する列を設定するだけで簡単に Always Encrypted を構成できます。

1. **[データベース]** > **空の** > **[テーブル]** を使用して、SQL データベース内の機密データを保護する方法について説明します。
2. **Patients** テーブルを右クリックして **[列の暗号化]** を選択すると、Always Encrypted ウィザードが起動します。

    ![[列の暗号化]](./media/sql-database-always-encrypted/encrypt-columns.png)

Always Encrypted ウィザードには、**[列の選択]**、**[マスター キー構成]** (CMK)、**[検証]**、および **[まとめ]** のセクションがあります。

### <a name="column-selection"></a>列の選択

**[説明]** ページの **[次へ]** をクリックして、**[列の選択]** ページを開きます。 このページで、暗号化する列、 [暗号化の種類、使用する列暗号化キー (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) を選択します。

各患者の **SSN** と **BirthDate** 情報を暗号化します。 **SSN** 列では決定論的な暗号化を使用します。この場合、等値のルックアップ、結合、グループ化を実行できます。 **BirthDate** 列ではランダム化された暗号化を使用します。この場合、操作は実行できません。

**[暗号化の種類]** として、**SSN** 列には **[決定論的]** を、**BirthDate** 列には **[ランダム化]** を選択します。 **[次へ]** をクリックします。

![[列の暗号化]](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>マスター キー構成

**[マスター キーの構成]** ページでは、CMK を設定し、その CMK を格納するキー ストア プロバイダーを選択します。 現時点では、Windows 証明書ストア、Azure Key Vault、またはハードウェア セキュリティ モジュール (HSM) に格納できます。 このチュートリアルでは、Windows 証明書ストアにキーを格納する方法を説明します。

**[Windows 証明書ストア]** が選択されていることを確認し、**[次へ]** をクリックします。

![マスター キー構成](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>検証

列の暗号化はすぐに実行することも、PowerShell スクリプトを保存して後から実行することもできます。 このチュートリアルでは、**[今すぐ続行して完了]** を選択して **[次へ]** をクリックします。

### <a name="summary"></a>まとめ

設定がすべて正しいことを確認し、 **[完了]** をクリックすれば、Always Encrypted の設定は完了です。

![まとめ](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>ウィザードのアクションの確認

ウィザードが完了すると、データベースに Always Encrypted が設定されています。 ウィザードでは、次の操作が実行されました。

* CMK が作成されました。
* CEK が作成されました。
* 選択した列の暗号化の構成 **Patients** テーブルにはまだデータがありませんが、選択した列にデータが存在していれば、この段階で暗号化されています。

SSMS でキーが生成されていることを確認するには、**[Clinic]** > **[セキュリティ]** > **[Always Encrypted キー]** の順に進みます。 ウィザードで生成された新しいキーを確認できます。

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>暗号化されたデータを扱うクライアント アプリケーションを作成する

Always Encrypted を設定したので、暗号化された列に対して、*inserts* や *selects* を実行するアプリケーションを構築できます。 サンプル アプリケーションを正常に実行するには、Always Encrypted ウィザードを実行したコンピューター上でアプリケーションを実行する必要があります。 別のコンピューター上でアプリケーションを実行する場合は、クライアント アプリケーションを実行するコンピューターに Always Encrypted 証明書をデプロイする必要があります。  

> [!IMPORTANT]
> Always Encrypted 列を構成したサーバーにプレーンテキスト データを渡す場合は、 [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) オブジェクトを使用する必要があります。 SqlParameter オブジェクトを使用せずにリテラル値を渡すと、例外が発生します。

1. Visual Studio を開き、新しい C# コンソール アプリケーションを作成します。 プロジェクトは必ず **.NET Framework 4.6** 以降に設定してください。
2. プロジェクトに **AlwaysEncryptedConsoleApp** という名前を付けて、**[OK]** をクリックします。

![新しいコンソール アプリケーション](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>接続文字列を変更して Always Encrypted を有効にする

このセクションでは、データベース接続文字列で Always Encrypted を有効にする方法を説明します。 次のセクション「Always Encrypted サンプル コンソール アプリケーション」で、作成したコンソール アプリケーションを変更します。

Always Encrypted を有効にするには、接続文字列に **Column Encryption Setting** キーワードを追加し、**Enabled** に設定します。

接続文字列に直接設定することも、 [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx)を使用して設定することもできます。 **SqlConnectionStringBuilder**を使用する方法については、次のセクションでサンプル アプリケーションを使って説明します。

> [!NOTE]
> Always Encrypted を有効にするためにクライアント アプリケーションで必要な変更はこれだけです。 既存のアプリケーションで接続文字列を外部の構成ファイルなどに格納している場合は、コードを変更しなくても Always Encrypted を有効にできる場合があります。

### <a name="enable-always-encrypted-in-the-connection-string"></a>接続文字列で Always Encrypted を有効にする

接続文字列に次のキーワードを追加します。

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>SqlConnectionStringBuilder を使って Always Encrypted を有効にする

次のコードは、[SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) を [Enabled](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx) に設定して Always Encrypted を有効にする方法を示しています。

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted サンプル コンソール アプリケーション

このサンプルでは次の操作を行います。

* 接続文字列を変更して Always Encrypted を有効にする。
* 暗号化された列にデータを挿入する。
* 暗号化された列をフィルター処理して、特定の値を持つレコードを選択する。

**Program.cs** の内容を次のコードに置き換えます。 Main メソッドのすぐ上の行にある connectionString のグローバル変数の接続文字列を、Azure ポータルから取得した有効な接続文字列に置き換えます。 コードに対する変更はこれだけです。

アプリケーションを実行して、Always Encrypted の動作を見てみましょう。

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

        static void Main(string[] args)
        {
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

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
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
```

## <a name="verify-that-the-data-is-encrypted"></a>データが暗号化されていることを確認する

サーバー上の実際のデータが暗号化されていることをすばやく確認するには、SSMS で **Patients** データをクエリします (Column Encryption Setting がまだ有効にされていない現在の接続を使用します)。

Clinic データベースで次のクエリを実行します。

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

暗号化された列にプレーンテキスト データが含まれていないことがわかります。

   ![新しいコンソール アプリケーション](./media/sql-database-always-encrypted/ssms-encrypted.png)

SSMS を使用してプレーンテキスト データにアクセスするには、接続に **Column Encryption Setting=enabled** パラメーターを追加します。

1. SSMS の**オブジェクト エクスプローラー**でサーバーを右クリックし、**[切断]** をクリックします。
2. **[接続]** > **[データベース エンジン]** の順にクリックして **[サーバーへの接続]** ウィンドウを開き、**[オプション]** をクリックします。
3. **[追加の接続パラメーター]** をクリックし、「**Column Encryption Setting=enabled**」と入力します。

    ![新しいコンソール アプリケーション](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. **Clinic** データベースで次のクエリを実行します。

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     暗号化された列のプレーンテキスト データを確認できます。

    ![新しいコンソール アプリケーション](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> 別のコンピューターの SSMS (または任意のクライアント) から接続した場合は暗号化キーにアクセスできず、データの暗号化を解除することはできません。

## <a name="next-steps"></a>次の手順

Always Encrypted を使用するデータベースを作成したら、次の操作を試してみてください。

* 別のコンピューターからこのサンプルを実行する。 別のコンピューターからは暗号化キーにアクセスできず、プレーンテキスト データにもアクセスできないので、サンプルは正常に実行されません。
* [キーのローテーションとクリーンアップを行う](https://msdn.microsoft.com/library/mt607048.aspx)。
* [Always Encrypted で既に暗号化されているデータを移行する。](https://msdn.microsoft.com/library/mt621539.aspx)
* [他のクライアント コンピューターにAlways Encrypted 証明書をデプロイする](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (「証明書をアプリケーションとユーザーが使用できるようにする」セクションを参照してください)。

## <a name="related-information"></a>関連情報

* [Always Encrypted (クライアント開発)](https://msdn.microsoft.com/library/mt147923.aspx)
* [透過的なデータ暗号化](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server の暗号化](https://msdn.microsoft.com/library/bb510663.aspx)
* [Always Encrypted ウィザード](https://msdn.microsoft.com/library/mt459280.aspx)
* [Always Encrypted に関するブログ](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)