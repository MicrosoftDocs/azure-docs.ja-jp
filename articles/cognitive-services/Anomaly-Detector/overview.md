---
title: Anomaly Detector API とは
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API の高度なアルゴリズムを利用し、時系列データ内の異常を特定します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 09/05/2019
ms.author: aahi
ms.openlocfilehash: 3bd60ff1e732940bbb13c2e224084cf7e331266b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934874"
---
# <a name="what-is-the-anomaly-detector-api"></a>Anomaly Detector API とは

Anomaly Detector API では、機械学習を利用することで、時系列データを監視し、その中の異常を検出できます。 Anomaly Detector API では、産業、シナリオ、データ量に関係なく、データに最適なモデルが自動的に特定され、適用されます。 この API では、時系列データを使用し、異常検出の境界、予想される値、異常となるデータ ポイントが判断されます。

![サービス要求のパターンの変化を検出する](./media/anomaly_detection2.png)

Anomaly Detector の使用にあたり、機械学習の経験は必要ありません。RESTful API によってサービスをアプリケーションやプロセスに簡単に統合できます。

## <a name="features"></a>機能

Anomaly Detector を利用すると、時系列データ全体で異常を自動的に検出、あるいは、異常が発生したときにリアルタイムで検出できます。 

|機能  |説明  |
|---------|---------|
|リアルタイムで発生した異常を検出します。 | 前に確認されたデータ ポイントを利用し、最新のデータ ポイントが異常であるかどうかを判断することでストリーミング データ内の異常を検出します。 この操作では、送信したデータ ポイントを利用してモデルが生成され、ターゲットのポイントが異常であるかどうかが判断されます。 生成する新しいデータ ポイントで API を呼び出すことで、作成時にデータを監視できます。 |
|バッチとして設定されたデータ全体で異常を検出します。 | 時系列データを使用し、データ全体に存在する可能性がある異常を検出します。 この操作により、時系列データ全体を使用してモデルが生成されます。各ポイントが同じモデルで分析されます。         |
| データに関する追加情報を取得します。 | 予想される値、異常の境界、位置など、データに関する役に立つ詳細と観察された異常を取得します。 |
| 異常検出の境界を調整します。 | Anomaly Detector API では、異常検出の境界が自動的に作成されます。 この境界を調整することでデータの異常に対する API の感度を増減させ、データを最適化します。 |

## <a name="demo"></a>デモ

この[対話型デモ](https://aka.ms/adDemo)をご覧になり、Anomaly Detector のしくみを理解してください。
デモを実行するには、Anomaly Detector のリソースを作成し、API キーとエンドポイントを取得する必要があります。

## <a name="notebook"></a>ノートブック

Anomaly Detector API を呼び出す方法については、こちらの [Azure Notebook](https://aka.ms/adNotebook) をお試しください。 Web でホストされるこの Jupyter Notebook では、API 要求を送信して結果を視覚化する方法について説明しています。

Notebook を実行するには、次の手順を完了します。

1. 有効な Anomaly Detector API サブスクリプション キーと API エンドポイントを取得します。 下のセクションに新規登録方法があります。
1. 右上隅でサインインし、[複製] をクリックします。
1. 複製操作を完了する前に、ダイアログ ボックスの [パブリック] オプションをオフにします。そうしないと、ノートブックはサブスクリプション キーを含めてパブリックになります。
1. **[無料のコンピューティングで実行]** をクリックします。
1. いずれかのノートブックを選択します。
1. 有効な Anomaly Detector API サブスクリプション キーを `subscription_key` 変数に追加します。 
1. `endpoint` 変数を自分のエンドポイントに変更します。 次に例を示します。`https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. 上部のメニュー バーで **[セル]** をクリックし、 **[すべて実行]** をクリックします。

## <a name="workflow"></a>ワークフロー

Anomaly Detector API は RESTful Web サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

新規登録後:

1. 時系列データを取り出し、有効な JSON 形式に変換します。 最良の結果を得るために、データを準備するとき、[ベスト プラクティス](concepts/anomaly-detection-best-practices.md)を使用してください。
1. Anomaly Detector API に自分のデータを含む要求を送信します。
1. 返された JSON メッセージを解析して API 応答を処理します。

## <a name="algorithms"></a>アルゴリズム

* 内部のアルゴリズムについては、こちらのテクニカル ブログ「[Azure Anomaly Detector API の概要](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)」を参照してください。
* Microsoft によって開発された最先端の SR-CNN アルゴリズムについては、こちらの論文「[Microsoft での時系列の異常検出サービス](https://arxiv.org/abs/1906.03821)」 (KDD 2019 採択済み) を参照してください。

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Anomaly Detector コミュニティに参加する

* [Microsoft Teams 上の Anomaly Detector Advisors グループ](https://aka.ms/AdAdvisorsJoin)に参加する
* [ユーザーが生成した厳選されたコンテンツ](user-generated-content.md)を見る

## <a name="next-steps"></a>次の手順

* [クイック スタート:Anomaly Detector REST API を使用し、時系列データ内の異常を検出する](quickstarts/detect-data-anomalies-csharp.md)
* Anomaly Detector API [オンライン デモ](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Anomaly Detector [REST API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
