---
title: サーバー グループをスケーリングする - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 負荷の増加に対処するためにサーバー グループのメモリ、ディスク、CPU リソースを調整する
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 5d8bbe493887c5340f0943a585eb6ff250bd3728
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977558"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループをスケーリングする

Azure Database for PostgreSQL - Hyperscale (Citus) では、増加した負荷に対処するためにセルフサービス スケーリングを行うことができます。 Azure portal を使用すると、新しいワーカー ノードの追加や、既存ノードの容量増加を簡単に実行できます。

## <a name="add-worker-nodes"></a>ワーカー ノードの追加

ノードを追加するには、Hyperscale (Citus) サーバー グループの **[構成]** タブに移動します。  **[ワーカー ノードの数]** のスライダーをドラッグすると、値が変化します。

![リソースのスライダー](./media/howto-hyperscale-scaling/01-sliders-workers.png)

**[保存]** ボタンをクリックして、変更した値を有効にします。

> [!NOTE]
> ワーカー ノードの数は、いったん増やして保存すると、スライダーを使用して減らすことはできません。

### <a name="rebalance-shards"></a>シャードの再調整

新しく追加したノードを利用するには、分散テーブルの[シャード](concepts-hyperscale-distributed-data.md#shards)を再調整する必要があります。これは、既存のノードから新しいノードにいくつかのシャードを移動することを意味します。 まず、新しいワーカーがプロビジョニングを正常に完了したことを確認します。 次に、シャードを再調整するバランサーを開始します。そのためには、psql を使用してクラスター コーディネーター ノードに接続し、以下を実行します。

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards` 関数により、引数に指定したテーブルの[コロケーション](concepts-hyperscale-colocation.md) グループ内にあるすべてのテーブルが再調整されます。 したがって、すべての分散テーブルに対してその関数を呼び出す必要はありません。各コロケーション グループの代表的なテーブルに対して呼び出すだけです。

## <a name="increase-vcores-or-storage-space"></a>仮想コアまたはストレージ容量を増やす

新しいノードを追加するだけでなく、既存のノードの能力を増強することもできます。 Hyperscale (Citus) サーバー グループの **[構成]** タブに移動し、 **[vCores]\(仮想コア\)** および **[ストレージ]** のスライダーをドラッグして、すべてのワーカー ノードに対してこれらの値を変更します。 必ず **[保存]** をクリックして変更を適用してください。

## <a name="next-steps"></a>次の手順

サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
