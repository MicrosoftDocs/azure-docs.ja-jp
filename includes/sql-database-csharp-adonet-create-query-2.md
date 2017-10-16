
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>C# プログラムの例

この記事の以降のセクションでは、ADO.NET を使って SQL データベースに Transact-SQL ステートメントを送る C# プログラムを紹介します。 次の操作を C# プログラムで行います。

1. [ADO.NET を使用して SQL データベースに接続する](#cs_1_connect)。
2. [テーブルを作成する](#cs_2_createtables)。
3. [T-SQL INSERT ステートメントを実行してテーブルにデータを挿入する](#cs_3_insert)。
4. [結合を使ってデータを更新する](#cs_4_updatejoin)。
5. [結合を使ってデータを削除する](#cs_5_deletejoin)。
6. [結合を使ってデータ行を選択する](#cs_6_selectrows)。
7. 接続を閉じる (一時テーブルはすべて tempdb から削除されます)。

この C# プログラムには、次の内容が含まれています。

- データベースに接続するための C# コード。
- T-SQL ソース コードを返すメソッド。
- T-SQL をデータベースに送信する 2 つのメソッド。

#### <a name="to-compile-and-run"></a>コンパイルして実行するには

この C# プログラムは、論理的には 1 つの .cs ファイルです。 ただし物理的には、プログラムが複数のコード ブロックに分割されています。これによって各ブロックが見やすくなり、また理解しやすくなっています。 このプログラムをコンパイルして実行するには、次の手順に従います。

1. Visual Studio で C# プロジェクトを作成します。
    - プロジェクトの種類にする必要があります、*コンソール*アプリケーションで、次の階層のようなものから:**テンプレート** > **Visual c#** > **Windows クラシック デスクトップ** > **コンソール アプリケーション (.NET Framework)**です。
3. **Program.cs** ファイルから、ひな形となる短いコード行を削除します。
3. 以下に示した各ブロックをコピーして Program.cs に貼り付けます。ここに記載されている順序で貼り付けてください。
4. Program.cs の **Main** メソッドで次の値を編集します。

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. **System.Data.dll** アセンブリが参照されていることを確認します。 **[ソリューション エクスプローラー]** ウィンドウで **[参照]** ノードを展開して確認してください。
6. Visual Studio の **[ビルド]** メニューをクリックしてプログラムをビルドします。
7. Visual Studio の **[開始]** ボタンをクリックしてプログラムを実行します。 cmd.exe ウィンドウにレポート出力が表示されます。

> [!NOTE]
> T-SQL を編集してテーブル名の前に **#** を追加すると、それらのテーブルが **tempdb** の一時テーブルになります。 この方法は、デモンストレーション用途で、利用できるテスト データベースがないときに活用できます。 一時テーブルは、接続が閉じると自動的に削除されます。 一時テーブルでは、外部キーの REFERENCES 制約の強制力がありません。
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>C# ブロック 1: ADO.NET を使った接続

- [[次へ]](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>C# ブロック 2: テーブルを作成するための T-SQL

- [前へ](#cs_1_connect) &nbsp; / &nbsp; [次へ](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>エンティティ関係図 (ERD: Entity Relationship Diagram)

前の CREATE TABLE ステートメントでは、2 つのテーブルの間に "*外部キー*" (FK) リレーションシップを作成するために **REFERENCES** キーワードが使われています。  tempdb を使用している場合は、先頭に二重ダッシュを付けて `--REFERENCES` キーワードをコメントアウトしてください。

以下に示したのは、2 つのテーブルのリレーションシップを示す ERD です。 #tabEmployee.DepartmentCode 列 ("*子*") の値は、#tabDepartment.Department 列 ("*親*") に存在する値に限定されています。

![外部キーを示す ERD](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>C# ブロック 3: データを挿入するための T-SQL

- [前へ](#cs_2_createtables) &nbsp; / &nbsp; [次へ](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>C# ブロック 4: 結合を使ってデータを更新する T-SQL

- [前へ](#cs_3_insert) &nbsp; / &nbsp; [次へ](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>C# ブロック 5: 結合を使ってデータを削除する T-SQL

- [前へ](#cs_4_updatejoin) &nbsp; / &nbsp; [次へ](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>C# ブロック 6: 行を選択する T-SQL

- [前へ](#cs_5_deletejoin) &nbsp; / &nbsp; [次へ](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>C# ブロック 6b: ExecuteReader

- [前へ](#cs_6_selectrows) &nbsp; / &nbsp; [次へ](#cs_7_executenonquery)

このメソッドは、**Build_6_Tsql_SelectEmployees** メソッドによって構築された T-SQL SELECT ステートメントを実行することを意図したものです。


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>C# ブロック 7: ExecuteNonQuery

- [前へ](#cs_6b_datareader) &nbsp; / &nbsp; [次へ](#cs_8_output)

データ行を返さずにテーブル内のデータ内容を変更する操作に関しては、このメソッドが呼び出されます。


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>C# ブロック 8: コンソールへの実際のテスト出力

- [前へ](#cs_7_executenonquery)

次のセクションは、プログラムからコンソールに送られた出力を捕捉したものです。 テスト ランのたびに GUID 値だけが変化します。


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
