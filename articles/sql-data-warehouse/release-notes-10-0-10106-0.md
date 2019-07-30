---
title: Azure SQL Data Warehouse リリース ノート | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: ee01ebad9e03aaa34911db49ce344d51b6a756d8
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798703"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL Data Warehouse リリース ノート

この記事は、[Azure SQL Data Warehouse](sql-data-warehouse-overview-what-is.md) の最新リリースで導入された新機能と機能強化をまとめたものです。 この記事では、今回のリリースとは直接関連しないものの、同じタイム フレームで公開された注目すべきコンテンツの更新についても一覧表示しています。 他の Azure サービスの機能強化については、「[サービスの更新情報](https://azure.microsoft.com/updates)」を参照してください

## <a name="check-your-azure-sql-data-warehouse-version"></a>Azure SQL Data Warehouse のバージョンを確認する

新機能はすべてのリージョンにロールアウトされますので、機能の可用性については、ご使用のインスタンスにデプロイされているバージョン、および最新の Azure SQL DW のリリース ノートを確認してください。 お使いの Azure SQL DW のバージョンを確認するには、SQL Server Management Studio (SSMS) を介してデータ ウェアハウスに接続して `SELECT @@VERSION AS 'SQL Data Warehouse';` を実行すると、Azure SQL DW の現在のバージョンが返されます。

出力例:

![SQL Data Warehouse のバージョン](./media/release-notes/sql_data_warehouse_version.png)

示された日付を使用して、お使いの Azure SQL DW に適用されているリリースを確認してください。

## <a name="july-2019"></a>2019 年 7 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**具体化されたビュー (プレビュー)**|具体化されたビューでは、ビュー定義クエリから返されるデータを保持し、基になるテーブルのデータが変更されると自動的に更新されます。 これによって、複雑なクエリ (一般に結合と集計を含むクエリ) のパフォーマンスが向上すると共に、メンテナンス操作が簡単になります。 詳細については、次を参照してください。 </br> - [CREATE MATERIALIZED VIEW AS SELECT &#40;Transact-SQL&#41;](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)</br> - [ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) </br> - [Azure SQL Data Warehouse でサポートされる T-SQL ステートメント](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)|
|**追加の T-SQL サポート**|SQL Data Warehouse の T-SQL 言語セキュリティが拡張され、次のサポートが含まれるようになりました。 </br> - [AT TIME ZONE](/sql/t-sql/queries/at-time-zone-transact-sql?view=azure-sqldw-latest)</br> - [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql?view=azure-sqldw-latest)|
|**結果セットのキャッシュ機能 (プレビュー)**|以前発表された結果セットのキャッシュを管理するために追加された DBCC コマンド。 詳細については、次を参照してください。 </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest) </br></br> 実行されたクエリが結果セットのキャッシュを使用した場合に表示される、[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) の新しい result_set_cache 列も確認してください。|
|**順序指定クラスター化列ストア インデックス (プレビュー)**|順序付けされたクラスター化列ストア インデックス内の列の順序を識別するために、[sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?view=azure-sqldw-latest) に追加された新しい column_store_order_ordinal 列。|

