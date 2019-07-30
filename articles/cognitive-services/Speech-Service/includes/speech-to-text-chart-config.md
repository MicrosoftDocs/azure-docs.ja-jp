---
title: Speech コンテナーをインストールする
titleSuffix: Azure Cognitive Services
description: 音声テキスト変換の helm チャートの構成オプションについて説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717236"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>音声テキスト変換 (サブチャート: charts/speechToText)

「アンブレラ」チャートを上書きするには、任意のパラメーターに `speechToText.` プレフィックスを追加して限定します。 たとえば、`speechToText.numberOfConcurrentRequest` が `numberOfConcurrentRequest` を上書きするように、対応するパラメーターを上書きします。

|パラメーター|説明|既定値|
| -- | -- | -- |
| `enabled` | **音声テキスト変換**サービスが有効かどうか。 | `false` |
| `numberOfConcurrentRequest` | **音声テキスト変換**サービスの同時要求数。 このチャートは、この値に基づいて CPU およびメモリ リソースを自動的に計算します。 | `2` |
| `optimizeForAudioFile`| サービスがオーディオ ファイルを使用してオーディオの入力を最適化する必要があるかどうか。 `true` の場合、このチャートではサービスにより多くの CPU リソースを割り当てます。 | `false` |
| `image.registry`| **音声テキスト変換** Docker イメージ レジストリ。 | `containerpreview.azurecr.io` |
| `image.repository` | **音声テキスト変換** Docker イメージ リポジトリ。 | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **音声テキスト変換** Docker イメージ タグ。 | `latest` |
| `image.pullSecrets` | **音声テキスト変換** Docker イメージをプルするためのイメージ シークレット。 | |
| `image.pullByHash`| Docker イメージをハッシュでプルするかどうか。 `true` の場合、`image.hash` は必須です。 | `false` |
| `image.hash`| **音声テキスト変換** Docker イメージ ハッシュ。 `image.pullByHash: true` の場合のみ使用されます。  | |
| `image.args.eula` (必須) | ライセンスに同意したことを示します。 唯一の有効な値 `accept` です。 | |
| `image.args.billing` (必須) | 課金エンドポイント URI の値は、Azure portal の Speech の [概要] ページで入手できます。 | |
| `image.args.apikey` (必須) | 課金情報の追跡に使用されます。 ||
| `service.type` | **音声テキスト変換**サービスの Kubernetes サービスの種類。 詳細およびクラウド プロバイダーのサポートの検証については、[Kubernetes サービスの種類の手順](https://kubernetes.io/docs/concepts/services-networking/service/)に関する記事を参照してください。 | `LoadBalancer` |
| `service.port`|  **音声テキスト変換**サービスのポート。 | `80` |
| `service.autoScaler.enabled` | [ポッドの水平オートスケーラー](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)が有効かどうか。 `true` の場合、`speech-to-text-autoscaler` が Kubernetes クラスターにデプロイされます。 | `true` |
| `service.podDisruption.enabled` | [ポッド中断バジェット](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)が有効かどうか。 `true` の場合、`speech-to-text-poddisruptionbudget` が Kubernetes クラスターにデプロイされます。 | `true` |