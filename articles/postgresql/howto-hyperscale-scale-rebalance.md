---
title: シャードの再調整 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: サーバー間でシャードを均等に分散してパフォーマンスを向上させる
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026388"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループでシャードを再調整する

新しく追加したノードを利用するには、分散テーブルの[シャード](concepts-hyperscale-distributed-data.md#shards)を再調整する必要があります。これは、既存のノードから新しいノードにいくつかのシャードを移動することを意味します。 まず、新しいワーカーがプロビジョニングを正常に完了したことを確認します。 次に、シャードを再調整するバランサーを開始します。そのためには、psql を使用してクラスター コーディネーター ノードに接続し、以下を実行します。

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

[rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 関数により、引数に指定されたテーブルの[コロケーション](concepts-hyperscale-colocation.md) グループ内にあるすべてのテーブルが再調整されます。 したがって、すべての分散テーブルに対してその関数を呼び出す必要はありません。各コロケーション グループの代表的なテーブルに対して呼び出すだけです。

**次の手順**


- サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
- [サーバー グループをスケールアップまたはスケールアウト](howto-hyperscale-scale-grow.md)する
- [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) リファレンス資料を参照する
