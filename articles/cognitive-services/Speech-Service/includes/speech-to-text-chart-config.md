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
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971304"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>音声テキスト変換 (サブチャート: charts/speechToText)

「アンブレラ」チャートを上書きするには、任意のパラメーターに `speechToText.` プレフィックスを追加して限定します。 たとえば、`speechToText.numberOfConcurrentRequest` が `numberOfConcurrentRequest` を上書きするように、対応するパラメーターを上書きします。

|パラメーター|説明|Default|
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
| `service.annotations` | サービスのメタデータに使用される**音声変換**の注釈。 注釈は、キーと値のペアになっています。 <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | [ポッドの水平オートスケーラー](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)が有効かどうか。 `true` の場合、`speech-to-text-autoscaler` が Kubernetes クラスターにデプロイされます。 | `true` |
| `service.podDisruption.enabled` | [ポッド中断バジェット](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)が有効かどうか。 `true` の場合、`speech-to-text-poddisruptionbudget` が Kubernetes クラスターにデプロイされます。 | `true` |