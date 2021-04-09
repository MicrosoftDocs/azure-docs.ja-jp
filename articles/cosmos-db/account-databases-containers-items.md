---
title: Azure Cosmos DB リソース モデル
description: この記事では、Azure Cosmos のアカウント、データベース、コンテナー、および項目を含む Azure Cosmos DB リソース モデルについて説明します。 Azure Cosmos アカウントにおける、これらの要素の階層についても取り上げます。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 4ed881b74f240946d98d9868344c898d3e9a9dad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627350"
---
# <a name="azure-cosmos-db-resource-model"></a>Azure Cosmos DB リソース モデル
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB はフルマネージドのサービスとしてのプラットフォーム (PaaS) です。 Azure Cosmos DB の使用を開始するには、最初に、Azure サブスクリプション内に Azure Cosmos アカウントを作成し、その下にデータベース、コンテナー、項目を作成する必要があります。 この記事では、Azure Cosmos DB リソース モデルと、リソース モデル階層内のさまざまなエンティティについて説明します。

Azure Cosmos アカウントは、グローバルな分散と高可用性の基本単位です。 Azure Cosmos アカウントには固有の DNS 名が含まれており、Azure portal または Azure CLI を使用するか、別の言語固有の SDK を使用してアカウントを管理できます。 詳細については、[Azure Cosmos アカウントの管理方法](how-to-manage-database-account.md)に関するページを参照してください。 データやスループットを複数の Azure リージョンにグローバルに分散させる場合は、お使いのアカウントに、いつでも Azure リージョンを追加したり、削除したりすることができます。 アカウントは、単一リージョンまたは複数の書き込みリージョンを持つように構成できます。 詳細については、[アカウントに Azure リージョンを追加および削除する方法](how-to-manage-database-account.md)に関するページを参照してください。 アカウントに対して[既定の一貫性](consistency-levels.md)レベルを構成できます。

## <a name="elements-in-an-azure-cosmos-account"></a>Azure Cosmos アカウントの要素

Azure Cosmos コンテナーは、スケーラビリティの基本単位です。 コンテナー上に持つことができるプロビジョニング スループット (RU/秒) とストレージは事実上無制限です。 Azure Cosmos DB では、プロビジョニング スループットとストレージを柔軟にスケーリングするために、指定した論理パーティション キーを使用してコンテナーを透過的に分割します。

現時点では、Azure サブスクリプションで最大 50 の Azure Cosmos アカウントを作成できます (これはサポート リクエストを通じて増加できるソフト制限です)。 1 つの Azure Cosmos アカウントで、事実上無制限のデータ量とプロビジョニング スループットを管理できます。 データとプロビジョニング スループットを管理するには、自分のアカウントで 1 つまたは複数の Azure Cosmos データベースを作成し、そのデータベース内に 1 つまたは複数のコンテナーを作成することができます。 次の画像は、Azure Cosmos アカウントの要素の階層を示しています。

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Azure Cosmos アカウントの階層" border="false":::

Azure サブスクリプションでアカウントを作成したら、データベース、コンテナー、および項目を作成することによって、アカウント内のデータを管理できます。 

次の画像は、Azure Cosmos DB アカウントのさまざまなエンティティの階層を示しています。

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos アカウントのエンティティ" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos データベース

ご利用のアカウントの下に、1 つまたは複数の Azure Cosmos データベースを作成できます。 データベースは名前空間に似ています。 データベースは、一連の Azure Cosmos コンテナーを管理する単位です。 次の表は、データベースがどのように API 固有のさまざまなエンティティにマップされているかを示しています。

| Azure Cosmos エンティティ | SQL API | Cassandra API | MongoDB 用 Azure Cosmos DB API | Gremlin API | テーブル API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos データベース | データベース | キースペース | データベース | データベース | NA |

> [!NOTE]
> Table API アカウントでは、最初のテーブルを作成すると、自分の Azure Cosmos アカウント内に既定のデータベースが自動的に作成されます。

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos データベースに対する操作

