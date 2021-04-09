---
title: Azure Cosmos DB の Table API についてよく寄せられる質問
description: Azure Cosmos DB の Table API についてよく寄せられる質問とその回答を紹介します
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: c5404c026385f9ebf06c4f0289a4db0bf944d172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341238"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Azure Cosmos DB の Table API についてよく寄せられる質問
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB Table API は [Azure portal](https://portal.azure.com) で利用できます。まず、Azure サブスクリプションにサインアップする必要があります。 サインアップしたら、Azure サブスクリプションに Azure Cosmos DB Table API アカウントを追加し、アカウントにテーブルを追加できます。 サポートされている言語および関連するクイックスタートは、「[Azure Cosmos DB の概要: Table API](table-introduction.md)」で確認できます。

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Azure Cosmos DB の Table API と Azure Table Storage

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Table API と Azure Table Storage の動作はどのような点が異なりますか?

これまで Azure Table Storage を使っていたユーザーが Azure Cosmos DB Table API でテーブルを作成する場合、知っておく必要のある動作の違いがいくつかあります。

* Azure Cosmos DB Table API ではパフォーマンスを保証するために予約容量モデルが使われていますが、これは、テーブルが作成されると直ちに、たとえ容量が使われていない場合でも、容量に対して課金されることを意味します。 Azure Table Storage では、使用された容量に対してのみ課金されます。 Table API では 99 パーセンタイルで 10 ミリ秒の読み取り SLA と 15 ミリ秒の書き込み SLA が提供されるのに対し、Azure Table Storage で提供される SLA が 10 秒であるのは、このためです。 ただし、その結果として、Table API のテーブルでは、要求が含まれない空のテーブルであっても、Azure Cosmos DB が提供する SLA でテーブルへの要求を処理できる容量を確保するためにコストがかかります。

* Table API によって返されるクエリ結果は、Azure Table Storage のようなパーティション キー/行キーの順序にはなりません。

* 行キーに許される最大長は 255 バイトです。

* バッチに含めることができるのは最大 2 MB です。

* CORS は現在サポートされていません。

* Azure Table Storage ではテーブル名の大文字と小文字は区別されませんが、Azure Cosmos DB Table API では区別されます。

* 現在、バイナリ フィールドなどのエンコード情報に対する Azure Cosmos DB の内部形式の一部は、それほど効率的ではありません。 そのため、データ サイズの予期しない制限が発生する可能性があります。 たとえば、現在、エンコードによってデータ サイズが増加するため、フル 1 メガのテーブル エンティティを使用してバイナリ データを格納することはできません。

* エンティティ プロパティ名 "ID" は現在サポートされていません。

* TableQuery TakeCount は 1,000 に制限されません。

* REST API に関しては、Azure Cosmos DB Table API によってサポートされないエンドポイント/クエリ オプションがいくつかあります。

  | REST メソッド | REST エンドポイント/クエリ オプション | ドキュメントの URL | 説明 |
  | ------------| ------------- | ---------- | ----------- |
  | GET、PUT | `/?restype=service@comp=properties`| 「[Set Table Service Properties](/rest/api/storageservices/set-table-service-properties)」(Table Service のプロパティを設定する) および「[Get Table Service Properties](/rest/api/storageservices/get-table-service-properties)」(Table Service のプロパティを取得する) | このエンドポイントは、CORS ルールの設定、ストレージ分析の構成、ログ記録の設定に使われます。 CORS は現在サポートされておらず、Azure Cosmos DB での分析とログ記録の処理は Azure Storage Table とは異なります。 |
  | OPTIONS | `/<table-resource-name>` | 「[Pre-flight CORS table request](/rest/api/storageservices/preflight-table-request)」(プレフライト CORS テーブル要求) | これは、Azure Cosmos DB が現在サポートしていない CORS の一部です。 |
  | GET | `/?restype=service@comp=stats` | 「[Get Table Service Stats](/rest/api/storageservices/get-table-service-stats)」(Table Service の統計情報を取得する) | プライマリとセカンダリの間でデータがレプリケートされる速度の情報を提供します。 Cosmos DB ではレプリケーションは書き込みの一部なので、これは必要ありません。 |
  | GET、PUT | `/mytable?comp=acl` | 「[Get Table ACL](/rest/api/storageservices/get-table-acl)」(テーブルの ACL を取得する) および[Set Table ACL](/rest/api/storageservices/set-table-acl)」(テーブルの ACL を設定する) | Shared Access Signature (SAS) の管理に使われる保存されたアクセス ポリシーを取得および設定します。 SAS はサポートされていますが、設定と管理の方法は異なります。 |

* Azure Cosmos DB Table API は JSON 形式のみをサポートし、ATOM はサポートしません。

* Azure Cosmos DB は Shared Access Signature (SAS) をサポートしていますが、一部のポリシーはサポートされていません。具体的には、新しいテーブルを作成する権限などの管理操作に関連するものです。

* 特に .NET SDK には、Azure Cosmos DB が現在サポートしていないクラスとメソッドがいくつかあります。

  | クラス | サポートされていないメソッド |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties* |
  |                  | \*ServiceStats* |
  | CloudTable | SetPermissions* |
  |            | GetPermissions* |
  | TableServiceContext | * (このクラスは非推奨です) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>その他のよく寄せられる質問

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Table API を使うには新しい SDK が必要ですか?

いいえ、既存のストレージ SDK でまだ動作するはずです。 ただし、最善のサポートと、多くの場合に優れたパフォーマンスを得るために、常に最新の SDK を使うことをお勧めします。 使うことができる言語の一覧については、「[Azure Cosmos DB の概要: Table API](table-introduction.md)」をご覧ください。

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Table API に接続するときに使う必要がある接続文字列を教えてください。

接続文字列は次のとおりです。

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

接続文字列は、Azure Portal の [接続文字列] ページから取得できます。

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Table API の .NET SDK で、要求オプションの構成設定をオーバーライドするにはどうすればよいですか?

一部の設定は CreateCloudTableClient メソッドで処理され、他の設定はクライアント アプリケーションの appSettings セクションの app.config で処理されます。 構成設定については、「[Azure Cosmos DB の機能](tutorial-develop-table-dotnet.md)」をご覧ください。

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>既存の Azure Table Storage SDK を使っている顧客に関する変更はありますか?

[なし] : 既存の Azure Table Storage SDK を使用する既存または新規のお客様を対象とする変更はありません。

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Table API で使うために、Azure Cosmos DB に格納されているテーブル データを表示するにはどうすればよいですか?

Azure Portal を使用してデータを参照できます。 また、Table API コードまたは次の回答で説明するツールを使うこともできます。

### <a name="which-tools-work-with-the-table-api"></a>Table API で動作するのはどのツールですか?

[Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) を使うことができます。

以前に指定した形式の接続文字列を取得する柔軟性を備えたツールは、新しい Table API に対応できます。 テーブル ツールの一覧については、「[Azure Storage クライアント ツール](../storage/common/storage-explorers.md)」をご覧ください。

### <a name="is-the-concurrency-on-operations-controlled"></a>操作のコンカレンシーは制御されますか?

はい。オプティミスティック コンカレンシーは、ETag メカニズムを使用して提供されます。

### <a name="is-the-odata-query-model-supported-for-entities"></a>エンティティの OData クエリ モデルはサポートされていますか?

はい。Table API は、OData クエリと LINQ クエリをサポートしています。

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>同じアプリケーションで Azure Table Storage と Azure Cosmos DB Table API に同時に接続できますか?

はい。同時に接続するには、CloudTableClient の 2 つのインスタンスを作成し、各インスタンスで接続文字列を使用して独自の URI を参照します。

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>既存の Azure Table Storage アプリケーションをこのサービスに移行するにはどうすればよいですか?

[AzCopy](../storage/common/storage-use-azcopy-v10.md) と [Azure Cosmos DB データ移行ツール](import-data.md)の両方がサポートされています。

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>たとえば、最初は *n* GB のデータが時間の経過と共に 1 TB に増加した場合、このサービスではストレージ サイズはどのように拡張されるのですか?

Azure Cosmos DB は、水平スケーリングを使用して無制限のストレージを提供するように設計されています。 このサービスは、ストレージを監視し、ストレージを効率的に増やすことができます。

### <a name="how-do-i-monitor-the-table-api-offering"></a>Table API を監視するにはどうすればよいですか?

Table API の **[メトリック]** ウィンドウを使って、要求とストレージ使用量を監視できます。

### <a name="how-do-i-calculate-the-throughput-i-require"></a>必要なスループットを計算するにはどうすればよいですか?

容量見積もりツールを使用して、操作に必要な TableThroughput を計算できます。 詳細については、「[Estimate Request Units and Data Storage (要求ユニットとデータ ストレージの見積もり)](https://www.documentdb.com/capacityplanner)」をご覧ください。 一般に、エンティティを JSON として示し、操作数を指定します。

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>エミュレーターで Table API SDK をローカルに使うことができますか?

現時点ではありません。

### <a name="can-my-existing-application-work-with-the-table-api"></a>既存のアプリケーションを Table API で動作させることはできますか?

はい。同じ API がサポートされています。

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Table API の機能を使わない場合、既存の Azure Table Storage アプリケーションを SDK に移行する必要はありますか?

いいえ。中断することなく、Azure Table Storage 資産を作成したり、既存の Azure Table Storage 資産を使用したりできます。 ただし、Table API を使わない場合、自動インデックス、追加の整合性オプション、またはグローバル分散によるメリットは得られません。

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Table API で、Azure の複数のリージョン間でのデータのレプリケーションを追加するにはどうすればよいですか?

Azure Cosmos DB ポータルの[グローバル レプリケーション設定](tutorial-global-distribution-sql-api.md#portal)を使用して、アプリケーションに適したリージョンを追加できます。 グローバル分散型のアプリケーションを開発するには、低待機時間の読み取りを実現するために、PreferredLocation 情報をローカル リージョンに設定したアプリケーションを追加する必要があります。

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Table API でアカウントのプライマリ書き込みリージョンを変更するにはどうすればよいですか?

Azure Cosmos DB のグローバル レプリケーション ポータル ウィンドウを使用してリージョンを追加し、必要なリージョンにフェールオーバーできます。 手順については、[複数リージョンの Azure Cosmos DB アカウントを使用した開発](high-availability.md)に関する記事をご覧ください。

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>データを分散するときに低待機時間を実現するために、優先読み取りリージョンを構成するにはどうすればよいですか?

ローカルの場所から読み取りを実行できるようにするには、app.config ファイルの PreferredLocation キーを使用します。 既存のアプリケーションの場合、LocationMode が設定されていると、Table API はエラーをスローします。 Table API は app.config ファイルからこの情報を取得するので、該当のコードを削除してください。 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Table API の整合レベルについてどのように考えればよいですか?

Azure Cosmos DB では、整合性、可用性、待機時間の最適なトレードオフを提供します。 Azure Cosmos DB には 5 つの整合性レベルが用意されているので、Table API 開発者は、データを照会するときにテーブル レベルで最適な整合性モデルを選び、個々の要求を行うことができます。 クライアントは、接続時に整合性レベルを指定できます。 レベルは、CreateCloudTableClient の consistencyLevel 引数を使って変更できます。

Table API では、既定で有界整合性制約の整合性が適用され、"自身の書き込みの読み取り" によって低待機時間の読み取りを実現します。 詳細については、[整合性レベル](consistency-levels.md)に関する記事をご覧ください。

既定では、Azure Standard Table はリージョン内では厳密な整合性を提供し、セカンダリの場所では最終的な整合性を提供します。

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB Table API では、Azure Table Storage より多くの整合性レベルが提供されますか?

はい。Azure Cosmos DB の分散特性のメリットを得る方法については、「[一貫性レベル](consistency-levels.md)」をご覧ください。 整合性レベルは保証の対象となるため、安心して使用できます。

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>グローバル分散を有効にした場合、データのレプリケーションにどのくらいの時間がかかりますか?

Azure Cosmos DB では、データはローカル リージョンで永続的にコミットされ、わずか数ミリ秒ですぐに他のリージョンにプッシュされます。 このレプリケーションは、データ センターのラウンドトリップ時間 (RTT) にのみ依存します。 Azure Cosmos DB のグローバル分散機能の詳細については、[Azure Cosmos DB:Azure のグローバル分散データベース サービス](distribute-data-globally.md)に関する記事をご覧ください。

### <a name="can-the-read-request-consistency-level-be-changed"></a>読み取り要求の整合性レベルを変更することはできますか?

Azure Cosmos DB では、コンテナー レベル (テーブル) で整合性レベルを設定できます。 レベルを変更するには、.NET SDK を使って、app.config ファイルで TableConsistencyLevel キーの値を指定します。 指定できる値は、Strong、Bounded Staleness、Session、Consistent Prefix、Eventual です。 詳細については、「[Azure Cosmos DB の調整可能なデータの一貫性レベル](consistency-levels.md)」をご覧ください。 基本的な考え方として、要求の整合性レベルは、テーブルの設定よりも高いレベルに設定することはできません。 たとえば、テーブルの整合性レベルを Eventual (最終的) に設定し、要求の整合性レベルを Strong (厳密) に設定することはできません。

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>リージョンがダウンした場合、Table API はフェールオーバーをどのように処理しますか?

Table API は、Azure Cosmos DB のグローバルに分散されたプラットフォームを利用します。 アプリケーションがデータ センターのダウンタイムを許容できるようにするには、Azure Cosmos DB ポータルでアカウントのリージョンを少なくとももう 1 つ有効にします ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](high-availability.md)に関する記事を参照)。 ポータルを使用してリージョンの優先順位を設定できます ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](high-availability.md)に関する記事を参照)。

