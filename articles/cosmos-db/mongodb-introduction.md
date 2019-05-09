---
title: Azure Cosmos DB の MongoDB 用 API の概要
description: Azure Cosmos DB の MongoDB 用 API を使用して、Azure Cosmos DB で膨大な量のデータを格納し、それに対してクエリを実行する方法について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: d0f61afaba094a1e499a91f9937a31554438759e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042174"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API

[Azure Cosmos DB](introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散型マルチモデル データベース サービスです。 Azure Cosmos DB は、[ターン キー グローバル分散](distribute-data-globally.md)、[スループットとストレージの世界規模でのエラスティック スケーリング](partition-data.md)、99 パーセンタイルの 1 桁ミリ秒の待機時間を提供し、高可用性を保証します。これらはすべて[業界最高レベルの SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によってサポートされています。 Azure Cosmos DB は、[データのインデックスを自動的に作成](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)します。スキーマとインデックスの管理に対処する必要はありません。 Azure Cosmos DB はマルチモデルであり、ドキュメント、キーと値、グラフ、列指向の各データ モデルをサポートします。 既定では、SQL API を使用して Cosmos DB を操作できます。 さらに、Cosmos DB サービスは、Cassandra、MongoDB、Gremlin、Azure Table Storage など、一般的な NoSQL API 向けのワイヤ プロトコルを実装しています。 これにより、使い慣れた NoSQL クライアント ドライバーとツールを使用して、Cosmos データベースを操作できます。

## <a name="wire-protocol-compatibility"></a>ワイヤ プロトコルの互換性

Azure Cosmos DB は、Cassandra、MongoDB、Gremlin、Azure Table Storage など、一般的な NoSQL データベース向けのワイヤ プロトコルを実装しています。 Cosmos DB 内部で直接かつ効率的にワイヤ プロトコルのネイティブ実装が提供されているため、NoSQL データベースの既存のクライアント SDK、ドライバー、ツールで透過的に Cosmos DB を操作できます。 Cosmos DB では、任意の NoSQL データベース向けにワイヤ互換性のある API を提供するためにデータベースのソース コードを使用することは一切ありません。

既定では、Azure Cosmos DB の MongoDB 用 API は、MongoDB のワイヤ プロトコルのバージョン 3.2 と互換性があります。 現在、ワイヤ プロトコルのバージョン 3.4 で追加された機能やクエリ演算子は、プレビュー機能として使用できます。 これらのプロトコル バージョンを認識する MongoDB クライアント ドライバーはすべて、Cosmos DB にネイティブに接続できる必要があります。

![Azure Cosmos DB の MongoDB 用 API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

## <a name="key-benefits"></a>主な利点

フル マネージドかつグローバルに分散されたサービスとしてのデータベースである Cosmos DB の主な利点は[こちら](introduction.md)で説明しています。 さらに、Cosmos DB では、人気の高い NoSQL API のワイヤ プロトコルをネイティブ実装することで、次のような利点も実現しています。

* アプリケーション ロジックの重要な部分を保持しながら、Cosmos DB にアプリケーションを簡単に移行できます。
* アプリケーションの移植性を保持して、クラウド ベンダーに非依存な状態を維持できます。
* Cosmos DB を利用した一般的な NoSQL API 向けに、業界最高レベルの、ご利用料金に基づく SLA が手に入ります。
* Cosmos データベースのプロビジョニング済みのスループットと容量を、ニーズに応じてエラスティックにスケーリングでき、お支払いは必要なスループットとストレージの分のみとなります。 これにより、大幅にコストを削減できます。
* マルチマスター レプリケーションによるターンキーのグローバル分散が可能になります。

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB の MongoDB 用 API

クイック スタートに従って Cosmos アカウントを作成し、既存の MongoDB アプリケーションを移行して Azure Cosmos DB を使用するか、新しいアプリケーションを構築します。

* [既存の MongoDB Node.js Web アプリを移行する](create-mongodb-nodejs.md)
* [Azure Cosmos DB の MongoDB 用 API と .NET SDK を使用して Web アプリを構築する](create-mongodb-dotnet.md)
* [Azure Cosmos DB の MongoDB 用 API と Java SDK を使用してコンソール アプリを構築する](create-mongodb-java.md)

## <a name="next-steps"></a>次の手順

使用し始めるためのいくつかのヒントを次に示します。

* チュートリアル「[Azure Cosmos DB への MongoDB アプリケーションの接続](connect-mongodb-account.md)」に従って、アカウントの接続文字列の情報を取得する方法について学習します。
* [Azure Cosmos DB での Studio 3T の使用](mongodb-mongochef.md)に関するチュートリアルに従って、Studio 3T で Cosmos データベースと MongoDB アプリの間の接続を作成する方法を学習します。
* [Azure Cosmos DB への MongoDB データのインポート](mongodb-migrate.md)に関するチュートリアルに従って、データを Cosmos データベースにインポートします。
* [Robo 3T](mongodb-robomongo.md) を使用して Cosmos アカウントに接続します。
* [グローバル分散アプリの読み取り設定を構成する](../cosmos-db/tutorial-global-distribution-mongodb.md)方法について学習します。

<sup>注:この記事では、MongoDB データベースとのワイヤ プロトコルの互換性を提供する Azure Cosmos DB の機能について説明します。Microsoft は、このサービスを提供するための MongoDB データベースの運営は行いません。Azure Cosmos DB は MongoDB, Inc. には所属していません。</sup>
