---
title: Azure Cosmos DB の MongoDB 用 API の概要
description: Azure Cosmos DB の MongoDB 用 API を使用して、Azure Cosmos DB で膨大な量のデータを格納し、それに対してクエリを実行する方法について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 03/02/2021
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5820592bf06cc9427e12aa0cd79c54dc1f0156e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101657997"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB](introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散型マルチモデル データベース サービスです。 Azure Cosmos DB は、[ターン キー グローバル分散](distribute-data-globally.md)、[スループットとストレージの世界規模でのエラスティック スケーリング](partitioning-overview.md)、99 パーセンタイルの 1 桁ミリ秒の待機時間を提供し、高可用性を保証します。これらはすべて[業界最高レベルの SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によってサポートされています。 Azure Cosmos DB は、[データのインデックスを自動的に作成](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)します。スキーマとインデックスの管理に対処する必要はありません。 Azure Cosmos DB はマルチモデルであり、ドキュメント、キーと値、グラフ、列指向の各データ モデルをサポートします。 Azure Cosmos DB サービスは、Cassandra、MongoDB、Gremlin、Azure Table Storage など、一般的な NoSQL API 向けのワイヤ プロトコルを実装しています。 これにより、使い慣れた NoSQL クライアント ドライバーとツールを使用して、Cosmos データベースを操作できます。

> [!NOTE]
> Azure Cosmos DB の MongoDB 用 API で[サーバーレス容量モード](serverless.md)が利用できるようになりました。

## <a name="wire-protocol-compatibility"></a>ワイヤ プロトコルの互換性

Azure Cosmos DB には MongoDB 用のワイヤ プロトコルが実装されています。 この実装により、ネイティブの MongoDB クライアント SDK、ドライバー、およびツールとの透過的な互換性が実現されます。 Azure Cosmos DB では、MongoDB データベース エンジンをホストします。 MongoDB でサポートされている機能の詳細については、次を参照してください。 
- [Azure Cosmos DB の Mongo DB 用 API バージョン 4.0](mongodb-feature-support-40.md)
- [Azure Cosmos DB の Mongo DB 用 API バージョン 3.6](mongodb-feature-support-36.md)

既定では、Azure Cosmos DB の MongoDB 用 API を使用して作成された新しいアカウントは、MongoDB ワイヤ プロトコルのバージョン 3.6 と互換性があります。 これらのプロトコル バージョンを認識する MongoDB クライアント ドライバーはすべて、Cosmos DB にネイティブに接続できます。

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Azure Cosmos DB の MongoDB 用 API" border="false":::

## <a name="key-benefits"></a>主な利点

フル マネージドかつグローバルに分散されたサービスとしてのデータベースである Cosmos DB の主な利点は[こちら](introduction.md)で説明しています。 さらに、Cosmos DB では、人気の高い NoSQL API のワイヤ プロトコルをネイティブ実装することで、次のような利点も実現しています。

* アプリケーション ロジックの重要な部分を保持しながら、Cosmos DB にアプリケーションを簡単に移行できます。
* アプリケーションの移植性を保持して、クラウド ベンダーに非依存な状態を維持できます。
* Cosmos DB を利用した一般的な NoSQL API 向けに、業界最高レベルの、ご利用料金に基づく SLA が手に入ります。
* Cosmos データベースのプロビジョニング済みのスループットと容量を、ニーズに応じてエラスティックにスケーリングでき、お支払いは必要なスループットとストレージの分のみとなります。 これにより、大幅にコストを削減できます。
* マルチリージョン書き込みレプリケーションによるターンキーのグローバル分散が可能になります。

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB の MongoDB 用 API

クイックスタートに従って Azure Cosmos アカウントを作成し、既存の MongoDB アプリケーションを移行して Azure Cosmos DB を使用するか、新しいアプリケーションを構築します。

* [既存の MongoDB Node.js Web アプリを移行する](create-mongodb-nodejs.md)
* [Azure Cosmos DB の MongoDB 用 API と .NET SDK を使用して Web アプリを構築する](create-mongodb-dotnet.md)
* [Azure Cosmos DB の MongoDB 用 API と Java SDK を使用してコンソール アプリを構築する](create-mongodb-java.md)

## <a name="next-steps"></a>次のステップ

使用し始めるためのいくつかのヒントを次に示します。

* チュートリアル「[Azure Cosmos DB への MongoDB アプリケーションの接続](connect-mongodb-account.md)」に従って、アカウントの接続文字列の情報を取得する方法について学習します。
* [Azure Cosmos DB での Studio 3T の使用](mongodb-mongochef.md)に関するチュートリアルに従って、Studio 3T で Cosmos データベースと MongoDB アプリの間の接続を作成する方法を学習します。
* [Azure Cosmos DB への MongoDB データのインポート](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)に関するチュートリアルに従って、データを Cosmos データベースにインポートします。
* [Robo 3T](mongodb-robomongo.md) を使用して Cosmos アカウントに接続します。
* [グローバル分散アプリの読み取り設定を構成する](../cosmos-db/tutorial-global-distribution-mongodb.md)方法について学習します。
* よく見られるエラーの解決策については、[トラブルシューティング ガイド](mongodb-troubleshoot.md)を参照してください


<sup>注意事項: この記事では、MongoDB データベースとのワイヤ プロトコルの互換性を提供する Azure Cosmos DB の機能について説明します。Microsoft は、このサービスを提供するための MongoDB データベースの運営は行いません。Azure Cosmos DB は MongoDB, Inc. には所属していません。</sup>
