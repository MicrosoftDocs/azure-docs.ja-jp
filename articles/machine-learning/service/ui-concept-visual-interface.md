---
title: ビジュアル インターフェイス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 用のビジュアル インターフェイス (プレビュー) を構成する用語、概念、ワークフローについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: f560887a48ce4754b26a54ef0e18093c5577af34
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128801"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Azure Machine Learning 用のビジュアル インターフェイスとは 

Azure Machine Learning 用のビジュアル インターフェイス (プレビュー) では、コードを記述することなく、機械学習モデルのデータの準備、トレーニング、テスト、デプロイ、管理、追跡を行うことができます。

プログラミングは必要ありません。[データセット](#dataset)と[モジュール](#module)を視覚的に接続して、モデルを構築します。

ビジュアル インターフェイスでは、Azure Machine Learning [ワークスペース](concept-workspace.md)を使用し、次の操作を行います。

+ ワークスペースに示す[実験](#experiment)の成果物を作成する
+ [データセット](#dataset)にアクセスする
+ ワークスペースで[コンピューティング リソース](#compute)を使用して実験を実行する 
+ [モデル](concept-azure-machine-learning-architecture.md#models)を登録する
+ ワークスペース内のコンピューティング リソースでモデルを Web サービスとして[デプロイ](#deployment)する

![ビジュアル インターフェイスの概要](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>ワークフロー

ビジュアル インターフェイスには、すばやくモデルでビルド、テスト、反復処理を行うために、対話型のビジュアル キャンバスが備わっています。 

+ [モジュール](#module)をキャンバスにドラッグ アンド ドロップします。
+ 複数のモジュールに接続して[実験](#experiment)を形成します。
+ Machine Learning Service ワークスペースのコンピューティング リソースを使用して実験を実行します。
+ 実験を編集してもう一度実行することで、モデル デザインを反復処理します。
+ 準備ができたら、**トレーニング実験**を**予測実験**に変換します。
+ ご利用のモデルに他のユーザーがアクセスできるように、予測実験を Web サービスとして[デプロイ](#deployment)します。

## <a name="experiment"></a>実験

実験を最初から作成するか、既存のサンプル実験をテンプレートとして使用します。  実験を実行するたびに、成果物は自分のワークスペースに格納されます。

実験には、接続してモデルを構築するために、データセットと分析モジュールが含まれます。 有効な実験に求められる具体的な条件を以下に示します。

* データセットはモジュールにのみ接続できる
* モジュールはデータセットにも別のモジュールにも接続できる
* モジュールのすべての入力ポートに、データ フローへの何らかの接続がある
* 各モジュールの必須パラメーターがすべて設定されている


ビジュアル インターフェイスの使用を開始する方法については、「[チュートリアル: ビジュアル インターフェイスで自動車価格を予測する](ui-tutorial-automobile-price-train-score.md)」を参照してください。

## <a name="dataset"></a>Dataset

データセットは、モデリング プロセスで使用するために、ビジュアル インターフェイスにアップロードされているデータです。 実験で試すことができる多くのサンプル データセットが含まれており、必要に応じて、さらにデータセットをアップロードできます。

## <a name="module"></a>モジュール

モジュールとは、データに対して実行できるアルゴリズムのことです。 ビジュアル インターフェイスには、データのイングレス機能や、プロセスのトレーニング、スコアリング、検証などのさまざまなモジュールが用意されています。

モジュールに一連のパラメーターが含まれている場合、これらを使用してモジュールの内部アルゴリズムを構成することができます。 キャンバスでモジュールを選択すると、モジュールのパラメーターは、キャンバス右側の [プロパティ] ウィンドウに表示されます。 このウィンドウでパラメーターを変更することにより、モデルを微調整できます。

![モジュールのプロパティ](media/ui-concept-visual-interface/properties.png)

利用できる機械学習アルゴリズムのライブラリ内の移動に関するヘルプについては、「[アルゴリズムとモジュールのリファレンスの概要](../algorithm-module-reference/module-reference.md)」を参照してください。

## <a name="compute"></a> コンピューティング リソース

自分のワークスペースからコンピューティング リソースを使用して、実験を実行するか、デプロイ済みのモデルを Web サービスとしてホストします。 サポートされているコンピューティング ターゲットを次に示します。


| コンピューティング ターゲット | トレーニング | Deployment |
| ---- |:----:|:----:|
| Azure Machine Learning コンピューティング | ✓ | |
| Azure Kubernetes Service | | ✓ |

コンピューティング先は、自分の Machine Learning [ワークスペース](concept-workspace.md)に接続されています。 [Azure portal](https://portal.azure.com) または[ワークスペースのランディング ページ (プレビュー)](https://ml.azure.com) で、自分のワークスペースのコンピューティング先を管理します。

## <a name="deployment"></a>Deployment

予測分析モデルの準備が整ったら、それをビジュアル インターフェイスから Web サービスとしてデプロイします。

Web サービスでは、アプリケーションと自分のスコアリング モデルの間のインターフェイスが提供されます。 外部のアプリケーションでは、スコアリング モデルとリアルタイムで通信できます。 Web サービスを呼び出すと、予測結果が外部のアプリケーションに返されます。 Web サービスの呼び出しを実行するには、Web サービスのデプロイ時に作成された API キーを渡します。 Web サービスは、Web プログラミング プロジェクトで広く使われているアーキテクチャの REST に基づいています。

モデルのデプロイ方法の詳細については、「[チュートリアル: ビジュアル インターフェイスで機械学習モデルをデプロイする](ui-tutorial-automobile-price-deploy.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* 予測分析と機械学習の基本について学習する: [チュートリアル: ビジュアル インターフェイスで自動車価格を予測する](ui-tutorial-automobile-price-train-score.md)
* いずれかのサンプルを使用して、ニーズに合わせて変更します。
    * [サンプル 1 - 回帰: 価格を予測する](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [サンプル 2 - 回帰: 価格を予測し、アルゴリズムを比較する](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [サンプル 3 - 分類: 信用リスクを予測する](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [サンプル 4 - 分類: 信用リスクを予測する (費用重視)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [サンプル 5 - 分類:顧客離れ、強い欲求、アップセルを予測する](how-to-ui-sample-classification-predict-churn.md)