Azure Cosmos API を使用して、次の表で示しているように Azure Cosmos データベースとやりとりできます。

| 操作 | Azure CLI | SQL API | Cassandra API | MongoDB 用 Azure Cosmos DB API | Gremlin API | テーブル API |
| --- | --- | --- | --- | --- | --- | --- |
|すべてのデータベースを列挙する| はい | はい | はい (データベースはキースペースにマップされる) | はい | NA | NA |
|データベースを読み込む| はい | はい | はい (データベースはキースペースにマップされる) | はい | NA | NA |
|新しいデータベースの作成| はい | はい | はい (データベースはキースペースにマップされる) | はい | NA | NA |
|データベースを更新する| はい | はい | はい (データベースはキースペースにマップされる) | はい | NA | NA |

## <a name="azure-cosmos-containers"></a>Azure Cosmos コンテナー

Azure Cosmos コンテナーは、プロビジョニング スループットとストレージの両方に関するスケーラビリティの単位です。 コンテナーは水平方向にパーティション分割され、その後、複数のリージョン間でレプリケートされます。 コンテナーに追加した項目は、パーティション キーに基づいて、論理パーティションに自動的にグループ化され、物理パーティション全体で自動的に分散されます。 コンテナー上のスループットは、物理パーティション全体で均等に分散されます。 パーティション分割とパーティション キーの詳細については、[データのパーティション分割](partitioning-overview.md)に関するページを参照してください。 

コンテナーを作成するときには、次のいずれかのモードでスループットを構成します。

* **専用プロビジョニング スループット モード**: コンテナーにプロビジョニングされたスループットは、そのコンテナー専用に予約され、SLA によってバックアップされます。 詳細については、[コンテナーでのスループットをプロビジョニングする方法](how-to-provision-container-throughput.md)に関するページを参照してください。

* **共有プロビジョニング スループット モード**: これらのコンテナーでは、同じデータベース内の他のコンテナー (専用プロビジョニング スループットで構成されたコンテナーを除く) とプロビジョニング スループットを共有します。 つまり、データベース上のプロビジョニング スループットは、すべての "共有スループット" コンテナー間で共有されます。 詳細については、[データベースでのスループットをプロビジョニングする方法](how-to-provision-database-throughput.md)に関するページを参照してください。

> [!NOTE]
> 共有および専用のスループットを構成できるのは、データベースとコンテナーを作成する場合のみになります。 コンテナーを作成した後に専用スループット モードから共有スループット モード (またはその逆) に切り替えるには、新しいコンテナーを作成して、その新しいコンテナーにデータを移行する必要があります。 データの移行は、Azure Cosmos DB の変更フィード機能を使用して行うことができます。

Azure Cosmos コンテナーでは、コンテナーを専用または共有のどちらのプロビジョニング スループット モードで作成していても、柔軟にスケーリングできます。

コンテナーは、スキーマに依存しない、項目のコンテナーです。 コンテナー内の項目には、任意のスキーマを含めることができます。 たとえば、人物を表す項目と自動車を表す項目を *同じコンテナー* に配置できます。 既定では、コンテナーに追加するすべての項目に自動的にインデックスが付けられ、明示的なインデックスやスキーマ管理は必要とされません。 コンテナーで[インデックス作成ポリシー](index-overview.md)を構成することによって、インデックス作成の動作をカスタマイズできます。 

コンテナー内の選択された項目、またはコンテナー全体に対して [Time to Live (TTL)](time-to-live.md) を設定すると、それらの項目をシステムから正常に消去できます。 Azure Cosmos DB では、項目の有効期限が切れるとそれらが自動的に削除されます。 これによって、コンテナーに対して実行されるクエリにより、固定されたバインド内の有効期限切れの項目が返されないことも保証されます。 詳細については、[コンテナーでの TTL の構成](how-to-time-to-live.md)に関するページを参照してください。

