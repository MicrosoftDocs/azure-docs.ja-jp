---
title: Azure Cosmos DB の MongoDB 用 API へのデータ移行の移行前手順
description: このドキュメントでは、MongoDB から Cosmos DB にデータを移行する前提条件の概要について説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anfeldma
ms.openlocfilehash: cdc5dc9cee3520d9a3f22ff710dfa193e6ef4fed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553291"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB から Azure Cosmos DB の MongoDB 用 API へのデータ移行の移行前手順
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> この MongoDB の移行前ガイドは、MongoDB の Azure Cosmos DB Mongo API への大規模な移行に関するシリーズの最初のガイドです。 自己管理型インフラストラクチャで MongoDB のライセンスを取得してデプロイしているお客様は、従量課金制の価格と柔軟なスケーラビリティを備えた Azure Cosmos DB などのマネージド クラウド サービスに移行することで、データ資産のコストの削減と管理ができます。 このシリーズの目的は、移行プロセスを通じてお客様を支援することです。
>
> 1. [移行前](mongodb-pre-migration.md) - 既存の MongoDB データ資産のインベントリを作成し、移行を計画して、適切な移行ツールを選択します。
> 2. 実行 - 提供されている[チュートリアル]()を使用して、MongoDB から Azure Cosmos DB に移行します。
> 3. [移行後](mongodb-post-migration.md) - 既存のアプリケーションを更新して最適化し、新しい Azure Cosmos DB データ資産に対して実行します。
>

堅実な移行前計画は、チームの移行の適時性と成功に大きな影響を与える可能性があります。 移行前の良い例えは、新しいプロジェクトを開始することです。要件を定義することから始めて、関連するタスクの概要を作成し、最初に取り組むべき最大のタスクに優先順位を付けることもできます。 これは、プロジェクト スケジュールを予測可能にするのに役立ちますが、もちろん、予期しない要件が発生し、プロジェクト スケジュールが複雑になる可能性もあります。 移行に戻る - 移行前フェーズで包括的な実行プランを作成することで、プロセスの後半で予期しない移行タスクが見つかる可能性が最小限に抑えられ、移行中の時間が短縮され、目標を確実に達成できるようになります。

(オンプレミスまたはクラウド内の) MongoDB から Azure Cosmos DB の MongoDB 用 API にデータを移行する前に、以下のことを行う必要があります。