アカウントのリージョンを必要な数だけ追加し、フェールオーバーの優先順位を指定してフェールオーバー先を制御できます。 データベースを使用するには、そのリージョンでもアプリケーションを提供する必要があります。 そうすれば、ダウンタイムが発生しなくなります。 [最新の .NET クライアント SDK](table-sdk-dotnet.md) は自動回帰しますが、他の SDK はしません。 つまり、ダウンしているリージョンを検出すると、新しいリージョンに自動的にフェールオーバーできます。

### <a name="is-the-table-api-enabled-for-backups"></a>Table API ではバックアップは有効になっていますか?

はい。Table API は、バックアップに Azure Cosmos DB のプラットフォームを利用します。 バックアップは自動的に作成されます。 詳細については、[Azure Cosmos DB でのオンライン バックアップと復元](online-backup-and-restore.md)に関する記事をご覧ください。

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Table API では、既定でエンティティのすべての属性のインデックスが作成されますか?

はい。エンティティのすべての属性のインデックスが既定で作成されます。 詳細については、「[Azure Cosmos DB でのインデックス作成ポリシー](index-policy.md)」をご覧ください。

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>つまり、クエリを満たすために複数のインデックスを作成する必要はないということですか?

はい。Azure Cosmos DB Table API はすべての属性の自動インデックス作成機能を備えています。スキーマ定義は不要です。 この自動化により、開発者はインデックスの作成と管理ではなく、アプリケーションに注力できるようになります。 詳細については、「[Azure Cosmos DB でのインデックス作成ポリシー](index-policy.md)」をご覧ください。

