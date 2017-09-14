---
title: "Azure Cosmos DB の概要: MongoDB 用 API | Microsoft Docs"
description: "人気のある OSS MongoDB API と Azure Cosmos DB を使用して、大量の JSON ドキュメントを低待機時間で格納およびクエリする方法を説明します。"
keywords: "MongoDB とは"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: anhoh
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4dbf91a3c1d6a287d7337647f9e059566c7ddbe5
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB の概要: MongoDB 用 API

[Azure Cosmos DB](../cosmos-db/introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散マルチモデル データベース サービスです。 Azure Cosmos DB は、[ターン キー グローバル分散](distribute-data-globally.md)、[スループットとストレージの世界規模でのエラスティック スケーリング](partition-data.md)、99 パーセンタイルの 1 桁ミリ秒の待機時間、[明確に定義された 5 種類の整合性レベル](consistency-levels.md)を提供し、高可用性を保証します。これらはすべて[業界最高レベルの SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によってサポートされています。 Azure Cosmos DB は、[データのインデックスを自動的に作成](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)します。スキーマとインデックスの管理に対処する必要はありません。 Azure Cosmos DB はマルチモデルであり、ドキュメント、キーと値、グラフ、列指向の各データ モデルをサポートします。 

![Azure Cosmos DB: MongoDB API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

[MongoDB](https://docs.mongodb.com/manual/introduction/) 向けに作成されたアプリのデータ ストアとして Cosmos DB データベースを使用できます。 つまり、既存の[ドライバー](https://docs.mongodb.org/ecosystem/drivers/)を使用することで、MongoDB 用に記述されたアプリケーションが Cosmos DB と通信し、MongoDB データベースではなく Cosmos DB データベースを使用できるようになりました。 多くの場合、接続文字列を変更するだけで、MongoDB から Cosmos DB に使用を切り替えることができます。 この機能を使用すると、既にある MongoDB のスキルとツールを無駄にすることなく、Azure Cosmos DB のグローバル配布と[包括的な業界トップレベルの SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) によって、MongoDB データベース アプリケーションを簡単に作成し、Azure クラウドで実行することができます。


## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>MongoDB 用の Azure Cosmos DB を使用するメリット

**スループットとストレージのスケールを柔軟に調整:** アプリケーションのニーズに合わせて MongoDB データベースを簡単にスケールアップまたはダウンできます。 データは SSD (Solid State Disk) に格納されるため、予測可能かつ低いレイテンシが期待できます。 Cosmos DB は、実質的に無制限のストレージ サイズとプロビジョニング スループットにスケールできる MongoDB コレクションをサポートしています。 アプリケーションの成長に合わせて Cosmos DB のスケールを臨機応変に拡張し、予測したとおりのパフォーマンスをシームレスに確保することができます。 

**複数リージョンのレプリケーション**: Cosmos DB は、MongoDB アカウントに関連付けられているすべてのリージョンにデータを透過的にレプリケートします。これにより、整合性、可用性、パフォーマンスを所定のレベルで確保して、これらのトレードオフを実現しながら、データへのグローバル アクセスを必要とするアプリケーションを開発できます。 Cosmos DB は、マルチホーミング API を使用した透過的なリージョン内フェールオーバーを提供します。また、スループットとストレージを世界規模で弾力的にスケーリングすることもできます。 詳細については、[データのグローバル分散](distribute-data-globally.md)に関するページを参照してください。

**MongoDB の互換性** : 既存の MongoDB の知識、アプリケーション コード、およびツールを使用できます。 MongoDB を使用してアプリケーションを開発し、グローバルに分散された完全管理型 Cosmos DB サービスを使用して運用環境にデプロイできます。

**サーバー管理が不要**: MongoDB データベースは管理およびスケールが不要です。 Cosmos DB は完全に管理されたサービスです。つまり、インフラストラクチャまたは仮想マシンを自分で管理する必要がありません。 Cosmos DB は、30 以上の [Azure リージョン](https://azure.microsoft.com/regions/services/)で使用できます。

**調整可能な整合性レベル:** 整合性とパフォーマンスの最適なトレードオフを実現するために、明確に定義された 5 つの整合性レベルの中から選択できます。 Cosmos DB では、クエリと読み取り操作に関して、厳密、有界整合性制約、セッション、一貫性のあるプレフィックス、結果の 5 種類の整合性レベルを提供します。 きめ細かな一貫性レベルが明確に定義されていることによって、一貫性、可用性、待機時間の最適なトレードオフを検討することができます。 詳細については、[整合性レベルを使用して可用性とパフォーマンスを最大化する方法](consistency-levels.md)に関するページを参照してください。

**自動インデックス作成:** 既定では、Cosmos DB は MongoDB データベースのドキュメント内のすべてのプロパティのインデックスを自動的に作成するので、スキーマや、セカンダリ インデックスの作成は不要です。

**エンタープライズ グレード** - Azure Cosmos DB では、ローカルとリージョンで障害が発生した場合に 99.99% の可用性とデータ保護を実現する、複数のローカル レプリカがサポートされます。 Azure Cosmos DB には、エンタープライズ グレードの[コンプライアンス認定](https://www.microsoft.com/trustcenter)とセキュリティ機能があります。 

詳細については、Scott Hanselman と Azure Cosmos DB プリンシパル エンジニアリング マネージャー Kirill Gavrylyuk による次の Azure Friday ビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Introducing-Azure-Cosmos-DB/player]
> 

## <a name="how-to-get-started"></a>ファースト ステップ

MongoDB のクイックスタートに従って、Cosmos DB アカウントを作成し、Cosmos DB を使用するために既存の MongoDB アプリケーションを移行するか、新しいアプリケーションを構築します。

* [既存の Node.js MongoDB Web アプリを移行する](create-mongodb-nodejs.md)。
* [.NET と Azure Portal による MongoDB API Web アプリの構築](create-mongodb-dotnet.md)
* [Java と Azure Portal による MongoDB API コンソール アプリの構築](create-mongodb-java.md)

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB の MongoDB API に関する情報は、Azure Cosmos DB ドキュメント全体に組み込まれていますが、開始するためのいくつかのヒントを次に示します。

* [MongoDB アカウントへの接続](connect-mongodb-account.md)に関するチュートリアルに従って、アカウントの接続文字列の情報を取得する方法について学習します。
* [Azure Cosmos DB での MongoChef の使用](mongodb-mongochef.md)に関するチュートリアルに従って、MongoChef で Azure Cosmos DB データベースと MongoDB アプリの接続を作成する方法について学習します。
* [MongoDB のプロトコル対応 Azure Cosmos DB へのデータ移行](mongodb-migrate.md) に関するチュートリアルに従って、データを MongoDB データベース用 API にインポートします。
* [Robomongo](mongodb-robomongo.md) を使用して、MongoDB 用 API アカウントに接続します。
* [GetLastRequestStatistics コマンドと Azure Portal のメトリック](request-units.md#GetLastRequestStatistics)を使用して、操作で使用している RU の数を確認する方法について学習します。
* [グローバル分散アプリの読み取り設定を構成する](../cosmos-db/tutorial-global-distribution-mongodb.md)方法について学習します。

