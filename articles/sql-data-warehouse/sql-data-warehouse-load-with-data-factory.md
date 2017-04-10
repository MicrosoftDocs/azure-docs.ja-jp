---

title: "Azure SQL Data Warehouse - Data Factory へのデータの読み込み | Microsoft Docs"
description: "このチュートリアルでは、Azure Data Factory を使用して Azure SQL Data Warehouse にデータを読み込み、データ ソースとして SQL Server データベースを使用します。"
services: sql-data-warehouse
documentationcenter: NA
author: linda33wj
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: jingwang;kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 6474104846eefa1aa7e137e7914b7a7f1ee8a83a
ms.openlocfilehash: aad76a633b127d23d59dae995d7a503023c5eac7
ms.lasthandoff: 02/09/2017



---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Data Factory による SQL Data Warehouse へのデータの読み込み

Azure Data Factory を使用して、[サポートされているソース データ ストア](../data-factory/data-factory-data-movement-activities.md#supported-data-stores-and-formats)からデータを Azure SQL Data Warehouse に読み込むことができます。 たとえば、Data Factory を使用して、Azure SQL Database や Oracle データベースのデータを SQL Data Warehouse に読み込めます。 この記事のチュートリアルでは、オンプレミスの SQL Server データベースから SQL Data Warehouse にデータを読み込む方法について説明します。

**Time estimate**: このチュートリアルでは、前提条件が満たされると、完了までに約 10 ～ 15 分かかります。

## <a name="prerequisites"></a>前提条件

- SQL Data Warehouse にコピーされるデータを含んだテーブルを持つ **SQL Server データベース**が必要です。  

- オンラインの **SQL Data Warehouse** が必要です。 データ ウェアハウスがまだない場合は、[Azure SQL Data Warehouse の作成](sql-data-warehouse-get-started-provision.md)方法に関するトピックを参照してください。

- **Azure ストレージ アカウント**が必要です。 ストレージ アカウントがまだない場合は、「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md)」をご覧ください。 最大のパフォーマンスを引き出すため、ストレージ アカウントとデータ ウェアハウスは同じ Azure リージョンに配置してください。

