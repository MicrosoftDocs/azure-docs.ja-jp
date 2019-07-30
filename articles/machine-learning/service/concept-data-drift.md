---
title: データの誤差の監視 (プレビュー)
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service でデータの誤差を監視する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: a03e3124647869e7148f271810bb523986a851c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442379"
---
# <a name="what-is-data-drift-monitoring-preview"></a>データの誤差の監視 (プレビュー) の概要

データの誤差は、データの分布の変化です。 機械学習のコンテキストでは、トレーニングされた機械学習モデルで、誤差が原因で予測パフォーマンスが低下する場合があります。 トレーニング データと予測の作成に使用されるデータの間の誤差を監視することは、パフォーマンスの問題を検出するのに役立ちます。

機械学習モデルの有用度は、それをトレーニングするために使用されるデータと同程度です。 パフォーマンスを監視せずにモデルを運用環境にデプロイすると、検出されない有害な影響が生じる場合があります。 データの誤差の監視により、データの誤差を検出し、それに対応することができます。 

## <a name="when-to-monitor-for-data-drift"></a>データの誤差を監視するタイミング

監視できるメトリックは次のとおりです。

+ 誤差の大きさ (ドリフト係数)
+ 誤差の原因 (フィーチャー別の誤差の寄与度)
+ 距離メトリック (Wasserstein、エネルギーなど)

この監視を実施すると、誤差の検出時のアラートまたはアクションを設定でき、データ サイエンティストは問題の根本原因を調査できます。 

デプロイしたモデルの入力データが変更される可能性がある場合は、データの誤差の検出を使用することを検討してください。

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Azure Machine Learning service でデータの誤差が監視される仕組み

**Azure Machine Learning service** を使用した場合、データの誤差はデータセットまたはデプロイを通じて監視されます。 データの誤差を監視するには、基準データセット (通常は、モデルのトレーニング データセット) を指定します。 基準データセットに対して 2 番目のデータセット (通常はデプロイから収集されたモデル入力データ) がテストされます。 両方のデータセットが[プロファイリング](how-to-explore-prepare-data.md#explore-with-summary-statistics)され、データの誤差の監視サービスに入力されます。 2 つのデータセット間の違いを検出するために、機械学習モデルがトレーニングされます。 モデルのパフォーマンスは、2 つのデータセット間の誤差の大きさを測定するドリフト係数に変換されます。 [モデルの解釈可能性](machine-learning-interpretability-explainability.md)を使用して、ドリフト係数に寄与するフィーチャーが計算されます。 データセットのプロファイルから、各フィーチャーに関する統計情報が追跡されます。 

## <a name="data-drift-metric-output"></a>データの誤差のメトリック出力

誤差のメトリックを表示する方法は複数あります。

* Jupyter ウィジェットを使用する。
* 任意の `datadriftRun` オブジェクト上で `get_metrics()` 関数を使用する。
* モデルについて Azure portal でメトリックを表示する。

データの誤差タスクの実行イテレーションごとに、次のメトリックが保存されます。

|メトリック|説明|
--|--|
wasserstein_distance|1 次元の数値分布に対して定義される統計的距離。|
energy_distance|1 次元の数値分布に対して定義される統計的距離。|
datadrift_coefficient|形式的なマシューズ相関係数 (-1 から 1 の範囲の実数)。 誤差のコンテキストでは、0 は誤差がないことを示し、1 は最大誤差を示します。|
datadrift_contribution|誤差に寄与するフィーチャーの重要度。|

## <a name="next-steps"></a>次の手順

例を参照し、データの誤差を監視する方法を確認してください。

+ [Azure Kubernetes Service (AKS) を通じてデプロイされたモデルのデータの誤差を監視する方法を確認してください](how-to-monitor-data-drift.md)
+ [Jupyter Notebook のサンプル](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)をお試しください。