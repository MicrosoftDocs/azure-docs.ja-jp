---
title: Azure Cosmos DB サービスのクォータ
description: Azure Cosmos DB サービスのクォータとさまざまなリソースの種類に対する既定の制限。
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: f33490e273104105502ecc8f4f255c354a2d2f3b
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800025"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB サービスのクォータ

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

この記事では、Azure Cosmos DB の各種リソースに提供される既定のクォータの概要を説明します。

## <a name="storage-and-database-operations"></a>ストレージとデータベースの操作

サブスクリプションで Azure Cosmos アカウントを作成したら、[データベース、コンテナー、および項目を作成](account-databases-containers-items.md)することによってアカウント内のデータを管理できます。

### <a name="provisioned-throughput"></a>プロビジョニング スループット

スループットは、[要求ユニット (RU/秒 または RU)](request-units.md) の単位で、コンテナー レベルまたはデータベース レベルでプロビジョニングできます。 次の表は、コンテナー/データベースあたりのストレージとスループットの制限の一覧を示しています。

| リソース | 既定の制限 |
| --- | --- |
| コンテナーあたりの最大 RU ([専用スループット プロビジョニング モード](account-databases-containers-items.md#azure-cosmos-containers)) | 既定では 1,000,000。 これは、[Azure サポート チケットを提出する](create-support-request-quota-increase.md)ことによって増やすことができます |
| データベースあたりの最大 RU ([共有スループット プロビジョニング モード](account-databases-containers-items.md#azure-cosmos-containers)) | 既定では 1,000,000。 これは、[Azure サポート チケットを提出する](create-support-request-quota-increase.md)ことによって増やすことができます |
| (論理) パーティションあたりの最大 RU | 10,000 |
| すべての項目にわたる、(論理) パーティションあたりの最大ストレージ | 20 GB |
| 個別の (論理) パーティション キーの最大数 | 無制限 |
| コンテナーあたりの最大ストレージ | 無制限 |
| データベースあたりの最大ストレージ | 無制限 |
| アカウントあたりの添付ファイルの最大サイズ (添付ファイル機能は非推奨になってきています) | 2 GB |
| 1 GB あたりに必要な最小 RU/秒 | 10 RU/秒<br>**注:** お使いのアカウントが ["高ストレージ/低スループット" プログラム](set-throughput.md#high-storage-low-throughput-program)の対象である場合、この最小値を下げることができます。 |

> [!NOTE]
> ストレージまたはスループットにより高い制限が必要なパーティション キーを持つワークロードを管理するためのベスト プラクティスについては、「[合成パーティション キーの作成](synthetic-partition-keys.md)」を参照してください。

### <a name="minimum-throughput-limits"></a>最小スループットの制限

Cosmos コンテナー (または共有スループット データベース) には、400 RU/秒以上のスループットが必要です。 コンテナーのサイズが大きくなるにつれて、Cosmos DB では、データベースまたはコンテナーにその操作を行うための十分なリソースがあることを確認するために最小スループットが必要になります。

コンテナーまたはデータベースの現在のスループットと最小スループットは、Azure portal または SDK から取得できます。 詳細については、「[コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)」を参照してください。 

実際の最小 RU/秒は、アカウントの構成によって異なる場合があります。 [Azure Monitor メトリック](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db)を使用して、プロビジョニングされたスループット (RU/秒) とリソース上のストレージの履歴を表示できます。 

#### <a name="minimum-throughput-on-container"></a>コンテナーの最小スループット 

手動スループットでコンテナーに必要とされる最小スループットを見積もるには、次のようにして最大値を得ます。

* 400 RU/秒 
* 現在のストレージ (GB 単位) × 10 RU/秒
* コンテナーでプロビジョニングされた最高 RU ÷ 100

例:400 RU/秒と 0 GB のストレージでプロビジョニングされたコンテナーがあるとします。 スループットを 50,000 RU/秒に増やし、20 GB のデータをインポートします。 この時点で、最小 RU/秒は `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500 RU/秒になります。 時間の経過と共に、ストレージは 200 GB まで増えました。 この時点で、最小 RU/秒は `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000 RU/秒になります。 

**注:** お使いのアカウントが ["高ストレージ/低スループット" プログラム](set-throughput.md#high-storage-low-throughput-program)の対象である場合は、ストレージの GB あたりの最小スループット 10 RU/秒を下げることができます。

#### <a name="minimum-throughput-on-shared-throughput-database"></a>共有スループット データベースでの最小スループット 
手動スループットで共有スループット データベースに必要とされる最小スループットを見積もるには、次のようにして最大値を得ます。

* 400 RU/秒 
* 現在のストレージ (GB 単位) × 10 RU/秒
* データベースでプロビジョニングされた最高 RU ÷ 100
* 400 + MAX(コンテナー数 - 25, 0) * 100 RU/秒

例:400 RU/秒、15 GB のストレージ、10 個のコンテナーでプロビジョニングされたデータベースがあるとします。 この時点で、最小 RU/秒は `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400 RU/秒になります。 データベースに 30 個のコンテナーがあった場合、最小 RU/秒は `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 RU/秒になります。 

**注:** お使いのアカウントが ["高ストレージ/低スループット" プログラム](set-throughput.md#high-storage-low-throughput-program)の対象である場合は、ストレージの GB あたりの最小スループット 10 RU/秒を下げることができます。

要約すると、最小プロビジョニング済み RU の制限を次に示します。 

| リソース | 既定の制限 |
| --- | --- |
| コンテナーあたりの最小 RU ([専用スループット プロビジョニング モード](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 |
| データベースあたりの最小 RU ([共有スループット プロビジョニング モード](./account-databases-containers-items.md#azure-cosmos-containers)) | 最初の 25 個のコンテナーに対して 400 RU/秒。 後で各コンテナーに 100 RU/s を追加。 |

Cosmos DB は、SDK またはポータルを経由した、コンテナーまたはデータベースあたりのスループット (RU) のプログラムによるスケーリングをサポートしています。    

プロビジョニングされた現在の RU/秒とリソースの設定によっては、最小 RU/秒から最大で 100 倍の最小 RU まで、各リソースを同期的かつ即座に拡張できます。 要求されたスループット値がこの範囲外である場合、スケーリングは非同期的に実行されます。 非同期のスケーリングは、要求されたスループットやコンテナー内のデータ ストレージ サイズに応じて、完了するまでに数分～数時間かかることがあります。  

### <a name="serverless"></a>サーバーレス

[サーバーレス](serverless.md)では、使用量に基づく方法で Azure Cosmos DB リソースを使用できます。 次の表は、コンテナー/データベースあたりのストレージとスループットのバースト能力に関する制限の一覧を示しています。

| リソース | 制限 |
| --- | --- |
| (論理) パーティションあたりの最大 RU | 5,000 |
| すべての項目にわたる、(論理) パーティションあたりの最大ストレージ | 20 GB |
| 個別の (論理) パーティション キーの最大数 | 無制限 |
| コンテナーあたりの最大ストレージ | 50 GB |

## <a name="control-plane-operations"></a>コントロール プレーン操作

Azure portal、Azure PowerShell、Azure CLI、および Azure Resource Manager テンプレートを使用して、[Azure Cosmos アカウントをプロビジョニングおよび管理](how-to-manage-database-account.md)できます。 次の表は、サブスクリプション、アカウント、および操作の数あたりの制限の一覧を示しています。

| リソース | 既定の制限 |
| --- | --- |
| サブスクリプションあたりの最大データベース アカウント | 既定では 50。 これは、[Azure サポート チケットを提出する](create-support-request-quota-increase.md)ことによって増やすことができます|
| リージョン内フェールオーバーの最大数 | 既定では 1 回/時間。 これは、[Azure サポート チケットを提出する](create-support-request-quota-increase.md)ことによって増やすことができます|

> [!NOTE]
> リージョン内フェールオーバーは、単一リージョン書き込みのアカウントにのみ適用されます。 複数リージョン書き込みのアカウントには、書き込みリージョンの変更に関する制限は必要ないか、または存在しません。

Cosmos DB は、データのバックアップを一定の間隔で自動的に取得します。 バックアップ保有期間の間隔とウィンドウの詳細については、「[Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元](online-backup-and-restore.md)」を参照してください。

## <a name="per-account-limits"></a>アカウントあたりの制限

### <a name="provisioned-throughput"></a>プロビジョニング スループット

| リソース | 既定の制限 |
| --- | --- |
| データベースの最大数 | 無制限 |
| データベースあたりのコンテナーの最大数 (共有スループット) |25 |
| データベースまたはアカウントあたりのコンテナーの最大数 (専用スループット)  |無制限 |
| リージョンの最大数 | 制限なし (すべての Azure リージョン) |

### <a name="serverless"></a>サーバーレス

| リソース | 制限 |
| --- | --- |
| データベースの最大数 | 無制限 |
| アカウントあたりのコンテナーの最大数  | 100 |
| リージョンの最大数 | 1 (任意の Azure リージョン) |

## <a name="per-container-limits"></a>コンテナーあたりの制限

どの API を使用するかに応じて、Azure Cosmos コンテナーは、コレクション、テーブル、グラフのいずれかを表すことができます。 コンテナーは、[一意キー制約](unique-keys.md)、[ストアド プロシージャ、トリガー、UDF](stored-procedures-triggers-udfs.md)、および[インデックス作成ポリシー](how-to-manage-indexing-policy.md)の構成をサポートしています。 次の表は、コンテナー内の構成に固有の制限の一覧を示しています。 

| リソース | 既定の制限 |
| --- | --- |
| データベースまたはコンテナー名の最大長 | 255 |
| コンテナーあたりの最大ストアド プロシージャ | 100 <sup>*</sup>|
| コンテナーあたりの最大 UDF | 50 <sup>*</sup>|
| インデックス作成ポリシー内のパスの最大数| 100 <sup>*</sup>|
| コンテナーあたりの一意キーの最大数|10 <sup>*</sup>|
| 一意キー制約あたりのパスの最大数|16 <sup>*</sup>|
| 最大 TTL 値 |2147483647|

<sup>*</sup> これらのコンテナーあたりの制限はいずれも、[Azure サポート リクエスト](create-support-request-quota-increase.md)を作成することによって増やすことができます。

## <a name="per-item-limits"></a>項目あたりの制限

使用する API に応じて、Azure Cosmos 項目では、コレクション内のドキュメント、テーブル内の行、グラフ内のノードまたはエッジのいずれかを表すことができます。 次の表は、Cosmos DB での項目あたりの制限を示しています。 

| リソース | 既定の制限 |
| --- | --- |
| 項目の最大サイズ | 2 MB (JSON 表現の UTF-8 の長さ) |
| パーティション キー値の最大長 | 2048 バイト |
| ID 値の最大長 | 1023 バイト |
| 項目あたりのプロパティの最大数 | 実質的に無制限 |
| プロパティ名の最大長 | 実質的に無制限 |
| プロパティ値の最大長 | 実質的に無制限 |
| 文字列のプロパティ値の最大長 | 実質的に無制限 |
| 数値のプロパティ値の最大長 | IEEE754 倍精度 64 ビット |
| 埋め込みオブジェクト/配列の入れ子の最大レベル | 128 |
| 最大 TTL 値 |2147483647|

パーティション キー値と ID 値に関する長さの制限および 2 MB の全体的なサイズ制限を除き、プロパティの数や入れ子の深さなどの項目ペイロードに制限はありません。 RU の消費を削減するために、大きな、または複雑な項目構造を持つコンテナーのインデックス作成ポリシーを構成することが必要になる場合があります。 実際の例や大きな項目を管理するためのパターンについては、[Cosmos DB での項目のモデル化](how-to-model-partition-example.md)に関するページを参照してください。

## <a name="per-request-limits"></a>要求あたりの制限

Azure Cosmos DB は、コンテナー、項目、データベースなどのリソースに対して [CRUD とクエリ操作](/rest/api/cosmos-db/)をサポートしています。 また、コンテナー内の同じパーティション キーを持つ複数の項目に対する[トランザクション バッチ要求](/dotnet/api/microsoft.azure.cosmos.transactionalbatch)もサポートしています。

| リソース | 既定の制限 |
| --- | --- |
| 1 つの操作 (ストアド プロシージャの実行や 1 回のクエリ ページ取得など) の最大実行時間| 5 秒 |
| 最大要求サイズ (ストアド プロシージャ、CRUD など)| 2 MB |
| 最大応答サイズ (ページ分割されたクエリなど) | 4 MB |
| トランザクション バッチ内の操作の最大数 | 100 |

クエリなどの操作が実行タイムアウトまたは応答サイズの制限に達すると、その操作は、実行を再開するために結果のページと継続トークンをクライアントに返します。 1 つのクエリをページや継続にまたがって実行できる期間に実質的に制限はありません。

Cosmos DB は HMAC を承認のために使用します。 コンテナー、パーティション キー、項目などのリソースへのきめ細かなアクセス制御のために、主キーまたは[リソース トークン](secure-access-to-data.md)のどちらかを使用できます。 次の表は、Cosmos DB での承認トークンの制限の一覧を示しています。

| リソース | 既定の制限 |
| --- | --- |
| プライマリ トークンの最大有効期限 | 15 分  |
| リソース トークンの最小有効期限 | 10 分  |
| リソース トークンの最大有効期限 | 既定では 24 時間。 これは、[Azure サポート チケットを提出する](create-support-request-quota-increase.md)ことによって増やすことができます|
| トークン承認の最大クロック スキュー| 15 分 |

Cosmos DB は、書き込み中のトリガーの実行をサポートしています。 このサービスでは、書き込み操作あたり最大 1 つのプリトリガーと 1 つのポストトリガーがサポートされます。

## <a name="metadata-request-limits"></a>メタデータ要求の制限

Azure Cosmos DB は、各アカウントのシステム メタデータを保持します。 このメタデータを使用すると、コレクション、データベース、その他の Azure Cosmos DB リソース、およびそれらの構成を無料で列挙できます。

| リソース | 既定の制限 |
| --- | --- |
|1 分あたりの最大コレクション作成レート|    100|
|1 分あたりの最大データベース作成レート|    100|
|1 分あたりのプロビジョニングされたスループットの最大更新レート|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>自動スケーリングでプロビジョニングされたスループットの制限

自動スケーリングでのスループットとストレージの制限の詳細については、[自動スケーリング](provision-throughput-autoscale.md#autoscale-limits)および [FAQ](autoscale-faq.md#lowering-the-max-rus) に関する記事をご覧ください。

| リソース | 既定の制限 |
| --- | --- |
| システムをスケーリングできる最大 RU/秒 |  `Tmax`: ユーザーによって設定された自動スケーリングの最大 RU/秒|
| システムをスケーリングできる最小 RU/秒 | `0.1 * Tmax`|
| システムがスケーリングされる現在の RU/秒  |  `0.1*Tmax <= T <= Tmax`: 使用量に基づきます|
| 請求可能な時間あたり最小 RU/秒| `0.1 * Tmax` <br></br>課金は時間単位で行われます。この場合、システムがスケーリングされる時間あたり最大 RU/秒または `0.1*Tmax` のいずれか大きい方について課金されます。 |
| コンテナーの自動スケーリング最大 RU/秒の最小値  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`: 最も近い 1000 RU/秒に丸められます |
| データベースの自動スケーリング最大 RU/秒の最小値  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`: 最も近い 1000 RU/秒に丸められます。 <br></br>データベースに 25 個を超えるコンテナーがある場合は、自動スケーリング最大 RU/秒の最小値が、追加コンテナー 1 つにつき 1000 RU/秒ずつインクリメントされることにご注意ください。 たとえば、コンテナーが 30 個ある場合、設定できる自動スケーリング最大 RU/秒の最小値は 9000 RU/秒 (900 から 9000 RU/秒の間でスケーリング) です。

## <a name="sql-query-limits"></a>SQL クエリの制限

Cosmos DB は、[SQL](./sql-query-getting-started.md) を使用した項目のクエリをサポートしています。 次の表では、クエリ ステートメントでの制限 (句の数やクエリの長さなどの単位) について説明します。

| リソース | 既定の制限 |
| --- | --- |
| SQL クエリの最大長| 256 KB |
| クエリあたりの最大 JOIN| 5 <sup>*</sup>|
| クエリあたりの最大 UDF| 10 <sup>*</sup>|
| 多角形あたりの最大ポイント| 4096 |
| コンテナーあたりの含まれる最大パス| 500 |
| コンテナーあたりの除外される最大パス| 500 |
| 複合インデックスの最大プロパティ| 8 |

<sup>*</sup> これらの SQL クエリの制限はいずれも、[Azure サポート リクエスト](create-support-request-quota-increase.md)を作成することによって増やすことができます。

## <a name="mongodb-api-specific-limits"></a>MongoDB API に固有の制限

Cosmos DB は、MongoDB に対して記述されたアプリケーションのための MongoDB ワイヤ プロトコルをサポートしています。 サポートされるコマンドおよびプロトコル バージョンは、[サポートされる MongoDB の機能と構文](mongodb-feature-support.md)に関するページで見つけることができます。

次の表は、MongoDB 機能のサポートに固有の制限の一覧を示しています。 SQL (コア) API に関して説明されているその他のサービス制限も MongoDB API に適用されます。

| リソース | 既定の制限 |
| --- | --- |
| MongoDB クエリの最大メモリ サイズ (この制限は 3.2 サーバー バージョンにのみ適用されます) | 40 MB |
|MongoDB 操作の最大実行時間 (3.2 サーバー バージョンの場合)| 15 秒|
|MongoDB 操作の最大実行時間 (3.6 サーバー バージョンの場合)| 60 秒|
| サーバー側の接続を終了するためのアイドル状態の接続のタイムアウト* | 30 分 |

\* クライアント アプリケーションではドライバー設定内のアイドル状態の接続のタイムアウトを 2 から 3 分に設定することをお勧めします。これは、[Azure LoadBalancer の既定のタイムアウトが 4 分である](../load-balancer/load-balancer-tcp-idle-timeout.md)ためです。  このタイムアウトにより、クライアント マシンと Azure Cosmos DB 間の中間ロード バランサーによってアイドル状態の接続が閉じられないようになります。

## <a name="try-cosmos-db-free-limits"></a>Cosmos DB 無料試用版の制限

次の表は、[Azure Cosmos DB 無料試用版](https://azure.microsoft.com/try/cosmosdb/)の制限の一覧を示しています。

| リソース | 既定の制限 |
| --- | --- |
| 試用版の期間 | 30 日 (期限が切れた後は、新しい試用版を要求できます) <br> 有効期限が切れると、格納されている情報は削除されます。 |
| サブスクリプションあたりの最大コンテナー (SQL、Gremlin、Table API) | 1 |
| サブスクリプションあたりの最大コンテナー (MongoDB API) | 3 |
| コンテナーあたりの最大スループット | 5000 |
| 共有スループット データベースあたりの最大スループット | 20000 |
| アカウントあたりの最大合計ストレージ | 10 GB |

Cosmos DB 試用版は、米国中部、北ヨーロッパ、および東南アジア リージョンでのみグローバル分散をサポートしています。 Azure Cosmos DB 試用版アカウントに対して Azure サポート チケットを作成することはできません。 ただし、既存のサポート プランを所有するサブスクライバーにはサポートが提供されます。

## <a name="azure-cosmos-db-free-tier-account-limits"></a>Azure Cosmos DB Free レベル アカウントの制限

次の表は、[Azure Cosmos DB の Free レベルのアカウント](optimize-dev-test.md#azure-cosmos-db-free-tier)に対する制限を一覧表示しています。

| リソース | 既定の制限 |
| --- | --- |
| Azure サブスクリプションあたりの Free レベルのアカウント数 | 1 |
| Free レベル割引の期間 | アカウントの有効期間。 アカウントの作成時にオプトインする必要があります。 |
| Free の最大 RU/秒 | 400 RU/秒 |
| Free の最大ストレージ | 5 GB |
| 共有スループット データベースの最大数 | 5 |
| 共有スループット データベース内のコンテナーの最大数 | 25 <br>Free レベルのアカウントでは、最大 25 個のコンテナーを使用する共有スループット データベースの最小 RU/秒は 400 RU/秒です。 |

上記に加えて、[アカウントあたりの制限](#per-account-limits)も、Free レベルのアカウントに適用されます。

> [!NOTE]
> Azure Cosmos DB Free レベルは、Azure 無料アカウントとは異なります。 Azure 無料アカウントでは、Azure クレジットおよびリソースが一定の期間無料で提供されます。 この無料アカウントの一部として Azure Cosmos DB を使用する場合、25 GB のストレージと 400 RU/秒のプロビジョニング済みスループットが 12 か月間利用できます。

## <a name="next-steps"></a>次のステップ

Cosmos DB の中心概念である[グローバル分散](distribute-data-globally.md)、[パーティション分割](partitioning-overview.md)、および[プロビジョニング スループット](request-units.md)に関する記事を参照してください。

以下のクイック スタートのいずれかに従って、実際に Azure Cosmos DB を使ってみましょう。

* [Azure Cosmos DB SQL API を使ってみる](create-sql-api-dotnet.md)
* [Azure Cosmos DB の MongoDB 用 API の概要](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API を使ってみる](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API を使ってみる](create-graph-dotnet.md)
* [Azure Cosmos DB Table API を使ってみる](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)
