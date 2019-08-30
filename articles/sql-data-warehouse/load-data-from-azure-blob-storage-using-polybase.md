---
title: チュートリアル:Azure SQL Data Warehouse へのてニューヨークのタクシー データの読み込み | Microsoft Docs
description: このチュートリアルでは、Azure Portal と SQL Server Management Studio を使って、ニューヨークのタクシー データをパブリックな Azure BLOB から Azure SQL Data Warehouse に読み込みます。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 04/26/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e3bef20a92322b07219e42c4f7fe8443917eae32
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575199"
---
# <a name="tutorial-load-new-york-taxicab-data-to-azure-sql-data-warehouse"></a>チュートリアル:Azure SQL Data Warehouse へのてニューヨークのタクシー データの読み込み

このチュートリアルでは、PolyBase を使って、ニューヨークのタクシー データをパブリックな Azure BLOB から Azure SQL Data Warehouse に読み込みます。 このチュートリアルでは、[Azure Portal](https://portal.azure.com) と [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) を使って、次のことを行います。 

> [!div class="checklist"]
> * Azure Portal でデータ ウェアハウスを作成する
> * Azure Portal でサーバーレベルのファイアウォール規則を設定する
> * SSMS でデータ ウェアハウスに接続する
> * データを読み込むように指定されたユーザーを作成する
> * Azure Blob Storage のデータ用の外部テーブルを作成する
> * CTAS T-SQL ステートメントを使ってデータをデータ ウェアハウスに読み込む
> * データ読み込みの進行状況を表示する
> * 新しく読み込まれたデータの統計を作成する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを始める前に、最新バージョンの [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) をダウンロードしてインストールします。


## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-a-blank-sql-data-warehouse"></a>空の SQL データ ウェアハウスを作成する

Azure SQL データ ウェアハウスは、定義された一連の[コンピューティング リソース](memory-and-concurrency-limits.md)を使用して作成されます。 データベースは、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)内と [Azure SQL 論理サーバー](../sql-database/sql-database-features.md)内に作成されます。 

空の SQL データ ウェアハウスを作成するには、次の手順に従います。 

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

