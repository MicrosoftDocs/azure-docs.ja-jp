---
title: Azure Database for MariaDB 用のサーバー ログ
description: Azure Database for MariaDB で利用できるログと、さまざまなログ記録レベルを有効にするため利用可能なパラメーターについて説明します。
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a26f61eb199d8f370e1a9dd010932dc868b74ae4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545180"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Azure Database for MariaDB でのサーバー ログ
Azure Database for MariaDB では、ユーザーは低速クエリ ログを使用できます。 トランザクション ログへのアクセスはサポートされていません。 低速クエリ ログは、トラブルシューティングの目的でパフォーマンスのボトルネックを特定するために使用できます。

低速クエリ ログについて詳しくは、[低速クエリ ログ](https://mariadb.com/kb/en/library/slow-query-log-overview/)に関する MariaDB のドキュメントをご覧ください。

## <a name="access-server-logs"></a>サーバー ログへのアクセス
Azure portal と Azure CLI を使用して、Azure Database for MariaDB のサーバー ログを一覧表示およびダウンロードできます。

Azure portal で Azure Database for MariaDB サーバーを選択します。 **[監視]** の見出しの下の、**[サーバー ログ]** ページを選択します。

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

## <a name="log-retention"></a>ログのリテンション期間
ログは、作成日から最大 7 日間使用できます。 使用可能なログの合計サイズが 7 GB を超える場合は、空き領域を利用できるようになるまで、古いファイルから削除されます。

ログのローテーションは、24 時間ごとか 7 GB ごとのどちらか早い方のタイミングで行われます。

## <a name="configure-logging"></a>ログの構成
既定では、低速クエリ ログは無効です。 有効にするには、slow_query_log をオンに設定します。

調整できるその他のパラメーターは次のとおりです。

- **long_query_time**: long_query_time (秒単位) より長いクエリがある場合は、そのクエリが記録されます。 既定値は 10 秒です。
- **log_slow_admin_statements**: オンの場合は、slow_query_log に書き込まれるステートメントに、ALTER_TABLE や ANALYZE_TABLE などの管理ステートメントが含まれます。
- **log_queries_not_using_indexes**: インデックスを使用していないクエリを slow_query_log に記録するかどうかを決定します。
- **log_throttle_queries_not_using_indexes**:このパラメーターは、低速クエリ ログに書き込むことができる、インデックスを使用していないクエリの数を制限します。 このパラメーターは、Log_queries_not_using_indexes がオンに設定されている場合に有効です。

低速クエリ ログのパラメーターの完全な説明については、MariaDB の[低速クエリ ログのドキュメント](https://mariadb.com/kb/en/library/slow-query-log-overview/)を参照してください。

## <a name="next-steps"></a>次の手順
- [Azure portal からサーバー ログを構成しアクセスする方法](howto-configure-server-logs-portal.md)