### <a name="can-i-change-the-indexing-policy"></a>インデックス作成ポリシーは変更できますか?

はい。インデックス定義を提供することでインデックス作成ポリシーを変更できます。 設定を適切にエンコードし、エスケープする必要があります。

.NET SDK がない場合、インデックス作成ポリシーを設定できる唯一の方法は次のとおりです。ポータルの **データ エクスプローラー** で、変更する特定のテーブルに移動し、 **[Scale & Settings]\(スケールと設定\)** > [インデックス作成ポリシー] で必要な変更を行って、 **[保存]** を選びます。

.NET SDK からは、app.config ファイルで送信できます。

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>プラットフォームとしての Azure Cosmos DB は、並べ替え、集計、階層などの多数の機能を備えているようですが、 これらの機能は Table API に追加される予定ですか?

Table API は Azure Table Storage と同じクエリ機能を提供します。 また、並べ替え、集計、地理空間クエリ、階層、さまざまな組み込み関数もサポートしています。 詳細については、[SQL クエリ](./sql-query-getting-started.md)に関する記事を参照してください。

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Table API の TableThroughput は、どのようなときに変更する必要がありますか?

次のいずれかの条件に該当する場合は、TableThroughput を変更してください。

* データの抽出、変換、読み込み (ETL) を実行している。または、短時間に大量のデータをアップロードする必要がある。
* バックエンドでコンテナーまたはコンテナーのセットのスループットを増やす必要がある (たとえば、使用されたスループットがプロビジョニング スループットを超えており、調整が行われている)。 詳細については、「[コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)」を参照してください。

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Table API のテーブルのスループットはスケールアップまたはスケールダウンできますか?

