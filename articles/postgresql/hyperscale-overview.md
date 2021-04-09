---
title: Azure Database for PostgreSQL - Hyperscale (Citus) の概要
description: Hyperscale (Citus) デプロイ オプションの概要を示します
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 1734128384d63749d3c777cf6315278fced9d140
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025142"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) とは

Azure Database for PostgreSQL は、開発者向けに構築された、Microsoft クラウドのリレーショナル データベース サービスです。 これは、オープンソースの [PostgreSQL](https://www.postgresql.org/) データベース エンジンのコミュニティ バージョンに基づいています。

Hyperscale (Citus) は、シャーディングを使用して複数のマシン間でクエリを水平にスケーリングするデプロイ オプションです。 そのクエリ エンジンにより、大規模なデータセットに対する応答を高速化するために、これらのサーバー間で受信 SQL クエリが並列処理されます。 他のデプロイ オプションより大きいスケールとパフォーマンスを必要とするアプリケーション (通常は、100 GB のデータに近づいているか、または既に超えているワークロード) に対して、サービスを提供します。

Hyperscale (Citus) では以下が提供されます:

- シャーディングを使用した複数のマシン間での水平スケーリング
- 大規模なデータセットに対する応答を迅速化するため、これらのサーバー間でクエリを並列処理
- マルチテナント アプリケーション、リアルタイムの運用分析、および高スループットのトランザクション ワークロードに対する優れたサポート

PostgreSQL 向けにビルドされたアプリケーションでは、標準の[接続ライブラリ](./concepts-connection-libraries.md)と最小限の変更により、Hyperscale (Citus) に対して分散クエリを実行できます。

## <a name="next-steps"></a>次のステップ

- まず、最初の Azure Database for PostgreSQL - Hyperscale (Citus) サーバー グループを[作成](./quickstart-create-hyperscale-portal.md)してください。
- コストの比較と計算については、[価格のページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。 Hyperscale (Citus) は、前払いの予約インスタンスの割引も提供します。詳細については、[Hyperscale (Citus) RI の価格](concepts-hyperscale-reserved-pricing.md)ページを参照してください。
- サーバー グループに最適な[初期サイズ](howto-hyperscale-scale-initial.md)を決定する
