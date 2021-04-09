---
title: Speech コンテナーをインストールする
titleSuffix: Azure Cognitive Services
description: テキスト読み上げ helm チャートの構成オプションについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002286"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>テキスト読み上げ (サブチャート: charts/textToSpeech)

「アンブレラ」チャートを上書きするには、任意のパラメーターに `textToSpeech.` プレフィックスを追加して限定します。 たとえば、`textToSpeech.numberOfConcurrentRequest` が `numberOfConcurrentRequest` を上書きするように、対応するパラメーターを上書きします。

|パラメーター|説明|Default|
| -- | -- | -- |
| `enabled` | **テキスト読み上げ** サービスが有効かどうか。 | `false` |
| `numberOfConcurrentRequest` | **テキスト読み上げ** サービスの同時要求数。 このチャートは、この値に基づいて CPU およびメモリ リソースを自動的に計算します。 | `2` |
| `optimizeForTurboMode`| サービスがテキスト ファイルを使用してテキストの入力を最適化する必要があるかどうか。 `true` の場合、このチャートではサービスにより多くの CPU リソースを割り当てます。 | `false` |
| `image.registry`| **テキスト読み上げ** Docker イメージ レジストリ。 | `containerpreview.azurecr.io` |
| `image.repository` | **テキスト読み上げ** Docker イメージ リポジトリ。 | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **テキスト読み上げ** Docker イメージ タグ。 | `latest` |
| `image.pullSecrets` | **テキスト読み上げ** Docker イメージをプルするためのイメージ シークレット。 | |
| `image.pullByHash`| Docker イメージをハッシュでプルするかどうか。 `true` の場合、`image.hash` は必須です。 | `false` |
| `image.hash`| **テキスト読み上げ** Docker イメージ ハッシュ。 `image.pullByHash: true` の場合のみ使用されます。  | |
| `image.args.eula` (必須) | ライセンスに同意したことを示します。 唯一の有効な値 `accept` です。 | |
| `image.args.billing` (必須) | 課金エンドポイント URI の値は、Azure portal の Speech の [概要] ページで入手できます。 | |
| `image.args.apikey` (必須) | 課金情報の追跡に使用されます。 ||
| `service.type` | **テキスト読み上げ** サービスの Kubernetes サービスの種類。 詳細およびクラウド プロバイダーのサポートの検証については、[Kubernetes サービスの種類の手順](https://kubernetes.io/docs/concepts/services-networking/service/)に関する記事を参照してください。 | `LoadBalancer` |
| `service.port`|  **テキスト読み上げ** サービスのポート。 | `80` |
| `service.annotations` | サービスのメタデータに使用される **テキスト読み上げ** の注釈。 注釈は、キーと値のペアになっています。 <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | [ポッドの水平オートスケーラー](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)が有効かどうか。 `true` の場合、`text-to-speech-autoscaler` が Kubernetes クラスターにデプロイされます。 | `true` |
| `service.podDisruption.enabled` | [ポッド中断バジェット](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)が有効かどうか。 `true` の場合、`text-to-speech-poddisruptionbudget` が Kubernetes クラスターにデプロイされます。 | `true` |