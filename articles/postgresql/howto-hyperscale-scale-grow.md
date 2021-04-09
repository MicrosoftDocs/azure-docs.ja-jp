---
title: サーバー グループをスケーリングする - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 負荷の増加に対処するためにサーバー グループのメモリ、ディスク、CPU リソースを調整する
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026422"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループをスケーリングする

Azure Database for PostgreSQL - Hyperscale (Citus) では、増加した負荷に対処するためにセルフサービス スケーリングを行うことができます。 Azure portal を使用すると、簡単に新しいワーカー ノードを追加したり既存ノードの仮想コアを増やしたりできます。 ノードを追加するとダウンタイムが発生しなくなります。また、クエリを中断させることなく、新しいノードにシャードを移動 ([シャードの再調整](howto-hyperscale-scale-rebalance.md)と呼ばれます) することも可能です。

## <a name="add-worker-nodes"></a>ワーカー ノードの追加

ノードを追加するには、ハイパースケール (Citus) サーバー グループの **[コンピューティングとストレージ]** タブに移動します。  **[ワーカー ノードの数]** のスライダーをドラッグすると、値が変化します。

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="リソースのスライダー":::

**[保存]** ボタンをクリックして、変更した値を有効にします。

> [!NOTE]
> ワーカー ノードの数は、いったん増やして保存すると、スライダーを使用して減らすことはできません。

> [!NOTE]
> 新しく追加したノードを利用するには、[分散テーブルのシャードを再調整する](howto-hyperscale-scale-rebalance.md)必要があります。つまり、既存のノードから新しいノードにいくつかの[シャード](concepts-hyperscale-distributed-data.md#shards)を移動します。

## <a name="increase-or-decrease-vcores-on-nodes"></a>ノードの仮想コアを増減させる

新しいノードを追加するだけでなく、既存のノードの能力を増強することもできます。 計算処理能力の調整 (増減) は、パフォーマンス実験のほか、トラフィック需要の短期的変更または長期的変更に役立つ場合があります。

すべてのワーカー ノードの仮想コアを変更するには、 **[構成 (ワーカー ノードあたり)]** の下の **[仮想コア]** スライダーを調整します。 コーディネーター ノードの仮想コアは、個別に調整することができます。 **[構成 (コーディネーター ノード)]** の **[仮想コア]** スライダーを調整します。

## <a name="next-steps"></a>次の手順

- サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
- すべてのワーカー ノードが並列クエリに参加できるように[分散テーブル シャードを再調整する](howto-hyperscale-scale-rebalance.md)
