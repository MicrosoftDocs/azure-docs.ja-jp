---
title: Azure Data Lake Storage を Gen1 から Gen2 に移行する
description: Azure Data Lake Storage を Gen1 から Gen2 に移行します。Gen2 は、Azure Blob Storage を基にして構築されており、ビッグ データ分析専用の機能セットを提供します。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 875c046406b7989a39f09dde82640ed5567b6b3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714869"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Azure Data Lake Storage を Gen1 から Gen2 に移行する

データ、ワークロード、アプリケーションを、Data Lake Storage Gen1 から Data Lake Storage Gen2 に移行することができます。

‎Azure Data Lake Storage Gen2 は [Azure Blob Storage](storage-blobs-introduction.md) を基にして構築されており、ビッグ データ分析専用の機能セットを提供します。 [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) では、ファイル システム セマンティクス、ディレクトリ、ファイル レベルのセキュリティおよびスケーリングなどの [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) の機能が、[Azure Blob Storage](storage-blobs-introduction.md) の低コスト、階層型ストレージ、高可用性およびディザスター リカバリー機能と組み合わされています。

> [!NOTE]
> 読みやすくするため、この記事では、Azure Data Lake Storage Gen1 を *Gen1* と呼び、Azure Data Lake Storage Gen2 を *Gen2* と呼びます。

## <a name="recommended-approach"></a>推奨される方法

Gen2 に移行するには、次の方法が推奨されます。

:heavy_check_mark:手順 1:適応性を評価する

:heavy_check_mark:手順 2:移行を準備する

:heavy_check_mark:手順 3:データとアプリケーション ワークロードを移行する

:heavy_check_mark:手順 4:Gen1 から Gen2 に切り替える

> [!NOTE]
> Gen1 と Gen2 は異なるサービスであり、インプレース アップグレードのエクスペリエンスはなく、意図的な移行作業が必要です。 

### <a name="step-1-assess-readiness"></a>手順 1:適応性を評価する