2. **[新規]** ページの **[データベース]** を選択し、 **[新規]** ページの **[おすすめ]** で **[SQL Data Warehouse]** を選択します。

    ![データ ウェアハウスを作成する](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. SQL Data Warehouse のフォームで、次の情報を入力します。   

   | Setting | 推奨値 | 説明 | 
   | ------- | --------------- | ----------- | 
   | **データベース名** | mySampleDataWarehouse | 有効なデータベース名については、「[Database Identifiers (データベース識別子)](/sql/relational-databases/databases/database-identifiers)」を参照してください。 | 
   | **サブスクリプション** | 該当するサブスクリプション  | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   | **[リソース グループ]** | myResourceGroup | 有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
   | **[ソースの選択]** | 空のデータベース | 空のデータベースの作成を指定します。 データ ウェアハウスはデータベースの 1 つの種類であることに注意してください。|

    ![データ ウェアハウスを作成する](media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. **[サーバー]** をクリックして、新しいデータベース用の新しいサーバーを作成して構成します。 **[新しいサーバー]** フォームには次の情報を入力してください。 

    | Setting | 推奨値 | 説明 | 
    | ------- | --------------- | ----------- |
    | **サーバー名** | グローバルに一意の名前 | 有効なサーバー名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 | 
    | **サーバー管理者ログイン** | 有効な名前 | 有効なログイン名については、「[Database Identifiers (データベース識別子)](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)」を参照してください。|
    | **パスワード** | 有効なパスワード | パスワードには 8 文字以上が使用され、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。 |
    | **Location** | 有効な場所 | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |

    ![データベース サーバーを作成する](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. **[選択]** をクリックします。

6. **[パフォーマンス レベル]** をクリックし、データ ウェアハウスが Gen1 または Gen2 のいずれであるかということと、Data Warehouse ユニットの数を指定します。 

7. このチュートリアルでは、SQL Data Warehouse の **[Gen2]** を選択します。 スライダーは、既定で **[DW1000c]** に設定されています。  上下に動かしてどうなるか試してみてください。 

    ![パフォーマンスを構成する](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. **[Apply]** をクリックします。
9. [SQL Data Warehouse] ページで、空のデータベースの **[照合順序]** を選びます。 このチュートリアルでは、既定の値を使います。 照合順序の詳細については、「[Collations (照合順序)](/sql/t-sql/statements/collations)」を参照してください。

11. これで SQL Database フォームの入力が完了したので、 **[作成]** をクリックして、データベースをプロビジョニングします。 プロビジョニングには数分かかります。 

    ![[作成] をクリックする](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

12. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。
    
     ![通知](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則を作成する

SQL Data Warehouse サービスでは、外部のアプリケーションやツールに、サーバーまたはサーバー上のすべてのデータベースへの接続を禁止するファイアウォールが、サーバーレベルで作成されます。 接続できるようにするには、特定の IP アドレスに接続を許可するファイアウォール規則を追加します。  次の手順に従って、クライアントの IP アドレスに対する[サーバーレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)を作成します。 

> [!NOTE]
> SQL Data Warehouse の通信は、ポート 1433 で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
>

1. デプロイが完了したら、左側のメニューから **[SQL データベース]** をクリックし、**SQL データベース** ページで、**mySampleDatabase** をクリックします。 このデータベースの概要ページが開くと、完全修飾サーバー名 (**mynewserver-20180430.database.windows.net** など) や追加の構成オプションが表示されます。 

2. この完全修飾サーバー名をコピーします。以降のクイック スタートでサーバーとそのデータベースに接続する際に必要となります。 その後、サーバー名をクリックしてサーバーの設定を開きます。

    ![サーバー名を検索する](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. サーバー名をクリックして、サーバーの設定を開きます。

    ![サーバーの設定](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. **[ファイアウォール設定の表示]** をクリックします。 SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。 

    ![サーバーのファイアウォール規則](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. ツール バーの **[クライアント IP の追加]** をクリックし、現在の IP アドレスをファイアウォール規則に追加します。 ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

5. **[Save]** をクリックします。 論理サーバーでポート 1433 を開いている現在の IP アドレスに対して、サーバーレベルのファイアウォール規則が作成されます。

6. **[OK]** をクリックし、 **[ファイアウォール設定]** ページを閉じます。

この IP アドレスを使って、SQL Server とそのデータ ウェアハウスに接続できるようになります。 接続するには、SQL Server Management Studio または他の適当なツールを使います。 接続するときは、前に作成した ServerAdmin アカウントを使います。  

> [!IMPORTANT]
> 既定では、すべての Azure サービスで、SQL Database ファイアウォール経由のアクセスが有効になります。 このページの **[オフ]** をクリックし、 **[保存]** をクリックして、すべての Azure サービスに対してファイアウォールを無効にします。

## <a name="get-the-fully-qualified-server-name"></a>完全修飾サーバー名を取得する

Azure Portal で、SQL サーバーの完全修飾サーバー名を取得します。 後でサーバーに接続するときに、完全修飾名を使います。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. 左側のメニューの **[SQL データ ウェアハウス]** を選択し、 **[SQL データ ウェアハウス]** ページで目的のデータベースをクリックします。 
3. そのデータベースの Azure Portal ページの **[基本]** ウィンドウで、**サーバー名**を見つけてコピーします。 この例の完全修飾名は mynewserver-20180430.database.windows.net です。 

    ![接続情報](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>サーバー管理者としてサーバーに接続する

このセクションでは、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) を使って、Azure SQL Server に対する接続を確立します。

1. SQL Server Management Studio を開きます。

2. **[サーバーへの接続]** ダイアログ ボックスで、次の情報を入力します。

    | Setting      | 推奨値 | 説明 | 
    | ------------ | --------------- | ----------- | 
    | サーバーの種類 | データベース エンジン | この値は必須です |
    | サーバー名 | 完全修飾サーバー名 | 名前は **mynewserver-20180430.database.windows.net** のような形式で指定する必要があります。 |
    | Authentication | SQL Server 認証 | このチュートリアルで構成した認証の種類は "SQL 認証" のみです。 |
    | ログイン | サーバー管理者アカウント | これは、サーバーの作成時に指定したアカウントです。 |
    | パスワード | サーバー管理者アカウントのパスワード | これは、サーバーの作成時に指定したパスワードです。 |

    ![[サーバーへの接続]](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. **[接続]** をクリックします。 SSMS でオブジェクト エクスプローラー ウィンドウが開きます。 

5. オブジェクト エクスプローラーで、 **[データベース]** を展開します。 **[システム データベース]** 、 **[master]** の順に展開し、マスター データベースのオブジェクトを表示します。  **mySampleDatabase** を展開して、新しいデータベースのオブジェクトを表示します。

    ![データベース オブジェクト](media/load-data-from-azure-blob-storage-using-polybase/connected.png) 

## <a name="create-a-user-for-loading-data"></a>データを読み込むためのユーザーを作成する

サーバー管理者アカウントは管理操作を実行するためのものであり、ユーザー データに対するクエリの実行には適していません。 データの読み込みは、メモリを大量に消費する操作です。 メモリの最大値は、プロビジョニングした SQL Data Warehouse の世代、[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)、[リソース クラス](resource-classes-for-workload-management.md)に従って定義されます。 

データの読み込みに専用のログインとユーザーを作成することをお勧めします。 その後、適切な最大メモリ割り当てを有効にする[リソース クラス](resource-classes-for-workload-management.md)に読み込みユーザーを追加します。

現在はサーバー管理者として接続しているので、ログインとユーザーを作成することができます。 以下の手順を使って、**LoaderRC20** という名前のログインとユーザーを作成します。 その後、そのユーザーを **staticrc20** リソース クラスに割り当てます。 

1.  SSMS で **[master]** を右クリックしてドロップダウン メニューを表示し、 **[新しいクエリ]** を選びます。 新しいクエリ ウィンドウが開きます。

    ![master の新しいクエリ](media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. クエリ ウィンドウで、次の T-SQL コマンドを入力して、LoaderRC20 という名前のログインとユーザーを作成します。"a123STRONGpassword!" は独自のパスワードに置き換えます。 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. **[実行]** をクリックします。

4. **mySampleDataWarehouse** を右クリックして、 **[新しいクエリ]** を選びます。 新しいクエリ ウィンドウが開きます。  

    ![サンプル データ ウェアハウスに対する新しいクエリ](media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)
 
5. 次の T-SQL コマンドを入力して、LoaderRC20 ログインに対する LoaderRC20 という名前のデータベース ユーザーを作成します。 2 行目では、新しいユーザーに新しいデータ ウェアハウスの CONTROL アクセス許可を付与しています。  これらのアクセス許可は、ユーザーをデータベースの所有者にする場合と似ています。 3 行目では、新しいユーザーを staticrc20 [リソース クラス](resource-classes-for-workload-management.md)のメンバーとして追加します。

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. **[実行]** をクリックします。

## <a name="connect-to-the-server-as-the-loading-user"></a>読み込みユーザーとしてサーバーに接続する

データを読み込むための最初のステップは、LoaderRC20 としてログインすることです。  

1. オブジェクト エクスプローラーで **[接続]** ドロップダウン メニューをクリックして、 **[データベース エンジン]** を選びます。 **[サーバーへの接続]** ダイアログ ボックスが表示されます。

    ![新しいログインで接続する](media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. 完全修飾サーバー名を入力し、ログインとして「**LoaderRC20**」と入力します。  LoaderRC20 のパスワードを入力します。

3. **[接続]** をクリックします。

4. 接続する準備ができると、オブジェクト エクスプローラーに 2 つのサーバー接続が表示されます。 1 つは ServerAdmin としての接続、もう 1 つは MedRCLogin としての接続です。

    ![接続成功](media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-external-tables-for-the-sample-data"></a>サンプル データ用の外部テーブルを作成する

新しいデータ ウェアハウスにデータを読み込むプロセスを始める準備ができました。 このチュートリアルでは、外部テーブルを使って Azure Storage Blob からニューヨーク市のタクシーのデータを読み込む方法を示します。 今後の参考として、データを Azure Blob Storage に取得する方法やソースから直接 SQL Data Warehouse に読み込む方法については、[読み込みの概要](sql-data-warehouse-overview-load.md)に関するページを参照してください。

次の SQL スクリプトを実行して、読み込むデータに関する情報を指定します。 この情報には、データが置かれている場所、データの内容の形式、およびデータのテーブル定義が含まれます。 

1. 前のセクションで、LoaderRC20 としてデータ ウェアハウスにログインしました。 SSMS で LoaderRC20 接続を右クリックして、 **[新しいクエリ]** を選びます。  新しいクエリ ウィンドウが表示されます。 

    ![新しい読み込みクエリ ウィンドウ](media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. 実際のクエリ ウィンドウと上の画像を比べてください。  新しいクエリ ウィンドウが LoaderRC20 として実行されていること、および MySampleDataWarehouse データベースに対するクエリを実行していることを確認します。 このクエリ ウィンドウを使って、すべての読み込み手順を実行します。

3. MySampleDataWarehouse データベースのマスター キーを作成します。 マスター キーは、データベースごとに 1 回作成するだけで済みます。 

    ```sql
    CREATE MASTER KEY;
    ```

4. 次の [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) ステートメントを実行して、Azure BLOB の場所を定義します。 これは、外部のタクシー データの場所です。  クエリ ウィンドウに追加したコマンドを実行するには、実行するコマンドを強調表示にして、 **[実行]** をクリックします。

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytaxiblob.blob.core.windows.net/'
    );
    ```

5. 次の [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) T-SQL ステートメントを実行して、外部データ ファイルの書式設定の特性とオプションを指定します。 このステートメントでは、外部データがテキストとして格納されており、値がパイプ ("|") 文字で区切られていることを指定します。 外部ファイルは Gzip で圧縮されています。 

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );
    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

6.  次の [CREATE SCHEMA](/sql/t-sql/statements/create-schema-transact-sql) コマンドを実行して、外部ファイルの形式のスキーマを作成します。 スキーマを使って、作成しようとしている外部テーブルを編成できます。

    ```sql
    CREATE SCHEMA ext;
    ```

7. 外部テーブルを作成する テーブルの定義は SQL Data Warehouse に格納されますが、テーブルは Azure Blob Storage に格納されているデータを参照します。 次の T-SQL コマンドを実行して、複数の外部テーブルを作成します。これらのテーブルは、そのすべてが以前に外部データ ソースで定義した Azure BLOB を指します。

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    ); 
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );      
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;  
    CREATE EXTERNAL TABLE [ext].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

8. オブジェクト エクスプローラーで、mySampleDataWarehouse を展開して、先ほど作成した外部テーブルの一覧を表示します。

    ![外部テーブルを表示する](media/load-data-from-azure-blob-storage-using-polybase/view-external-tables.png)

## <a name="load-the-data-into-your-data-warehouse"></a>データ ウェアハウスにデータを読み込む

このセクションでは、先ほど定義した外部テーブルを使って、サンプル データを Azure Storage Blob から SQL Data Warehouse に読み込みます。  

> [!NOTE]
> このチュートリアルでは、最終テーブルにデータを直接読み込みます。 運用環境では、通常、CREATE TABLE AS SELECT を使用して、ステージング テーブルに読み込みます。 データがステージング テーブルにある間に、必要な変換を実行できます。 ステージング テーブルのデータを運用テーブルに追加するには、INSERT...SELECT ステートメントを使用します。 詳細については、「[運用テーブルにデータを挿入する](guidance-for-loading-data.md#inserting-data-into-a-production-table)」を参照してください。
> 

このスクリプトは [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL ステートメントを使って、Azure Storage Blob からデータ ウェアハウスの新しいテーブルにデータを読み込みます。 CTAS は、select ステートメントの結果に基づいて新しいテーブルを作成します。 新しいテーブルでは、select ステートメントの結果と同じ列およびデータ型が保持されます。 select ステートメントが外部テーブルから選択すると、SQL Data Warehouse はデータ ウェアハウスのリレーショナル テーブルにデータをインポートします。 

1. データ ウェアハウス内の新しいテーブルにデータを読み込むには、次のスクリプトを実行します。

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. 読み込んだデータを表示します。 数 GB のデータを読み込み、高パフォーマンスのクラスター化列ストア インデックスに圧縮しています。 動的管理ビュー (DMV) を使用する次のクエリを実行して、読み込みの状態を表示します。 SQL Data Warehouse での処理にはある程度の時間がかかります。クエリを開始したら、完了するまでお待ちください。

    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024/1024.0 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [dbo].[Date]' OR
        r.[label] = 'CTAS : Load [dbo].[Geography]' OR
        r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
        r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
        r.[label] = 'CTAS : Load [dbo].[Time]' OR
        r.[label] = 'CTAS : Load [dbo].[Weather]' OR
        r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc, 
        gb_processed desc;
    ```

3. すべてのシステム クエリを表示します。

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. データ ウェアハウスにデータが適切に読み込まれました。

    ![読み込まれたテーブルを表示する](media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="authenticate-using-managed-identities-to-load-optional"></a>マネージド ID により認証して読み込む (省略可能)
PolyBase を使用して読み込み、マネージド ID を使用して認証するのが最も安全なメカニズムであり、Azure ストレージで VNet サービス エンドポイントを活用できる方法です。 

### <a name="prerequisites"></a>前提条件
1.  この[ガイド](https://docs.microsoft.com/powershell/azure/install-az-ps)を使用して、Azure PowerShell をインストールします。
2.  汎用 v1 または BLOB ストレージ アカウントを使用している場合は、この[ガイド](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)を使用して、最初に汎用 v2 にアップグレードする必要があります。
3.  Azure ストレージ アカウントの **[Firewalls and Virtual networks]\(ファイアウォールと仮想ネットワーク\)** 設定メニューで、 **[Allow trusted Microsoft services to access this storage account]\(信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します\)** をオンにする必要があります。 詳しくは、この[ガイド](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)をご覧ください。

#### <a name="steps"></a>手順
1. PowerShell で、Azure Active Directory (AAD) に **SQL Database サーバーを登録します**。

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
    
   1. この[ガイド](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)を使用して、**汎用 v2 ストレージ アカウント**を作成します。

   > [!NOTE]
   > - 汎用 v1 または BLOB ストレージ アカウントを使用している場合は、この[ガイド](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)を使用して、**最初に v2 にアップグレードする**必要があります。
    
1. お使いのストレージ アカウントで、 **[アクセス制御 (IAM)]** に移動し、 **[ロール割り当ての追加]** をクリックします。 **[ストレージ BLOB データ共同作成者]** RBAC ロールを SQL Database サーバーに割り当てます。

   > [!NOTE] 
   > 所有者特権を持つメンバーのみが、この手順を実行できます。 Azure リソースのさまざまな組み込みロールについては、この[ガイド](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)をご覧ください。
  
1. **Azure ストレージ アカウントへの Polybase 接続:**
    
   1. **IDENTITY = 'Managed Service Identity'** を使用して、データベース スコープ資格情報を作成します。

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - このメカニズムは内部的に[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) を使用するため、Azure Storage アクセス キーにシークレットを指定する必要はありません。
       > - Azure Storage アカウントで PolyBase 接続を使用するためには、IDENTITY の名前を **'Managed Service Identity'** にする必要があります。
    
   1. マネージド サービス ID でデータベース スコープ資格情報をして、外部データ ソースを作成します。
        
   1. [外部テーブル](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)を使用して、通常どおりクエリを実行します。

SQL Data Warehouse 用の仮想ネットワーク サービス エンドポイントを設定する場合は、次の[ドキュメント](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)を参照してください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

コンピューティング リソースとデータ ウェアハウスに読み込んだデータには課金されています。 これらは別々に請求されます。 

- データをストレージに保持しておく場合は、データ ウェアハウスを使わない間、コンピューティング リソースを一時停止できます。 コンピューティング リソースを一時停止すると課金はデータ ストレージだけになり、データを使う準備ができたらいつでもコンピューティング リソースを再開できます。
- それ以上課金されないようにする場合は、データ ウェアハウスを削除できます。 

必要に応じて、以下の手順でリソースをクリーンアップします。

1. [Azure Portal](https://portal.azure.com) にログインし、お使いのデータ ウェアハウスをクリックします。

    ![リソースのクリーンアップ](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. コンピューティング リソースを一時停止するには、 **[一時停止]** ボタンをクリックします。 データ ウェアハウスが一時停止すると、ボタンの表示が **[開始]** になります。  コンピューティング リソースを再開するには、 **[開始]** をクリックします。

3. コンピューティング リソースやストレージに課金されないようにデータ ウェアハウスを削除するには、 **[削除]** をクリックします。

4. 作成した SQL Server を削除するには、前の画像の **mynewserver-20180430.database.windows.net** をクリックして、 **[削除]** をクリックします。  サーバーを削除すると、サーバーに割り当てられているすべてのデータベースが削除されるので、注意してください。

5. リソース グループを削除するには、**myResourceGroup** をクリックして、 **[リソース グループの削除]** をクリックします。

## <a name="next-steps"></a>次の手順 
このチュートリアルでは、データ ウェアハウスを作成し、データを読み込むためのユーザーを作成する方法について学習しました。 外部テーブルを作成して Azure Storage Blob に格納されているデータの構造を定義した後、PolyBase の CREATE TABLE AS SELECT ステートメントを使って、データ ウェアハウスにデータを読み込みました。 

以下のことを行いました。
> [!div class="checklist"]
> * Azure Portal でデータ ウェアハウスを作成しました
> * Azure Portal でサーバーレベルのファイアウォール規則を設定する
> * SSMS でデータ ウェアハウスに接続しました
> * データを読み込むように指定されたユーザーを作成しました
> * Azure Storage Blob のデータ用の外部テーブルを作成しました
> * CTAS T-SQL ステートメントを使ってデータをデータ ウェアハウスに読み込みました
> * データ読み込みの進行状況を表示しました
> * 新しく読み込まれたデータの統計を作成しました

開発の概要に進んで、既存のデータベースを SQL Data Warehouse に移行する方法を学習してください。

> [!div class="nextstepaction"]
>[既存のデータベースを SQL Data Warehouse に移行するための設計上の決定](sql-data-warehouse-overview-migrate.md)
