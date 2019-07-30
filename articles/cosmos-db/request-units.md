---
title: Azure Cosmos DB での要求ユニットとスループット
description: Azure Cosmos DB で要求ユニットの要件を指定したり、推定したりする方法について説明します
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: a1143f912d894c1219de05b03a2338dc4e5bdc5f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467650"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB の要求ユニット

Azure Cosmos DB では、プロビジョニングするスループットおよび消費するストレージに対して時間単位で支払います。 Azure Cosmos データベースで常に十分なシステム リソースを確実に使用できるようにするには、スループットをプロビジョニングする必要があります。 [Azure Cosmos DB の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) かそれ以上を満たすのに十分なリソースが必要です。

Azure Cosmos DB では、多くの API (SQL、MongoDB、Cassandra、Gremlin、Table など) がサポートされています。 各 API には、固有のデータベース操作のセットがあります。 これらの操作の範囲は、単純なポイント読み取り/書き込みから、複雑なクエリまで及びます。 各データベース操作は、それらの操作の複雑さに基づいて、システム リソースを消費します。 

すべてのデータベース操作のコストは Azure Cosmos DB によって正規化され、"*要求ユニット*" (RU) によって表されます。 1 秒間の RU は、スループットのための通貨と考えることができます。 1 秒あたりの RU は、速度に基づく通貨です。 それにより、Azure Cosmos DB によってサポートされるデータベース操作を実行するために必要な CPU、IOPS、メモリなどのシステム リソースが抽象化されます。 

1 KB のアイテムを読み取るコストは 1 要求ユニット (つまり 1 RU) です。 その他のすべてのデータベース操作にも、同様に RU を使用してコストが割り当てられます。 Azure Cosmos コンテナーの操作に使用する API に関係なく、コストは RU によって測定されます。 書き込み、読み取り、クエリのどのデータベース操作でも、コストは常に RU で測定されます。

次の図では、RU の高度な概念を示します。

![データベース操作による要求ユニットの消費](./media/request-units/request-units.png)

容量を管理および計画するために、Azure Cosmos DB では、特定のデータセットに対する特定のデータベース操作の RU の数が決定的であることが確認されます。 すべてのデータベース操作によって消費される RU の数を追跡するには、応答ヘッダーを調べます。 [RU 使用量に影響を与える要因](request-units.md#request-unit-considerations)とアプリケーションのスループット要件を理解したら、費用対効果の高い方法でアプリケーションを実行できます。

アプリケーションの RU の数は秒単位でプロビジョニングします (増分は 100 RU/秒)。 アプリケーション用にプロビジョニングされたスループットをスケーリングするには、いつでも RU の値を増やしたり減らしたりできます。 増減の変更は 100 RU 単位で行うことができます。 変更は、プログラムまたは Azure portal を使用して行うことができます。 課金は時間単位です。

次の 2 つの個別の細分性でスループットをプロビジョニングできます。 

* **コンテナー**:詳しくは、「[Azure Cosmos コンテナー上でのスループットをプロビジョニングする](how-to-provision-container-throughput.md)」をご覧ください。
* **データベース**: 詳しくは、「[Azure Cosmos データベース上でのスループットをプロビジョニングする](how-to-provision-database-throughput.md)」をご覧ください。

## <a name="request-unit-considerations"></a>要求ユニットの考慮事項

プロビジョニングする 1 秒あたりの RU 数を推定するときは、次の要因を検討します。

* **アイテムのサイズ**:アイテムのサイズが増加すると、そのアイテムの読み取りまたは書き込みのために消費される RU の数も増加します。

* **アイテムのインデックス付け**:既定では、アイテムごとにインデックスが自動的に作成されます。 コンテナー内の一部のアイテムにインデックスを作成しないように選択すると、消費される RU は少なくなります。

* **アイテム プロパティの数**:すべてのプロパティで既定のインデックス作成を実行した場合、アイテム プロパティの数が増加すると、アイテムを書き込むために消費される RU の数が増加します。

* **インデックス付きプロパティ**:各コンテナーのインデックス ポリシーによって、既定でインデックスが作成されるプロパティが決まります。 書き込み操作の RU 消費量を削減するには、インデックス付きプロパティの数を制限します。

* **データの一貫性**:強力な有界整合性制約の整合性レベルでは、他の緩和された整合性レベルの場合と比べて、読み取り操作の実行中に約 2 倍の RU が消費されます。

* **クエリのパターン**:クエリの複雑さは、操作のために消費される RU の数に影響を与えます。 クエリ操作のコストに影響する要因は次のとおりです。 
    
    - クエリ結果の数
    - 述語の数
    - 述語の性質
    - ユーザー定義関数の数
    - ソース データのサイズ
    - 結果セットのサイズ
    - プロジェクション

  Azure Cosmos DB では、同じデータに対する同じクエリが、繰り返しの実行で常に同じ数の RU を要することが保証されます。

* **スクリプトの使用**:クエリと同様に、ストアド プロシージャとトリガーは、実行される操作の複雑さに基づいて RU を消費します。 アプリケーションを開発する場合は、[要求使用量ヘッダー](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query)を調べて、各操作で消費される RU 量をより深く理解してください。

## <a name="next-steps"></a>次の手順

* [Azure Cosmos のコンテナーとデータベースのスループットをプロビジョニングする](set-throughput.md)方法の詳細を確認する。
* [論理パーティション](partition-data.md)の詳細を確認する。
* [プロビジョニング済みスループットをグローバルにスケーリングする](scaling-throughput.md)方法の詳細を確認する。
* [Azure Cosmos コンテナーのスループットをプロビジョニングする](how-to-provision-container-throughput.md)方法を確認する。
* [Azure Cosmos データベースのスループットをプロビジョニングする](how-to-provision-database-throughput.md)方法を確認する。
* [操作の要求ユニット使用量を調べる](find-request-unit-charge.md)方法について確認する。
* [Azure Cosmos DB でプロビジョニング済みのスループット コストを最適化する](optimize-cost-throughput.md)方法を確認する。
* [Azure Cosmos DB の読み取りと書き込みのコストの最適化](optimize-cost-reads-writes.md)方法を確認する。
* [Azure Cosmos DB でのクエリ コストを最適化する](optimize-cost-queries.md)方法を確認する。
* [メトリックを使用してスループットを監視する](use-metrics.md)方法を確認する。