1. [Data Lake Storage Gen2 のオファリング](https://azure.microsoft.com/services/storage/data-lake-storage/) (利点、コスト、一般的なアーキテクチャ) について確認します。 

2. Gen1 と Gen2 の[機能を比較します](#gen1-gen2-feature-comparison)。 

3. [既知の問題](data-lake-storage-known-issues.md)の一覧を確認して、機能のギャップを評価します。

4. Gen2 では、[診断ログ](../common/storage-analytics-logging.md)、[アクセス レベル](storage-blob-storage-tiers.md)、[Blob Storage ライフサイクル管理ポリシー](storage-lifecycle-management-concepts.md)などの Blob Storage の機能がサポートされています。 これらの機能を使用することに興味がある場合は [現在のサポート レベル](./data-lake-storage-supported-blob-storage-features.md)を確認してください。

5. [Azure エコシステムのサポート](./data-lake-storage-multi-protocol-access.md) の現状を確認し、ソリューションが依存するすべてのサービスが Gen2 でサポートされていることを確認します。

### <a name="step-2-prepare-to-migrate"></a>手順 2:移行を準備する

1. 移行するデータ セットを明らかにします。

   この機会を利用して、使用しなくなったデータセットをクリーンアップします。 すべてのデータを一度に移行することを計画しているのでない限り、この時間を使って、段階的に移行できるデータの論理グループを明らかにします。
   
2. 移行がビジネスに与える影響を決定します。

   たとえば、移行の実行中にダウンタイムを許容できるかどうかを検討します。 これらの考慮事項は、適切な移行パターンを特定し、最適なツールを選択するのに役立ちます。

3. 移行計画を作成します。 

   これらの[移行パターン](#migration-patterns)をお勧めします。 これらのパターンのいずれかを選択するか、これらを組み合わせるか、独自のカスタム パターンを設計することができます。

### <a name="step-3-migrate-data-workloads-and-applications"></a>手順 3:データ、ワークロード、アプリケーションを移行する

好みのパターンを使用して、データ、ワークロード、アプリケーションを移行します。 シナリオを段階的に検証することをお勧めします。

1. [ストレージ アカウントを作成](../common/storage-account-create.md)し、階層型名前空間の機能を有効にします。 

2. データを移行します。 

3. Gen2 エンドポイントを指し示すように、[ワークロード内のサービス](./data-lake-storage-supported-azure-services.md)を構成します。 
   
4. Gen2 API を使用するようにアプリケーションを更新します。 [.NET](data-lake-storage-directory-file-acl-dotnet.md)、[Java](data-lake-storage-directory-file-acl-java.md)、[Python](data-lake-storage-directory-file-acl-python.md)、[JavaScript](data-lake-storage-directory-file-acl-javascript.md)、[REST](/rest/api/storageservices/data-lake-storage-gen2) に関するガイドを参照してください。 
   
5. Data Lake Storage Gen2 の [PowerShell コマンドレット](data-lake-storage-directory-file-acl-powershell.md)および [Azure CLI コマンド](data-lake-storage-directory-file-acl-cli.md)を使用するようにスクリプトを更新します。
   
6. コード ファイル、Databricks ノートブック、Apache Hive HQL ファイル、またはワークロードの一部として使用されるその他のファイルで、文字列 `adl://` が含まれる URI 参照を検索します。 これらの参照を、新しいストレージ アカウントの [Gen2 形式の URI](data-lake-storage-introduction-abfs-uri.md) に置き換えます。 たとえば、Gen1 の URI `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` は、`abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` になる可能性があります。 

7. [Azure ロール](../common/storage-auth-aad-rbac-portal.md)、[ファイルとフォルダー レベルのセキュリティ](data-lake-storage-access-control.md)、および [Azure Storage ファイアウォールと仮想ネットワーク](../common/storage-network-security.md)を含むように、アカウントのセキュリティを構成します。

### <a name="step-4-cutover-from-gen1-to-gen2"></a>手順 4:Gen1 から Gen2 に切り替える

Gen2 でアプリケーションとワークロードが安定していることを確認したら、Gen2 を使用してビジネス シナリオを満たすことができます。 Gen1 で実行されている残りのパイプラインをオフにし、Gen1 アカウントの使用を停止します。 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 と Gen2 の機能の比較

次の表は、Gen1 の機能と Gen2 の機能を比較したものです。

|領域 |Gen1   |Gen2 |
|---|---|---|
|データの編成|[階層構造の名前空間](data-lake-storage-namespace.md)<br>ファイルとフォルダーのサポート|[階層構造の名前空間](data-lake-storage-namespace.md)<br>コンテナー、ファイル、フォルダーのサポート |
|geo 冗長| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage)、[ZRS](../common/storage-redundancy.md#zone-redundant-storage)、[GRS](../common/storage-redundancy.md#geo-redundant-storage)、[RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|認証|[AAD マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)<br>[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)<br>[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)<br>[共有アクセス キー](/rest/api/storageservices/authorize-with-shared-key)|
|承認|管理 - [Azure RBAC](../../role-based-access-control/overview.md)<br>データ – [ACL](data-lake-storage-access-control.md)|管理 – [Azure RBAC](../../role-based-access-control/overview.md)<br>データ - [ACL](data-lake-storage-access-control.md)、[Azure RBAC](../../role-based-access-control/overview.md) |
|暗号化 – 保存データ|サーバー側 - [Microsoft マネージド](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)または[カスタマー マネージド](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) キー|サーバー側 - [Microsoft マネージド](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)または[カスタマー マネージド](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) キー|
|VNET のサポート|[VNET 統合](../../data-lake-store/data-lake-store-network-security.md)|[サービス エンドポイント](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、[プライベート エンドポイント](../common/storage-private-endpoints.md)|
|開発者エクスペリエンス|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md)、[.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md)、[Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md)、[Python](../../data-lake-store/data-lake-store-data-operations-python.md)、[PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)、[Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|一般公開 - [REST](/rest/api/storageservices/data-lake-storage-gen2)、[.NET](data-lake-storage-directory-file-acl-dotnet.md)、[Java](data-lake-storage-directory-file-acl-java.md)、[Python](data-lake-storage-directory-file-acl-python.md)<br>パブリック プレビュー - [JavaScript](data-lake-storage-directory-file-acl-javascript.md)、[PowerShell](data-lake-storage-directory-file-acl-powershell.md)、[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|リソース ログ|クラシック ログ<br>[Azure Monitor 統合](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[クラシック ログ](../common/storage-analytics-logging.md) - 一般公開<br>[Azure Monitor 統合](monitor-blob-storage.md) – プレビュー|
|エコシステム|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)、[Azure Databricks (3.1 以降)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html)、[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)、[ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6、4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)、[Azure Databricks (5.1 以降)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)、[Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md)、[ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1 から Gen2 へのパターン

移行パターンを選択し、必要に応じてそのパターンを変更します。

|||
|---|---|
|**リフト アンド シフト**|最も簡単なパターンです。 データ パイプラインがダウンタイムを許容できる場合に最適です。|
|**増分コピー**|"*リフト アンド シフト*" に似ていますが、ダウンタイムは少なくなります。 コピーに時間がかかる大量のデータに適しています。|
|**デュアル パイプライン**|ダウンタイムをまったく許容できないパイプラインに最適です。|
|**双方向同期**|"*デュアル パイプライン*" に似ていますが、より複雑なパイプラインに適した、いっそう段階的なアプローチです。|

各パターンについて詳しく説明します。
 
### <a name="lift-and-shift-pattern"></a>リフト アンド シフト パターン

これは最も簡単なパターンです。 

1. Gen1 へのすべての書き込みを停止します。

2. Gen1 から Gen2 にデータを移動します。 [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md) をお勧めします。 ACL はデータと共にコピーします。

3. インジェスト操作とワークロードで Gen2 をポイントします。

4. Gen1 の使用を停止します。

リフト アンド シフト パターンのサンプル コードについては、[リフト アンド シフト移行のサンプル](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md)を参照してください。

> [!div class="mx-imgBorder"]
> ![リフト アンド シフト パターン](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>リフト アンド シフト パターンを使用する場合の考慮事項

:heavy_check_mark:すべてのワークロードを Gen1 から Gen2 に同時に切り替えます。

:heavy_check_mark:移行と切り替えの期間中にダウンタイムが予想されます。

:heavy_check_mark:ダウンタイムを許容できるパイプラインに最適であり、すべてのアプリを一度にアップグレードできます。

### <a name="incremental-copy-pattern"></a>増分コピー パターン

1. Gen1 から Gen2 にデータを移動を始めます。 [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md) をお勧めします。 ACL はデータと共にコピーします。

2. Gen1 から新しいデータを増分コピーします。

3. すべてのデータがコピーされたら、Gen1 へのすべての書き込みを停止し、ワークロードで Gen2 をポイントします。

4. Gen1 の使用を停止します。

増分コピー パターンのサンプル コードについては、[増分コピー移行のサンプル](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md)を参照してください。


> [!div class="mx-imgBorder"]
> ![増分コピー パターン](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>増分コピー パターンを使用する場合の考慮事項:

:heavy_check_mark:すべてのワークロードを Gen1 から Gen2 に同時に切り替えます。

:heavy_check_mark:切り替え期間中にのみダウンタイムが予想されます。

:heavy_check_mark:すべてのアプリが一度にアップグレードされるパイプラインに最適ですが、データのコピーにはより多くの時間が必要です。

### <a name="dual-pipeline-pattern"></a>デュアル パイプライン パターン

1. Gen1 から Gen2 にデータを移動します。 [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md) をお勧めします。 ACL はデータと共にコピーします。

2. Gen1 と Gen2 の両方に新しいデータを取り込みます。

3. ワークロードで Gen2 をポイントします。

4. Gen1 へのすべての書き込みを停止し、Gen1 の使用を停止します。

デュアル パイプライン パターンのサンプル コードについては、[デュアル パイプライン移行のサンプル](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md)を参照してください。

> [!div class="mx-imgBorder"]
> ![デュアル パイプライン パターン](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>デュアル パイプライン パターンを使用する場合の考慮事項:

:heavy_check_mark:Gen1 パイプラインと Gen2 パイプラインがサイドバイサイドで実行されます。

:heavy_check_mark:ゼロ ダウンタイムをサポートします。

:heavy_check_mark:ワークロードとアプリケーションでダウンタイムを許容できず、両方のストレージ アカウントに取り込むことができる場合に適しています。

### <a name="bi-directional-sync-pattern"></a>双方向同期パターン

1. Gen1 と Gen2 の間に双方向のレプリケーションを設定します。 [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/) をお勧めします。 既存のデータに修復機能が提供されます。

3. すべての移動が完了したら、Gen1 へのすべての書き込みを停止し、双方向レプリケーションをオフにします。

4. Gen1 の使用を停止します。

双方向同期パターンのサンプル コードについては、[双方向同期移行のサンプル](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md)を参照してください。

> [!div class="mx-imgBorder"]
> ![双方向パターン](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>双方向同期パターンを使用する場合の考慮事項:

:heavy_check_mark:段階的なアプローチがより効果的な、多数のパイプラインと依存関係が関係する複雑なシナリオに適しています。  

:heavy_check_mark:移行作業は多くなりますが、Gen1 と Gen2 に対してサイドバイサイドのサポートが提供されます。

## <a name="next-steps"></a>次のステップ

- ストレージ アカウントに対するセキュリティの設定のさまざまな部分について学習します。 [Azure Storage セキュリティ ガイド](./security-recommendations.md)に関する記事をご覧ください。
- Data Lake Store のパフォーマンスを最適化します。 「[パフォーマンス用に Azure Data Lake Storage Gen2 を最適化する](data-lake-storage-performance-tuning-guidance.md)」をご覧ください
- Data Lake Store の管理に関するベスト プラクティスを確認します。 「[Azure Data Lake Storage Gen2 の使用に関するベスト プラクティス](data-lake-storage-best-practices.md)」をご覧ください