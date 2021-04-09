---
title: Azure API Management の監視 | Microsoft Docs
description: Azure API Management の全監視オプションの概要。
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094627"
---
# <a name="observability-in-azure-api-management"></a>Azure API Management の監視

監視とは、あるシステムから生成されるデータからそのシステムの内部状態を理解するための機能であり、そのデータをいろいろ調べ、発生した問題とその理由に関する疑問に答える機能です。 

Azure API Management を利用すると、組織は全 API の管理を一元化できます。 全 API トラフィックの単一エントリ ポイントとして機能するため、API を監視する場所として理想的です。 

## <a name="observability-tools"></a>監視ツール

次の表は、API を監視するために API Management でサポートされている全ツールをまとめたものです。それぞれのツールが 1 つまたは複数のシナリオで役に立ちます。

| ツール        | 適した用途    | データ ラグ | 保持 | サンプリング | データの種類 | Enabled|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API インスペクター](api-management-howto-api-inspector.md)** | テストとデバッグ | すぐに | 最後の 100 トレース | 要求ごとにオンにする | 要求のトレース | Always (常に)
| **ビルトイン分析** | レポートと監視 | 分 | 有効期間 | 100% | レポートとログ | Always (常に) |
| **[Azure Monitor のメトリック](api-management-howto-use-azure-monitor.md)** | レポートと監視 | 分 | 93 日 (延長アップグレード) | 100% | メトリック | Always (常に) |
| **[Azure Monitor ログ](api-management-howto-use-azure-monitor.md)** | レポート、監視、デバッグ | 分 | 31 日/5 GB (延長アップグレード) | 100% (調整可能) | ログ | 省略可能 |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | レポート、監視、デバッグ | Seconds | 90 日/5 GB (延長アップグレード) | Custom | ログ、メトリック | 省略可能 |
| **[Azure Event Hub を使用したログ](api-management-howto-log-event-hubs.md)** | カスタム シナリオ | Seconds | ユーザー管理 | Custom | Custom | 省略可能 |

### <a name="self-hosted-gateway"></a>セルフホステッド ゲートウェイ

上記のツールはすべて、クラウドのマネージド ゲートウェイでサポートされています。 [セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md)からは現在、診断ログが Azure Monitor に送信されません。 ただし、セルフホステッド ゲートウェイがデプロイされている場所にローカルにログを構成して永続化することができます。 詳細については、[セルフホステッド ゲートウェイにクラウド メトリックとログを構成する](how-to-configure-cloud-metrics-logs.md)方法に関するページと[セルフホステッド ゲートウェイにローカル メトリックとログを構成する](how-to-configure-local-metrics-logs.md)方法に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [チュートリアルに従って API Management の詳細を確認します](import-and-publish.md)