## <a name="may-2019"></a>2019 年 5 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**動的データ マスク (プレビュー)**|動的データ マスク (DDM) は、定義されたマスキング規則に基づいて、クエリ結果内で機密データを即座に難読化することにより、データ ウェアハウス内の機密データへの未承認アクセスを防止します。 詳細情報については、[SQL Database の動的データ マスク](/azure/sql-database/sql-database-dynamic-data-masking-get-started)に関する記事をご覧ください。|
|**ワークロードの重要度が一般提供されました**|ワークロード管理の分類と重要度により、クエリの実行順序に影響を与えることができます。 ワークロード重要度の詳細については、ドキュメントの[分類](sql-data-warehouse-workload-classification.md)と[重要度](sql-data-warehouse-workload-importance.md)に関する概要記事を参照してください。 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) のドキュメントも参照してください。<br/><br/>下の動画でワークロード重要度が実際に使われている様子をご覧ください。<br/> -[ワークロード管理の概念](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[ワークロード管理のシナリオ](https://www.youtube.com/embed/_2rLMljOjw8)|
|**追加の T-SQL サポート**|SQL Data Warehouse の T-SQL 言語セキュリティが拡張され、次のサポートが含まれるようになりました。 </br> - [TRIM](/sql/t-sql/functions/trim-transact-sql?view=azure-sqldw-latest)|
|**JSON 関数**|ビジネス アナリストは、Azure Data Warehouse で次の新しい JSON 関数を使用することにより、使い慣れた T-SQL 言語を使用して、JSON データとして書式設定されたドキュメントのクエリおよび操作を行えるようになりました。</br> - [ISJSON](/sql/t-sql/functions/isjson-transact-sql?view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?view=azure-sqldw-latest)</br> - [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest)|
|**結果セットのキャッシュ機能 (プレビュー)**|結果セットのキャッシュ機能により、ビジネス分析で分析情報を得るまでにかかる時間を短縮し、ユーザーを報告しながら、即時のクエリ応答が可能になります。 詳細については、次を参照してください。</br> - [ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest)</br> - [ALTER DATABASE SET オプション (Transact SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)</br> - [SET RESULT SET CACHING (Transact-SQL)](/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)</br> - [SET ステートメント (Transact-SQL)](/sql/t-sql/statements/set-statements-transact-sql)</br> - [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?view=azure-sqldw-latest)|
|**順序指定クラスター化列ストア インデックス (プレビュー)**|列ストアは、大量のデータの格納および効率的なクエリを実現する鍵となります。 テーブルごとに、受信データが行グループに分割され、行グループの各列がディスク上のセグメントを構成します。  順序指定クラスター化列ストア インデックスは、効率的なセグメントの除外を有効にすることにより、クエリの実行をさらに最適化します。   詳細については、次を参照してください。</br> -  [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest)</br> -  [CREATE COLUMNSTORE INDEX (Transact-SQL)](/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest).|

## <a name="march-2019"></a>2019 年 3 月

| サービスの機能強化 | 詳細 |
| --- | --- |
|**データの検出と分類**|Azure SQL Data Warehouse のパブリック プレビューで、データの検出と分類を利用できるようになりました。 機密データや顧客のプライバシーを保護することは非常に重要です。 ビジネスおよび顧客のデータ資産が増大するにつれて、データの検出、分類、保護が管理不能になります。 Azure SQL Data Warehouse でネイティブに導入される "データの検出と分類" 機能を使用すると、データ保護がより管理しやすくなります。 この機能の全体的な利点は次のとおりです。<br/>&bull; &nbsp; データのプライバシー基準および規制のコンプライアンス要件を満たします。<br/>&bull; &nbsp; 機密性の高いデータを含むデータ ウェアハウスへのアクセスを制限し、セキュリティを強化します。<br/>&bull; &nbsp; 機密データへの異常なアクセスを監視し、アラートを出します。<br/>&bull; &nbsp; Azure portal の中央ダッシュボードで機密データを視覚化します。 </br></br>データの検出と分類は、脆弱性評価と脅威検出が含まれた Advanced Data Security の一部として、すべての Azure リージョンの Azure SQL Data Warehouse で利用できます。 データの検出と分類の詳細については、[ブログ記事](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)およびオンラインの[ドキュメント](/azure/sql-database/sql-database-data-discovery-and-classification)をご覧ください。|
|**GROUP BY ROLLUP**|ROLLUP が Azure データ ウェアハウスの GROUP BY オプションでサポートされるようになりました。   GROUP BY ROLLUP によって列式の組み合わせごとにグループが作成されます。 GROUP BY ではまた、結果が小計と総計に "ロール アップ" されます。 GROUP BY 関数は、右から左に処理し、グループと集計が作成される列式の数を減らします。  列の順序は ROLLUP 出力に影響を与えます。結果セット内の行数に影響を与えることもあります。<br/><br/>GROUP BY ROLLUP の詳細については、「[GROUP BY (Transact-SQL)](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest)」をご覧ください。
|**DWU の使用量と CPU ポータルのメトリックの精度向上**|SQL Data Warehouse によって、Azure portal のメトリックの精度が大幅に改善されます。  このリリースでは、CPU と DWU の使用量のメトリックの定義が修正され、コンピューティング ノード全体でワークロードが正しく反映されます。 この修正の前は、メトリック値は実際より少なく報告されていました。 Azure portal では、使用された DWU と CPU メトリックに増加が予想されます。 |
|**行レベルのセキュリティ**|2017 年 11 月に、行レベルのセキュリティ機能を導入しました。 このサポートを外部テーブルにも拡張しました。 さらに、セキュリティ フィルター述語を定義するために必要なインライン テーブル値関数 (インラインTVF) で非決定論的関数を呼び出すためのサポートを追加しました。 この追加により、セキュリティ フィルター述語で IS_ROLEMEMBER()、USER_NAME() などを指定できるようになりました。 詳細については、[行レベルのセキュリティ ドキュメント](/sql/relational-databases/security/row-level-security)の例を参照してください。|
|**追加の T-SQL サポート**|SQL Data Warehouse の T-SQL 言語セキュリティが拡張され、[STRING_SPLIT (Transact-SQL)](/sql/t-sql/functions/string-split-transact-sql) のサポートが含まれるようになりました。
|**クエリ オプティマイザーの機能強化** |クエリの最適化は、あらゆるデータベースの重要な構成要素です。 クエリの最も優れた実行方法を最適に選択することによって、大幅な改善が見込まれます。  分散環境で複雑な分析クエリを実行する場合、実行される操作の数が重要になります。 より質の高いプランを生成することで、クエリのパフォーマンスが向上しています。 これらのプランによって、高価なデータ転送操作や冗長な計算 (重複したサブクエリなど) が最小限に抑えられます。 詳細については、この Azure SQL Data Warehouse の[ブログの投稿](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)を参照してください。|
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
|**PartitionMove と BroadcastMove でのデータ移動の機能強化**|Azure SQL Data Warehouse Gen2 では、種類が ShuffleMove であるデータ移動手順で、瞬時データ移動手法が使用されています。  詳細については、[パフォーマンスの強化に関するブログ](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)をご覧ください。 このリリースでは、PartitionMove と BroadcastMove で、同じ瞬時データ移動手法が利用されるようになりました。 これらの種類のデータ移動手順を使用するユーザー クエリの実行では、パフォーマンスが向上します。 これらのパフォーマンスの向上を利用するためにコードを変更する必要はありません。|
|**重要なバグ**|正しくない Azure SQL Data Warehouse のバージョン - `SELECT @@VERSION` で、正しくないバージョン 10.0.9999.0 が返る場合があります。 現在のリリースの正しいバージョンは 10.0.10106.0 です。 このバグは既に報告されており、検討中です。
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
|**仮想ネットワーク サービス エンドポイントの一般提供**|このリリースでは、すべての Azure リージョンで、Azure SQL Data Warehouse 用の仮想ネットワーク (VNet) サービス エンドポイントの一般提供が開始されます。 VNet サービス エンドポイントを利用すると、お客様の仮想ネットワーク内にある特定のサブネットまたは一連のサブネットからお客様の論理サーバーへの接続を分離できます。 お客様の VNet から Azure SQL Data Warehouse へのトラフィックは、常に Azure バックボーン ネットワーク内に留まります。 この直接ルートは、仮想アプライアンス経由またはオンプレミス経由のインターネット トラフィックを受け取る特定のルートより優先されます。 サービス エンドポイント経由の仮想ネットワーク アクセスに対して別途課金されることはありません。 [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) の現在の価格モデルがそのまま適用されます。<br/><br/>このリリースでは、[Azure BLOB ファイル システム](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS) ドライバーを使用した、[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS) への PolyBase 接続も可能になりました。 Azure Data Lake Storage Gen2 は、分析データのライフサイクル全体に必要なすべての特性を Azure Storage にもたらします。 Azure Blob Storage と Azure Data Lake Storage Gen1 という、既にある 2 つの Azure ストレージ サービスの機能が集約されています。 ファイル システム セマンティクス、ファイルレベルのセキュリティ、スケーリングなど、[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) に由来する機能が、[Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) の低コストの階層型ストレージ、高可用性およびディザスター リカバリー機能と組み合わされています。<br/><br/>PolyBase を使用すると、VNet に対してセキュリティで保護された Azure Storage のデータを Azure SQL Data Warehouse にインポートすることもできます。 同様に、VNet に対してセキュリティで保護された Azure Storage に Azure SQL Data Warehouse からデータをエクスポートすることも、PolyBase によってサポートされています。<br/><br/>Azure SQL Data Warehouse における VNet サービス エンドポイントの詳細については、[ブログ記事](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)または[ドキュメント](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)を参照してください。|
|**自動パフォーマンス監視 (プレビュー)**|Azure SQL Data Warehouse では、[クエリ ストア](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)がプレビューで使用できるようになりました。 クエリ ストアは、クエリ パフォーマンスのトラブルシューティングを支援するように設計されています。クエリ、クエリ プラン、ランタイム統計、およびクエリ履歴を追跡することにより、お客様のデータ ウェアハウスのアクティビティとパフォーマンスを監視できるようにします。 クエリ ストアは、内部ストアと動的管理ビュー (DMV) のセットであり、以下のことを行えます。<br/><br/>&bull; &nbsp; 最もリソース消費量の多いクエリを特定し調整する<br/>&bull; &nbsp; 計画外のワークロードを識別して改善する<br/>&bull; &nbsp; 統計、インデックス、またはシステム サイズ (DWU 設定) の変化によって、クエリのパフォーマンスとプランへの影響を評価する<br/>&bull; &nbsp; 実行されたすべてのクエリの完全なクエリ テキストを表示する<br/><br/>クエリ ストアには、3 つの実際のストアが含まれています。<br/>&bull; &nbsp; 実行プラン情報を保持するためのプラン ストア<br/>&bull; &nbsp; ランタイム統計情報を保持するためのランタイム統計ストア<br/>&bull; &nbsp; 待機統計情報を保持するための待機統計ストア<br/><br/>SQL Data Warehouse では、これらのストアが自動的に管理され、過去 7 日間に保存された無制限の数のクエリが追加料金なしで提供されます。 クエリ ストアを有効にするのは、ALTER DATABASE T-SQL ステートメントを実行するのと同じくらい簡単です。 <br/>sql ----ALTER DATABASE [DatabaseName] SET QUERY_STORE = ON;-------Azure SQL Data Warehouse のクエリ ストアの詳細については、「[クエリのストアを使用した、パフォーマンスの監視](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)」という記事や、[sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql) などのクエリ ストア DMV を参照してください。 リリースを発表した[ブログ記事](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)はこちらです。|
|**Azure SQL Data Warehouse Gen2 の下位コンピューティング レベル**|Azure SQL Data Warehouse Gen2 で下位コンピューティング レベルがサポートされるようになりました。 お客様は、Azure SQL Data Warehouse の優れたパフォーマンス、柔軟性、およびセキュリティ機能を 100 cDWU ([Data Warehouse ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)) から数分で 30,000 cDWU まで拡張できます。 2018 年 12 月半ばから、こちらの[リージョン](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)では、Gen2 のパフォーマンスと柔軟性を下位のコンピューティング レベルでご利用いただけます。その他のリージョンでは、2019 年中に利用できるようになります。<br/><br/>Microsoft は次世代データ ウェアハウスのエントリ ポイントを下げ、セキュリティで保護された高パフォーマンスなデータ ウェアハウスの利点をすべて評価する必要のある価値重視型のお客様に門戸を開いています。お客様が最適な試用環境を推測する必要はありません。 現在の 500 cDWU のエントリ ポイントではなく、100 cDWU から始められます。 SQL Data Warehouse Gen2 では、一時停止操作と再開操作が引き続きサポートされます。また、単なるコンピューティングの柔軟性以外の利点もあります。 Gen2 は、無制限の列ストア ストレージ容量、1 クエリあたり 2.5 倍以上のメモリ、最大 128 件の同時クエリ、および[アダプティブ キャッシング](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)機能もサポートしています。 これらの機能は、同じ料金の Gen1 の同じ Data Warehouse ユニットと比較して、平均 5 倍以上のパフォーマンスを実現しています。 Gen2 では地理的冗長バックアップが標準であり、保証付きデータ保護が組み込まれています。 Azure SQL Data Warehouse Gen2 は必要に応じてスケーリングすることができます。|
|**列ストアのバックグラウンド マージ**|Azure SQL Data Warehouse (Azure SQL DW) では、既定で、[行グループ](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)と呼ばれるマイクロパーティションを使用して、列形式でデータを格納します。 場合によっては、インデックスの作成時またはデータの読み込み時のメモリの制約により、行グループが最適サイズの 100 万行未満に圧縮されることがあります。 行グループは、削除によって断片化することもあります。 小さな行グループや断片化されている行グループでは、メモリの消費量が増え、クエリの実行が非効率になります。 Azure SQL DW のこのリリースでは、メモリの使用効率を高め、クエリの実行を高速化するために、列ストアのバックグラウンド メンテナンス タスクによって小さな圧縮済み行グループがマージされ、より大きな行グループが作成されます。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
|**データウェアハウス用の DevOps**|SQL Data Warehouse (SQL DW) に要求の多かった機能が Visual Studio での SQL Server Data Tool (SSDT) のサポートでプレビューになりました。 開発者のチームは、1 つのバージョン管理されたコードベースで共同作業し、世界中の任意のインスタンスに変更をすばやくデプロイできます。 参加に関心がおありですか。 この機能は、現在プレビューで使用できます。 [SQL Data Warehouse Visual Studio SQL Server Data Tools (SSDT) - プレビュー登録フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)にアクセスして登録することができます。 需要が多いため、お客様にとって最適なエクスペリエンスを実現するためにプレビューへの受け入れを管理しています。 サインアップ後、7 営業日以内に状態を確認することを目指しています。|
|**行レベルセキュリティの一般公開**|Azure SQL Data Warehouse (SQL DW) では、機密データをセキュリティで保護する強力な機能を追加する行レベル セキュリティ (RLS) をサポートするようになりました。 RLS を導入することにより、テーブル内でアクセスできる行とアクセスできる人など、行に対するアクセスを制御するためのセキュリティ ポリシーを実装できます。 RLS を使用すると、ご使用のデータ ウェアハウスを再設計しないでも、このようなきめ細やかなアクセス制御が可能になります。 アクセス制限のロジックは、別のアプリケーションのデータから離れた場所ではなく、データベース層そのものに配置されているため、全般的なセキュリティ モデルが RLS によって簡素化されます。 また、RLS により、アクセス制御を管理するために行をフィルター処理するビューを導入する必要がなくなります。 すべてのお客様について、このエンタープライズ グレードのセキュリティ機能の追加コストはありません。|
|**高度なアドバイザー**|データ ウェアハウスの推奨事項とメトリックの追加により、Azure SQL Data Warehouse (SQL DW) の高度なチューニングが簡単になりました。 任意に活用できる Azure Advisor からの以下の高度なパフォーマンス レコメンデーションが追加されています。<br/><br/>1.アダプティブ キャッシュ – キャッシュ使用率の最適化のために拡大縮小するタイミングに関するアドバイスを得られます。<br/>2.テーブルの分散 – データ移動を減らしてワークロードのパフォーマンスを向上させるために、テーブルをレプリケートするタイミングを判別します。<br/>手順 3.Tempdb – Tempdb 競合を減らすために、リソース クラスを拡大縮小したり構成したりするタイミングを把握します。<br/><br/>概要ブレードでのほぼリアルタイムのメトリックに関する拡張されたカスタマイズ可能な監視グラフなど、[Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) によるデータ ウェアハウス メトリックのより深い統合が提供されています。 使用状況の監視や、データ ウェアハウス レコメンデーションの検証や適用の際、Azure Monitor メトリックにアクセスするためにデータ ウェアハウスの概要ブレードを移動しなくてもよくなりました。 さらに、パフォーマンス レコメンデーションを補完する、新しいメトリック (Tempdb やアダプティブ キャッシュ使用率など) を使用できるようになりました。|
|**統合されたアドバイザーによる高度なチューニング**|データ ウェアハウスの推奨事項とメトリックの追加、および Azure Advisor と Azure Monitor で統合されたエクスペリエンスを提供するポータル概要ブレードのデザイン変更により、Azure SQL Data Warehouse (SQL DW) の高度なチューニングが簡単になりました。|
|**高速データベース復旧 (ADR)**|Azure SQL Data Warehouse の高速データベース復旧 (ADR) がパブリック プレビューになりました。 ADR は、現在の復旧プロセスを一から再設計することで、特に実行時間の長いトランザクションがある場合などにデータベースの可用性を大幅に向上させる、新しい SQL Server エンジンです。 ADR の主な利点は、高速かつ一貫性のあるデータベースの復旧と、瞬時のトランザクション ロールバックです。|
|**Azure Monitor 診断ログ**|Azure Monitor 診断ログと直接統合されることにより、SQL Data Warehouse (SQL DW) では、分析ワークロードに対する拡張された分析情報を利用できるようになりました。 この新しい機能により、開発者は長期間にわたってワークロード ビヘイビアーを分析し、クエリの最適化や容量の管理に関して十分に情報を得たうえで決定を下すことができます。 データ ウェアハウスのワークロードに対する追加の分析情報を提供する [Azure Monitor 診断ログ](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)により、外部ログ プロセスを導入しました。 ボタンを 1 回クリックすることで、[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries) を使用して履歴クエリのパフォーマンスをトラブルシューティングするための診断ログを構成できます。 Azure Monitor 診断ログでは、監査目的でログをストレージ アカウントに保存することによって、カスタマイズ可能な保有期間をサポートしています。また、ほぼリアルタイムでのテレメトリの分析情報を入手するためにログをイベント ハブにストリームしたり、ログ クエリで Log Analytics を使用することによってログを分析したりすることが可能です。 診断ログは、データ ウェアハウスのテレメトリ ビューで構成されています。これは、よく使用される SQL Data Warehouse でのパフォーマンス トラブルシューティングの DMV に相当します。 この最初のリリースでは、以下のシステム動的管理ビューが有効になっています。<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**列ストアのメモリ管理**|圧縮された列ストア行グループの数が増えると、その行グループ内の列セグメント メタデータを管理するために必要なメモリも増えます。  その結果、一部の列ストア動的管理ビュー (DMV) に対して実行されるクエリのパフォーマンスとクエリが低下する可能性があります。  このリリースでは、このようなケースの内部メタデータのサイズを最適化する改善が加えられ、こうしたクエリのエクスペリエンスとパフォーマンスが向上しました。|
|**Azure Data Lake Storage Gen2 の統合 (GA)**|Azure SQL Data Warehouse (SQL DW) は、Azure Data Lake Storage Gen2 とネイティブに統合するようになりました。 お客様は、外部テーブルを使用して ABFS から SQL DW にデータを読み込むことができます。 この機能を使用することにより、Data Lake Storage Gen2 内のデータ レイクと統合できます。|
|**重要なバグ**|DW2000 以降のデータ ウェアハウスでの小さいリソース クラスにおける Parquet に対する CETAS のエラー - この修正により、Create External Table As 内での Parquet コード パスに対する null 参照が正しく識別されます。<br/><br/>一部の CTAS 操作で ID 列の値が失われることがある - 別のテーブルへの CTAS を行うと、ID 列の値が維持されない場合があります。 [ブログ](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)で報告。<br/><br/>クエリがまだ実行している間にセッションが終了されたときに発生する場合がある内部エラー - この修正では、クエリがまだ実行している間にセッションが終了されると、InvalidOperationException がトリガーされます。<br/><br/>(2018 年 11 月に配置) 顧客が Polybase を使用して複数の小さなファイルを ADLS (Gen1) からの読み込もうとしたときに、パフォーマンスは十分に最適ではありませんでした。 - AAD セキュリティ トークンの検証中のシステムのパフォーマンスがボトルネックでした。 セキュリティ トークンのキャッシュを有効にすると、パフォーマンスの問題は軽減されました。 |
| | |

## <a name="next-steps"></a>次の手順

- [SQL Data Warehouse の作成](./create-data-warehouse-portal.md)

## <a name="more-information"></a>詳細情報

- [ブログ - Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Customer Advisory Team のブログ](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [顧客の成功事例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 用語集](../azure-glossary-cloud-terminology.md)