## <a name="configure-a-data-factory"></a>データ ファクトリ ページを構成する
1. [Azure ポータル][]にログインします。
2. データ ウェアハウスを見つけて、クリックして開きます。
3. メイン ブレードで、**[データの読み込み]** > **[Azure Data Factory]** をクリックします。

    ![データの読み込みウィザードを起動する](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Azure サブスクリプションのデータ ファクトリがない場合は、ブラウザーの別のタブに **[新しいデータ ファクトリ]**ダイアログ ボックスが表示されます。 必要な情報を入力し、**[作成]** をクリックします。 データ ファクトリを作成した後、**[新しいデータ ファクトリ]** ダイアログ ボックスが閉じ、**[Select Data Factory (データ ファクトリの選択)]** ダイアログ ボックスが表示されます。

    Azure サブスクリプションに既に&1; つ以上のデータ ファクトリがある場合は、**[Select Data Factory (データ ファクトリの選択)]** ダイアログ ボックスが表示されます。 このダイアログボックスでは、既存のデータ ファクトリを選択するか、**[Data Factory の新規作成]** をクリックして新しいデータ ファクトリを作成します。

    ![Configure Data Factory](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. **[Select Data Factory (データ ファクトリの選択)]** ダイアログ ボックスでは、**[データの読み込み]** オプションが既定で選択されています。 **[次へ]** をクリックして、データ読み込みタスクの作成を開始します。

## <a name="configure-the-data-factory-properties"></a>データ ファクトリのプロパティを構成する
データ ファクトリを作成したら、次にデータの読み込みのスケジュールを構成します。

1. **[タスク名]** には、「**DWLoadData-fromSQLServer**」と入力します。
2. 既定の **[一度だけ実行する]** オプションを使用して、**[次へ]** をクリックします。

    ![読み込みのスケジュールを構成する](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>ソース データ ストアとゲートウェイを構成する
ここで、データを読み込むオンプレミスの SQL Server データベースをデータ ファクトリに指示します。

1. サポートされているソース データのストア カタログから **[SQL Server]** を選択して、**[次へ]** をクリックします。

    ![SQL Server のソースを選択する](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. **[Specify the on-premises SQL Server database (オンプレミスの SQL Server データベースを指定)]** ダイアログが表示されます。 最初の **[接続名]** フィールドには値が自動入力されます。 2 つ目のフィールドでは、**ゲートウェイ**の名前が要求されます。 ゲートウェイが既にある既存のデータ ファクトリを使用している場合は、ドロップダウン リストからゲートウェイを選択して再利用できます。 **[ゲートウェイの作成]** リンクをクリックしてデータ管理ゲートウェイを作成します。  

    > [!NOTE]
    > ソース データ ストアがオンプレミスまたは Azure IaaS 仮想マシンにある場合は、データ管理ゲートウェイが必要です。 ゲートウェイは、データ ファクトリと 1 対 1 の関係にあります。 別のデータ ファクトリからは使用できませんが、同じデータ ファクトリでは複数のデータ読み込みタスクで使用できます。 ゲートウェイは、データ読み込みタスクを実行するときに複数のデータ ストアへの接続に使用できます。
    >
    > ゲートウェイの詳細については、「[Data Management Gateway](../data-factory/data-factory-data-management-gateway.md)」に関する記事をご覧ください。

3. **[ゲートウェイの作成]** ダイアログ ボックスが表示されます。 [名前] に「**GatewayForDWLoading**」と入力して、**[作成]** をクリックします。

4. **[Configure Gateway (ゲートウェイの構成)]** ダイアログ ボックスが表示されます。 **[Launch express setup on this computer (このコンピューターで高速セットアップを起動)]** をクリックして、お使いのマシンにデータ管理ゲートウェイを自動でダウンロード、インストール、登録します。 進行状況は、ポップアップ ウィンドウに表示されます。 マシンがデータ ストアに接続できない場合は、データ ストアに接続できるマシンに手動で[ゲートウェイをダウンロードしてインストール](https://www.microsoft.com/download/details.aspx?id=39717)し、キーを使用して登録します。
    > [!NOTE]
    > 高速セットアップは、Microsoft Edge および Internet Explorer でネイティブに動作します。 Google Chrome を使用している場合は、最初に Chrome Web ストアから ClickOnce 拡張機能をインストールしてください。

    ![[Launch Express setup (高速セットアップの起動)]](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. ゲートウェイ セットアップが完了するまで待ちます。 ゲートウェイが正常に登録されてオンラインになると、ポップアップ ウィンドウが閉じてゲートウェイ フィールドに新しいゲートウェイが表示されます。 その後、残りの必須フィールドを次のように入力し、**[次へ]** をクリックします。
    - **[サーバー名]**: オンプレミスの SQL Server の名前。
    - **[データベース名]**: SQL Server データベース。
    - **[Credential encryption (資格情報の暗号化)]**: 既定の [By web browser (Web ブラウザー)] を使用します。
    - **[認証の種類]**: 使用している認証の種類を選択します。
    - **[ユーザー名]** と **[パスワード]**: データをコピーする権限を持っているユーザーのユーザー名とパスワードを入力します。

    ![[Launch Express setup (高速セットアップの起動)]](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. 次に、データのコピー元のテーブルを選択します。 キーワードを使用してテーブルをフィルター処理することができます。 下部のパネルで、データとテーブルのスキーマをプレビューすることができます。 選択が完了したら、**[次へ]** をクリックします。

    ![テーブルを選択する](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>コピー先の SQL Data Warehouse を構成する

次に、コピー先の情報をデータ ファクトリに教えます。

1. SQL Data Warehouse 接続情報は自動的に入力されます。 入力されたユーザー名のパスワードを入力します。 **[次へ]** をクリックします。

    ![コピー先を構成する](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. テーブル名に基づいてコピー元テーブルをコピー先テーブルにマップする、インテリジェント テーブル マッピングが表示されます。 マッピング先にテーブルが存在しない場合、既定では同じ名前のテーブルが ADF によって作成されます (これは SQL Server または Azure SQL Database にコピー元として適用されます)。 既存のテーブルにマップすることもできます。 確認して、**[次へ]** をクリックします。

    ![テーブルをマップする](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. スキーマ マッピングを確認し、エラーまたは警告メッセージを検索します。 インテリジェント マッピングは、列名に基づきます。 コピー元とコピー先の列の間でサポートされていないデータ型変換がある場合、対応するテーブルの横にエラー メッセージが表示されます。 データ ファクトリの自動テーブル作成を選択すると、マップ元とマップ先のストアの非互換性を修正する必要がある場合に、該当するデータ型の変換が発生します。

    ![スキーマをマップする](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. **[次へ]**にログインします。

## <a name="configure-the-performance-settings"></a>パフォーマンスの設定を構成する
パフォーマンスの構成では、Azure ストレージ アカウントを構成します。このアカウントは、[PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly) を使用してデータを SQL Data Warehouse にすばやく読み込む前に、データをステージングする目的で使用されます。 ストレージ内の暫定データは、コピーの完了後に自動的にクリーンアップされます。

既存の Azure ストレージ アカウントを選択し、**[次へ]** をクリックします。

![ステージング BLOB の構成](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>概要情報を確認してパイプラインをデプロイする

構成を確認し、**[完了]** ボタンをクリックしてパイプラインをデプロイします。

![データ ファクトリのデプロイ](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>データ読み込みの進行状況を監視する

デプロイメントの進行状況と結果は、**[デプロイメント]** ページで確認できます。

1. デプロイメントが完了したら、**[Click here to monitor copy pipeline (コピーのパイプラインを監視するにはここをクリック)]** というリンクをクリックして、データ読み込みの進行状況を監視します。

    ![パイプラインを監視する](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. 新しく作成された **DWLoadData-fromSQLServer** データ読み込みパイプラインが、左の**リソース エクスプ ローラー**から自動的に選択されます。

    ![パイプラインの表示](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. 中央のパネルでパイプラインをクリックすると、アクティビティにマップされる各テーブルの詳細なステータスが表示されます。

    ![テーブルの利用状況の表示](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. さらに、アクティビティをクリックすると、データのサイズ、行、スループットなどのデータ読み込みの詳細が右側のパネルに表示されます。

    ![テーブルの利用状況詳細の表示](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. この監視ビューを後で起動するには、SQL Data Warehouse に移動して **[データの読み込み > Azure Data Factory]** をクリックし、ファクトリを選択して **[Monitor existing loading tasks (既存の読み込み中タスクを監視)]** を選択します。

## <a name="next-steps"></a>次のステップ

SQL Data Warehouse にデータベースを移行するには、[移行の概要](sql-data-warehouse-overview-migrate.md)に関する記事を参照してください。

Azure Data Factory とそのデータ移動機能の詳細については、次の記事をご覧ください。

- [Azure Data Factory の概要](../data-factory/data-factory-introduction.md)
- [コピー アクティビティを使用したデータの移動](../data-factory/data-factory-data-movement-activities.md)
- [Azure Data Factory を使用した Azure SQL Data Warehouse との間でのデータの移動](../data-factory/data-factory-azure-sql-data-warehouse-connector.md)

SQL Data Warehouse のデータを探索するには、次の記事をご覧ください。

- [Visual Studio と SSDT を使用して SQL Data Warehouse に接続する](sql-data-warehouse-query-visual-studio.md)
- [Power BI を使用したビジュアル データ](sql-data-warehouse-get-started-visualize-with-power-bi.md)

<!-- Azure references -->
[Azure ポータル]: https://portal.azure.com