はい。スループットのスケーリングは、Azure Cosmos DB ポータルのスケール ウィンドウを使用して実行できます。 詳細については、[スループットの設定](set-throughput.md)に関する記事をご覧ください。

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>新しくプロビジョニングされたテーブルには、既定の TableThroughput が設定されるのですか?

はい。app.config で TableThroughput をオーバーライドしておらず、Azure Cosmos DB であらかじめ作成されているコンテナーを使用していない場合、スループットが 400 に設定されたテーブルが作成されます。

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Azure Table Storage サービスの既存の顧客を対象とする価格の変更はありますか?

[なし] : Azure Table Storage の既存のお客様を対象とする価格の変更はありません。

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Table API の料金はどのように計算されますか?

料金は、割り当てられた TableThroughput によって異なります。

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Table API では、テーブルのレート制限にどのように対処すればよいですか?

要求レートが基になるコンテナーまたはコンテナーのセットのプロビジョニング スループットの容量を超えると、エラーが発生し、SDK では再試行ポリシーの適用によって呼び出しが再試行されます。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Azure Cosmos DB の Table API サービスを利用するために、PartitionKey および RowKey とは別にスループットを選ぶ必要があるのはなぜですか?

Azure Cosmos DB では、app.config ファイルまたはポータルでコンテナーのスループットが指定されていない場合、既定のスループットが設定されます。

