---
title: Azure Database for MariaDB での監視
description: この記事では、Azure Database for MariaDB での監視およびアラート生成のためのメトリック (CPU、ストレージ、および接続の統計を含む) について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: f34e32b9bda83ac1185d0c7b7dcaaaa7b47161a1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58082485"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Azure Database for MariaDB での監視
サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for MariaDB には、サーバーの動作への洞察を提供する各種のメトリックが用意されています。

## <a name="metrics"></a>メトリック
すべての Azure メトリックは 1 分間隔で、各メトリックの 30 日間の履歴が保持されます。 メトリックにアラートを構成できます。 その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。

詳細な手順については、[アラートの設定方法](howto-alert-metric.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
これらのメトリックは、Azure Database for MariaDB に使用できます。

|メトリック|メトリックの表示名|単位|説明|
|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|使用されている CPU の割合|
|memory_percent|メモリの割合|Percent|使用されているメモリの割合|
|io_consumption_percent|IO の割合|Percent|使用されている IO の割合|
|storage_percent|ストレージの割合|Percent|サーバーの最大数のうち使用されているストレージの割合|
|storage_used|使用済みストレージ|Bytes|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログが含まれることがあります。|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|サーバーの最大サーバー ログ ストレージのうち、使用されているサーバー ログ ストレージの割合。|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|Bytes|使用されているサーバー ログ ストレージの量。|
|serverlog_storage_limit|サーバー ログ ストレージの上限|Bytes|このサーバーの最大サーバー ログ ストレージ。|
|storage_limit|ストレージの制限|Bytes|このサーバーの最大のストレージ|
|active_connections|アクティブな接続|Count|サーバーへのアクティブな接続の数|
|connections_failed|失敗した接続|Count|サーバーへの失敗した接続の数|
|network_bytes_egress|ネットワーク送信|Bytes|アクティブな接続全体のネットワーク送信。|
|network_bytes_ingress|Network In|Bytes|アクティブな接続全体のネットワーク受信。|

## <a name="server-logs"></a>サーバー ログ
サーバーで低速クエリ ログを有効にできます。 ログ記録の詳細については、[サーバー ログ](concepts-server-logs.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
- Azure Portal、REST API、または CLI を使用してメトリックへのアクセスおよびメトリックのエクスポートを行う方法の詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。
  - メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](howto-alert-metric.md)に関するページをご覧ください。
