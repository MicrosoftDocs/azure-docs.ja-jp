---
title: Anomaly Detector API とは
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API のアルゴリズムを利用し、時系列データに異常検出を適用します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: 異常検出, 機械学習, アルゴリズム
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: ad3bd1eed6e5e7efb783c4a40958e16c3ca23691
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572083"
---
# <a name="what-is-the-anomaly-detector-api"></a>Anomaly Detector API とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Anomaly Detector API では、時系列データを監視し、その中の異常を検出できます。機械学習の知識は必要ありません。 Anomaly Detector API のアルゴリズムでは、産業、シナリオ、データ量に関係なく、データに最適なモデルが自動的に特定され、適用されます。 この API では、時系列データを使用し、異常検出の境界、予想される値、異常となるデータ ポイントが判断されます。

![サービス要求のパターンの変化を検出する](./media/anomaly_detection2.png)

Anomaly Detector の使用にあたり、機械学習の経験は必要ありません。RESTful API によってサービスをアプリケーションやプロセスに簡単に統合できます。

## <a name="features"></a>特徴

Anomaly Detector を利用すると、時系列データ全体で異常を自動的に検出、あるいは、異常が発生したときにリアルタイムで検出できます。

|特徴量  |説明  |
|---------|---------|
|リアルタイムの異常検出。 | 前に確認されたデータ ポイントを利用し、最新のデータ ポイントが異常であるかどうかを判断することでストリーミング データ内の異常を検出します。 この操作では、送信したデータ ポイントを利用してモデルが生成され、ターゲットのポイントが異常であるかどうかが判断されます。 生成する新しいデータ ポイントで API を呼び出すことで、作成時にデータを監視できます。 |
|バッチとして設定されたデータ全体で異常を検出します。 | 時系列データを使用し、データ全体に存在する可能性がある異常を検出します。 この操作により、時系列データ全体を使用してモデルが生成されます。各ポイントが同じモデルで分析されます。         |
|バッチとして設定されたデータ全体で変化点を検出します。 | 時系列データを使用し、データに存在する傾向の変化点を検出します。 この操作により、時系列データ全体を使用してモデルが生成されます。各ポイントが同じモデルで分析されます。    |
| データに関する追加情報を取得します。 | 予想される値、異常の境界、位置など、データに関する役に立つ詳細と観察された異常を取得します。 |
| 異常検出の境界を調整します。 | Anomaly Detector API では、異常検出の境界が自動的に作成されます。 この境界を調整することでデータの異常に対する API の感度を増減させ、データを最適化します。 |

## <a name="demo"></a>デモ

この[対話型デモ](https://aka.ms/adDemo)をご覧になり、Anomaly Detector のしくみを理解してください。
デモを実行するには、Anomaly Detector のリソースを作成し、API キーとエンドポイントを取得する必要があります。

## <a name="notebook"></a>ノートブック

Anomaly Detector API を呼び出す方法については、こちらの [ Notebook](https://aka.ms/adNotebook) をお試しください。 この Jupyter Notebook では、API 要求を送信して結果を視覚化する方法について説明しています。

Notebook を実行するには、次の手順を完了します。

1. 有効な Anomaly Detector API サブスクリプション キーと API エンドポイントを取得します。 下のセクションに新規登録方法があります。
1. 右上隅でサインインし、[複製] を選択します。
1. 複製操作を完了する前に、ダイアログ ボックスの [パブリック] オプションをオフにします。そうしないと、すべてのサブスクリプション キーを含め、ノートブックはパブリックになります。
1. **[Run on free compute]\(無料のコンピューティングで実行\)** を選択します
1. いずれかのノートブックを選択します。
1. 有効な Anomaly Detector API サブスクリプション キーを `subscription_key` 変数に追加します。
1. `endpoint` 変数を自分のエンドポイントに変更します。 例: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. 上部のメニュー バーで **[セル]** 、 **[すべてを実行]** の順に選択します。

## <a name="workflow"></a>ワークフロー

Anomaly Detector API は RESTful Web サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

新規登録後:

1. 時系列データを取り出し、有効な JSON 形式に変換します。 最良の結果を得るために、データを準備するとき、[ベスト プラクティス](concepts/anomaly-detection-best-practices.md)を使用してください。
1. Anomaly Detector API に自分のデータを含む要求を送信します。
1. 返された JSON メッセージを解析して API 応答を処理します。

## <a name="algorithms"></a>アルゴリズム

* 使用されているアルゴリズムについては、次のテクニカル ブログを参照してください。
    * [Azure Anomaly Detector API の概要](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Azure Anomaly Detector の SR-CNN アルゴリズムの概要](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Microsoft によって開発された SR-CNN アルゴリズムの詳細については、論文「[Microsoft での時系列の異常検出サービス](https://arxiv.org/abs/1906.03821)」(KDD 2019 採択済み) を参照してください。

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>サービスの可用性と冗長性

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>Anomaly Detector サービスにゾーン回復性はありますか?

はい。 Anomaly Detector サービスは、既定ではゾーン回復性を備えています。

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>どのように Anomaly Detector サービスにゾーン回復性を構成しますか?

ゾーン回復性を有効にするために、顧客による構成は必要ありません。 Anomaly Detector リソースのゾーン回復性は、既定で使用できるようになっており、サービス自体によって管理されます。

## <a name="deploy-on-premises-using-docker-containers"></a>Docker コンテナーを使用してオンプレミスにデプロイする

[Anomaly Detector コンテナーを使用](anomaly-detector-container-howto.md)して、API 機能をオンプレミスにデプロイします。 Docker コンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由から、データにより近いところでサービスを使用できます。

## <a name="join-the-anomaly-detector-community"></a>Anomaly Detector コミュニティに参加する

* [Microsoft Teams 上の Anomaly Detector Advisors グループ](https://aka.ms/AdAdvisorsJoin)に参加する
* [ユーザーが生成した厳選されたコンテンツ](user-generated-content.md)を見る

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Anomaly Detector を使用して時系列データ内の異常を検出する](quickstarts/client-libraries.md)
* Anomaly Detector API [オンライン デモ](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Anomaly Detector [REST API リファレンス](https://aka.ms/anomaly-detector-rest-api-ref)
