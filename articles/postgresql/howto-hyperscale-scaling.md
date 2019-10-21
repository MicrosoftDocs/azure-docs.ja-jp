---
title: Azure Database for PostgreSQL - Hyperscale (Citus) サーバー グループのスケーリング
description: 負荷の増加に対処するためにサーバー グループのメモリ、ディスク、CPU リソースを調整する
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263018"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループをスケーリングする

Azure Database for PostgreSQL - Hyperscale (Citus) では、増加した負荷に対処するためにセルフサービス スケーリングを行うことができます。 Azure portal を使用することで、新しいワーカー ノードを簡単に追加できます。

これを行うには、Hyperscale (Citus) サーバー グループの **[構成]** タブに移動します。
**[ワーカー ノードの数]** のスライダーをドラッグして、値を変更します。

![リソースのスライダー](./media/howto-hyperscale-scaling/01-sliders-workers.png)

[保存] ボタンをクリックして、変更した値を有効にします。

> [!NOTE]
> ワーカー ノードの数は、いったん増やして保存すると、スライダーを使用して減らすことはできません。
>
> また、このユーザー インターフェイスを使用しているコーディネーターまたはワーカーで、仮想コアとストレージを調整することはできません。 コーディネーター ノードまたはワーカー ノードでコンピューティングをスケーリングする必要がある場合は、サポート チケットを開きます。

新しく追加したノードを利用するには、分散テーブルの[シャード](concepts-hyperscale-distributed-data.md#shards)を再調整する必要があります。これは、既存のノードから新しいノードにいくつかのシャードを移動することを意味します。 シャードを再調整するバランサーを開始するには、psql を使用してクラスター コーディネーター ノードに接続し、以下を実行します。

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards` 関数により、引数に指定したテーブルの[コロケーション](concepts-hyperscale-colocation.md) グループ内にあるすべてのテーブルが再調整されます。 したがって、すべての分散テーブルに対してその関数を呼び出す必要はありません。各コロケーション グループの代表的なテーブルに対して呼び出すだけです。

## <a name="next-steps"></a>次の手順

サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
