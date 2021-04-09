---
title: クエリ統計コレクションを最適化する - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure Database for PostgreSQL - Single Server のクエリ統計コレクションを最適化する方法について説明します
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bc731f6f6a5a60bce0851bf8fe5874f7149f3899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90901467"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server のクエリ統計コレクションを最適化する
この記事では、Azure Database for PostgreSQL サーバーのクエリ統計コレクションの最適化方法について説明します。

## <a name="use-pg_stats_statements"></a>pg_stats_statements の使用
**pg_stat_statements** は、Azure Database for PostgreSQL で既定で有効になる PostgreSQL 拡張機能です。 この拡張機能では、サーバーで実行されるすべての SQL ステートメントの実行統計を追跡する手段が提供されます。 このモジュールでは、すべてのクエリの実行がフックされるため、些細とは言えないパフォーマンス コストが発生します。 **pg_stat_statements** を有効にすると、ディスク上のファイルにクエリ テキストが強制的に書き込まれます。

クエリ テキストが長い独自のクエリを使用する場合、または **pg_stat_statements** を積極的に監視しない場合、パフォーマンスを最大化するために **pg_stat_statements** を無効にします。 これを行うには、設定を `pg_stat_statements.track = NONE` に変更します。

一部のお客様のワークロードでは、**pg_stat_statements** を無効にすることで、パフォーマンスが最大 50 パーセント向上したことが確認されています。 pg_stat_statements の無効化によるトレードオフとして、パフォーマンスに関する問題をトラブルシューティングできなくなります。

`pg_stat_statements.track = NONE` を設定するには:

- Azure Portal で、[[PostgreSQL リソースの管理] ページに移動し、[サーバー パラメーター] ブレードを選択します](howto-configure-server-parameters-using-portal.md)。

  :::image type="content" source="./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png" alt-text="[PostgreSQL サーバー パラメーター] ブレード":::

- [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用して、az postgres server configuration set to `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE` を実行します。

## <a name="use-the-query-store"></a>クエリ ストアの使用 
Azure Database for PostgreSQL の[クエリ ストア](concepts-query-store.md)機能では、クエリ統計を追跡するより効果的な方法が提供されます。 *pg_stats_statements* を使用する代わりに、この機能を使用することをお勧めします。 

## <a name="next-steps"></a>次のステップ
[Azure Portal](howto-configure-server-parameters-using-portal.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用して、`pg_stat_statements.track = NONE` を設定することを検討します。

詳細については、次を参照してください。 
- [クエリ ストアの使用シナリオ](concepts-query-store-scenarios.md) 
- [クエリ ストアのベスト プラクティス](concepts-query-store-best-practices.md) 
