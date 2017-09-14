---
title: "Azure Cosmos DB の概要 | Microsoft Docs"
description: "Azure Cosmos DB について説明します。 このグローバルに分散されたマルチモデル データベースは、待ち時間が少なく、柔軟なスケーラビリティを備え、高可用性を実現することを目指して構築されています。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/14/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: c9d04ae0bc11b99f893e5f003f136fbfe0dfccc9
ms.contentlocale: ja-jp
ms.lasthandoff: 08/11/2017

---

# <a name="welcome-to-azure-cosmos-db"></a>Azure Cosmos DB の概要

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB では、ボタンを 1 つクリックするだけで Azure のリージョンをいくつでもまたいでスループットとストレージを柔軟かつ個別にスケールできます。 このサービスは包括的な[サービス レベル アグリーメント](https://aka.ms/acdbsla) (SLA) により、スループット、待ち時間、可用性、整合性が保証されています。この点は、他のどのデータベース サービスにもないメリットです。

![Azure Cosmos DB は Microsoft のグローバルに分散されたデータベース サービスであり、柔軟なスケールアウト、短い待ち時間の保証、5 つの整合性モデル、包括的な保証を提供する SLA を特徴としています](./media/introduction/azure-cosmos-db.png)

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Azure Cosmos DB の恩恵を受けるソリューション

さまざまなデータについて、短い応答時間と[グローバル](distribute-data-globally.md)な規模で膨大な量の読み書きを処理する必要のある [Web、モバイル、ゲーム、IoT アプリケーション](use-cases.md)は、Azure Cosmos DB の [保証された](https://azure.microsoft.com/support/legal/sla/cosmos-db/)可用性、高スループット、短い待ち時間、調整可能な整合性の恩恵を受けます。

## <a name="key-capabilities"></a>主な機能
Azure Cosmos DB はグローバルに分散されたデータベース サービスであるため、スケーラブルで応答性に優れたアプリケーションの構築に役立つ次の機能が用意されています。

* **ターンキー グローバル配信**
    * [ボタンをクリック](tutorial-global-distribution-documentdb.md)して、任意の数の [Azure リージョン](https://azure.microsoft.com/regions/)に[データを配布](distribute-data-globally.md)できます。 これにより、ユーザーの近くにデータを配置でき、顧客の待ち時間を最小限に抑えることができます。 
    * Azure Cosmos DB のマルチホーム API を使用すると、アプリは最も近いリージョンを常に把握し、最も近いデータ センターに要求を送信します。 これはすべて構成を変更せずにできます。書き込みリージョンと必要な数の読み取りリージョンを設定し、残りは自動的に処理されます。

* **データに対するアクセスとクエリのため、各種のデータ モデルと人気の API をサポート**
    * Azure Cosmos DB の基盤となるアトム レコード シーケンス (ARS) ベースのデータ モデルでは、ドキュメント、グラフ、キーと値、テーブル、列指向の各データ モデルなど、複数のデータ モデルをネイティブでサポートします。
    * 次のデータ モデルの API は SDK でサポートされており、複数の言語で利用できます。
        * [DocumentDB API](documentdb-introduction.md)
        * [MongoDB API](mongodb-introduction.md)
        * [テーブル API](table-introduction.md)
        * [グラフ (Gremlin) API](graph-introduction.md)
        * 近日追加予定のデータ モデル 

* **世界中のどこからでも、必要なときにスループットとストレージを柔軟にスケール**
    * [秒単位](request-units.md)の粒度でデータベースのスループットを簡単にスケールし、必要なときにいつでも変更できます。 
    * ストレージ サイズを[透過的かつ自動的に](partition-data.md)スケールできるため、現在も将来も変わらずサイズに関する要件に対応できます。

* **応答性に優れたミッション クリティカルなアプリケーションを構築**
    * Azure Cosmos DB では、お客様に対してエンドツーエンドの待ち時間を 99% の確率で低水準にとどめることを保証しています。 
    * 一般的な 1 KB の項目であれば、エンドツーエンドの待ち時間は読み取りが 10 ミリ秒未満、インデックス付きの書き込みが 15 ミリ秒未満となる確率が 99% です (同じ Azure リージョンを対象とする場合に限ります)。 待ち時間の中央値はこれよりも大幅に低く、5 ミリ秒未満です。

* **"常時オン" の可用性の確保**
    * 1 リージョン内では、99.99% の可用性を実現しています。
    * いくつもの [Azure リージョン](https://azure.microsoft.com/regions)にデプロイすることによって、可用性をさらに高めることもできます。
    * データ損失ゼロを保証しているリージョンでは、[障害のシミュレーション](regional-failover.md)が可能です。 

* **グローバルに分散されたアプリケーションを正しく作成**
    * 5 種類の[整合性モデル](consistency-levels.md)により、SQL のような厳密な整合性から NoSQL のような結果整合性 (およびその 2 つの中間) に至るまで、幅広く対応できます。 
  
* **返金保証**
    * データを短時間で取得できない場合には、返金します。 
    * 可用性、待ち時間、スループット、整合性に関して[サービス レベル アグリーメント](https://aka.ms/acdbsla)を用意しています。 

* **データベース スキーマ/インデックスの管理が不要**
    * データベース スキーマやインデックスをアプリケーションのスキーマと同期させる必要はありません。 Azure Cosmos DB はスキーマ フリーです。 
    * Azure Cosmos DB のデータベース エンジンは、完全にスキーマ フリーとなっています。取り込んだデータにはすべて自動でインデックスが作成されるため、スキーマやインデックスの指定が求められることはありません。さらに、クエリもきわめて高速です。 

* **保有コストを削減**
    * 管理されていないソリューションの 5 ～ 10 倍の[コスト効率](https://aka.ms/cosmos-db-tco-paper)を実現しました。
    * コストは DynamoDB のわずか 3 分の 1 です。

## <a name="capability-comparison"></a>機能の比較

Azure Cosmos DB では、リレーショナル データベースと非リレーショナル データベースのどちらにも最適な機能を用意しています。

| 機能 | リレーショナル データベース   | 非リレーショナル (NoSQL) データベース |    Azure Cosmos DB |
| --- | --- | --- | --- |
| グローバル配信 | いいえ | いいえ | あり (30 以上のリージョンでターンキー配信、マルチホーム API を使用した場合)|
| 水平スケール | いいえ | あり | あり (ストレージとスループットを個別に調整できます) | 
| 待ち時間の保証 | いいえ | あり | あり (読み取りが 10 ミリ秒未満、書き込みが 15 ミリ秒未満となる確率が 99% です) | 
| 高可用性 | いいえ | あり | あり (Cosmos DB は常時オンで、PACELC のトレードオフがあり、自動および手動のフェールオーバー オプションを備えています)|
| データモデル + API | リレーショナル + SQL | マルチモデル + OSS API | マルチモデル + SQL + OSS API (近日追加予定) |
| SLA | あり | いいえ | あり (待ち時間、スループット、整合性、可用性に関する包括的 SLA) |


## <a name="next-steps"></a>次のステップ
以下のクイック スタートのいずれかに従って、実際に Azure Cosmos DB を使ってみましょう。

* [Azure Cosmos DB の DocumentDB API の概要](create-documentdb-dotnet.md)
* [Azure Cosmos DB の MongoDB API の概要](create-mongodb-nodejs.md)
* [Azure Cosmos DB の Graph API の概要](create-graph-dotnet.md)
* [Azure Cosmos DB の Table API の概要](create-table-dotnet.md)

