---
title: 高可用性 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 高可用性とディザスター リカバリーの概念
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 406b4f2ada665d65ee0452579e24744d1f7cfc63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91314855"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL の高可用性 - Hyperscale (Citus)

高可用性 (HA) は、サーバー グループ内の各ノードのスタンバイ レプリカを維持することでデータベースのダウンタイムを回避する機能です。 ノードがダウンした場合、Hyperscale (Citus) は、障害が発生したノードからそのスタンバイに受信接続を切り替えます。 フェールオーバーは数分で完了し、昇格されたノードには、PostgreSQL の同期ストリーミング レプリケーションを通じて常に最新のデータが格納されます。

コーディネーター ノードで HA を利用するためには、接続の切断とトランザクションの失敗をデータベース アプリケーションが検出して再試行する必要があります。 新たに昇格されたコーディネーターには、同じ接続文字列でアクセスできます。

復旧は、検出、フェールオーバー、完全復旧の 3 つのステージに分けることができます。  すべてのノードは、Hyperscale (Citus) によって定期的に正常性チェックが実行されます。チェックで 4 回不合格になると、そのノードはダウンしていると判断されます。 その後 Hyperscale (Citus) は、スタンバイ ノードの状態をプライマリに昇格させ (フェールオーバー)、新しいスタンバイ候補をプロビジョニングします。
ストリーミング レプリケーションが開始されて、新しいノードが最新の状態になります。  すべてのデータのレプリケートが完了したとき、ノードは完全復旧の状態に達します。

### <a name="next-steps"></a>次のステップ

- Hyperscale (Citus) サーバー グループで[高可用性を有効にする](howto-hyperscale-high-availability.md)方法について学習します。