[変更フィード](change-feed.md)を使用して、コンテナーの論理パーティションごとに管理されている操作ログにサブスクライブできます。 変更フィードでは、項目の前後のイメージと共に、コンテナーに対して実行されたすべての更新のログが提供されます。 詳細については、[変更フィードを使用した待ち受け型のアプリケーションの構築](serverless-computing-database.md)に関するページを参照してください。 また、コンテナーの変更フィード ポリシーを使用することで、変更フィードの保有期間を構成することもできます。

コンテナーには、[ストアド プロシージャ、トリガー、ユーザー定義関数 (UDF)](stored-procedures-triggers-udfs.md)、[マージ プロシージャ](how-to-manage-conflicts.md)を登録できます。

Azure Cosmos コンテナーで[一意キー制約](unique-keys.md)を指定できます。 一意キー ポリシーを作成することで、論理パーティション キーごとに、1 つまたは複数の値の一意性を確保します。 一意キー ポリシーを使用してコンテナーを作成する場合、一意キー制約で指定されている値と重複する値を持つ新しい項目や更新された項目を作成することはできません。 詳細については、[一意キーの制約](unique-keys.md)に関するページを参照してください。

コンテナーは、次の表に示すように、API 固有のエンティティに特化されます。

| Azure Cosmos エンティティ | SQL API | Cassandra API | MongoDB 用 Azure Cosmos DB API | Gremlin API | テーブル API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos コンテナー | コンテナー | テーブル | コレクション | グラフ | テーブル |

> [!NOTE]
> コンテナーを作成するときは、同じ名前で大文字と小文字が異なる 2 つのコンテナーを作成しないようにしてください。 これは、Azure プラットフォームの一部で大文字と小文字が区別されないため、このような名前のコンテナーでテレメトリとアクションの混同や衝突が発生する可能性があるためです。

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos コンテナーのプロパティ

Azure Cosmos コンテナーには、一連のシステム定義のプロパティがあります。 使用する API によっては、一部のプロパティが直接公開されない場合があります。 次の表は、システム定義のプロパティの一覧を示しています。

| システム定義のプロパティ | システム生成またはユーザー構成可能 | 目的 | SQL API | Cassandra API | MongoDB 用 Azure Cosmos DB API | Gremlin API | テーブル API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | システム生成 | コンテナーの一意識別子 | はい | いいえ | いいえ | いいえ | いいえ |
|\_etag | システム生成 | オプティミスティック同時実行制御に使用されるエンティティ タグ | はい | いいえ | いいえ | いいえ | いいえ |
|\_ts | システム生成 | コンテナーの最終更新時のタイムスタンプ | はい | いいえ | いいえ | いいえ | いいえ |
|\_self | システム生成 | コンテナーのアドレス指定可能な URI | はい | いいえ | いいえ | いいえ | いいえ |
|id | ユーザー構成可能 | コンテナーのユーザーが定義した一意の名前 | はい | はい | はい | はい | はい |
|indexingPolicy | ユーザー構成可能 | インデックスのパス、インデックスの種類、インデックス モードを変更する機能を提供します | はい | いいえ | いいえ | いいえ | はい |
|TimeToLive | ユーザー構成可能 | 設定した期間後にコンテナーから自動的に項目を削除する機能を提供します。 詳細については、[Time to Live](time-to-live.md) に関するページを参照してください。 | はい | いいえ | いいえ | いいえ | はい |
|changeFeedPolicy | ユーザー構成可能 | コンテナー内の項目に加えられた変更の読み取りに使用されます。 詳細については、[変更フィード](change-feed.md)に関するページを参照してください。 | はい | いいえ | いいえ | いいえ | はい |
|uniqueKeyPolicy | ユーザー構成可能 | 論理パーティション内の 1 つまたは複数の値の一意性を確保するために使用されます。 詳細については、[一意キー制約](unique-keys.md)に関するページを参照してください。 | はい | いいえ | いいえ | いいえ | はい |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos コンテナーに対する操作

