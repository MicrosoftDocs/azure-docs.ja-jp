---
title: チュートリアル:ニューヨークのタクシー データを読み込む
description: このチュートリアルでは、Azure portal と SQL Server Management Studio を使用して、Synapse SQL 用の Azure BLOB からニューヨークのタクシー データを読み込みます。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/31/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c4dbc63e8829d8a9ca3a3820fbb6675da4fad357
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261582"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>チュートリアル:ニューヨークのタクシー データセットを読み込む

このチュートリアルでは、[COPY ステートメント](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)を使用して、Azure BLOB ストレージ アカウントからニューヨークのタクシー データセットを読み込みます。 このチュートリアルでは、[Azure Portal](https://portal.azure.com) と [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) を使って、次のことを行います。

> [!div class="checklist"]
>
> * Azure portal で SQL プールを作成する
> * Azure Portal でサーバーレベルのファイアウォール規則を設定する
> * SSMS でデータ ウェアハウスに接続する
> * データを読み込むように指定されたユーザーを作成する
> * サンプル データセット用のテーブルを作成する 
> * COPY T-SQL ステートメントを使ってデータをデータ ウェアハウスに読み込む
> * データ読み込みの進行状況を表示する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを始める前に、最新バージョンの [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) をダウンロードしてインストールします。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-a-blank-database"></a>空のデータベースの作成

SQL プールは、定義された一連の[コンピューティング リソース](memory-concurrency-limits.md)を使用して作成されます。 データベースは、[Azure リソース グループ](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)内と[論理 SQL サーバー](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に作成されます。

次の手順に従って、空のデータベースを作成します。

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。

2. **[新規]** ページの **[データベース]** を選択し、 **[新規]** ページの **[おすすめ]** で **[Azure Synapse Analytics]** を選択します。

    ![データ ウェアハウスを作成する](./media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. フォームに次の情報を入力します。

   | 設定            | 推奨値       | 説明                                                  |
   | ------------------ | --------------------- | ------------------------------------------------------------ |
   | *[名前]* *            | mySampleDataWarehouse | 有効なデータベース名については、「[Database Identifiers (データベース識別子)](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)」を参照してください。 |
   | **サブスクリプション**   | 該当するサブスクリプション     | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   | **リソース グループ** | myResourceGroup       | 有効なリソース グループ名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関するページを参照してください。 |
   | **ソースの選択**  | 空のデータベース        | 空のデータベースの作成を指定します。 データ ウェアハウスはデータベースの一種です。 |

    ![データ ウェアハウスを作成する](./media/load-data-from-azure-blob-storage-using-polybase/create-data-warehouse.png)

4. **[サーバー]** を選択して、新しいデータベース用の新しいサーバーを作成して構成します。 **[新しいサーバー]** フォームには次の情報を入力してください。

    | 設定                | 推奨値          | 説明                                                  |
    | ---------------------- | ------------------------ | ------------------------------------------------------------ |
    | **サーバー名**        | グローバルに一意の名前 | 有効なサーバー名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関するページを参照してください。 |
    | **サーバー管理者ログイン** | 有効な名前           | 有効なログイン名については、「[Database Identifiers (データベース識別子)](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)」を参照してください。 |
    | **パスワード**           | 有効なパスワード       | パスワードには 8 文字以上が使用され、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。 |
    | **場所**           | 有効な場所       | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |

    ![サーバーを作成する](./media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. **[選択]** を選択します。

6. **[パフォーマンス レベル]** を選択し、データ ウェアハウスが Gen1 または Gen2 のいずれであるかということと、データ ウェアハウス ユニットの数を指定します。

7. このチュートリアルでは、SQL プールの **[Gen2]** を選択します。 スライダーは、既定で **[DW1000c]** に設定されています。  上下に動かしてどうなるか試してみてください。

    ![パフォーマンスを構成する](./media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. **[適用]** を選択します。
9. [プロビジョニング] ブレードで、空のデータベース用の **[照合順序]** を選びます。 このチュートリアルでは、既定の値を使います。 照合順序の詳細については、「[Collations (照合順序)](/sql/t-sql/statements/collations?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)」を参照してください。

10. これでフォームの入力が完了したので、 **[作成]** をクリックして、データベースをプロビジョニングします。 プロビジョニングには数分かかります。

11. デプロイ プロセスを監視するために、ツール バーの **[通知]** を選択します。
  
     ![通知 (notification)](./media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則を作成する

サーバーレベルのファイアウォールにより、外部のアプリケーションとツールは、サーバーやサーバー上のすべてのデータベースに接続できなくなります。 接続できるようにするには、特定の IP アドレスに接続を許可するファイアウォール規則を追加します。  次の手順に従って、クライアントの IP アドレスに対する[サーバーレベルのファイアウォール規則](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を作成します。

> [!NOTE]
> SQL Data Warehouse の通信は、ポート 1433 で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、サーバーに接続することはできません。

1. デプロイが完了したら、左側のメニューから **[SQL データベース]** を選択し、 **[SQL データベース]** ページで、 **[mySampleDatabase]** を選択します。 このデータベースの概要ページが開くと、完全修飾サーバー名 (**mynewserver-20180430.database.windows.net** など) や追加の構成オプションが表示されます。

2. この完全修飾サーバー名をコピーします。以降のクイック スタートでサーバーとそのデータベースに接続する際に必要となります。 次に、サーバー名を選択して、サーバー設定を開きます。

    ![サーバー名を検索する](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

3. サーバー名を選択して、サーバー設定を開きます。

    ![サーバー設定](./media/load-data-from-azure-blob-storage-using-polybase/server-settings.png)

4. **[ファイアウォール設定の表示]** を選択します。 サーバーの **[ファイアウォール設定]** ページが開きます。

    ![サーバーのファイアウォール規則](./media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png)

5. ツール バーの **[クライアント IP の追加]** を選択し、現在の IP アドレスを新しいファイアウォール規則に追加します。 ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

6. **[保存]** を選択します。 サーバー上でポート 1433 を開いている現在の IP アドレスに対して、サーバーレベルのファイアウォール規則が作成されます。

7. **[OK]** を選択し、 **[ファイアウォール設定]** ページを閉じます。

これで、この IP アドレスを使って、サーバーとそのデータ ウェアハウスに接続できるようになりました。 接続するには、SQL Server Management Studio または他の適当なツールを使います。 接続するときは、前に作成した ServerAdmin アカウントを使います。  

> [!IMPORTANT]
> 既定では、すべての Azure サービスで、SQL Database ファイアウォール経由のアクセスが有効になります。 このページの **[オフ]** を選択した後、 **[保存]** を選択して、すべての Azure サービスに対してファイアウォールを無効にします。

## <a name="get-the-fully-qualified-server-name"></a>完全修飾サーバー名を取得する

Azure portal 上で、サーバーの完全修飾サーバー名を取得します。 後でサーバーに接続するときに、完全修飾名を使います。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. 左側のメニューから **[Azure Synapse Analytics]** を選択し、 **[Azure Synapse Analytics]** ページで目的のデータベースを選択します。
3. そのデータベースの Azure Portal ページの **[基本]** ウィンドウで、**サーバー名**を見つけてコピーします。 この例の完全修飾名は mynewserver-20180430.database.windows.net です。

    ![接続情報](././media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)  

## <a name="connect-to-the-server-as-server-admin"></a>サーバー管理者としてサーバーに接続する

このセクションでは、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) を使って、サーバーへの接続を確立します。

1. SQL Server Management Studio を開きます。

2. **[サーバーへの接続]** ダイアログ ボックスで、次の情報を入力します。

    | 設定        | 推奨値                            | 説明                                                  |
    | -------------- | ------------------------------------------ | ------------------------------------------------------------ |
    | サーバーの種類    | データベース エンジン                            | この値は必須です                                       |
    | サーバー名    | 完全修飾サーバー名            | 名前は **mynewserver-20180430.database.windows.net** のような形式で指定する必要があります。 |
    | 認証 | SQL Server 認証                  | このチュートリアルで構成した認証の種類は "SQL 認証" のみです。 |
    | ログイン          | サーバー管理者アカウント                   | これはサーバーを作成したときに指定したアカウントです。 |
    | Password       | サーバー管理者アカウントのパスワード | これはサーバーを作成したときに指定したパスワードです。 |

    ![[サーバーに接続]](./media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. **[接続]** を選択します。 SSMS で [オブジェクト エクスプローラー] ウィンドウが開きます。

4. オブジェクト エクスプローラーで、 **[データベース]** を展開します。 **[システム データベース]** 、 **[master]** の順に展開し、マスター データベースのオブジェクトを表示します。  **mySampleDatabase** を展開して、新しいデータベースのオブジェクトを表示します。

    ![データベース オブジェクト](./media/load-data-from-azure-blob-storage-using-polybase/connected.png)

## <a name="create-a-user-for-loading-data"></a>データを読み込むためのユーザーを作成する

サーバー管理者アカウントは管理操作を実行するためのものであり、ユーザー データに対するクエリの実行には適していません。 データの読み込みは、メモリを大量に消費する操作です。 メモリの最大値は、[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)と[リソース クラス](resource-classes-for-workload-management.md)の構成に従って定義されます。

データの読み込みに専用のログインとユーザーを作成することをお勧めします。 その後、適切な最大メモリ割り当てを有効にする[リソース クラス](resource-classes-for-workload-management.md)に読み込みユーザーを追加します。

現在はサーバー管理者として接続しているので、ログインとユーザーを作成することができます。 以下の手順を使って、**LoaderRC20** という名前のログインとユーザーを作成します。 その後、そのユーザーを **staticrc20** リソース クラスに割り当てます。

1. SSMS で **[master]** を右クリックしてドロップダウン メニューを表示し、 **[新しいクエリ]** を選びます。 新しいクエリ ウィンドウが開きます。

    ![master の新しいクエリ](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. クエリ ウィンドウで、次の T-SQL コマンドを入力して、LoaderRC20 という名前のログインとユーザーを作成します。"a123STRONGpassword!" は独自のパスワードに置き換えます。

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. **[実行]** を選択します。

4. **mySampleDataWarehouse** を右クリックして、 **[新しいクエリ]** を選びます。 新しいクエリ ウィンドウが開きます。  

    ![サンプル データ ウェアハウスに対する新しいクエリ](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. 次の T-SQL コマンドを入力して、LoaderRC20 ログインに対する LoaderRC20 という名前のデータベース ユーザーを作成します。 2 行目では、新しいユーザーに新しいデータ ウェアハウスの CONTROL アクセス許可を付与しています。  これらのアクセス許可は、ユーザーをデータベースの所有者にする場合と似ています。 3 行目では、新しいユーザーを staticrc20 [リソース クラス](resource-classes-for-workload-management.md)のメンバーとして追加します。

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. **[実行]** を選択します。

## <a name="connect-to-the-server-as-the-loading-user"></a>読み込みユーザーとしてサーバーに接続する

データを読み込むための最初のステップは、LoaderRC20 としてログインすることです。  

1. オブジェクト エクスプローラーで **[接続]** ドロップダウン メニューを選択し、 **[データベース エンジン]** を選択します。 **[サーバーに接続]** ダイアログ ボックスが表示されます。

    ![新しいログインで接続する](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. 完全修飾サーバー名を入力し、ログインとして「**LoaderRC20**」と入力します。  LoaderRC20 のパスワードを入力します。

3. **[接続]** を選択します。

4. 接続する準備ができると、オブジェクト エクスプローラーに 2 つのサーバー接続が表示されます。 1 つは ServerAdmin としての接続、もう 1 つは MedRCLogin としての接続です。

    ![接続成功](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>サンプル データ用のテーブルを作成する

新しいデータ ウェアハウスにデータを読み込むプロセスを始める準備ができました。 チュートリアルのこのパートでは、COPY ステートメントを使用して、Azure Storage Blob からニューヨーク市のタクシーのデータセットを読み込む方法を示します。 今後の参考として、データを Azure Blob Storage に取得する方法やソースから直接読み込む方法については、[読み込みの概要](design-elt-data-loading.md)に関するページを参照してください。

次の SQL スクリプトを実行して、読み込むデータに関する情報を指定します。 この情報には、データが置かれている場所、データの内容の形式、およびデータのテーブル定義が含まれます。

1. 前のセクションで、LoaderRC20 としてデータ ウェアハウスにログインしました。 SSMS で LoaderRC20 接続を右クリックして、 **[新しいクエリ]** を選びます。  新しいクエリ ウィンドウが表示されます。

    ![新しい読み込みクエリ ウィンドウ](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. 実際のクエリ ウィンドウと上の画像を比べてください。  新しいクエリ ウィンドウが LoaderRC20 として実行されていること、および MySampleDataWarehouse データベースに対するクエリを実行していることを確認します。 このクエリ ウィンドウを使って、すべての読み込み手順を実行します。

7. 次の T-SQL ステートメントを実行して、テーブルを作成します。

    ```sql
    CREATE TABLE [dbo].[Date]
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
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
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
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
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
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
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
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>データ ウェアハウスにデータを読み込む

このセクションでは、Azure Storage Blob からサンプル データを[読み込むための COPY ステートメント](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)を使用します。  

> [!NOTE]
> このチュートリアルでは、最終テーブルにデータを直接読み込みます。 通常は、運用ワークロード用のステージング テーブルに読み込みます。 データがステージング テーブルにある間に、必要な変換を実行できます。 

1. 次のステートメントを実行して、データを読み込みます。

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. 読み込んだデータを表示します。 数 GB のデータを読み込み、高パフォーマンスのクラスター化列ストア インデックスに圧縮しています。 動的管理ビュー (DMV) を使用する次のクエリを実行して、読み込みの状態を表示します。

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. すべてのシステム クエリを表示します。

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. データ ウェアハウスにデータが適切に読み込まれました。

    ![読み込まれたテーブルを表示する](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

コンピューティング リソースとデータ ウェアハウスに読み込んだデータには課金されています。 これらは別々に請求されます。

* データをストレージに保持しておく場合は、データ ウェアハウスを使わない間、コンピューティング リソースを一時停止できます。 コンピューティング リソースを一時停止すると課金はデータ ストレージだけになり、データを使う準備ができたらいつでもコンピューティング リソースを再開できます。
* それ以上課金されないようにする場合は、データ ウェアハウスを削除できます。

必要に応じて、以下の手順でリソースをクリーンアップします。

1. [Azure portal](https://portal.azure.com) にログインし、データ ウェアハウスを選択します。

    ![リソースをクリーンアップする](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. コンピューティング リソースを一時停止するには、 **[一時停止]** ボタンを選択します。 データ ウェアハウスが一時停止すると、ボタンの表示が **[開始]** になります。  コンピューティング リソースを再開するには、 **[開始]** を選択します。

3. コンピューティング リソースやストレージに課金されないようにデータ ウェアハウスを削除するには、 **[削除]** を選択します。

4. 作成したサーバーを削除するには、前の図の **mynewserver-20180430.database.windows.net** を選び、 **[削除]** を選択します。  サーバーを削除すると、サーバーに割り当てられているすべてのデータベースが削除されるので、注意してください。

5. リソース グループを削除するには、**myResourceGroup** を選択して、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、データ ウェアハウスを作成し、データを読み込むためのユーザーを作成する方法について学習しました。 シンプルな [COPY ステートメント](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples)を使って、データをデータ ウェアハウスに読み込みました。

以下のことを行いました。
> [!div class="checklist"]
>
> * Azure Portal でデータ ウェアハウスを作成しました
> * Azure Portal でサーバーレベルのファイアウォール規則を設定する
> * SSMS でデータ ウェアハウスに接続しました
> * データを読み込むように指定されたユーザーを作成しました
> * サンプル データ用のテーブルを作成しました
> * COPY T-SQL ステートメントを使ってデータをデータ ウェアハウスに読み込みました
> * データ読み込みの進行状況を表示しました

開発の概要に進んで、既存のデータベースを Azure Synapse Analytics に移行する方法を確認してください。

> [!div class="nextstepaction"]
> [既存のデータベースを Azure Synapse Analytics に移行するための設計上の決定](sql-data-warehouse-overview-develop.md)

読み込みの例とリファレンスに関する詳細については、次のドキュメントを確認してください。

- [COPY ステートメントのリファレンス ドキュメント](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [各認証方法での COPY の例](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [単一テーブルでの COPY に関するクイック スタート](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
