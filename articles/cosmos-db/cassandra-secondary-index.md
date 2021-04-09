---
title: Azure Cosmos DB Cassandra API アカウントでのインデックス作成
description: Azure Cosmos DB Cassandra API アカウントでのセカンダリ インデックス作成のしくみについて説明します。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 98e8f713ad2e4eef47e40d89a23dbf49a98ad67c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339895"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API でのセカンダリ インデックス作成
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB の Cassandra API は、基になるインデックス作成インフラストラクチャを利用して、プラットフォームに固有のインデックス作成の長所を公開します。 ただし、コア SQL API とは異なり、Azure Cosmos DB の Cassandra API では、既定ですべての属性にインデックスが付けられるわけではありません。 その代わりとして、特定の属性に対してインデックスを作成するセカンダリ インデックス作成機能をサポートしており、この機能は Apache Cassandra と同じように動作します。  

通常、パーティション分割されていない列に対してフィルター クエリを実行することはお勧めしません。 ALLOW FILTERING 構文を明示的に使用する必要があり、その結果、操作が正常に実行されない可能性があります。 Azure Cosmos DB では、カーディナリティが低い属性に対してこのようなクエリを実行できます (クエリがパーティション全体に広がって結果を取得するため)。

頻繁に更新される列に対してインデックスを作成することはお勧めしません。 テーブルを定義するときにインデックスを作成するのが賢明です。 これにより、データとインデックスが整合性のある状態になります。 現在、既存のデータに新しいインデックスを作成した場合、テーブル用のインデックスの進行状況の変化を追跡することはできません。 この操作の進行状況を追跡する必要がある場合は、[サポート チケット]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)を使用して進行状況の変化をリクエストする必要があります。


> [!NOTE]
> セカンダリ インデックスは、次のオブジェクトではサポートされていません。
> - frozen コレクション型、decimal 型、variant 型などのデータ型。
> - 静的列
> - クラスタリング キー

## <a name="indexing-example"></a>インデックス作成の例

最初に、CQL シェル プロンプトで次のコマンドを実行して、サンプルのキースペースとテーブルを作成します。

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

次に、次のコマンドを使用してサンプル ユーザー データを挿入します。

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

次のステートメントを実行しようとすると、`ALLOW FILTERING` を使用するように求めるエラーが発生します。 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Cassandra API では ALLOW FILTERING をサポートしていますが、前のセクションで説明したようにお勧めしていません。 代わりに、次の例に示すように、インデックスを作成する必要があります。

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
"lastname" フィールドにインデックスを作成した後、前のクエリを正常に実行できるようになりました。 Azure Cosmos DB の Cassandra API では、インデックス名を指定する必要はありません。 `tablename_columnname_idx` 形式の既定のインデックスが使用されます。 たとえば、` t1_lastname_idx` は前のテーブルのインデックス名です。

## <a name="dropping-the-index"></a>インデックスの削除 
インデックスを削除するには、インデックス名がわかっている必要があります。 `desc schema` コマンドを実行して、テーブルの記述を取得してください。 このコマンドの出力には、`CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` 形式のインデックス名が含まれています。 その後、次の例に示すように、インデックス名を使用してインデックスを削除することができます。

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB での[自動インデックス作成](index-overview.md)のしくみを確認する
* [Azure Cosmos DB の Cassandra API でサポートされる Apache Cassandra の機能](cassandra-support.md)
