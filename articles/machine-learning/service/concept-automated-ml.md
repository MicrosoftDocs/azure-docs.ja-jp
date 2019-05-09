---
title: 自動化された ML アルゴリズムの選択とチューニング
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で、モデルにとって最適なアルゴリズムを選択するために指定するパラメーターと条件を使用することで、どのようにして自動的にアルゴリズムを選択し、それからモデルを生成して時間を節約できるかを説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 620dbd22613df37fdc3c20e34906684446b2251f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546013"
---
# <a name="what-is-automated-machine-learning"></a>自動化された機械学習とは

自動化された機械学習は、定義された対象の機能を使用してトレーニング データを取得し、アルゴリズムと機能の選択の組み合わせを反復処理し、トレーニング スコアに基づいてデータに最適なモデルを自動的に選択するプロセスです。 従来の機械学習モデルの開発プロセスでは、リソースが大量に消費され、数十個のモデルを実行して結果を比較するために、その領域に関する膨大な知識と時間の投資が必要とされます。 自動化された機械学習は、実験を実行するための時間や、どのモデルをブラック リストに追加するかなど、実験のために定義した目標と制約からのチューニングが行われたモデルを生成することによって、このプロセスを簡略化します。

## <a name="how-it-works"></a>動作のしくみ

1. 解決しようとしている機械学習の問題の種類を構成します。 教師あり学習のカテゴリがサポートされています。
   + 分類
   + 回帰
   + 予測

   自動化された機械学習は一般に提供されていますが、**予測機能はまだパブリック プレビュー段階にあります。**

   [モデルの一覧](how-to-configure-auto-train.md#select-your-experiment-type)を参照してください。トレーニング時に Azure Machine Learning を試すことができます。

1. トレーニング データのソースと形式を指定します。 データにはラベルを付ける必要があり、開発環境または Azure Blob Storage に格納できます。 データを開発環境に格納する場合、トレーニング スクリプトと同じディレクトリに格納する必要があります。 このディレクトリは、トレーニング用に選択したコンピューティング ターゲットにコピーされます。

    トレーニング スクリプトでは、Numpy 配列または Pandas データフレームにデータを読み取ることができます。

    トレーニングと検証のデータを選択する分割オプションを構成するか、別のトレーニングと検証データ セットを指定できます。

1. モデルのトレーニングに使用する[コンピューティング ターゲット](how-to-set-up-training-targets.md)を構成します。

1. 自動化された機械学習構成を行います。 これは、Azure Machine Learning が、最適モデルを決定する際に確認するさまざまなモデル、ハイパーパラメーター設定、およびメトリックに対して繰り返し使用するパラメーターを制御します

1. トレーニングの実行を送信します。

トレーニング中、Azure Machine Learning サービスは、さまざまなアルゴリズムとパラメーターを試行する多数のパイプラインを作成します。 これは、指定した繰り返し回数制限に達した場合、または指定したメトリックの目標値に到達した場合に停止します。

[![自動化された機械学習](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

ログされた実行情報を検査できます。これには、実行中に収集したメトリックが含まれています。 トレーニングを実行すると、モデルおよびデータ前処理を含む Python シリアル化オブジェクト (`.pkl` ファイル) も生成されます。


## <a name="next-steps"></a>次の手順

例を参照して、Automated Machine Learning を使用してモデルを構築する方法を学習してください。

+ [チュートリアル:Azure Automated Machine Learning で分類モデルを自動的にトレーニングする](tutorial-auto-train-models.md)

+ [ノートブックのサンプル](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

+ [リモート リソースで自動トレーニングを使用する](how-to-auto-train-remote.md)

+ [自動トレーニングの設定を構成する](how-to-configure-auto-train.md)