Azure Cosmos コンテナーでは、いずれかの Azure Cosmos API を使用する場合、以下の操作をサポートします。

| 操作 | Azure CLI | SQL API | Cassandra API | MongoDB 用 Azure Cosmos DB API | Gremlin API | テーブル API |
| --- | --- | --- | --- | --- | --- | --- |
| データベース内のコンテナーを列挙する | はい | はい | はい | はい | NA | NA |
| コンテナーを読み取る | はい | はい | はい | はい | NA | NA |
| 新しいコンテナーを作成する | はい | はい | はい | はい | NA | NA |
| コンテナーを更新する | はい | はい | はい | はい | NA | NA |
| コンテナーを削除する | はい | はい | はい | はい | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos 項目

使用する API に応じて、Azure Cosmos 項目では、コレクション内のドキュメント、テーブル内の行、グラフ内のノードまたはエッジのいずれかを表すことができます。 次の表は、API 固有のエンティティと Azure Cosmos 項目との間のマッピングを示しています。

| Cosmos エンティティ | SQL API | Cassandra API | MongoDB 用 Azure Cosmos DB API | Gremlin API | テーブル API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 項目 | Item | 行 | ドキュメント | ノードまたはエッジ | Item |

### <a name="properties-of-an-item"></a>項目のプロパティ

Azure Cosmos のどの項目にも、以下のシステム定義プロパティがあります。 使用する API に応じて、一部は直接公開されない場合があります。

| システム定義のプロパティ | システム生成またはユーザー構成可能| 目的 | SQL API | Cassandra API | MongoDB 用 Azure Cosmos DB API | Gremlin API | テーブル API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | システム生成 | 項目の一意識別子 | はい | いいえ | いいえ | いいえ | いいえ |
|\_etag | システム生成 | オプティミスティック同時実行制御に使用されるエンティティ タグ | はい | いいえ | いいえ | いいえ | いいえ |
|\_ts | システム生成 | 項目の最終更新のタイムスタンプ | はい | いいえ | いいえ | いいえ | いいえ |
|\_self | システム生成 | 項目のアドレス指定可能な URI | はい | いいえ | いいえ | いいえ | いいえ |
|id | 接続前/接続後 | 論理パーティション内のユーザーが定義した一意の名前。 | はい | はい | はい | はい | はい |
|任意のユーザー定義のプロパティ | ユーザー定義 | API のネイティブ表現 (JSON、BSON、CQL など) で表されるユーザー定義のプロパティ | はい | はい | はい | はい | はい |

> [!NOTE]
> `id` プロパティの一意性は、各論理パーティション内でのみ適用されます。 複数のドキュメントで、異なるパーティション キー値を持つ同じ `id` プロパティを使用できます。

### <a name="operations-on-items"></a>項目に対する操作

Azure Cosmos 項目では、次の操作をサポートします。 Azure Cosmos API のいずれかを使用して操作を実行できます。

| 操作 | Azure CLI | SQL API | Cassandra API | MongoDB 用 Azure Cosmos DB API | Gremlin API | テーブル API |
| --- | --- | --- | --- | --- | --- | --- |
| 挿入、置換、削除、アップサート、読み取り | いいえ | はい | はい | はい | はい | はい |

## <a name="next-steps"></a>次のステップ

Azure Cosmos アカウントの管理方法とその他の概念を確認します。

* [Azure Cosmos アカウントを管理する方法](how-to-manage-database-account.md)
* [グローバル配信](distribute-data-globally.md)
* [一貫性レベル](consistency-levels.md)
* [Azure Cosmos アカウントの VNET サービス エンドポイント](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos アカウントの IP ファイアウォール](how-to-configure-firewall.md)
* [Azure Cosmos アカウントに Azure リージョンを追加および削除する方法](how-to-manage-database-account.md)
* [Azure Cosmos DB の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