1. [Azure Cosmos DB の MongoDB 用 API の使用に関する重要な考慮事項の参照](#considerations)
2. [データを移行するためのオプションの選択](#options)
3. [ワークロードに必要なスループットの見積もり](#estimate-throughput)
4. [データに最適なパーティション キーの選択](#partitioning)
5. [データに設定できるインデックス作成ポリシーの理解](#indexing)

上記の移行の前提条件が既に完了している場合は、[Azure Database Migration Service を使用して、Azure Cosmos DB の MongoDB 用 API に MongoDB データを移行](../dms/tutorial-mongodb-cosmos-db.md)することができます。 また、アカウントを作成していない場合は、アカウントを作成する手順を示す[クイックスタート](create-mongodb-dotnet.md)のいずれかを参照できます。

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Azure Cosmos DB の MongoDB 用 API を使用する場合の考慮事項

以下は、Azure Cosmos DB の MongoDB 用 API に関する具体的な特性です。

- **容量モデル**:Azure Cosmos DB のデータベース容量は、スループットベースのモデルに基づいています。 このモデルは[要求ユニット/秒](request-units.md)に基づいています。これは、1 秒ごとにコレクションに対して実行できるデータベース操作の数を表す単位です。 この容量は、[データベースまたはコレクション レベル](set-throughput.md)で割り当てることができます。また、割り当てモデルに対してプロビジョニングすることも、[自動スケーリングでプロビジョニングされたスループット](provision-throughput-autoscale.md)を使用してプロビジョニングすることもできます。

- **要求ユニット**:すべてのデータベース操作には、Azure Cosmos DB に関連付けられた要求ユニット (RU) コストがあります。 実行すると、指定された秒で使用可能な要求ユニット レベルからこれが減算されます。 要求に、現在割り当てられている RU/秒よりも多くの RU が必要な場合、問題を解決するための 2 つの選択肢があります。つまり、RU の量を増やすか、次の秒が開始されるまで待機してから操作を再試行します。

- **エラスティック容量**:特定のコレクションまたはデータベースの容量は、いつでも変更できます。 これにより、データベースをワークロードのスループット要件に柔軟に適応させることができます。

- **自動シャーディング**:Azure Cosmos DB では、シャード (またはパーティション キー) のみを必要とする自動パーティション分割システムが提供されます。 [自動パーティション分割メカニズム](partitioning-overview.md)は、すべての Azure Cosmos DB API 間で共有されます。これにより、水平分布を通じて、シームレスなデータおよびスループットのスケーリングが可能になります。

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Azure Cosmos DB の MongoDB 用 API の移行オプション

[Azure Cosmos DB の MongoDB 用 API の Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) では、フル マネージド ホスティング プラットフォーム、移行の監視オプションおよび自動調整処理を提供することで、データの移行を簡略化するメカニズムが提供されます。 オプションの完全な一覧を以下に示します。

|**移行の種類**|**ソリューション**|**考慮事項**|
|---------|---------|---------|
|オンライン|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します <br/>&bull; 大規模なデータセットに適し、ライブ変更のレプリケーションを処理します <br/>&bull; 他の MongoDB ソースでのみ機能します|
|オフライン|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します <br/>&bull; 大規模なデータセットに適し、ライブ変更のレプリケーションを処理します <br/>&bull; 他の MongoDB ソースでのみ機能します|
|オフライン|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; セットアップが簡単で、さまざまなソースをサポートします <br/>&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します <br/>&bull; 大規模なデータセットに適しています <br/>&bull; チェックポイントがなく、移行の途中で問題が発生した場合、移行プロセスを全部やり直す必要があります<br/>&bull; 配信不能キューがなく、エラーが含まれるファイルがいくつかあると、移行プロセス全体が停止することがあります <br/>&bull; 特定のデータ ソースの読み取りスループットを向上させるためのカスタム コードが必要です|
|オフライン|[既存の Mongo ツール (mongodump、mongorestore、Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; セットアップと統合が簡単 <br/>&bull; スロットルのカスタム処理が必要です|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> ワークロードに必要なスループットの見積もり

Azure Cosmos DB では、スループットは事前にプロビジョニングされ、1 秒あたりの要求ユニット (RU) で測定されます。 VM やオンプレミス サーバーとは異なり、RU はいつでも簡単にスケールアップしたりスケールダウンしたりすることができます。 プロビジョニングされた RU の数をすぐに変更することができます。 詳細については、「[Azure Cosmos DB の要求ユニット](request-units.md)」を参照してください。

[Azure Cosmos DB Capacity Calculator](https://cosmos.azure.com/capacitycalculator/) を使用すると、データベース アカウントの構成、データの量、ドキュメント サイズ、1 秒あたりの必要な読み取りと書き込みに基づいて、要求ユニットの量を確認できます。

必要な RU 数に影響する主な要因は、次のとおりです。
- **ドキュメント サイズ**:アイテム/ドキュメントのサイズが増加すると、そのアイテム/ドキュメントの読み書きに消費される RU 数も増加します。

- **ドキュメント プロパティの数**: ドキュメントの作成または更新で消費される RU の数は、そのプロパティの数、複雑さ、長さに関連します。 [インデックス付きのプロパティ数を制限する](mongodb-indexing.md)と、書き込み操作で消費される要求ユニットを削減できます。

- **クエリのパターン**:クエリが複雑であると、そのクエリで消費される要求ユニット数に影響します。 

クエリのコストを理解する最良の方法は、Azure Cosmos DB でサンプル データを使用し、`getLastRequestStastistics` コマンドを使って [MongoDB Shell からサンプル クエリを実行](connect-mongodb-account.md)して、消費される RU の数を出力する、要求の料金を取得することです。

`db.runCommand({getLastRequestStatistics: 1})`

このコマンドでは、以下のような JSON ドキュメントが出力されます。

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

[診断設定](cosmosdb-monitor-resource-logs.md)を使用して、Azure Cosmos DB に対して実行されるクエリの頻度とパターンを理解することもできます。 診断ログの結果は、ストレージ アカウント、EventHub インスタンスまたは [Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) に送信できます。  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>パーティション キーの選択
パーティション分割 (シャーディングともいう) は、データを移行する前に考慮すべき重要な点です。 Azure Cosmos DB では、フルマネージド パーティション分割を使用して、ストレージとスループットの要件を満たすためにデータベースの容量を増やします。 この機能では、ルーティング サーバーのホストや構成は必要ありません。   

同様の方法で、パーティション分割機能によって自動的に容量が追加され、それに応じてデータが再調整されます。 データに適したパーティション キーの選択に関する詳細および推奨事項については、「[パーティション キーの選択](partitioning-overview.md#choose-partitionkey)」記事を参照してください。 

## <a name="index-your-data"></a><a id="indexing"></a>データのインデックス作成

MongoDB サーバー バージョン 3.6 以降を対象とする Azure Cosmos DB の API では、`_id` フィールドのみ、インデックスが自動的に作成されます。 このフィールドは削除できません。 シャード キーごとに `_id` フィールドの一意性が自動的に適用されます。 その他のフィールドのインデックスを作成するには、[MongoDB インデックス管理コマンド](mongodb-indexing.md)を適用します。 この既定のインデックス作成ポリシーは Azure Cosmos DB の SQL API とは異なり、既定ですべてのフィールドのインデックスが作成されます。

Azure Cosmos DB によって提供されるインデックス作成機能には、複合インデックス、一意のインデックス、Time-to-Live (TTL) インデックスの追加が含まれます。 インデックス管理インターフェイスは、`createIndex()` コマンドにマップされます。 詳細については、[Azure Cosmos DB の MongoDB 用 API でのインデックス作成](mongodb-indexing.md)に関する記事を参照してください。

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) では、一意のインデックスがある MongoDB のコレクションは自動的に移行されます。 ただし、一意のインデックスは移行前に作成する必要があります。 Azure Cosmos DB では、コレクションにデータが既にある場合、一意のインデックスの作成をサポートしていません。 詳細については、[Azure Cosmos DB における一意のキー](unique-keys.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
* [Database Migration Service を使用してお使いの MongoDB データを Cosmos DB に移行する。](../dms/tutorial-mongodb-cosmos-db.md) 
* [Azure Cosmos のコンテナーとデータベースにスループットをプロビジョニングする](set-throughput.md)
* [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
* [Azure Cosmos DB でのグローバル分散](distribute-data-globally.md)
* [Azure Cosmos DB のインデックス作成](index-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
