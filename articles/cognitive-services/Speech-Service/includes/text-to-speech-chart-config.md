---
title: Speech コンテナーをインストールする
titleSuffix: Azure Cognitive Services
description: テキスト読み上げ helm チャートの構成オプションについて説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717214"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>テキスト読み上げ (サブチャート: charts/textToSpeech)

「アンブレラ」チャートを上書きするには、任意のパラメーターに `textToSpeech.` プレフィックスを追加して限定します。 たとえば、`textToSpeech.numberOfConcurrentRequest` が `numberOfConcurrentRequest` を上書きするように、対応するパラメーターを上書きします。

|パラメーター|説明|既定値|
| -- | -- | -- |
| `enabled` | **テキスト読み上げ**サービスが有効かどうか。 | `false` |
| `numberOfConcurrentRequest` | **テキスト読み上げ**サービスの同時要求数。 このチャートは、この値に基づいて CPU およびメモリ リソースを自動的に計算します。 | `2` |
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
| `service.type` | **テキスト読み上げ**サービスの Kubernetes サービスの種類。 詳細およびクラウド プロバイダーのサポートの検証については、[Kubernetes サービスの種類の手順](https://kubernetes.io/docs/concepts/services-networking/service/)に関する記事を参照してください。 | `LoadBalancer` |
| `service.port`|  **テキスト読み上げ**サービスのポート。 | `80` |
| `service.autoScaler.enabled` | [ポッドの水平オートスケーラー](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)が有効かどうか。 `true` の場合、`text-to-speech-autoscaler` が Kubernetes クラスターにデプロイされます。 | `true` |
| `service.podDisruption.enabled` | [ポッド中断バジェット](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)が有効かどうか。 `true` の場合、`text-to-speech-poddisruptionbudget` が Kubernetes クラスターにデプロイされます。 | `true` |