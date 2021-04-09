---
title: BI 分析ツールを使用して Azure Cosmos DB に接続する
description: Azure Cosmos DB の ODBC ドライバーを使用して表とビューを作成し、正規化されたデータを BI とデータ分析ソフトウェアで表示できるようにする方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e7d6a67f5322c5bb640430f66ccb0917f6faada1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003499"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>BI 分析ツールと ODBC ドライバーを使用して Azure Cosmos DB に接続する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB の ODBC ドライバーを使用すると、SQL Server Integration Services、Power BI Desktop、Tableau などの BI 分析ツールを使って Azure Cosmos DB に接続できるため、これらのソリューションで Azure Cosmos DB データを分析したり視覚化したりできます。

Azure Cosmos DB の ODBC ドライバーは ODBC 3.8 に準拠していて、ANSI SQL-92 構文をサポートしています。 ODBC ドライバーは、Azure Cosmos DB でデータを再正規化するために役立つ豊富な機能を提供します。 このドライバーを使うと、Azure Cosmos DB のデータを表やビューで表示できます。 またドライバーによって、表やビューに対して、挿入、更新、削除などのクエリを含む SQL 操作を実行できます。

> [!NOTE]
> ODBC ドライバーによる Azure Cosmos DB への接続は、現在は Azure Cosmos DB SQL API アカウントでのみサポートされています。

## <a name="why-do-i-need-to-normalize-my-data"></a>データを正規化すべき理由
Azure Cosmos DB はスキーマレス データベースであり、厳格なスキーマの制約を受けないので、アプリケーションを迅速に開発し、データ モデルを迅速に反復することができます。 1 つの Azure Cosmos データベースに、さまざまな構造の JSON ドキュメントを格納できます。 これは迅速なアプリケーション開発には最適ですが、データ分析と BI ツールを使ってデータを分析し、レポートを作成する場合は、通常データをフラット化して特定のスキーマに準拠させる必要があります。

ここで役立つのが ODBC ドライバーです。 ODBC ドライバーを使用すると、Azure Cosmos DB 内のデータをデータ分析やレポート作成のニーズに適したテーブルとビューに再正規化できます。 再正規化されたスキーマが基になるデータに影響を与えたり、それに従うように開発者を制限したりすることはありません。 それどころか、ODBC に準拠したツールを活用してデータにアクセスできるようになります。 そのため、Azure Cosmos データベースは開発者チームにとって便利なだけでなく、データ アナリストも満足することでしょう。

それでは、ODBC ドライバーを使ってみましょう。

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>手順 1:Azure Cosmos DB ODBC ドライバーをインストールする

1. お使いの環境用のドライバーをダウンロードします。

    | Installer | サポートされるオペレーティング システム| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) (64 ビット Windows 用)| Windows 8.1 以降、Windows 8、Windows 7、Windows Server 2012 R2、Windows Server 2012、および Windows Server 2008 R2 の 64 ビット バージョン。| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) (64 ビット上の 32 ビット Windows 用)| Windows 8.1 以降、Windows 8、Windows 7、Windows XP、Windows Vista、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2、Windows Server 2003 の 64 ビット バージョン。| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) (32 ビット Windows 用)|Windows 8.1 以降、Windows 8、Windows 7、Windows XP、Windows Vista の 32 ビット バージョン。|

    msi ファイルをローカルで実行すると、**Microsoft Azure Cosmos DB ODBC ドライバーのインストール ウィザード** が開始されます。 

1. 既定の入力を使用して ODBC ドライバーをインストールし、インストール ウィザードを完了します。

1. **ODBC データ ソース管理者** アプリをコンピューターで開きます。 これを行うには、Windows の検索ボックスに「**ODBC データ ソース**」と入力します。 
    ドライバーがインストールされたことを確認するには、 **[ドライバー]** タブをクリックして **Microsoft Azure Cosmos DB ODBC ドライバー** が一覧に記載されていることを確認します。

    :::image type="content" source="./media/odbc-driver/odbc-driver.png" alt-text="Azure Cosmos DB ODBC データ ソース管理者":::

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>手順 2:Azure Cosmos データベースに接続する

