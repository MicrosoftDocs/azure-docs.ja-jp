---
title: Azure Cosmos DB Bulk Executor ライブラリの概要
description: Bulk Executor ライブラリによって提供される一括インポート API と一括更新 API を通じて、Azure Cosmos DB で一括操作を実行します。
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 211fc85f97069fcf3251048a074d625e777f8e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100475"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB Bulk Executor ライブラリの概要
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Azure Cosmos DB は高速で柔軟なグローバル分散データベース サービスであり、弾力的にスケールアウトして以下をサポートするように設計されています。 

* 読み取りと書き込みの大きなスループット (1 秒あたり数百万回の操作)。  
* 予測可能なミリ秒単位の待機時間での、大量のトランザクション データと運用データの格納 (数百テラバイトまたはそれ以上)。  

Bulk Executor ライブラリは、このような大きなスループットとストレージを利用するのに役立ちます。 Bulk Executor ライブラリを使うと、一括インポート API と一括更新 API を通じて、Azure Cosmos DB で一括操作を実行できます。 以下のセクションでは Bulk Executor ライブラリの機能についてさらに説明します。 

> [!NOTE] 
> 現時点では、Bulk Executor ライブラリはインポート操作と更新操作をサポートし、Azure Cosmos DB SQL API アカウントと Gremlin API アカウントによってのみサポートされます。

> [!IMPORTANT]
> バルク エグゼキューター ライブラリは現在、[サーバーレス](serverless.md) アカウントではサポートされていません。 .NET では、SDK の V3 バージョンで使用できる[一括サポート](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/)を使用することをお勧めします。
 
## <a name="key-features-of-the-bulk-executor-library"></a>Bulk Executor ライブラリの主な機能  
 
* コンテナーに割り当てられているスループットを最大限に利用するために必要なクライアント側計算リソースが大幅に減少します。 クライアント コンピューターの CPU を最大限に利用した状態で比較した場合、一括インポート API を使ってデータを書き込むシングル スレッド アプリケーションは、データを並列に書き込むマルチ スレッド アプリケーションの 10 倍の書き込みスループットを達成します。  

* ライブラリ内で効率的に処理することにより、要求レートの制限、要求タイムアウト、およびその他の一時的な例外を処理するアプリケーション ロジックを記述する単調なタスクを抽象化します。  

* 一括操作を実行するアプリケーションをスケールアウトするシンプルなメカニズムを提供します。Azure VM で実行する 1 つの Bulk Executor インスタンスは 500K RU/秒以上を消費でき、個々のクライアント VM にインスタンスを追加することでより高いスループットを実現できます。  
 
* スケールアウト アーキテクチャを使用することで、1 時間以内に 1 テラバイトを超えるデータを一括インポートできます。  

* Azure Cosmos コンテナー内の既存のデータを、パッチとして一括更新できます。 
 
## <a name="how-does-the-bulk-executor-operate"></a>Bulk Executor の動作方法 

ドキュメントをインポートまたは更新する一括操作がエンティティのバッチでトリガーされると、最初に、その Azure Cosmos DB パーティション キーの範囲に対応するバケットにシャッフルされます。 パーティション キーの範囲に対応する各バケット内では、バケットがミニバッチに分割され、各ミニバッチはサーバー側でコミットされるペイロードとして機能します。 Bulk Executor ライブラリには、これらのミニバッチをパーティション キーの範囲内と範囲間の両方で同時実行するための最適化が組み込まれています。 次の図では、Bulk Executor が異なるパーティション キーにデータをバッチ処理する方法を示しています。  

:::image type="content" source="./media/bulk-executor-overview/bulk-executor-architecture.png" alt-text="バルク エグゼキューターのアーキテクチャ" :::

Bulk Executor ライブラリは、コレクションに割り当てられているスループットを最大限に活用します。 Azure Cosmos DB の各パーティション キー範囲に対して  [AIMD スタイルの輻輳制御メカニズム](https://tools.ietf.org/html/rfc5681)を使用し、レートの制限とタイムアウトを効率的に処理します。 

## <a name="next-steps"></a>次の手順 
  
* [.NET](bulk-executor-dot-net.md) と [Java](bulk-executor-java.md) で Bulk Executor ライブラリを使用するサンプル アプリケーションを試して、さらに詳しく学習します。  
* [.NET](sql-api-sdk-bulk-executor-dot-net.md) と [Java](sql-api-sdk-bulk-executor-java.md) の Bulk Executor SDK 情報とリリース ノートを確認してください。
* Bulk Executor ライブラリは Cosmos DB Spark コネクタに統合されています。詳細については、[Azure Cosmos DB Spark コネクタ](spark-connector.md)に関する記事をご覧ください。  
* Bulk Executor ライブラリは、データをコピーするために Azure Data Factory の [Azure Cosmos DB コネクタ](../data-factory/connector-azure-cosmos-db.md)の新しいバージョンにも統合されています。
