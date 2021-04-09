---
title: 専用 SQL プール (旧称 SQL DW) のリリース ノート
description: Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) のリリース ノート
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 4/30/2020
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 4f333c4bb66195e6c99bd45dcc7c29a0b9a2eaa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591410"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-release-notes"></a>Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) のリリース ノート

この記事では、Azure Synapse Analytics の[専用 SQL プール (旧称 SQL DW)](sql-data-warehouse-overview-what-is.md) の最新リリースで導入された新機能と機能強化をまとめています。 この記事では、今回のリリースとは直接関連しないものの、同じタイム フレームで公開された注目すべきコンテンツの更新についても一覧表示しています。 他の Azure サービスの機能強化については、「[サービスの更新情報](https://azure.microsoft.com/updates)」を参照してください

## <a name="check-your-dedicated-sql-pool-formerly-sql-dw-version"></a>お使いの専用 SQL プール (旧称 SQL DW) のバージョンの確認

新機能がすべてのリージョンにロールアウトされるのに伴い、機能の可用性について、ご使用のインスタンスにデプロイされているバージョン、および最新のリリース ノートを確認してください。 バージョンを確認するには、SQL Server Management Studio (SSMS) を介してお使いの専用 SQL プール (旧称 SQL DW) に接続して `SELECT @@VERSION;` を実行し、現在のバージョンを返します。 このバージョンで、お使いの専用 SQL プール (旧称 SQL DW) に適用されているリリースを確認してください。 出力の日付によって、専用 SQL プール (旧称 SQL DW) に適用されるリリースの月が識別されます。 これは、サービスレベルの機能強化にのみ適用されます。 

ツールの機能強化については、リリース ノートに適切なバージョンがインストールされていることを確認してください。 

> [!NOTE]
> SELECT @@VERSION によって返される製品名は、Microsoft Azure SQL Data Warehouse から Microsoft Azure Synapse Analytics に変更されます。 変更を行う前に、Microsoft から詳細な通知を送信します。 この変更は、使用しているアプリケーション コード内の SELECT @@VERSION の結果から製品名を解析する顧客に関連しています。 製品のブランド変更が原因でアプリケーション コードが変更されるのを回避するには、次のコマンドを使用して、データベースの製品名とバージョンを SERVERPROPERTY に照会してください。バージョン番号 XX.X.XXXXX.X (製品名なし) を返すには、次のコマンドを使用します。
>
> ```sql
> SELECT SERVERPROPERTY('ProductVersion')
>
> --To return engine edition, use this command that returns 6 for Azure Synapse Analytics:
>
> SELECT SERVERPROPERTY('EngineEdition')
> ```

## <a name="dec-2020"></a>2020 年 12 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**列に対するストアド プロシージャ sp_rename (プレビュー)**|[CTAS](./sql-data-warehouse-develop-ctas.md) なしで列の名前を変更することがより簡単になりました。 Azure Synapse SQL では、システム ストアド プロシージャ sp_rename (プレビュー) のサポートが追加され、ユーザー テーブルの非ディストリビューション列の名前を変更できるようになりました。 この機能は現在プレビュー段階であり、GA のツールでサポートされます。 詳細については、[sp_rename](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?view=azure-sqldw-latest&preserve-view=true) を参照してください。|
|**T-SQL Predict の追加パラメーター**|この新しいリリースでは、既存の T-SQL PREDICT ステートメントに対して、'RUNTIME' という必須の追加パラメーターが追加されています。 既存のスクリプトを更新する方法については、[T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true) の例を参照してください。|

## <a name="oct-2020"></a>2020 年 10 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**T-SQL インライン テーブル値関数 (プレビュー)**|このリリースでは、Transact-SQL を使用してインライン テーブル値関数を作成し、テーブルの場合と同様に結果を照会できるようになりました。 この機能は現在プレビュー段階であり、GA のツールでサポートされます。 詳細については、「[CREATE FUNCTION (Azure Synapse Analytics)](/sql/t-sql/statements/create-function-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)」を参照してください。|
|**MERGE コマンド (プレビュー)**|ソース テーブルとの結合結果から、挿入、更新、または削除操作を対象テーブルに対して実行できるようになりました。 たとえば、他のテーブルとの違いに基づいて、あるテーブル内の行を挿入、更新、または削除することにより、2 つのテーブルを同期できます。  詳細については、「[MERGE](/sql/t-sql/statements/merge-transact-sql??view=azure-sqldw-latest&preserve-view=true)」を確認してください。|

## <a name="aug-2020"></a>2020 年 8 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**ワークロード管理 - ポータル エクスペリエンス**|ユーザーは、Azure portal を使用して、ワークロード管理の設定を構成および管理できます。 重要度が設定された[ワークロード グループ](./quickstart-configure-workload-isolation-portal.md)と[ワークロード分類子](./quickstart-create-a-workload-classifier-portal.md)を構成することができます。|
|**テーブル マッピングのカタログ ビューの改善**|新しいカタログ ビュー [sys.pdw_permanent_table_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-permanent-table-mappings-transact-sql) は、永続的なユーザー テーブルの **object_ids** を物理テーブル名にマップします。|

## <a name="july-2020"></a>2020 年 7 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**列レベルの暗号化 (パブリック プレビュー)**|Transact-SQL を使用してデータの列に対称暗号化を適用することで、Azure Synapse Analytics 内の機密情報を保護します。 列レベルの暗号化には、証明書、パスワード、対称キー、または非対称キーでさらに保護される対称キーを使用してデータを暗号化するために使用できる、組み込み関数が用意されています。 詳細については、「[データの列の暗号化](/sql/relational-databases/security/encryption/encrypt-a-column-of-data?view=azure-sqldw-latest&preserve-view=true)」を参照してください。|
|**互換性レベルのサポート (GA)**|ユーザーはこのリリースで、Synapse SQL エンジンの特定のバージョンの Transact-SQL 言語とクエリ処理の動作を使用できるよう、データベースの互換性レベルを設定できるようになりました。 詳細については、「[sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」と「[データベース スコープ構成の変更](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。|
|**行レベルのセキュリティ**|このリリースには、RLS が適用された行に対する更新および削除の操作の改善が含まれています。 このリリースでは、組み込み関数で DML ターゲット テーブルの列が参照されていない場合、"is_rolemember" のような組み込み関数での更新操作と削除操作は成功します。 この改善の前は、これらの操作は、基になる DML 操作での制限のために失敗しました。|
|**DBCC SHRINKDATABASE (GA)**|指定したデータベース内のデータ ファイルとログ ファイルのサイズを圧縮できるようになりました。 詳細については、この[ドキュメント](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql?view=azure-sqldw-latest&preserve-view=true)を参照してください。|

## <a name="may-2020"></a>2020 年 5 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**ワークロードの分離 (GA)**|[ワークロードの分離](./sql-data-warehouse-workload-isolation.md)の一般提供が開始されました。  [ワークロード グループ](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest&preserve-view=true)を使用して、リソースを予約して含めることができます。  クエリ タイムアウトを構成して、ランナウェイ クエリを取り消すこともできます。|
|**ワークロード管理ポータル エクスペリエンス (プレビュー)**| ユーザーは、Azure portal を使用して、ワークロード管理の設定を構成および管理できます。  重要度が設定された[ワークロード グループ](./quickstart-create-a-workload-classifier-portal.md)と[ワークロード分類子](./quickstart-create-a-workload-classifier-portal.md)を構成することができます。|
|**ワークロード グループの変更**|[ALTER WORKLOAD GROUP](/sql/t-sql/statements/alter-workload-group-transact-sql?view=azure-sqldw-latest&preserve-view=true) コマンドを使用できるようになりました。  alter を使用して、既存の[ワークロード グループ](./sql-data-warehouse-workload-isolation.md)の構成を変更します。|
|**COPY コマンドでの Parquet ファイルの自動スキーマ検出 (プレビュー)**|[COPY コマンド](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)による Parquet ファイルの読み込み時に自動スキーマ検出がサポートされるようになりました。 このコマンドでは、読み込みの前に Parquet ファイルのスキーマを自動的に検出し、テーブルを作成します。 この機能を有効にするには、メール配布リスト sqldwcopypreview@service.microsoft.com にお問い合わせください。 |
|**COPY コマンドでの複雑な Parquet データ型の読み込み (プレビュー)**|[COPY コマンド](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)で複雑な Parquet 型の読み込みがサポートされるようになりました。 Maps や Lists などの複雑な型を文字列型の列に読み込むことができます。  この機能を有効にするには、メール配布リスト sqldwcopypreview@service.microsoft.com にお問い合わせください。 |
|**COPY コマンドでの Parquet ファイルの自動圧縮検出**|[COPY コマンド](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)で Parquet ファイルの圧縮方法の自動検出がサポートされるようになりました。 この機能を有効にするには、メール配布リスト sqldwcopypreview@service.microsoft.com にお問い合わせください。|
|**その他の読み込みに関する推奨事項**|Synapse SQL 向けの[読み込みに関する推奨事項](./sql-data-warehouse-concept-recommendations.md)が提供されるようになりました。 最大スループットを確保するためにファイルを分割する必要がある場合にプロアクティブな通知を受け取ります。ストレージ アカウントを専用 SQL プール (旧称 SQL DW)　と併置します。または、SQLBulkCopy API や BCP などの読み込みユーティリティを使用するときにバッチ サイズを増やします。|
|**T-SQL の更新可能なディストリビューション列 (GA)**|ユーザーは、ディストリビューション列に格納されているデータを更新できるようになりました。 詳細については、[専用 SQL プール (旧称 SQL DW) での分散テーブルの設計に関するガイダンス](./sql-data-warehouse-tables-distribute.md)に関する記事を参照してください。|
|**T-SQL Update/Delete from...Join (GA)**|別のテーブルとの Join の結果に基づく Update と Delete が利用可能になりました。 詳細については、[Update](/sql/t-sql/queries/update-transact-sql?view=azure-sqldw-latest&preserve-view=true) および [Delete](/sql/t-sql/statements/delete-transact-sql?view=azure-sqldw-latest&preserve-view=true) のドキュメントを参照してください。|
|**T-SQL PREDICT (プレビュー)**|データ ウェアハウス内の機械学習モデルを予測することで、大規模で複雑なデータ移動を回避できるようになりました。 T-SQL PREDICT 関数は、オープン モデル フレームワークに基づき、データと機械学習モデルを入力として受け取り、予測を生成します。 詳細については、[こちらのドキュメント](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)を参照してください。|

## <a name="april-2020"></a>2020 年 4 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**データベース互換性レベル (プレビュー)**| ユーザーはこのリリースで、Synapse SQL エンジンの特定のバージョンの Transact-SQL 言語とクエリ処理の動作を使用できるよう、データベースの互換性レベルを設定できるようになりました。 詳細については、「[sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」と「[データベース スコープ構成の変更](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。|
|**Sp_describe_undeclared_parameters**| ユーザーが Transact-SQL バッチの宣言されていないパラメーターのメタデータを表示できるようになります。 詳細については、「[sp_describe_undeclared_parameters](/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。| <br/><br/><br/>

| ツールの機能強化                                         | 詳細                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 5](/visualstudio/releases/2019/release-notes-preview#--visual-studio-2019-version-166-preview-5-) - SQL Server Data Tools (SSDT)** | このリリースには、SSDT に対する次の機能強化と修正が含まれています。 </br> </br> - データの検出と分類<br/> - COPY ステートメント <br/> - UNIQUE 制約付きのテーブル<br/> - 順序付けされたクラスター化列ストア インデックス付きのテーブル<br/> <br/>このリリースには、SSDT に対する以下の修正が行われています。 </br></br>  - ディストリビューション列のデータ型を変更すると、SSDT によって生成される更新スクリプトでは、テーブルを削除して再作成する代わりに、CTAS と RENAME 操作が実行されます。 </br> |

## <a name="march-2020"></a>2020 年 3 月

| ツールの機能強化                                         | 詳細                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 2](/visualstudio/releases/2019/release-notes-preview#whats-new-in-visual-studio-2019) - SQL Server Data Tools (SSDT)** | このリリースには、SSDT に対する次の機能強化と修正が含まれています。 </br> </br> - マテリアライズドビュー (MV) によって参照されるテーブルを変更すると、MV でサポートされていない ALTER VIEW ステートメントが生成される問題を解決しました。<br/><br/> - データベースまたはプロジェクトに行レベルのセキュリティ オブジェクトが存在する場合に、Schema Compare 操作が失敗しないことを確実にするための変更を実装しました。 行レベルのセキュリティ オブジェクトは現在、SSDT ではサポートされていません。  <br/><br/> - データベース内の多数のオブジェクトを一覧表示するときにタイムアウトが発生しないように、SQL Server オブジェクト エクスプローラーのタイムアウトのしきい値を増加しました。<br/><br/> - SQL Server オブジェクト エクスプローラーでデータベース オブジェクトの一覧を取得して、オブジェクト エクスプローラーの設定時の不安定な状態を低減し、パフォーマンスを向上させる方法を最適化しました。 |

## <a name="january-2020"></a>2020 年 1 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**ワークロード管理ポータル メトリック (プレビュー)**|10 月にプレビューでリリースされた[ワークロードの分離](sql-data-warehouse-workload-isolation.md)により、ユーザーは独自の[ワークロード グループ](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)を作成してシステム リソースを効率的に管理し、ビジネス SLA を確実に満たすことができます。  Azure Synapse Analytics の全体的な[ワークロード管理](sql-data-warehouse-workload-management.md)拡張機能の一部として、新しい[ワークロード管理の監視メトリック](sql-data-warehouse-workload-management-portal-monitor.md)が使用できるようになりました。</br> </br> ワークロードの監視では、次のメトリックを使用してより多くの分析情報を得ることができるようになりました。 </br> - 有効な上限リソース割合  </br> - 有効な最小リソース割合 </br> - ワークロード グループのアクティブなクエリ </br> - 最大リソース割合別のワークロード グループの割り当て </br> - システム割合別のワークロード グループの割り当て </br> - ワークロード グループのクエリのタイムアウト </br> - ワークロード グループのキューに登録されたクエリ </br></br> これらのメトリックを使用して、[ワークロード グループのボトルネック](sql-data-warehouse-workload-management-portal-monitor.md#workload-group-bottleneck)または[使用率が低いワークロードの分離](sql-data-warehouse-workload-management-portal-monitor.md#underutilized-workload-isolation)で構成されているワークロード グループを特定します。  これらのメトリックは、ワークロード グループ別の分割を可能にする Azure portal で使用できます。  お気に入りのグラフをフィルター処理してダッシュボードにピン留めすることで、分析情報にすばやくアクセスできます。|
|**ポータル監視メトリック**| クエリ全体の利用状況を監視するために、次のメトリックがポータルに追加されました。 </br> - アクティブなクエリ </br> - キューに置かれたクエリ </br> </br>これらのメトリックについては、既存のメトリックと共に[リソース使用率とクエリ アクティビティの監視に関するドキュメント](sql-data-warehouse-concept-resource-utilization-query-activity.md)に詳しく記載されています。|

## <a name="october-2019"></a>2019 年 10 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**Copy (プレビュー)**|データ インジェストのためのシンプルで柔軟な COPY ステートメントのパブリック プレビューが発表されました。 高い特権を持つユーザーを必要とせずに、1 つのステートメントを使用するだけで、非常に柔軟かつシームレスにデータを取り込めるようになりました。 詳細については、[COPY コマンドのドキュメント](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)を参照してください。|
|**ワークロードの分離 (プレビュー)**|データ ウェアハウスを民主化するお客様をサポートするために、インテリジェントなワークロード管理のための新機能が発表されました。 新しい[ワークロードの分離](sql-data-warehouse-workload-isolation.md)機能では、データ ウェアハウスのリソースへの柔軟性と制御を提供しながら、異種ワークロードの実行を管理できるようにします。 これにより、実行の予測可能性が向上し、定義済みの SLA を満たすための機能が強化されます。 </br>ワークロードの分離に加えて、[ワークロード分類](sql-data-warehouse-workload-classification.md)で追加のオプションを使用できるようになりました。  ログイン分類を超えて、[Create Workload Classifier](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文により、クエリ ラベル、セッション コンテキスト、および時刻に基づいて要求を分類する機能が提供されます。|
|**PREDICT (プレビュー)**|データ ウェアハウス内の機械学習モデルをスコア付けすることで、大規模で複雑なデータ移動を回避できるようになりました。 T-SQL PREDICT 関数は、オープン モデル フレームワークに基づき、データと機械学習モデルを入力として受け取り、予測を生成します。
|**SSDT CI/CD (GA)**|SQL Analytics の[最も要望の多い機能](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t)である、SQL Server Data Tools (SSDT) データベース プロジェクトが一般提供されることが発表されました。 このリリースには、Visual Studio 2019 での SSDT のサポートと、Azure DevOps とのネイティブ プラットフォーム統合が含まれており、エンタープライズ レベルのデプロイ用に組み込みの継続的インテグレーションとデプロイ (CI/CD) 機能が用意されています。 |
|**マテリアライズドビュー (GA)**|具体化されたビューでは、ビュー定義クエリから返されるデータを保持し、基になるテーブルのデータが変更されると自動的に更新されます。 これによって、複雑なクエリ (一般に結合と集計を含むクエリ) のパフォーマンスが向上すると共に、メンテナンス操作が簡単になります。 詳細については、「[マテリアライズドビューを使用したパフォーマンス チューニング](performance-tuning-materialized-views.md)」を参照してください。  マテリアライズドビューのスクリプトを作成するために、[SQL Server Management Studio 18.4 以降](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)をインストールします。|
|**動的データ マスク (GA)**|動的データ マスク (DDM) は、定義されたマスキング規則に基づいて、クエリ結果内で機密データを即座に難読化することにより、データ ウェアハウス内の機密データへの未承認アクセスを防止します。 詳細情報については、[SQL Database の動的データ マスク](../../azure-sql/database/dynamic-data-masking-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関する記事をご覧ください。|
|**Read Committed スナップショット分離 (GA)**|ALTER DATABASE を使用して、ユーザー データベースのスナップショット分離を有効または無効にすることができます。 現在のワークロードへの影響を回避するには、データベースのメンテナンス期間中にこのオプションを設定するか、データベースへの他のアクティブな接続がなくなるまで待機します。 詳細については、[ALTER DATABASE SET オプション](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)に関するページを参照してください。|
|**順序指定クラスター化列ストア インデックス (GA)**|列ストアは、大量のデータの格納および効率的なクエリを実現する鍵となります。 順序指定クラスター化列ストア インデックスは、効率的なセグメントの除外を有効にすることにより、クエリの実行をさらに最適化します。   詳細については、「[順序指定クラスター化列ストア インデックスを使用したパフォーマンスのチューニング](performance-tuning-ordered-cci.md)」を参照してください。|
|**結果セットのキャッシュ (GA)**|結果セットのキャッシュが有効になっている場合、繰り返し使用できるよう、クエリ結果がユーザー データベースに自動的にキャッシュされます。 これにより、以降のクエリ実行で永続キャッシュから直接結果を取得できるため、再計算は必要ありません。 結果セットのキャッシュにより、クエリのパフォーマンスが向上し、コンピューティング リソースの使用量が減少します。 さらに、キャッシュされた結果セットを使用するクエリはコンカレンシー スロットをまったく使用しないため、既存のコンカレンシー制限にはカウントされません。 セキュリティのため、キャッシュされた結果にアクセスできるのは、そのユーザーがキャッシュされた結果を作成したユーザーと同じデータ アクセス許可を持っている場合のみです。 詳細については、「[結果セットのキャッシュを使用したパフォーマンスのチューニング](performance-tuning-result-set-caching.md)」を参照してください。 バージョン 10.0.10783.0 以上に適用されます。|

## <a name="september-2019"></a>2019 年 9 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**Azure Private Link (プレビュー)**|[Azure Private Link](https://azure.microsoft.com/blog/announcing-azure-private-link/) を使用すると、お使いの Virtual Network (VNet) にプライベート エンドポイントを作成し、お使いの専用 SQL プールにマップできます。 これらのリソースには、VNet 内のプライベート IP アドレスを使用してアクセスできます。これにより、Azure ExpressRoute プライベート ピアリングや VPN ゲートウェイを介したオンプレミスからの接続が可能になります。 全体として、ネットワーク構成をパブリック IP アドレスに公開する必要がないため、ネットワーク構成を単純化できます。 これにより、データ流出のリスクに対する保護も可能になります。 詳細については、[概要](../../private-link/private-link-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関するページと [SQL Analytics のドキュメント](../../azure-sql/database/private-endpoint-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を参照してください。|
|**データの検出と分類 (GA)**|[データの検出および分類](../../azure-sql/database/data-discovery-and-classification-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)機能は、現在、一般提供されています。 この機能によって、データベース内の機密データを **検出、分類、ラベル付け、および保護する** ための高度な能力が提供されます。|
|**Azure Advisor とのワンクリック統合**|Azure Synapse の SQL Analytics の概要ブレードに、Azure Advisor 推奨事項が直接統合され、ワンクリックで参照できるようになりました。 Azure Advisor ブレードに移動しなくても、概要ブレードで推奨事項を表示できるようになりました。 推奨事項の詳細については、[こちら](sql-data-warehouse-concept-recommendations.md)を参照してください。|
|**Read Committed スナップショット分離 (プレビュー)**|ALTER DATABASE を使用して、ユーザー データベースのスナップショット分離を有効または無効にすることができます。  現在のワークロードへの影響を回避するには、データベースのメンテナンス期間中にこのオプションを設定するか、データベースへの他のアクティブな接続がなくなるまで待機します。 詳細については、[ALTER DATABASE SET オプション](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)に関するページを参照してください。|
|**EXECUTE AS (Transact-SQL)**| [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL がサポートされるようになりました。これにより、セッションの実行コンテキストを、指定したユーザーに設定できます。|
|**追加の T-SQL サポート**|Synapse SQL の T-SQL 言語セキュリティが拡張され、次のサポートが含まれるようになりました。 </br> - [FORMAT (Transact-SQL)](/sql/t-sql/functions/format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [TRY_PARSE (Transact-SQL)](/sql/t-sql/functions/try-parse-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [TRY_CAST (Transact-SQL)](/sql/t-sql/functions/try-cast-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [TRY_CONVERT (Transact-SQL)](/sql/t-sql/functions/try-convert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [sys.user_token (Transact-SQL)](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)|

## <a name="july-2019"></a>2019 年 7 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**マテリアライズドビュー (プレビュー)**|具体化されたビューでは、ビュー定義クエリから返されるデータを保持し、基になるテーブルのデータが変更されると自動的に更新されます。 これによって、複雑なクエリ (一般に結合と集計を含むクエリ) のパフォーマンスが向上すると共に、メンテナンス操作が簡単になります。 詳細については、次を参照してください。 </br> - [CREATE MATERIALIZED VIEW AS SELECT &#40;Transact-SQL&#41;](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) </br> - [Synapse SQL でサポートされる T-SQL ステートメント](sql-data-warehouse-reference-tsql-statements.md)|
|**追加の T-SQL サポート**|Synapse SQL の T-SQL 言語セキュリティが拡張され、次のサポートが含まれるようになりました。 </br> - [AT TIME ZONE (Transact-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [STRING_AGG (Transact-SQL)](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)|
|**結果セットのキャッシュ機能 (プレビュー)**|以前発表された結果セットのキャッシュを管理するために追加された DBCC コマンド。 詳細については、次を参照してください。 </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) </br></br> 実行されたクエリが結果セットのキャッシュを使用した場合に表示される、[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) の新しい result_set_cache 列も確認してください。|
|**順序指定クラスター化列ストア インデックス (プレビュー)**|順序付けされたクラスター化列ストア インデックス内の列の順序を識別するために、[sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) に追加された新しい column_store_order_ordinal 列。|

## <a name="may-2019"></a>2019 年 5 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**動的データ マスク (プレビュー)**|動的データ マスク (DDM) は、定義されたマスキング規則に基づいて、クエリ結果内で機密データを即座に難読化することにより、データ ウェアハウス内の機密データへの未承認アクセスを防止します。 詳細情報については、[SQL Database の動的データ マスク](../../azure-sql/database/dynamic-data-masking-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関する記事をご覧ください。|
|**ワークロードの重要度が一般提供されました**|ワークロード管理の分類と重要度により、クエリの実行順序に影響を与えることができます。 ワークロード重要度の詳細については、ドキュメントの[分類](sql-data-warehouse-workload-classification.md)と[重要度](sql-data-warehouse-workload-importance.md)に関する概要記事を参照してください。 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) のドキュメントも参照してください。<br/><br/>下の動画でワークロード重要度が実際に使われている様子をご覧ください。<br/> -[ワークロード管理の概念](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[ワークロード管理のシナリオ](https://www.youtube.com/embed/_2rLMljOjw8)|
|**追加の T-SQL サポート**|Synapse SQL の T-SQL 言語セキュリティが拡張され、次のサポートが含まれるようになりました。 </br> - [TRIM](/sql/t-sql/functions/trim-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)|
|**JSON 関数**|ビジネス アナリストは、次の新しい JSON 関数を使用することにより、使い慣れた T-SQL 言語を使用して、JSON データとして書式設定されたドキュメントのクエリおよび操作を行えるようになりました。</br> - [ISJSON](/sql/t-sql/functions/isjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)|
|**結果セットのキャッシュ機能 (プレビュー)**|結果セットのキャッシュ機能により、ビジネス分析で分析情報を得るまでにかかる時間を短縮し、ユーザーを報告しながら、即時のクエリ応答が可能になります。 詳細については、次を参照してください。</br> - [ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [ALTER DATABASE SET オプション (Transact SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [SET RESULT SET CACHING (Transact-SQL)](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [SET ステートメント (Transact-SQL)](/sql/t-sql/statements/set-statements-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> - [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)|
|**順序指定クラスター化列ストア インデックス (プレビュー)**|列ストアは、大量のデータの格納および効率的なクエリを実現する鍵となります。 テーブルごとに、受信データが行グループに分割され、行グループの各列がディスク上のセグメントを構成します。  順序指定クラスター化列ストア インデックスは、効率的なセグメントの除外を有効にすることにより、クエリの実行をさらに最適化します。   詳細については、次を参照してください。</br> -  [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)</br> -  [CREATE COLUMNSTORE INDEX (Transact-SQL)](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).|

## <a name="march-2019"></a>2019 年 3 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**データの検出と分類**|Synapse SQL のパブリック プレビューで、データの検出と分類を利用できるようになりました。 機密データや顧客のプライバシーを保護することは重要です。 ビジネスおよび顧客のデータ資産が増大するにつれて、データの検出、分類、保護が管理不能になります。 Synapse SQL にネイティブに導入されるデータの検出と分類機能を使用すると、自分のデータの保護がより管理しやすくなります。 この機能の全体的な利点は次のとおりです。<br/>&bull; &nbsp; データのプライバシー基準および規制のコンプライアンス要件を満たします。<br/>&bull; &nbsp; 機密性の高いデータを含むデータ ウェアハウスへのアクセスを制限し、セキュリティを強化します。<br/>&bull; &nbsp; 機密データへの異常なアクセスを監視し、アラートを出します。<br/>&bull; &nbsp; Azure portal の中央ダッシュボードで機密データを視覚化します。 </br></br>データの検出と分類は、脆弱性評価と脅威検出が含まれた Advanced Data Security の一部として、すべての Azure リージョンで利用できます。 データの検出と分類の詳細については、[ブログ記事](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)およびオンラインの[ドキュメント](../../azure-sql/database/data-discovery-and-classification-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)をご覧ください。|
|**GROUP BY ROLLUP**|ROLLUP が GROUP BY オプションでサポートされるようになりました。   GROUP BY ROLLUP によって列式の組み合わせごとにグループが作成されます。 GROUP BY ではまた、結果が小計と総計に "ロール アップ" されます。 GROUP BY 関数は、右から左に処理し、グループと集計が作成される列式の数を減らします。  列の順序は ROLLUP 出力に影響を与えます。結果セット内の行数に影響を与えることもあります。<br/><br/>GROUP BY ROLLUP の詳細については、「[GROUP BY (Transact-SQL)](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」をご覧ください。
|**DWU の使用量と CPU ポータルのメトリックの精度向上**|Synapse SQL によって、Azure portal のメトリックの精度が大幅に改善されます。  このリリースでは、CPU と DWU の使用量のメトリックの定義が修正され、コンピューティング ノード全体でワークロードが正しく反映されます。 この修正の前は、メトリック値は実際より少なくレポートされていました。 Azure portal では、使用された DWU と CPU メトリックに増加が予想されます。 |
|**行レベルのセキュリティ**|2017 年 11 月に、行レベルのセキュリティ機能を導入しました。 このサポートを外部テーブルにも拡張するようになりました。 さらに、セキュリティ フィルター述語を定義するために必要なインライン テーブル値関数 (インライン TVF) で非決定論的関数を呼び出すためのサポートを追加しました。 この追加により、セキュリティ フィルター述語で IS_ROLEMEMBER()、USER_NAME() などを指定できるようになりました。 詳細については、[行レベルのセキュリティ ドキュメント](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)の例を参照してください。|
|**追加の T-SQL サポート**|Synapse SQL の T-SQL 言語セキュリティが拡張され、[STRING_SPLIT (Transact-SQL)](/sql/t-sql/functions/string-split-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) のサポートが含まれるようになりました。
|**クエリ オプティマイザーの機能強化** |クエリの最適化は、あらゆるデータベースの重要な構成要素です。 クエリの最も優れた実行方法を最適に選択することによって、大幅な改善が見込まれます。  分散環境で複雑な分析クエリを実行する場合、実行される操作の数が重要になります。 より質の高いプランを生成することで、クエリのパフォーマンスが向上しています。 これらのプランによって、高価なデータ転送操作や冗長な計算 (重複したサブクエリなど) が最小限に抑えられます。 詳細については、この Azure Synapse の[ブログの投稿](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)を参照してください。|
| | |

### <a name="documentation-improvements"></a>ドキュメントの改善

| ドキュメントの改善 | 詳細 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
|**並べ替え後の戻りの最適化**|このリリースでは、SELECT…ORDER BY クエリのパフォーマンスが大幅に向上しています。   すべてのコンピューティング ノードの結果が単一のコンピューティング ノードに送信されるようになりました。 そのノードで結果がマージされ、並べ替えられた結果がユーザーに返されます。  単一のコンピューティング ノードを経由してマージすることにより、クエリの結果セットに多数の行が含まれている場合、パフォーマンスが大幅に向上します。 これまでは、クエリ実行エンジンによって各コンピューティング ノード上で結果が並べ替えられていました。 その後、その結果はコントロール ノードにストリーミングされました。 コントロール ノードで結果がマージされました。|
|**PartitionMove と BroadcastMove でのデータ移動の機能強化**|種類が ShuffleMove であるデータ移動手順では、瞬時データ移動手法が使用されています。  詳細については、[パフォーマンスの強化に関するブログ](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)をご覧ください。 このリリースでは、PartitionMove と BroadcastMove で、同じ瞬時データ移動手法が利用されるようになりました。 これらの種類のデータ移動手順を使用するユーザー クエリの実行では、パフォーマンスが向上します。 これらのパフォーマンスの向上を利用するためにコードを変更する必要はありません。|
|**重要なバグ**|不正な Azure Synapse のバージョン - `SELECT @@VERSION` で、正しくないバージョン 10.0.9999.0 が返る場合があります。 現在のリリースの正しいバージョンは 10.0.10106.0 です。 このバグは既に報告されており、検討中です。
| | |

### <a name="documentation-improvements"></a>ドキュメントの改善

| ドキュメントの改善 | 詳細 |
| --- | --- |
|なし | |
| | |

## <a name="december-2018"></a>2018 年 12 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
|**仮想ネットワーク サービス エンドポイントの一般提供**|このリリースでは、すべての Azure リージョンの Azure Synapse の SQL Analytics で、仮想ネットワーク (VNet) サービス エンドポイントの一般提供が開始されます。 VNet サービス エンドポイントを利用すると、お客様の仮想ネットワーク内にある特定のサブネットまたは一連のサブネットからお客様のサーバーへの接続を分離できます。 お客様の VNet から Azure Synapse へのトラフィックは、常に Azure バックボーン ネットワーク内に留まります。 この直接ルートは、仮想アプライアンス経由またはオンプレミス経由のインターネット トラフィックを受け取る特定のルートより優先されます。 サービス エンドポイント経由の仮想ネットワーク アクセスに対して別途課金されることはありません。 [Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) の現在の価格モデルがそのまま適用されます。<br/><br/>このリリースでは、[Azure BLOB ファイル システム](../../storage/blobs/data-lake-storage-abfs-driver.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (ABFS) ドライバーを使用した、[Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (ADLS) への PolyBase 接続も可能になりました。 Azure Data Lake Storage Gen2 は、分析データのライフサイクル全体に必要なすべての特性を Azure Storage にもたらします。 Azure Blob Storage と Azure Data Lake Storage Gen1 という、既にある 2 つの Azure ストレージ サービスの機能が集約されています。 ファイル システム セマンティクス、ファイルレベルのセキュリティ、スケーリングなど、[Azure Data Lake Storage Gen1](../../data-lake-store/index.yml?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) に由来する機能が、[Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) の低コストの階層型ストレージ、高可用性およびディザスター リカバリー機能と組み合わされています。<br/><br/>PolyBase を使用すると、VNet でセキュリティで保護された Azure Storage のデータを Azure Synapse の SQL Analytics にインポートすることもできます。 同様に PolyBase では、VNet でセキュリティで保護された Azure Storage に Azure Synapse からデータをエクスポートすることも可能です。<br/><br/>Azure Synapse における VNet サービス エンドポイントの詳細については、こちらの[ブログ記事](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)または[ドキュメント](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を参照してください。|
|**自動パフォーマンス監視 (プレビュー)**|Azure Synapse の SQL Analytics で[クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)がプレビューとしてご利用いただけるようになりました。 クエリ ストアは、クエリ パフォーマンスのトラブルシューティングを支援するように設計されています。クエリ、クエリ プラン、ランタイム統計、およびクエリ履歴を追跡することにより、お客様のデータ ウェアハウスのアクティビティとパフォーマンスを監視できるようにします。 クエリ ストアは、内部ストアと動的管理ビュー (DMV) のセットであり、以下のことを行えます。<br/><br/>&bull; &nbsp; 最もリソース消費量の多いクエリを特定し調整する<br/>&bull; &nbsp; 計画外のワークロードを識別して改善する<br/>&bull; &nbsp; 統計、インデックス、またはシステム サイズ (DWU 設定) の変化によって、クエリのパフォーマンスとプランへの影響を評価する<br/>&bull; &nbsp; 実行されたすべてのクエリの完全なクエリ テキストを表示する<br/><br/>クエリ ストアには、3 つの実際のストアが含まれています。<br/>&bull; &nbsp; 実行プラン情報を保持するためのプラン ストア<br/>&bull; &nbsp; ランタイム統計情報を保持するためのランタイム統計ストア<br/>&bull; &nbsp; 待機統計情報を保持するための待機統計ストア<br/><br/>Azure Synapse の SQL Analytics では、これらのストアが自動的に管理され、過去 7 日間に保存されたクエリを数を制限せず、追加料金なしで提供します。 クエリ ストアを有効にするのは、ALTER DATABASE T-SQL ステートメントを実行するのと同じくらい簡単です。 <br/>sql ----ALTER DATABASE [DatabaseName] SET QUERY_STORE = ON;-------クエリ ストアの詳細については、「[クエリのストアを使用した、パフォーマンスの監視](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)」という記事や、[sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) などのクエリ ストア DMV を参照してください。 リリースを発表した[ブログ記事](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)はこちらです。|
|**SQL Analytics 向けの下位のコンピューティング レベル**|Azure Synapse の SQL Analytics では下位のコンピューティング レベルがサポートされるようになりました。 お客様は、Azure Synapse の優れたパフォーマンス、柔軟性、セキュリティ機能を、最初は 100 cDWU ([データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)) で開始し、数分で 30,000 cDWU まで拡張できます。 2018 年 12 月半ばから、こちらの[リージョン](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)では、Gen2 のパフォーマンスと柔軟性を下位のコンピューティング レベルでご利用いただけます。その他のリージョンでは、2019 年中に利用できるようになります。<br/><br/>Microsoft は次世代データ ウェアハウスのエントリ ポイントを下げ、セキュリティで保護された高パフォーマンスなデータ ウェアハウスの利点をすべて評価する必要のある価値重視型のお客様に門戸を開いています。お客様が最適な試用環境を推測する必要はありません。 現在の 500 cDWU のエントリ ポイントではなく、100 cDWU から始められます。 SQL Analytics では、一時停止操作と再開操作が引き続きサポートされます。また、単なるコンピューティングの柔軟性以外の利点もあります。 Gen2 は、無制限の列ストア ストレージ容量、1 クエリあたり 2.5 倍以上のメモリ、最大 128 件の同時クエリ、および[アダプティブ キャッシング](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)機能もサポートしています。 これらの機能では、同じ料金の Gen1 の同じデータ ウェアハウス ユニットと比較して、平均 5 倍以上のパフォーマンスが実現されています。 Gen2 では地理的冗長バックアップが標準であり、保証付きデータ保護が組み込まれています。 Azure Synapse の SQL Analytics は、いつでも拡張することができます。|
|**列ストアのバックグラウンド マージ**|Azure SQL Data では、既定で、[行グループ](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)と呼ばれるマイクロパーティションを使用して、列形式でデータを格納します。 場合によっては、インデックスの作成時またはデータの読み込み時のメモリの制約により、行グループが最適サイズの 100 万行未満に圧縮されることがあります。 行グループは、削除によって断片化することもあります。 小さな行グループや断片化されている行グループでは、メモリの消費量が増え、クエリの実行が非効率になります。 このリリースでは、メモリの使用効率を高め、クエリの実行を高速化するために、列ストアのバックグラウンド メンテナンス タスクによって小さな圧縮済み行グループがマージされ、より大きな行グループが作成されます。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
|**データウェアハウス用の DevOps**|Azure Synapse の Synapse SQL で要求が多かった機能が、Visual Studio での SQL Server Data Tool (SSDT) のサポートによってプレビュー状態になりました。 開発者のチームは、1 つのバージョン管理されたコードベースで共同作業し、世界中の任意のインスタンスに変更をすばやくデプロイできます。 参加に関心がおありですか。 この機能は、現在プレビューで使用できます。 [Visual Studio SQL Server Data Tools (SSDT) - プレビュー登録フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)にアクセスして登録することができます。 需要が多いため、お客様にとって最適なエクスペリエンスを実現するためにプレビューへの受け入れを管理しています。 サインアップ後、7 営業日以内に状態を確認することを目指しています。|
|**行レベルセキュリティの一般公開**|Azure Synapse の Synapse SQL では、機密データをセキュリティ保護する強力な機能を追加する行レベル セキュリティ (RLS) がサポートされるようになりました。 RLS を導入することにより、テーブル内でアクセスできる行とアクセスできる人など、行に対するアクセスを制御するためのセキュリティ ポリシーを実装できます。 RLS を使用すると、ご使用のデータ ウェアハウスを再設計しないでも、このようなきめ細やかなアクセス制御が可能になります。 アクセス制限のロジックは、別のアプリケーションのデータから離れた場所ではなく、データベース層そのものに配置されているため、全般的なセキュリティ モデルが RLS によって簡素化されます。 また、RLS により、アクセス制御を管理するために行をフィルター処理するビューを導入する必要がなくなります。 すべてのお客様について、このエンタープライズ グレードのセキュリティ機能の追加コストはありません。|
|**高度なアドバイザー**|データ ウェアハウスのレコメンデーションとメトリックの追加により、Azure Synapse の Synapse SQL を簡単に高度にチューニングできるようになりました。 任意に活用できる Azure Advisor からの以下の高度なパフォーマンス レコメンデーションが追加されています。<br/><br/>1.アダプティブ キャッシュ – キャッシュ使用率の最適化のために拡大縮小するタイミングに関するアドバイスを得られます。<br/>2.テーブルの分散 – データ移動を減らしてワークロードのパフォーマンスを向上させるために、テーブルをレプリケートするタイミングを判別します。<br/>3.Tempdb – Tempdb 競合を減らすために、リソース クラスを拡大縮小したり構成したりするタイミングを把握します。<br/><br/>概要ブレードでのほぼリアルタイムのメトリックに関する拡張されたカスタマイズ可能な監視グラフなど、[Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) によるデータ ウェアハウス メトリックのより深い統合が提供されています。 使用状況の監視や、データ ウェアハウス レコメンデーションの検証や適用の際、Azure Monitor メトリックにアクセスするためにデータ ウェアハウスの概要ブレードを移動しなくてもよくなりました。 さらに、パフォーマンス レコメンデーションを補完する、新しいメトリック (Tempdb やアダプティブ キャッシュ使用率など) を使用できるようになりました。|
|**統合されたアドバイザーによる高度なチューニング**|データ ウェアハウスの推奨事項とメトリックの追加、および Azure Advisor と Azure Monitor が統合されたポータル概要ブレードのデザイン変更により、Azure Synapse を簡単に高度にチューニングできるようになりました。|
|**高速データベース復旧 (ADR)**|Azure Synapse の高速データベース復旧 (ADR) がパブリック プレビューになりました。 ADR は、現在の復旧プロセスを一から再設計することで、特に実行時間の長いトランザクションがある場合などにデータベースの可用性を大幅に向上させる、新しい SQL Server エンジンです。 ADR の主な利点は、高速かつ一貫性のあるデータベースの復旧と、瞬時のトランザクション ロールバックです。|
|**Azure Monitor リソース ログ**|Azure Synapse では、Azure Monitor リソース ログを直接統合し、分析ワークロードのより高度な分析情報を利用できるようになりました。 この新しい機能により、開発者は長期間にわたってワークロード ビヘイビアーを分析し、クエリの最適化や容量の管理に関して十分に情報を得たうえで決定を下すことができます。 データ ウェアハウスのワークロードに対する追加の分析情報を提供する [Azure Monitor リソース ログ](../../azure-monitor/data-platform.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#logs)により、外部ログ プロセスを導入しました。 ボタンを 1 回クリックすることで、[Log Analytics](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) を使用して履歴クエリのパフォーマンスをトラブルシューティングするためのリソース ログを構成できます。 Azure Monitor リソース ログでは、監査目的でログをストレージ アカウントに保存することによって、カスタマイズ可能な保有期間をサポートしています。また、ほぼリアルタイムでのテレメトリの分析情報を入手するためにログをイベント ハブにストリームしたり、ログ クエリで Log Analytics を使用することによってログを分析したりすることが可能です。 リソース ログは、お使いのデータ ウェアハウスのテレメトリ ビューで構成されています。これは、よく使用される Azure Synapse の SQL Analytics でのパフォーマンス トラブルシューティングの DMV に相当します。 この最初のリリースでは、以下のシステム動的管理ビューが有効になっています。<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)|
|**列ストアのメモリ管理**|圧縮された列ストア行グループの数が増えると、その行グループ内の列セグメント メタデータを管理するために必要なメモリも増えます。  その結果、一部の列ストア動的管理ビュー (DMV) に対して実行されるクエリのパフォーマンスとクエリが低下する可能性があります。  このリリースでは、このようなケースの内部メタデータのサイズを最適化する改善が加えられ、こうしたクエリのエクスペリエンスとパフォーマンスが向上しました。|
|**Azure Data Lake Storage Gen2 の統合 (GA)**|Synapse Analytics が、Azure Data Lake Storage Gen2 とネイティブに統合されるようになりました。 お客様は、外部テーブルを使用して、ABFS から 専用 SQL プール (旧称 SQL DW) にデータを読み込むことができるようになりました。 この機能を使用することにより、Data Lake Storage Gen2 内のデータ レイクと統合できます。|
|**重要なバグ**|DW2000 以降のデータ ウェアハウスでの小さいリソース クラスにおける Parquet に対する CETAS のエラー - この修正により、Create External Table As 内での Parquet コード パスに対する null 参照が正しく識別されます。<br/><br/>一部の CTAS 操作で ID 列の値が失われることがある - 別のテーブルへの CTAS を行うと、ID 列の値が維持されない場合があります。 [ブログ](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)で報告。<br/><br/>クエリがまだ実行している間にセッションが終了されたときに発生する場合がある内部エラー - この修正では、クエリがまだ実行している間にセッションが終了されると、InvalidOperationException がトリガーされます。<br/><br/>(2018 年 11 月に配置) 顧客が Polybase を使用して複数の小さなファイルを ADLS (Gen1) からの読み込もうとしたときに、パフォーマンスは十分に最適ではありませんでした。 - AAD セキュリティ トークンの検証中のシステムのパフォーマンスがボトルネックでした。 セキュリティ トークンのキャッシュを有効にすると、パフォーマンスの問題は軽減されました。 |
| | |

## <a name="next-steps"></a>次のステップ

- [専用 SQL プール (旧称 SQL DW) を作成する](create-data-warehouse-portal.md)

## <a name="more-information"></a>詳細情報

- [ブログ ‐ Azure Synapse Analytics](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Customer Advisory Team のブログ](/archive/blogs/sqlcat/)
- [顧客の成功事例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 用語集](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)