1. [Azure Cosmos DB ODBC ドライバーをインストール](#install)したら、 **[ODBC データ ソース管理者]** ウィンドウで **[追加]** をクリックします。 ユーザー DSN またはシステム DSN を作成できます。 この例では、ユーザー DSN を作成します。

1. **[新規データ ソースの作成]** ウィンドウで、 **[Microsoft Azure Cosmos DB ODBC ドライバー]** を選択し、 **[完了]** をクリックします。

1. **[Azure Cosmos DB ODBC Driver SDN Setup]\(Azure Cosmos DB ODBC ドライバーの SDN セットアップ\)** ウィンドウに、次の内容を入力します。 

    :::image type="content" source="./media/odbc-driver/odbc-driver-dsn-setup.png" alt-text="Azure Cosmos DB ODBC ドライバーの DSN セットアップ ウィンドウ":::
    - **データ ソース名**:ODBC DSN の独自のフレンドリ名。 この名前は Azure Cosmos DB アカウントに対して一意なので、複数のアカウントがある場合は適切に名前を付けてください。
    - **説明**:データ ソースの簡単な説明。
    - **[Host]\(ホスト\)** : Azure Cosmos DB アカウントの URI。 Azure Portal の [Azure Cosmos DB キー] ページから取得できます。次のスクリーンショットをご覧ください。 
    - **アクセス キー**:Azure portal の [Azure Cosmos DB キー] ページから、プライマリまたはセカンダリ、読み取り/書き込みまたは読み取り専用のキーを選びます。次のスクリーンショットをご覧ください。 DSN を読み取り専用のデータ処理とレポート作成に使用する場合は、読み取り専用キーを使用することをお勧めします。
    :::image type="content" source="./media/odbc-driver/odbc-cosmos-account-keys.png" alt-text="[Azure Cosmos DB キー] ページ":::
    - **Encrypt Access Key for (アクセスキーの暗号化)** :このコンピューターのユーザーに基づいた最適な選択肢を選択します。 
    
1. **[テスト]** ボタンをクリックして、Azure Cosmos DB アカウントに接続できることを確認します。 

1.  **[詳細オプション]** をクリックして、次の値を設定します。
    *  **REST API バージョン**: 操作に使用する [REST API バージョン](/rest/api/cosmos-db/)を選択します。 既定値は 2015-12-16 です。 [大きいパーティションキー](large-partition-keys.md)を持つコンテナーがあり、REST API バージョン 2018-12-31 が必要な場合:
        - REST API バージョンとして「**2018-12-31**」を入力します
        - **スタート** メニューで「regedit」と入力し、**レジストリ エディター** アプリケーションを検索して開きます。
        - レジストリ エディターで、次のパスに移動します。**Computer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI**
        - DSN と同じ名前の新しいサブキーを作成します。例: "Contoso Account ODBC DSN"
        - "Contoso Account ODBC DSN" サブキーに移動します。
        - 右クリックして、新しい **文字列** 値を追加します。
            - 値の名前: **IgnoreSessionToken**
            - 値のデータ:**1**
            :::image type="content" source="./media/odbc-driver/cosmos-odbc-edit-registry.png" alt-text="レジストリ エディターの設定":::
    - **Query Consistency (クエリの一貫性)** :操作の [一貫性レベル](consistency-levels.md)を選択します。 既定ではセッションです。
    - **再試行回数**:サービス レートの制限のために最初の要求が完了しない場合、操作を再試行する回数を入力します。
    - **スキーマ ファイル**:これにはオプションがいくつかあります。
        - 既定では、このエントリをそのまま (空白) にすると、ドライバーですべてのコンテナーのデータの最初のページがスキャンされ、各コンテナーのスキーマが判定されます。 これは、コンテナー マッピングとして知られています。 スキーマ ファイルが定義されていないと、ドライバーはドライバー セッションごとにスキャンを実行する必要があるため、DSN を使ったアプリケーションの起動に時間がかかることがあります。 スキーマ ファイルを DSN と常に関連付けることをお勧めします。
        - (スキーマ エディターを使用して作成した) スキーマ ファイルが既にある場合は、 **[参照]** をクリックしてファイルに移動し、 **[保存]** をクリックしてから **[OK]** をクリックします。
        - 新しいスキーマを作成する場合は、 **[OK]** をクリックしてからメイン ウィンドウの **[スキーマ エディター]** をクリックします。 次にスキーマ エディターの情報に進みます。 新しいスキーマ ファイルを作成したら、 **[詳細オプション]** ウィンドウに戻って、新しく作成されたスキーマ ファイルを含めることを忘れないでください。

1. 操作を完了して **[Azure Cosmos DB ODBC Driver DSN Setup (Azure Cosmos DB ODBC ドライバーの DSN セットアップ)]** ウィンドウを閉じると、新しいユーザー DSN が [ユーザー DSN] タブに追加されます。

    :::image type="content" source="./media/odbc-driver/odbc-driver-user-dsn.png" alt-text="[ユーザー DSN] タブの新しい Azure Cosmos DB ODBC DSN":::

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>手順 3:コンテナー マッピングの方法を使ってスキーマ定義を作成する

使用できるサンプリング方法は、**コンテナー マッピング** と **テーブル区切り記号** の 2 種類です。 サンプリング セッションではどちらのサンプリング方法も利用できますが、各コンテナーが使用できるのは特定のサンプリング方法のみになります。 次の手順では、コンテナー マッピングの方法を使って 1 つまた複数のコンテナー内のデータのスキーマを作成します。 このサンプリング方法では、コンテナーのページのデータを取得して、データの構造を判定します。 それにより、ODBC 側のテーブルにコンテナーを入れ替えます。 このサンプリング方法は、コンテナーのデータの種類が同じ場合は効率的で迅速です。 コンテナーに異なる型のデータが含まれる場合は、[テーブル区切り記号のマッピング方法](#table-mapping)を使用することをお勧めします。この方法は、コンテナー内のデータ構造を判定するより堅牢なサンプリング方法を提供します。 

1. 「[Azure Cosmos データベースに接続する](#connect)」の手順 1 から 4 が完了したら、 **[Azure Cosmos DB ODBC Driver DSN Setup]\(Azure Cosmos DB ODBC ドライバーの DSN セットアップ\)** ウィンドウの **[スキーマ エディター]** をクリックします。

    :::image type="content" source="./media/odbc-driver/odbc-driver-schema-editor.png" alt-text="Azure Cosmos DB ODBC ドライバーの DSN セットアップ ウィンドウの [スキーマ エディター] ボタン":::
1. **[スキーマ エディター]** ウィンドウで、 **[新規作成]** をクリックします。
    **[スキーマを生成する]** ウィンドウに、Azure Cosmos DB アカウントのすべてのコンテナーが表示されます。 

1. サンプリングするコンテナーを 1 つまたは複数選択し、 **[サンプル]** をクリックします。 

1. **[デザイン ビュー]** タブに、データベース、スキーマ、テーブルが表示されます。 [テーブル ビュー] には、列名 (SQL 名、ソース名など) と関連付けられている一連のプロパティがスキャンによって表示されます。
    列ごとに、列の SQL 名、SQL 型、SQL の長さ (該当する場合)、スケール (該当する場合)、有効桁数 (該当する場合)、Null 許容を変更できます。
    - クエリ結果から列を除外したい場合は、 **[列の非表示]** を **true** に設定できます。 [列の非表示] が true でマークされた列は、まだスキーマの一部ではあるものの、選択やプロジェクションでは返されません。 たとえば、Azure Cosmos DB のシステムで必要な "_" で始まるすべてのプロパティを非表示にすることができます。
    - **ID** 列は非表示にできない唯一のフィールドですが、これは正規化されたスキーマでプライマリ キーとして使用されるためです。 

1. スキーマの定義を完了したら、 **[ファイル]**  |  **[保存]** をクリックし、スキーマを保存するディレクトリに移動して、 **[保存]** をクリックします。

1. このスキーマを DSN と共に使用する場合は、 **[Azure Cosmos DB ODBC Driver DSN Setup\(Azure Cosmos DB ODBC ドライバーの DSN セットアップ\)]** ウィンドウを (ODBC データ ソース管理者経由で) 開き、 **[詳細オプション]** をクリックして、 **[スキーマ ファイル]** ボックスの保存されたスキーマに移動します。 スキーマ ファイルを既存の DSN に保存すると、DNS 接続のスコープが、スキーマによって定義されたデータと構造に変更されます。

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>手順 4:テーブル区切り記号のマッピング方法を使ってスキーマ定義を作成する

使用できるサンプリング方法は、**コンテナー マッピング** と **テーブル区切り記号** の 2 種類です。 サンプリング セッションではどちらのサンプリング方法も利用できますが、各コンテナーが使用できるのは特定のサンプリング方法のみになります。 

次の手順では、**テーブル区切り記号** のマッピング方法を使って 1 つまたは複数のコンテナー内のデータのスキーマを作成します。 コンテナーに異なる型のデータが含まれる場合は、このサンプリング方法を使用することをお勧めします。 この方法を使って、サンプリングのスコープを一連の属性と対応する値に設定できます。 たとえば、ドキュメントに "型" のプロパティが含まれる場合は、サンプリングのスコープをこのプロパティの値に設定できます。 サンプリングの最終的な結果は、指定した型の各値が記載されたテーブルのセットになります。 たとえば、型が自動車の場合は自動車のテーブルが生成され、型が飛行機の場合は飛行機のテーブルが生成されます。

1. 「[Azure Cosmos データベースに接続する](#connect)」の手順 1 から 4 が完了したら、[Azure Cosmos DB ODBC Driver DSN Setup]\(Azure Cosmos DB ODBC ドライバーの DSN セットアップ\) ウィンドウの **[スキーマ エディター]** をクリックします。

1. **[スキーマ エディター]** ウィンドウで、 **[新規作成]** をクリックします。
    **[スキーマを生成する]** ウィンドウに、Azure Cosmos DB アカウントのすべてのコンテナーが表示されます。 

1. **[サンプル ビュー]** タブでコンテナーを選択し、コンテナーの **[マッピング定義]** 列で **[編集]** をクリックします。 次に **[マッピング定義]** ウィンドウで **[Table Delimiters]** (テーブル区切り記号) の方法を選択します。 次に、次を実行します。

    a. **[属性]** ボックスに、区切り記号のプロパティ名を入力します。 これは、サンプリングのスコープを設定するドキュメントのプロパティ (例：市区町村) です。次に Enter キーを押します。 

    b. サンプリングのスコープを前の手順で入力した属性の特定の値に設定する場合は、[選択] ボックスで属性を選択し、 **[値]** ボックスに値 (例: シアトル) を入力して Enter キーを押します。 引き続き属性に複数の値を追加できます。 値を入力するときは、適切な属性が選択されていることを確認してください。

    たとえば、市区町村の **属性** 値を含めて、テーブルにニューヨークとドバイの都市の値の行のみを含めるよう制限する場合は、[属性] ボックスに「市区町村」と入力し、 **[値]** ボックスには「ニューヨーク」と「ドバイ」を入力します。

1. **[OK]** をクリックします。 

1. サンプリングするコンテナーのマッピング定義が完了したら、 **[スキーマ エディター]** ウィンドウで **[サンプル]** をクリックします。
     列ごとに、列の SQL 名、SQL 型、SQL の長さ (該当する場合)、スケール (該当する場合)、有効桁数 (該当する場合)、Null 許容を変更できます。
    - クエリ結果から列を除外したい場合は、 **[列の非表示]** を **true** に設定できます。 [列の非表示] が true でマークされた列は、まだスキーマの一部ではあるものの、選択やプロジェクションでは返されません。 たとえば、`_` で始まる Azure Cosmos DB システムに必要なすべてのプロパティを非表示にできます。
    - **ID** 列は非表示にできない唯一のフィールドですが、これは正規化されたスキーマでプライマリ キーとして使用されるためです。 

1. スキーマの定義を完了したら、 **[ファイル]**  |  **[保存]** をクリックし、スキーマを保存するディレクトリに移動して、 **[保存]** をクリックします。

1. **[Azure Cosmos DB ODBC Driver DSN Setup] (Azure Cosmos DB ODBC ドライバーの DSN セットアップ)** ウィンドウに戻り、 **[詳細オプション]** をクリックします。 次に、 **[スキーマ ファイル]** ボックスで保存したスキーマ ファイルに移動し、 **[OK]** をクリックします。 もう 1 度 **[OK]** をクリックして DSN を保存します。 これにより、DSN に作成したスキーマが保存されます。 

## <a name="optional-set-up-linked-server-connection"></a>(省略可能) リンク サーバーの接続を設定する

リンク サーバーの接続を設定することによって、SQL Server Management Studio (SSMS) から Azure Cosmos DB にクエリを実行できます。

1. [手順 2.](#connect) の説明に従って、たとえば `SDS Name` という名前のシステム データ ソースを作成します。

1. [SQL Server Management Studio をインストール](/sql/ssms/download-sql-server-management-studio-ssms)し、サーバーに接続します。 

1. SSMS クエリ エディターで、次のコマンドを使用して、データ ソースのリンク サーバー オブジェクト `DEMOCOSMOS` を作成します。 `DEMOCOSMOS` をリンク サーバーの名前に、`SDS Name` をシステム データ ソースの名前に置き換えます。

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
新しいリンク サーバー名を表示するには、リンク サーバーの一覧を更新します。

:::image type="content" source="./media/odbc-driver/odbc-driver-linked-server-ssms.png" alt-text="SSMS でのリンク サーバー":::

### <a name="query-linked-database"></a>リンクされたデータベースにクエリを実行する

リンクされたデータベースにクエリを実行するには、SSMS クエリを入力します。 この例では、クエリで `customers` という名前のコンテナー内のテーブルから選択します。

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

クエリを実行します。 結果は次のようになります。

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> リンクされた Cosmos DB サーバーは、4 部構成の名前付けをサポートしていません。 次のメッセージのようなエラーが返されます。

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(省略可能) ビューを作成する
サンプリング プロセスの一環として、ビューの定義と作成ができます。 これらのビューは、SQL ビューと同じです。 これらは読み取り専用で、Azure Cosmos DB SQL クエリの選択とプロジェクションが定義されているスコープです。 

データのビューを作成するには、 **[スキーマ エディター]** ウィンドウの **[ビューの定義]** 列で、サンプリングするコンテナーの行の **[追加]** をクリックします。 

:::image type="content" source="./media/odbc-driver/odbc-driver-create-view.png" alt-text="データのビューを作成する":::


次に **[ビューの定義]** ウィンドウで、次の操作をします。

1. **[新規]** をクリックしてビューの名前 (例：EmployeesfromSeattleView) を入力し、 **[OK]** をクリックします。

1. **[ビューの編集]** ウィンドウで、Azure Cosmos DB クエリを入力します。 これは、たとえば `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"` のような [Azure Cosmos DB SQL](./sql-query-getting-started.md) クエリである必要があります。次に **[OK]** をクリックします。

    :::image type="content" source="./media/odbc-driver/odbc-driver-create-view-2.png" alt-text="ビューの作成時にクエリを追加する":::


ビューは好きな数だけ作成できます。 ビューの定義が完了したら、データをサンプリングできます。 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>手順 5:Power BI Desktop などの BI ツールでデータを表示する

新しい DSN を使用して、Azure Cosmos DB を ODBC に準拠しているツールと接続できます。この手順では、Power BI Desktop に接続して Power BI を視覚化する方法について簡単に説明します。

1. Power BI Desktop を開きます。

1. **[データの取得]** をクリックします。

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data.png" alt-text="Power BI Desktop でデータを取得する":::

1. **[データの取得]** ウィンドウで、 **[Other]** (その他) |  **[ODBC]**  |  **[接続]** の順にクリックします。

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-2.png" alt-text="Power BI の [データの取得] で ODBC データ ソースを選択する":::

1. **[From ODBC]** (ODBC から) ウィンドウで作成したデータ ソース名を選択し、 **[OK]** をクリックします。 **[詳細オプション]** エントリは空白のままにしておくことができます。

   :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-3.png" alt-text="Power BI の [データの取得] でデータ ソース名 (DSN) を選択する":::

1. **[ODBC ドライバーを使用してデータ ソースにアクセスします]** ウィンドウで、 **[既定またはカスタム]** を選択し、 **[接続]** をクリックします。 **資格情報の接続文字列プロパティ** を含める必要はありません。

1. **[ナビゲーター]** ウィンドウの左ペインでデータベースとスキーマを展開し、テーブルを選択します。 結果ペインには作成したスキーマを使用するデータが含まれています。

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-4.png" alt-text="Power BI の [データの取得] でテーブルを選択する":::

1. Power BI Desktop でデータを視覚化するには、テーブル名の前のボックスをオンにし、 **[読み込む]** をクリックします。

1. Power BI Desktop の左端の [データ] タブ :::image type="icon" source="./media/odbc-driver/odbc-driver-data-tab.png"::: を選択し、データがインポートされたことを確認します。 

1. [レポート] タブ :::image type="icon" source="./media/odbc-driver/odbc-driver-report-tab.png"::: をクリックし、 **[新しいビジュアル]** をクリックしてタイルをカスタマイズすることにより、Power BI を使用してビジュアルを作成できます。 Power BI Desktop での視覚化の詳細については、「[Power BI での視覚化の種類](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/)」を参照してください。 

## <a name="troubleshooting"></a>トラブルシューティング

次のエラーが発生した場合は、[手順 2](#connect) で、Azure Portal からコピーした **ホスト** と **アクセス キー** の値が正しいことを確認して再度試してください。 Azure Portal の **ホスト** と **アクセス キー** の値の右側にある [コピー] ボタンを使って、値を正しくコピーします。

```output
[HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}
```


## <a name="next-steps"></a>次のステップ

Azure Cosmos DB の詳細については、「[Azure Cosmos DB の概要](introduction.md)」を参照してください。