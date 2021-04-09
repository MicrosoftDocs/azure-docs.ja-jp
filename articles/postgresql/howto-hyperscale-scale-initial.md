---
title: 初期サーバー グループ サイズ - Hyperscale (Citus) - Azure Database for PostgreSQL
description: ユース ケースに適切な初期サイズを選択する
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026405"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Hyperscale (Citus) サーバー グループの初期サイズを選択する

サーバー グループのサイズは、ノードの数とそのハードウェア容量のいずれも、[簡単に変更できます](howto-hyperscale-scale-grow.md)。 ただし、新しいサーバー グループの初期サイズを選択する必要があります。 ここでは、適切な選択に向けたヒントをいくつか紹介します。

## <a name="multi-tenant-saas-use-case"></a>マルチテナント SaaS のユース ケース

既存の単一ノード PostgreSQL データベース インスタンスから Hyperscale (Citus) への移行時には、ワーカー仮想コアの数と RAM の合計が、元のインスタンスでの合計と等しいクラスターを選択します。 このようなシナリオでは、シャーディングによるリソース使用率の向上や、より小さいインデックスの許可などのため、パフォーマンスが 2 倍から 3 倍向上しています。

実際には、仮想コアの数を決定するだけです。 RAM 割り当ては、現在、[Hyperscale (Citus) 構成オプション](concepts-hyperscale-configuration-options.md)のページに記載されているように、仮想コアの数に基づいて決定されます。
コーディネーター ノードにはワーカーと同量の RAM は必要ありませんが、RAM と仮想コアを別々に選択する方法はありません。

## <a name="real-time-analytics-use-case"></a>リアルタイム分析のユース ケース

仮想コアの合計: 作業データが RAM に収まる場合、Hyperscale (Citus) では、ワーカー コアの数に比例してパフォーマンスが直線的に向上することが期待できます。 ニーズに合った適切な仮想コア数を決定するには、単一ノード データベースでのクエリの現在の待機時間と、Hyperscale (Citus) で必要な待機時間を考慮します。 現在の待機時間を必要な待機時間で除算し、結果を丸めます。

ワーカー RAM: 最適なケースは、ワーキング セットの大部分がメモリに収まる十分なメモリを提供することです。 アプリケーションによって使用されるクエリの種類は、メモリ要件に影響します。 クエリで EXPLAIN ANALYZE を実行して、必要なメモリの量を確認できます。 [Hyperscale (Citus) 構成オプション](concepts-hyperscale-configuration-options.md)に関する記事で説明されているように、仮想コアと RAM はまとめてスケーリングされることに注意してください。

## <a name="next-steps"></a>次のステップ

- [サーバー グループをスケーリングする](howto-hyperscale-scale-grow.md)
- サーバー グループの[パフォーマンス オプション](concepts-hyperscale-configuration-options.md)の詳細を確認します。
