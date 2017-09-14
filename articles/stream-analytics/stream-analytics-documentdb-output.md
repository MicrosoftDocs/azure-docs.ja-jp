---
title: "Stream Analytics の JSON 出力 | Microsoft Docs"
description: "Stream Analytics で、Azure Cosmos DB for JSON 出力をターゲットにして、構造化されていない JSON データに対するデータ アーカイブと待機時間の短いクエリを有効にする方法について説明します。"
keywords: "JSON 出力"
documentationcenter: 
services: stream-analytics,documentdb
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: cc80b0080c806541362a1ef2d71b95862bd51ca2
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>Stream Analytics からの JSON 出力に Azure Cosmos DB をターゲットにする
Stream Analytics では、 JSON 出力のターゲットを [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) にすることができるため、構造化されていない JSON データに対してデータ アーカイブと待機時間の短いクエリを有効にすることができます。 このドキュメントでは、この構成を実装するためのベスト プラクティスについて説明します。

Cosmos DB を詳しく理解していない場合は、開始する前に [Azure Cosmos DB のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)に関するページをご覧ください。 

注: Mongo DB API ベースの Cosmos DB コレクションは現在サポートされていません。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>出力ターゲットとしての Cosmos DB の基礎
Stream Analytics で Azure Cosmos DB 出力を使用すると、ストリーム処理の結果を JSON 出力として Cosmos DB コレクションに書き込むことができます。 Stream Analytics は、データベース内にコレクションを作成せず、代わりにユーザーが前もってコレクションを作成するように要求します。 これにより、Cosmos DB コレクションの課金によるコストがユーザーに対して明白になり、[Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用することでコレクションのパフォーマンス、整合性、容量などを直接調整できるようになりました。 ストリーミング ジョブのコレクションを論理的に分離するには、ストリーミング ジョブごとに 1 つの Cosmos DB データベースを使用することをお勧めします。

Cosmos DB コレクションの一部のオプションの詳細を以下に示します。

## <a name="tune-consistency-availability-and-latency"></a>調整の整合性、可用性、および待機時間
Cosmos DB では、アプリケーション要件を満たすために、データベースやコレクションを微調整し、整合性、可用性、待機時間の間で妥協点を見つけることができます。 読み取りおよび書き込みの待機時間に対してシナリオで求められる読み取りの一貫性レベルに応じて、データベース アカウントでの一貫性レベルを選択することができます。 また Cosmos DB では、コレクションへの各 CRUD 操作に対する同期インデックス作成も、既定で有効になっています。 この機能も、Cosmos DB で書き込みと読み取りのパフォーマンスを制御するための便利なオプションの 1 つです。 このトピックの詳細については、 [データベースとクエリの一貫性レベルの変更](../documentdb/documentdb-consistency-levels.md) に関する記事を参照してください。

## <a name="upserts-from-stream-analytics"></a>Stream Analytics からのアップサート
Stream Analytics を Cosmos DB と統合することで、特定のドキュメント ID 列に基づき、Cosmos DB コレクションでレコードを挿入または更新できるようになります。 この機能は、 *アップサート*とも呼ばれています。

Stream Analytics では、オプティミスティック アップサート手法を採用しています。この手法では、ドキュメント ID の競合が原因で挿入に失敗した場合にのみ更新が実行されます。 この更新は Stream Analytics によって PATCH として実行されるため、ドキュメントに対する部分更新が可能になります。つまり、新しいプロパティの追加や既存のプロパティの置き換えは段階的に実行されます。 JSON ドキュメント内の配列プロパティの値を変更すると、配列全体が上書きされることになるので注意してください。つまり、配列はマージされません。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB でのデータ パーティション分割
Cosmos DB [パーティション分割コレクション](../cosmos-db/partition-data.md)は、データをパーティション分割する場合にお勧めの方法です。 

単一の Cosmos DB コレクションの場合、Stream Analytics では引き続き、クエリ パターンとアプリケーションのパフォーマンス ニーズの両方に応じてデータをパーティション分割できます。 各コレクションに格納できるデータは最大 10 GB で、現時点ではコレクションをスケールアップ (またはオーバーフロー) する方法はありません。 スケールアウトの場合、Stream Analytics では、特定のプレフィックスを持つ複数のコレクションに書き込むことができます (使用方法の詳細については以下を参照)。 Stream Analytics では、ユーザー指定の PartitionKey 列に基づく一貫性のある [ハッシュ パーティション リゾルバー](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) 方式を使用し、出力レコードをパーティションに分割します。 ストリーミング ジョブの開始時における特定のプレフィックスを持つコレクションの数が出力パーティションの数として使用され、ジョブはその出力パーティションに並行して書き込みを行います (Cosmos DB コレクション = 出力パーティション)。 1 つのコレクションと遅延インデックス作成で挿入のみを実行する場合、予想される書き込みスループットは約 0.4 MB/秒です。 複数のコレクションを使用することで、スループットの向上と容量の増加が実現できます。

将来パーティション数を増やす予定がある場合は、ジョブを停止し、既存のコレクションから新しいコレクションへとデータの再パーティション分割を行った後、Stream Analytics ジョブを再開することが必要になる場合があります。 PartitionResolver の使用方法、再パーティション分割、サンプル コードなどの詳細は、フォローアップ記事に含まれる予定です。 この詳細については、[Cosmos DB でのパーティション分割とスケーリング](../documentdb/documentdb-partition-data.md)に関する記事でも説明しています。

## <a name="cosmos-db-settings-for-json-output"></a>JSON 出力の Cosmos DB 設定
Stream Analytics で Cosmos DB を出力として作成すると、情報の入力を求めるプロンプトが以下のように表示されます。 このセクションでは、各プロパティの定義について説明します。

パーティション分割コレクション | 複数の "単一パーティション" コレクション
---|---
![documentdb stream analytics 出力画面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![documentdb stream analytics 出力画面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> **複数の "単一パーティション" コレクション**のシナリオでは、パーティション キーが必要ですが、この構成はサポートされています。 

* **出力のエイリアス** – ASA クエリ内でこの出力を意味するエイリアス。  
* **アカウント名** – Cosmos DB アカウントの名前またはエンドポイント URI。  
* **アカウント キー** – Cosmos DB アカウントの共有アクセス キー。  
* **データベース** – Cosmos DB データベース名。  
* **コレクション名のパターン** – 使用するコレクションのコレクション名またはそのパターン。 コレクション名の形式は、オプションの {partition} トークンを使用して構成できます。この場合、パーティションは 0 から開始します。 有効な入力値のサンプルを次に示します。  
  1\) MyCollection – "MyCollection" という名前のコレクションが 1 つ必要です。  
  2\) MyCollection{partition} – "MyCollection0"、"MyCollection1"、"MyCollection2" などのコレクションが必要です。  
* **パーティション キー** – 省略可能。 コレクション名のパターンに {partition} トークンを使用している場合のみ必要です。 コレクション全体で出力をパーティション分割するためのキーを指定するために使用される、出力イベント内のフィールドの名前。 コレクションの出力が 1 つの場合は、PartitionId など、任意の出力列を使用できます。  
* **Document ID** – 省略可能です。 挿入操作または更新操作の基準となるプライマリ キーを指定するために使用される、出力イベント内のフィールドの名前。  