Azure Cosmos DB では、操作に上限を設定してパフォーマンスと待機時間を保証します。 この保証は、エンジンがテナントの操作にガバナンスを適用できる場合に可能になります。 TableThroughput を設定すると、プラットフォームでこの容量が予約され、操作が正常に完了することが保証されるので、保証されたスループットと待機時間が確保されます。

また、スループットの仕様により、スループットを弾力的に変更して、アプリケーションの季節性によるメリットを享受し、スループットのニーズを満たして、コストを削減できます。

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>データの保存にしか料金を支払っておらず、クエリを実行することはほとんどないため、Azure Table Storage は安価でした。 Azure Cosmos DB Table API では、トランザクションを 1 つも実行していない場合や何も保存していない場合でも課金されているようです。 説明できますか。

Azure Cosmos DB は、可用性、待機時間、スループットが保証された、グローバル分散型の SLA ベースのシステムとして設計されています。 他のシステムのスループットとは異なり、Azure Cosmos DB でスループットを予約すると、そのスループットが保証されます。 Azure Cosmos DB には、セカンダリ インデックスやグローバル分散など、お客様から要望があった追加機能が用意されています。

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Azure Table Storage にデータを取り込んだときに、(パーティションがいっぱいであることを示す) "クォータが上限に達した" ことを通知するメッセージが表示されたことは一度もありませんでした。 Table API では、このメッセージが表示されました。 このサービスには制限があり、既存のアプリケーションを変更しなければならないのでしょうか?

Azure Cosmos DB は、待機時間、スループット、可用性、整合性を保証し、無制限のスケールを提供する SLA ベースのシステムです。 保証された Premium パフォーマンスを確保するために、データ サイズとインデックスが管理可能であり、スケーラブルであることを確認してください。 パーティション キーごとのエンティティ数または項目数に 20 GB の制限を設けているのは、検索やクエリの優れたパフォーマンスを確実に提供するためです。 すべての情報を 1 つのパーティションに格納し、そのパーティションに対してクエリを実行すると、ホット パーティションになります。Azure Storage でもアプリケーションが適切にスケールできるように、ホット パーティションが発生 *しない* ようにすることをお勧めします。

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Table API で PartitionKey と RowKey が必要なのはそのためですか?

はい。 Table API の外部からアクセスできる領域は、Azure Table Storage SDK のその領域とほぼ同じであるため、パーティション キーによってデータを効率的に分散させることができます。 行キーはそのパーティション内で一意です。 行キーが存在する必要があり、Standard SDK の場合と同様に、行キーを null にすることはできません。 RowKey の長さは 255 バイト、PartitionKey の長さは 1 KB です。

### <a name="what-are-the-error-messages-for-the-table-api"></a>Table API のエラー メッセージはどのようなものですか?

Azure Table Storage と Azure Cosmos DB Table API は同じ SDK を使っているので、ほとんどのエラーは同じです。

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Table API で多数のテーブルを次々に作成しようとすると調整が行われるのはなぜですか?

Azure Cosmos DB は、待機時間、スループット、可用性、整合性を保証する SLA ベースのシステムです。 Azure Cosmos DB はプロビジョニングされるシステムであるため、これらの要件を保証するためにリソースが予約されています。 テーブルを次々に作成すると、その作成ペースが検出され、調整が行われます。 テーブルの作成ペースを確認し、1 分あたり 5 つ未満に抑えることをお勧めします。 Table API はプロビジョニングされるシステムであることに注意してください。 プロビジョニングした時点から料金が発生します。

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>SDK やバグに関するフィードバックを提供するにはどうすればよいですか?

次のいずれかの方法でフィードバックをお寄せください。

* [ユーザーの声](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Microsoft Q&A 質問ページ](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow は、プログラミングに関する質問に最適です。 質問が[的を得ており](https://stackoverflow.com/help/on-topic)、かつ[その質問を明確で回答可能なものにするようにできるだけ多くの詳細情報が含まれている](https://stackoverflow.com/help/how-to-ask)ことを確認してください。

## <a name="next-steps"></a>次のステップ

* [.NET SDK と Azure Cosmos DB で Table API アプリをビルドする](create-table-dotnet.md)
* [Azure Cosmos DB Table API データを管理する Java アプリを作成する](create-table-java.md)