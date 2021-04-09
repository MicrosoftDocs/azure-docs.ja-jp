---
title: Azure API Management のセルフホステッド ゲートウェイにクラウド メトリックとログを構成する | Microsoft Docs
description: Azure API Management のセルフホステッド ゲートウェイにクラウド メトリックとログを構成する方法について説明します
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: c420c62e6f8f09a2b29398590cdb4ad410e5d296
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574055"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Azure API Management のセルフホステッド ゲートウェイにクラウド メトリックとログを構成する

この記事では、[セルフホステッド ゲートウェイ](./self-hosted-gateway-overview.md)にクラウド メトリックとログを構成する方法の詳細について説明します。

セルフホステッド ゲートウェイは API 管理サービスに関連付けられている必要があり、ポート 443 で Azure への送信 TCP/IP 接続が必要です。 ゲートウェイは送信接続を利用して、テレメトリを Azure に送信します (そのように構成されている場合)。 

## <a name="metrics"></a>メトリック
既定では、セルフホステッド ゲートウェイは[クラウド内](api-management-howto-use-azure-monitor.md)のマネージド ゲートウェイと同じように、[Azure Monitor](https://azure.microsoft.com/services/monitor/) を通じて多数のメトリックを出力します。 

この機能は、ゲートウェイのデプロイの ConfigMap で `telemetry.metrics.cloud` キーを使用して有効または無効にすることができます。 次に、使用可能な構成の詳細を示します。

| フィールド  | Default | 説明 |
| ------------- | ------------- | ------------- |
| telemetry.metrics.cloud  | `true` | Azure Monitor を通じてログ記録を有効にします。 値は `true`、`false` が可能です。 |


サンプル構成を次に示します。

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

セルフホステッド ゲートウェイは現在 Azure Monitor を通じて次のメトリックを出力します。

| メトリック  | 説明 |
| ------------- | ------------- |
| Requests  | 期間内の API 要求の数 |
| ゲートウェイ要求の期間 | ゲートウェイが要求を受信した時点から、応答全体が送信された時点までのミリ秒数 |
| バックエンド要求の期間 | バックエンドの IO 全体 (接続バイト、送信バイト、受信バイト) に費やされたミリ秒数  |

## <a name="logs"></a>ログ

セルフホステッド ゲートウェイは現在、[診断ログ](./api-management-howto-use-azure-monitor.md#activity-logs)をクラウドに送信しません。 ただし、セルフホステッド ゲートウェイがデプロイされている場所に[ローカルにログを構成して永続化する](how-to-configure-local-metrics-logs.md)ことができます。 

ゲートウェイが [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) にデプロイされている場合は、[コンテナーに対する Azure Monitor](../azure-monitor/containers/container-insights-overview.md) を有効にして、コンテナーからログを収集し、Log Analytics でログを表示することができます。 


## <a name="next-steps"></a>次のステップ

* セルフホステッド ゲートウェイの詳細については、[Azure API Management のセルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関する記事を参照してください
* [ローカルでのログの構成と永続化](how-to-configure-local-metrics-logs.md)について学習する
