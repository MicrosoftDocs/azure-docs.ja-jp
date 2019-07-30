---
title: Azure CDN の使用パターンを分析する | Microsoft Docs
description: この記事では、Azure CDN 製品で使用可能なさまざまな種類の分析レポートについて説明します。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 238dea3c136daf13d3db7be41bed103a0cbf7636
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593680"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Azure CDN の使用パターンを分析する

アプリケーションの CDN を有効にした後、CDN の使用状況の監視、配信の正常性の確認、潜在的な問題のトラブルシューティングを行うことができます。 Azure CDN では、これらの機能が次の方法で利用できます。 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Azure 診断ログによるコア分析

コア分析は、、すべての価格レベルの CDN エンドポイントで使用できます。 Azure Diagnostics ログを使用すると、コア分析を、Azure Storage、イベント ハブ、または Azure Monitor ログにエクスポートできます。 Azure Monitor ログには、ユーザーによる構成とカスタマイズに対応したグラフを含むソリューションが用意されています。 Azure 診断ログの詳細については、「[Azure 診断ログ](cdn-azure-diagnostic-logs.md)」を参照してください。

## <a name="verizon-core-reports"></a>Verizon コア レポート

**Azure CDN Standard from Verizon** または **Azure CDN Premium from Verizon** プロファイルを所有する Azure CDN ユーザーは、Verizon 補助ポータルで Verizon コア レポートを表示できます。 Verizon コア レポートには、Azure Portal の **[管理]** オプションからアクセスできます。このレポートには、さまざまなグラフとビューが用意されています。 詳細については、「[Verizon からのコア レポート](cdn-analyze-usage-patterns.md)」を参照してください。

## <a name="verizon-custom-reports"></a>Verizon カスタム レポート

**Azure CDN Standard from Verizon** または **Azure CDN Premium from Verizon** プロファイルを所有する Azure CDN ユーザーは、Verizon 補助ポータルで Verizon カスタム レポートを表示できます。 Verizon カスタム レポートには、Azure Portal の **[管理]** オプションからアクセスできます。 Verizon カスタム レポート ページには、Azure CDN プロファイルに属する各エッジ CName のヒット数や転送されたデータが表示されます。 データは、HTTP 応答コードまたは一定期間にわたるキャッシュの状態でグループ化できます。 詳細については、「[Custom Reports from Verizon (Verizon のカスタム レポート)](cdn-verizon-custom-reports.md)」をご覧ください。

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium from Verizon レポート

**Azure CDN Premium from Verizon** で、次のレポートにアクセスすることもできます。
   * [詳細な HTTP レポート](cdn-advanced-http-reports.md)
   * [リアルタイム統計](cdn-real-time-stats.md)
   * [エッジ ノードのパフォーマンス](cdn-edge-performance.md)

