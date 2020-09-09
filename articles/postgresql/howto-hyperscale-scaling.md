---
title: サーバー グループをスケーリングする - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 負荷の増加に対処するためにサーバー グループのメモリ、ディスク、CPU リソースを調整する
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: 85a1f0dcc2e778a09cf0d19b2a85d6faf371f032
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88134524"
---
# <a name="server-group-size"></a>サーバー グループのサイズ

Hyperscale (Citus) デプロイ オプションでは、協調するデータベース サーバーを使用して、クエリの実行を並列化し、より多くのデータを格納します。 サーバー グループの "サイズ" とは、サーバーの数と、それぞれのハードウェア リソースの両方を意味します。

## <a name="picking-initial-size"></a>初期サイズの選択

ノードの数とそのハードウェア容量に関するサーバー グループのサイズは、簡単に変更できます ([下記参照](#scale-a-hyperscale-citus-server-group))。 ただし、新しいサーバー グループの初期サイズを選択する必要があります。 ここでは、適切な選択に向けたヒントをいくつか紹介します。

### <a name="multi-tenant-saas-use-case"></a>マルチテナント SaaS のユース ケース

既存の単一ノード PostgreSQL データベース インスタンスから Hyperscale (Citus) に移行する場合は、ワーカー仮想コアの数と RAM の合計が、元のインスタンスでの合計と等しいクラスターを選択することをお勧めします。 このようなシナリオでは、シャーディングによるリソース使用率の向上や、より小さいインデックスの許可などのため、パフォーマンスが 2 倍から 3 倍向上しています。

コーディネーター ノードに必要な仮想コアの数は、既存のワークロード (書き込み/読み取りスループット) によって異なります。 コーディネーター ノードは、ワーカー ノードほど RAM を必要としませんが、RAM 割り当ては仮想コア数に基づいて決定されるため ([ハイパースケール構成オプション](concepts-hyperscale-configuration-options.md)の説明を参照)、本質的に仮想コア数は、実際に決定された数です。

### <a name="real-time-analytics-use-case"></a>リアルタイム分析のユース ケース

仮想コアの合計: 作業データが RAM に収まる場合、Hyperscale (Citus) では、ワーカー コアの数に比例してパフォーマンスが直線的に向上することが期待できます。 ニーズに合った適切な仮想コア数を決定するには、単一ノード データベースでのクエリの現在の待機時間と、Hyperscale (Citus) で必要な待機時間を考慮します。 現在の待機時間を必要な待機時間で除算し、結果を丸めます。

ワーカー RAM: 最適なケースは、ワーキング セットの大部分がメモリに収まる十分なメモリを提供することです。 アプリケーションによって使用されるクエリの種類は、メモリ要件に影響します。 クエリで EXPLAIN ANALYZE を実行して、必要なメモリの量を確認できます。 [ハイパースケール構成オプション](concepts-hyperscale-configuration-options.md)に関する記事で説明されているように、仮想コアと RAM はまとめてスケーリングされることに注意してください。

## <a name="scale-a-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループをスケーリングする

Azure Database for PostgreSQL - Hyperscale (Citus) では、増加した負荷に対処するためにセルフサービス スケーリングを行うことができます。 Azure portal を使用すると、簡単に新しいワーカー ノードを追加したり既存ノードの仮想コアを増やしたりできます。 ノードを追加するとダウンタイムが発生しなくなります。また、クエリを中断させることなく、新しいノードにシャードを移動 ([シャードの再調整](#rebalance-shards)と呼ばれます) することも可能です。

### <a name="add-worker-nodes"></a>ワーカー ノードの追加

ノードを追加するには、Hyperscale (Citus) サーバー グループの **[構成]** タブに移動します。  **[ワーカー ノードの数]** のスライダーをドラッグすると、値が変化します。

![リソースのスライダー](./media/howto-hyperscale-scaling/01-sliders-workers.png)

**[保存]** ボタンをクリックして、変更した値を有効にします。

> [!NOTE]
> ワーカー ノードの数は、いったん増やして保存すると、スライダーを使用して減らすことはできません。

#### <a name="rebalance-shards"></a>シャードの再調整

新しく追加したノードを利用するには、分散テーブルの[シャード](concepts-hyperscale-distributed-data.md#shards)を再調整する必要があります。これは、既存のノードから新しいノードにいくつかのシャードを移動することを意味します。 まず、新しいワーカーがプロビジョニングを正常に完了したことを確認します。 次に、シャードを再調整するバランサーを開始します。そのためには、psql を使用してクラスター コーディネーター ノードに接続し、以下を実行します。

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards` 関数により、引数に指定したテーブルの[コロケーション](concepts-hyperscale-colocation.md) グループ内にあるすべてのテーブルが再調整されます。 したがって、すべての分散テーブルに対してその関数を呼び出す必要はありません。各コロケーション グループの代表的なテーブルに対して呼び出すだけです。

### <a name="increase-or-decrease-vcores-on-nodes"></a>ノードの仮想コアを増減させる

> [!NOTE]
> 現在、この機能はプレビュー段階にあります。 サーバー グループのノードを対象に仮想コアの変更を要求するには、[Azure サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

新しいノードを追加するだけでなく、既存のノードの能力を増強することもできます。 計算処理能力の調整 (増減) は、パフォーマンス実験のほか、トラフィック需要の短期的変更または長期的変更に役立つ場合があります。

すべてのワーカー ノードの仮想コアを変更するには、 **[構成 (ワーカー ノードあたり)]** の下の **[仮想コア]** スライダーを調整します。 コーディネーター ノードの仮想コアは、個別に調整することができます。 **[構成 (コーディネーター ノード)]** の **[仮想コア]** スライダーを調整します。

## <a name="next-steps"></a>次の手順

- サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
