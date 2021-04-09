---
title: Azure CDN の使用パターンを分析する
description: この記事では、Azure CDN 製品で使用可能なさまざまな種類の分析レポートについて説明します。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: allensu
ms.openlocfilehash: 2c593cda7761ce1defa6bdb31e0dbb528b9e5eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96483990"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Azure CDN の使用パターンを分析する

アプリケーションの CDN を有効にした後、CDN の使用状況の監視、配信の正常性の確認、潜在的な問題のトラブルシューティングを行うことができます。 Azure CDN では、これらの機能が次の方法で利用できます。 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Microsoft の Azure CDN の生ログ
標準の Microsoft プロファイルでは、生ログを有効にし、以下へのログのストリーミングを選択できます。

* Azure Storage
* Event Hubs
* Azure Log Analytics

Azure Log Analytics を使用すると、監視メトリックの表示やアラートの設定を行うことができます。 

詳細については、「[Azure CDN HTTP 生ログ](monitoring-and-access-log.md)」を参照してください。


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Azure 診断ログによるコア分析

コア分析は、、すべての価格レベルの CDN エンドポイントで使用できます。 Azure Diagnostics ログを使用すると、コア分析を、Azure Storage、イベント ハブ、または Azure Monitor ログにエクスポートできます。 Azure Monitor ログには、ユーザーによる構成とカスタマイズに対応したグラフを含むソリューションが用意されています。 Azure 診断ログの詳細については、「[Azure 診断ログ](cdn-azure-diagnostic-logs.md)」を参照してください。

## <a name="verizon-core-reports"></a>Verizon コア レポート

**Azure CDN Standard from Verizon** プロファイルまたは **Azure CDN Premium from Verizon** プロファイルは、コア レポートを提供します。 コア レポートは、Verizon 補助ポータルで表示できます。 Verizon コア レポートには、Azure portal の **[管理]** オプションからアクセスできます。このレポートには、さまざまなグラフとビューが用意されています。 詳細については、「[Verizon からのコア レポート](cdn-analyze-usage-patterns.md)」を参照してください。

## <a name="verizon-custom-reports"></a>Verizon カスタム レポート

**Azure CDN Standard from Verizon** プロファイルまたは **Azure CDN Premium from Verizon** プロファイルは、カスタム レポートを提供します。 カスタム レポートは、Verizon 補助ポータルで表示できます。 Verizon カスタム レポートには、Azure portal の **[管理]** オプションからアクセスできます。 

カスタム レポートには、各エッジ CNAME のヒット数または転送されたデータが表示されます。 データは、HTTP 応答コードまたは一定期間のキャッシュの状態でグループ化されます。 詳細については、「[Custom Reports from Verizon (Verizon のカスタム レポート)](cdn-verizon-custom-reports.md)」をご覧ください。

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium from Verizon レポート

**Azure CDN Premium from Verizon** で、次のレポートにアクセスすることもできます。
   * [詳細な HTTP レポート](cdn-advanced-http-reports.md)
   * [リアルタイム統計](cdn-real-time-stats.md)
   * [Azure CDN エッジ ノードのパフォーマンス](cdn-edge-performance.md)

## <a name="next-steps"></a>次のステップ
この記事では、Azure CDN の分析レポートのさまざまなオプションについて学習しました。

Azure CDN とこの記事で言及しているその他の Azure サービスの詳細については、次をご覧ください。

* [Azure CDN とは](cdn-overview.md)
* [Azure CDN HTTP 生ログ](monitoring-and-access-log.md)
