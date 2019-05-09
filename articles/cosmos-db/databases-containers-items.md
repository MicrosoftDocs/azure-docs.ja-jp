---
title: Azure Cosmos DB のデータベース、コンテナー、および項目の操作
description: この記事では、Azure Cosmos DB のデータベース、コンテナー、および項目を作成して使用する方法を説明します
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: f3bec1b279c07e62e246ebfa933b3942e38406de
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762907"
---
# <a name="work-with-databases-containers-and-items"></a>データベース、コンテナー、アイテムの操作

Azure サブスクリプションで [Azure Cosmos アカウント](account-overview.md)を作成したら、データベース、コンテナー、および項目を作成することによってアカウント内のデータを管理できます。 この記事では、データベース、コンテナー、項目の各エンティティについて説明します。 次の画像は、Azure Cosmos アカウントのさまざまなエンティティの階層を示しています。

![Azure Cosmos アカウントのエンティティ](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos データベース

ご利用のアカウントの下に、1 つまたは複数の Azure Cosmos データベースを作成できます。 データベースは名前空間に似ています。 それは、一連の Azure Cosmos コンテナーを管理する単位です。 次の表は、Azure Cosmos データベースがどのように、API 固有のさまざまなエンティティにマップされてるかを示しています。

| **Azure Cosmos エンティティ** | **SQL API** | **Cassandra API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API** | **テーブル API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos データベース | Database | キースペース | Database | Database | NA |

> [!NOTE]
> Table API アカウントでは、最初のテーブルを作成すると、Azure Cosmos アカウント内に既定のデータベースが自動的に作成されます。

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos データベースに対する操作

Azure Cosmos API を使用して、次のように Azure Cosmos データベースを操作できます。

| **操作** | **Azure CLI**|**SQL API** | **Cassandra API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API** | **テーブル API** |
| --- | --- | --- | --- | --- | --- | --- |
|すべてのデータベースを列挙する| はい | はい | はい (データベースはキースペースにマップされる) | はい | NA | NA |
|データベースを読み込む| はい | はい | はい (データベースはキースペースにマップされる) | はい | NA | NA |
|新しいデータベースの作成| はい | はい | はい (データベースはキースペースにマップされる) | はい | NA | NA |
|データベースを更新する| はい | はい | はい (データベースはキースペースにマップされる) | はい | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos コンテナー

Azure Cosmos コンテナーは、プロビジョニング スループットとストレージの両方に関するスケーラビリティの単位です。 コンテナーは水平方向にパーティション分割され、その後、複数のリージョン間でレプリケートされます。 コンテナーに追加した項目と、コンテナーに対してプロビジョニングしたスループットは、パーティション キーに基づいて一連の論理パーティションにわたって自動的に分散されます。 パーティション分割とパーティション キーの詳細については、[この](partition-data.md)記事を参照してください。 

Azure Cosmos コンテナーを作成するときには、次のいずれかのモードでスループットを構成します。

* **専用プロビジョニング スループット** モード: コンテナーにプロビジョニングされたスループットは、そのコンテナー専用に予約され、SLA によってバックアップされます。 詳細については、[Azure Cosmos コンテナーのスループットをプロビジョニングする方法](how-to-provision-container-throughput.md)に関するページを参照してください。

* **共有プロビジョニング スループット** モード: これらのコンテナーは、同じデータベース内の他のコンテナー (専用プロビジョニング スループットで構成されたコンテナーを除く) とプロビジョニング スループットを共有します。 つまり、データベース上のプロビジョニング スループットは、すべての "共有スループット" コンテナー間で共有されます。 詳細については、[Azure Cosmos データベースに対してプロビジョニングされるスループットを構成する方法](how-to-provision-database-throughput.md)に関するページを参照してください。

Azure Cosmos コンテナーは、コンテナーを "共有" または "専用" のどちらのプロビジョニング スループット モードで作成するかにかかわらず、柔軟にスケーリングできます。

Azure Cosmos コンテナーは、スキーマに依存しない、項目のコンテナーです。 コンテナー内の項目は、任意のスキーマを持つことができます。 たとえば、人物を表す項目と自動車を表す項目を*同じコンテナー*に配置できます。 既定では、コンテナーに追加するすべての項目に自動的にインデックスが付けられ、明示的なインデックスやスキーマ管理は必要とされません。 コンテナーで[インデックス作成ポリシー](index-overview.md)を構成することによって、インデックス作成の動作をカスタマイズできます。 

Azure Cosmos コンテナー内の選択された項目、またはコンテナー全体に対して [Time To Live (TTL)](time-to-live.md) を設定することによって、それらの項目をシステムから正常に消去できます。 Azure Cosmos DB は、項目の有効期限が切れるとそれらを自動的に削除します。 これによって、コンテナーに対して実行されるクエリが、固定されたバインド内の有効期限切れの項目を返さないことも保証されます。 詳細については、[コンテナーに対して TTL を構成する方法](how-to-time-to-live.md)に関するページを参照してください。

[変更フィード](change-feed.md)を使用すると、コンテナーの論理パーティションごとに管理されている操作ログにサブスクライブできます。 変更フィードでは、コンテナーに対して実行されたすべての更新のログと共に、項目の前後のイメージが提供されます。 [変更フィードを使用して反応型のアプリケーションを構築する方法](serverless-computing-database.md)に関するページを参照してください。 また、コンテナーの変更フィード ポリシーを使用して、変更フィードの保存期間を構成することもできます。 

Azure Cosmos コンテナーには、[ストアド プロシージャ、トリガー、ユーザー定義関数 (UDF)](stored-procedures-triggers-udfs.md)、および[マージ プロシージャ](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy-with-a-stored-procedure)を登録できます。 

Azure Cosmos コンテナーで[一意キー制約](unique-keys.md)を指定できます。 一意キー ポリシーを作成することで、論理パーティション キーごとに、1 つまたは複数の値の一意性を確保します。 一意キー ポリシーを使用して作成したコンテナーでは、一意キー制約で指定されている値と重複する値を持つ項目の新規作成や更新ができなくなります。 詳細については、[一意キーの制約](unique-keys.md)に関するページを参照してください。

Azure Cosmos コンテナーは、以下のような API 固有のエンティティに特化されます。

| **Azure Cosmos エンティティ** | **SQL API** | **Cassandra API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API** | **テーブル API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos コンテナー | コレクション | テーブル | コレクション | Graph | テーブル |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos コンテナーのプロパティ

Azure Cosmos コンテナーには、一連のシステム定義のプロパティがあります。 選択する API によっては、その一部が直接公開されないことがあります。 次の表は、システム定義のプロパティの一覧を示しています。

| **システム定義のプロパティ** | **システム生成かユーザーが構成可能か** | **目的** | **SQL API** | **Cassandra API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API** | **テーブル API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | システム生成 | コンテナーの一意識別子 | はい | いいえ  | いいえ  | いいえ  | いいえ  |
|_etag | システム生成 | オプティミスティック同時実行制御に使用されるエンティティ タグ | はい | いいえ  | いいえ  | いいえ  | いいえ  |
|_ts | システム生成 | コンテナーの最終更新時のタイムスタンプ | はい | いいえ  | いいえ  | いいえ  | いいえ  |
|_self | システム生成 | コンテナーのアドレス指定可能な URI | はい | いいえ  | いいえ  | いいえ  | いいえ  |
|id | ユーザーが構成可能 | コンテナーのユーザーが定義した一意の名前 | はい | はい | はい | はい | はい |
|indexingPolicy | ユーザーが構成可能 | インデックスのパス、インデックスの種類、およびインデックス モードを変更する機能を提供します。 | はい | いいえ  | いいえ  | いいえ  | はい |
|TimeToLive | ユーザーが構成可能 | 一定期間後にコンテナーから自動的に項目を削除する機能を提供します。 詳細については、[Time To Live](time-to-live.md) に関するページを参照してください。 | はい | いいえ  | いいえ  | いいえ  | はい |
|changeFeedPolicy | ユーザーが構成可能 | コンテナー内の項目に加えられた変更の読み取りに使用されます。 詳細については、[変更フィード](change-feed.md)に関する記事を参照してください。 | はい | いいえ  | いいえ  | いいえ  | はい |
|uniqueKeyPolicy | ユーザーが構成可能 | 論理パーティション内の 1 つ以上の値の一意性を確保するために使用されます。 詳細については、[一意キー制約](unique-keys.md)に関する記事を参照してください。 | はい | いいえ  | いいえ  | いいえ  | はい |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos コンテナーに対する操作

Azure Cosmos コンテナーでは、いずれかの Azure Cosmos API を使用して、以下の操作を行えます。

| **操作** | **Azure CLI** | **SQL API** | **Cassandra API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API** | **テーブル API** |
| --- | --- | --- | --- | --- | --- | --- |
| データベース内のコンテナーを列挙する | はい | はい | はい | はい | NA | NA |
| コンテナーを読み取る | はい | はい | はい | はい | NA | NA |
| 新しいコンテナーを作成する | はい | はい | はい | はい | NA | NA |
| コンテナーを更新する | はい | はい | はい | はい | NA | NA |
| コンテナーを削除する | はい | はい | はい | はい | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos 項目

API の選択に応じて、Azure Cosmos 項目は、コレクション内のドキュメント、テーブル内の行、またはグラフ内のノード/エッジのいずれかを表す場合があります。 次の表に、API 固有のエンティティと Azure Cosmos 項目との間のマッピングを示します。

| **Cosmos エンティティ** | **SQL API** | **Cassandra API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API** | **テーブル API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 項目 | ドキュメント | 行 | ドキュメント | ノードまたはエッジ | Item |

### <a name="properties-of-an-item"></a>項目のプロパティ

Azure Cosmos のどの項目にも、以下のシステム定義プロパティがあります。 選択する API によっては、その一部が直接公開されないことがあります。

|**システム定義のプロパティ** | **システム生成かユーザーが構成可能か**| **目的** | **SQL API** | **Cassandra API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API** | **テーブル API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | システム生成 | 項目の一意識別子 | はい | いいえ  | いいえ  | いいえ  | いいえ  |
|_etag | システム生成 | オプティミスティック同時実行制御に使用されるエンティティ タグ | はい | いいえ  | いいえ  | いいえ  | いいえ  |
|_ts | システム生成 | 項目の最終更新のタイムスタンプ | はい | いいえ  | いいえ  | いいえ  | いいえ  |
|_self | システム生成 | 項目のアドレス指定可能な URI | はい | いいえ  | いいえ  | いいえ  | いいえ  |
|id | 使用できるのは | 論理パーティション内の、ユーザーが定義した一意の名前。 ユーザーが id を指定しない場合、システムによって id が自動生成されます。 | はい | はい | はい | はい | はい |
|任意のユーザー定義のプロパティ | ユーザー定義 | API のネイティブ表現 (JSON、BSON、CQL など) で表されるユーザー定義のプロパティ | はい | はい | はい | はい | はい |

### <a name="operations-on-items"></a>項目に対する操作

Azure Cosmos の項目は、以下の操作をサポートしています。それらの操作は、いずれかの Azure Cosmos API を使用して実行できます。

| **操作** | **Azure CLI** | **SQL API** | **Cassandra API** | **Azure Cosmos DB の MongoDB 用 API** | **Gremlin API** | **テーブル API** |
| --- | --- | --- | --- | --- | --- | --- |
| 挿入、置換、削除、アップサート、読み取り | いいえ  | 可能  | はい | はい | はい | はい |

## <a name="next-steps"></a>次の手順

これで、次の概念の学習に進むことができます。

* [Azure Cosmos データベースに対してプロビジョニングされるスループットを構成する方法](how-to-provision-database-throughput.md)
* [Azure Cosmos コンテナーに対してプロビジョニングされるスループットを構成する方法](how-to-provision-container-throughput.md)
* [論理パーティション](partition-data.md)
* [Azure Cosmos コンテナーに対して TTL を構成する方法](how-to-time-to-live.md)
* [変更フィードを使用して反応型のアプリケーションを構築する方法](change-feed.md)
* [Azure Cosmos コンテナーに対して一意キーの制約を構成する方法](unique-keys.md)
