---
title: Azure Cosmos DB サービスのクォータ
description: Azure Cosmos DB サービスのクォータとさまざまなリソースの種類に対する既定の制限。
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 74df0038676e8459028084890da569ed3b75a682
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797281"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB サービスのクォータ

この記事では、Azure Cosmos DB の各種リソースに提供される既定のクォータの概要を説明します。

## <a name="storage-and-throughput"></a>ストレージとスループット

サブスクリプションで Azure Cosmos アカウントを作成したら、[データベース、コンテナー、および項目を作成](databases-containers-items.md)することによってアカウント内のデータを管理できます。 スループットは、[要求ユニット (RU/秒 または RU)](request-units.md) の単位で、コンテナー レベルまたはデータベース レベルでプロビジョニングできます。 次の表は、コンテナー/データベースあたりのストレージとスループットの制限の一覧を示しています。

| Resource | 既定の制限 |
| --- | --- |
| コンテナーあたりの最大 RU ([専用スループット プロビジョニング モード](databases-containers-items.md#azure-cosmos-containers)) | 既定では 1,000,000。 これは、[Azure サポート チケットを提出する](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)か、または [Ask Cosmos DB](mailto:askcosmosdb@microsoft.com) 経由で Microsoft に連絡することによって増やすことができます |
| データベースあたりの最大 RU ([共有スループット プロビジョニング モード](databases-containers-items.md#azure-cosmos-containers)) | 既定では 1,000,000。 これは、[Azure サポート チケットを提出する](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)か、または [Ask Cosmos DB](mailto:askcosmosdb@microsoft.com) 経由で Microsoft に連絡することによって増やすことができます |
| (論理) パーティション キーあたりの最大 RU | 10,000 |
| (論理) パーティション キーあたりのすべての項目にまたがる最大ストレージ| 10 GB |
| 個別の (論理) パーティション キーの最大数 | 無制限 |
| コンテナーあたりの最大ストレージ | 無制限 |
| データベースあたりの最大ストレージ | 無制限 |

> [!NOTE]
> ストレージまたはスループットのより高い制限が必要なパーティション キーを含むワークロードを管理するためのベスト プラクティスについては、[ホット パーティション キーの設計](synthetic-partition-keys.md)に関するページを参照してください。
>

Cosmos コンテナー (または共有スループット データベース) には、400 RU の最小スループットが必要です。 コンテナーが大きくなるにつれ、サポートされる最小スループットは次の要因にも依存するようになります。

* これまでにコンテナーにプロビジョニングされた最大スループット。 このサービスでは、コンテナーのスループットをプロビジョニングされた最大値の 10% まで下げることがサポートされます。 たとえば、スループットが 10000 RU まで増やされた場合、可能性のある最も低いプロビジョニング スループットは 1000 RU になります。
* これまでに共有スループット データベース内に作成したコンテナーの総数 (コンテナーあたり 100 RU で測定されます)。 たとえば、共有スループット データベース内に 5 つのコンテナーを作成した場合、スループットは少なくとも 500 RU である必要があります。

コンテナーまたはデータベースの現在のスループットと最小スループットは、Azure portal または SDK から取得できます。 詳細については、「[コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)」を参照してください。 要約すると、最小プロビジョニング済み RU の制限を次に示します。 

| Resource | 既定の制限 |
| --- | --- |
| コンテナーあたりの最小 RU ([専用スループット プロビジョニング モード](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| データベースあたりの最小 RU ([共有スループット プロビジョニング モード](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 共有スループット データベース内のコンテナーあたりの最小 RU | 100 |

Cosmos DB は、SDK またはポータルを経由した、コンテナーまたはデータベースあたりのスループット (RU) のエラスティック スケーリングをサポートしています。 各コンテナーは、10 ～ 100 倍 (最小値～最大値) のスケール範囲内で同期的に、かつ直ちにスケーリングできます。 要求されたスループット値がこの範囲外である場合、スケーリングは非同期的に実行されます。 非同期のスケーリングは、要求されたスループットやコンテナー内のデータ ストレージ サイズに応じて、完了するまでに数分～数時間かかることがあります。  

## <a name="control-plane-operations"></a>コントロール プレーン操作

Azure portal、Azure PowerShell、Azure CLI、および Azure Resource Manager テンプレートを使用して、[Azure Cosmos アカウントをプロビジョニングおよび管理](how-to-manage-database-account.md)できます。 次の表は、サブスクリプション、アカウント、および操作の数あたりの制限の一覧を示しています。

| Resource | 既定の制限 |
| --- | --- |
| サブスクリプションあたりの最大データベース アカウント | 既定では 50。 これは、[Azure サポート チケットを提出する](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)か、または [Ask Cosmos DB](mailto:askcosmosdb@microsoft.com) 経由で Microsoft に連絡することによって増やすことができます|
| リージョン内フェールオーバーの最大数 | 既定では 1 回/時間。 これは、[Azure サポート チケットを提出する](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)か、または [Ask Cosmos DB](mailto:askcosmosdb@microsoft.com) 経由で Microsoft に連絡することによって増やすことができます|

> [!NOTE]
> リージョン内フェールオーバーは、単一リージョン書き込みのアカウントにのみ適用されます。 複数リージョン書き込みのアカウントには、書き込みリージョンの変更に関する制限は必要ないか、または存在しません。

Cosmos DB は、データのバックアップを一定の間隔で自動的に取得します。 バックアップ保有期間の間隔とウィンドウの詳細については、「[Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元](online-backup-and-restore.md)」を参照してください。

## <a name="per-container-limits"></a>コンテナーあたりの制限

どの API を使用するかに応じて、Azure Cosmos コンテナーは、コレクション、テーブル、グラフのいずれかを表すことができます。 コンテナーは、[一意キー制約](unique-keys.md)、[ストアド プロシージャ、トリガー、UDF](stored-procedures-triggers-udfs.md)、および[インデックス作成ポリシー](how-to-manage-indexing-policy.md)の構成をサポートしています。 次の表は、コンテナー内の構成に固有の制限の一覧を示しています。 

| Resource | 既定の制限 |
| --- | --- |
| データベースまたはコンテナー名の最大長 | 255 |
| コンテナーあたりの最大ストアド プロシージャ | 100 <sup>*</sup>|
| コンテナーあたりの最大 UDF | 25 <sup>*</sup>|
| インデックス作成ポリシー内のパスの最大数| 100 <sup>*</sup>|
| コンテナーあたりの一意キーの最大数|10 <sup>*</sup>|
| 一意キー制約あたりのパスの最大数|16 <sup>*</sup>|

<sup>*</sup> これらのコンテナーあたりの制限はいずれも、Azure Support に連絡するか、または [Ask Cosmos DB](mailto:askcosmosdb@microsoft.com) 経由で Microsoft に連絡することによって増やすことができます。

## <a name="per-item-limits"></a>項目あたりの制限

どの API を使用するかに応じて、Azure Cosmos 項目は、コレクション内のドキュメント、テーブル内の行、グラフ内のノードまたはエッジのいずれかを表すことができます。 次の表は、Cosmos DB での項目あたりの制限を示しています。 

| Resource | 既定の制限 |
| --- | --- |
| 項目の最大サイズ | 2 MB (JSON 表現の UTF-8 の長さ) |
| パーティション キー値の最大長 | 2048 バイト |
| ID 値の最大長 | 1024 バイト |
| 項目あたりのプロパティの最大数 | 実質的に無制限 |
| 最大の入れ子の深さ | 実質的に無制限 |
| プロパティ名の最大長 | 実質的に無制限 |
| プロパティ値の最大長 | 実質的に無制限 |
| 文字列のプロパティ値の最大長 | 実質的に無制限 |
| 数値のプロパティ値の最大長 | IEEE754 倍精度 64 ビット |

パーティション キー値と ID 値に関する長さの制限および 2 MB の全体的なサイズ制限を除き、プロパティの数や入れ子の深さなどの項目ペイロードに制限はありません。 RU の消費を削減するために、大きな、または複雑な項目構造を持つコンテナーのインデックス作成ポリシーを構成することが必要になる場合があります。 実際の例や大きな項目を管理するためのパターンについては、[Cosmos DB での項目のモデル化](how-to-model-partition-example.md)に関するページを参照してください。

## <a name="per-request-limits"></a>要求あたりの制限

Cosmos DB は、コンテナー、項目、データベースなどのリソースに対して [CRUD とクエリ操作](https://docs.microsoft.com/rest/api/cosmos-db/)をサポートしています。  

| Resource | 既定の制限 |
| --- | --- |
| 1 つの操作 (ストアド プロシージャの実行や 1 回のクエリ ページ取得など) の最大実行時間| 5 秒 |
| 最大要求サイズ (ストアド プロシージャ、CRUD)| 2 MB |
| 最大応答サイズ (ページ分割されたクエリなど) | 4 MB |

クエリなどの操作が実行タイムアウトまたは応答サイズの制限に達すると、その操作は、実行を再開するために結果のページと継続トークンをクライアントに返します。 1 つのクエリをページや継続にまたがって実行できる期間に実質的に制限はありません。

Cosmos DB は HMAC を承認のために使用します。 コンテナー、パーティション キー、項目などのリソースへのきめ細かなアクセス制御のために、マスター キーまたは[リソース トークン](secure-access-to-data.md)のどちらかを使用できます。 次の表は、Cosmos DB での承認トークンの制限の一覧を示しています。

| Resource | 既定の制限 |
| --- | --- |
| マスター トークンの最大有効期限 | 15 分  |
| リソース トークンの最小有効期限 | 10 分  |
| リソース トークンの最大有効期限 | 既定では 24 時間。 これは、[Azure サポート チケットを提出する](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)か、または [Ask Cosmos DB](mailto:askcosmosdb@microsoft.com) 経由で Microsoft に連絡することによって増やすことができます|
| トークン承認の最大クロック スキュー| 15 分 |

Cosmos DB は、書き込み中のトリガーの実行をサポートしています。 このサービスでは、書き込み操作あたり最大 1 つのプリトリガーと 1 つのポストトリガーがサポートされます。 

## <a name="sql-query-limits"></a>SQL クエリの制限

Cosmos DB は、[SQL](how-to-sql-query.md) を使用した項目のクエリをサポートしています。 次の表では、クエリ ステートメントでの制限 (句の数やクエリの長さなどの単位) について説明します。

| Resource | 既定の制限 |
| --- | --- |
| SQL クエリの最大長| 256 KB <sup>*</sup>|
| クエリあたりの最大 JOIN| 5 <sup>*</sup>|
| クエリあたりの最大 AND| 2000 <sup>*</sup>|
| クエリあたりの最大 OR| 2000 <sup>*</sup>|
| クエリあたりの最大 UDF| 10 <sup>*</sup>|
| IN 式あたりの最大引数| 6000 <sup>*</sup>|
| 多角形あたりの最大ポイント| 4096 <sup>*</sup>|

<sup>*</sup> これらの SQL クエリの制限はいずれも、Azure Support に連絡するか、または [Ask Cosmos DB](mailto:askcosmosdb@microsoft.com) 経由で Microsoft に連絡することによって増やすことができます。

## <a name="mongodb-api-specific-limits"></a>MongoDB API に固有の制限

Cosmos DB は、MongoDB に対して記述されたアプリケーションのための MongoDB ワイヤ プロトコルをサポートしています。 サポートされるコマンドおよびプロトコル バージョンは、[サポートされる MongoDB の機能と構文](mongodb-feature-support.md)に関するページで見つけることができます。

次の表は、MongoDB 機能のサポートに固有の制限の一覧を示しています。 SQL (コア) API に関して説明されているその他のサービス制限も MongoDB API に適用されます。

| Resource | 既定の制限 |
| --- | --- |
| MongoDB クエリの最大メモリ サイズ | 40 MB |
| MongoDB 操作の最大実行時間| 30 秒 |

## <a name="try-cosmos-db-free-limits"></a>Cosmos DB 無料試用版の制限

次の表は、[Azure Cosmos DB 無料試用版](https://azure.microsoft.com/try/cosmosdb/)の制限の一覧を示しています。

| Resource | 既定の制限 |
| --- | --- |
| 試用版の期間 | 30 日 (何回でも更新できます) |
| サブスクリプションあたりの最大コンテナー (SQL、Gremlin、Table API) | 1 |
| サブスクリプションあたりの最大コンテナー (MongoDB API) | 3 |
| コンテナーあたりの最大スループット | 5000 |
| 共有スループット データベースあたりの最大スループット | 20000 |
| アカウントあたりの最大合計ストレージ | 10 GB |

Cosmos DB 試用版は、米国中部、北ヨーロッパ、および東南アジア リージョンでのみグローバル分散をサポートしています。 Azure Cosmos DB 試用版アカウントに対して Azure サポート チケットを作成することはできません。 ただし、既存のサポート プランを所有するサブスクライバーにはサポートが提供されます。

## <a name="next-steps"></a>次の手順

Cosmos DB の中心概念である[グローバル分散](distribute-data-globally.md)、[パーティション分割](partitioning-overview.md)、および[プロビジョニング スループット](request-units.md)に関する記事を参照してください。

以下のクイック スタートのいずれかに従って、実際に Azure Cosmos DB を使ってみましょう。

* [Azure Cosmos DB SQL API を使ってみる](create-sql-api-dotnet.md)
* [Azure Cosmos DB の MongoDB 用 API の概要](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API を使ってみる](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API を使ってみる](create-graph-dotnet.md)
* [Azure Cosmos DB Table API を使ってみる](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)
