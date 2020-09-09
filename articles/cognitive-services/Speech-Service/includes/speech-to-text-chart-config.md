---
title: Speech コンテナーをインストールする
titleSuffix: Azure Cognitive Services
description: 音声テキスト変換の helm チャートの構成オプションについて説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82876055"
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

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>センチメント分析 (サブチャート: charts/speechToText)

音声テキスト変換コンテナー v2.2.0 および Helm Chart v0.2.0 から、Text Analytics API を使用したセンチメント分析には、次のパラメーターが使用されています。

|パラメーター|説明|値|Default|
| --- | --- | --- | --- |
|`textanalytics.enabled`| **テキスト分析**サービスが有効かどうか| true/false| `false`|
|`textanalytics.image.registry`| **テキスト分析** Docker イメージ レジストリ| 有効な Docker イメージ レジストリ| |
|`textanalytics.image.repository`| **テキスト分析** Docker イメージ リポジトリ| 有効な Docker イメージ リポジトリ| |
|`textanalytics.image.tag`| **テキスト分析** Docker イメージ タグ| 有効な Docker イメージ タグ| |
|`textanalytics.image.pullSecrets`| **テキスト分析** Docker イメージをプルするためのイメージ シークレット| 有効なシークレット名| |
|`textanalytics.image.pullByHash`| ハッシュを使用して Docker イメージをプルするかどうかを指定します。  `yes` の場合は、`image.hash` にも必要です。 `no` の場合は、"false" に設定します。 既定値は `false` です。| true/false| `false`|
|`textanalytics.image.hash`| **テキスト分析** Docker イメージ ハッシュ。 `image.pullByHash:true` の場合にのみ使用してください。| 有効な Docker イメージ ハッシュ | |
|`textanalytics.image.args.eula`| **テキスト分析**コンテナーによって要求された引数の 1 つ。ライセンスに同意したことを示します。 このオプションの値は `accept` である必要があります。| コンテナーを使用する場合は、`accept` にします | |
|`textanalytics.image.args.billing`| **テキスト分析**コンテナーによって要求された引数の 1 つ。課金エンドポイント URI を指定します。 課金エンドポイント URI の値は、Azure portal の Speech の [概要] ページで入手できます。|有効な課金エンドポイント URI||
|`textanalytics.image.args.apikey`| **テキスト分析**コンテナーによって要求される引数の 1 つ。課金情報を追跡するために使用されます。| 有効な ApiKey||
|`textanalytics.cpuRequest`| **テキスト分析**コンテナーで要求された CPU| INT| `3000m`|
|`textanalytics.cpuLimit`| **テキスト分析**コンテナーの CPU 制限| | `8000m`|
|`textanalytics.memoryRequest`| **テキスト分析**コンテナーで要求されたメモリ| | `3Gi`|
|`textanalytics.memoryLimit`| **テキスト分析**コンテナーのメモリ制限| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| センチメント分析の URI サフィックス。URI 全体は "http://`<service>`:`<port>`/`<sentimentURISuffix>`" という形式になっています。 | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Kubernetes の**テキスト分析**サービスの種類。 詳細については、[Kubernetes サービスの種類](https://kubernetes.io/docs/concepts/services-networking/service/)に関する記事を参照してください | 有効な Kubernetes サービスの種類 | `LoadBalancer` |
|`textanalytics.service.port`| **テキスト分析** サービスのポート| INT| `50085`|
|`textanalytics.service.annotations`| ユーザーが**テキスト分析**サービスのメタデータに追加できる注釈。 次に例を示します。<br/> **注釈:**<br/>`   ` **some/annotation1: value1**<br/>`  ` **some/annotation2: value2** | 注釈 (各行に 1 つ)| |
|`textanalytics.serivce.autoScaler.enabled`| [ポッドの水平オートスケーラー](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)が有効かどうか。 有効の場合、`text-analytics-autoscaler` が Kubernetes クラスターにデプロイされます | true/false| `true`|
|`textanalytics.service.podDisruption.enabled`| [ポッド中断バジェット](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)が有効かどうか。 有効の場合、`text-analytics-poddisruptionbudget` が Kubernetes クラスターにデプロイされます| true/false| `true`|
