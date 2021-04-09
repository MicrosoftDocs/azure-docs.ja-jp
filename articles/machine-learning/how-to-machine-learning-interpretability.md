---
title: Azure Machine Learning におけるモデルの解釈可能性 (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK を使用したトレーニング中および推論中に、機械学習モデルで予測を行う方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: 44ccf6b6d2459b87040fcac7d9cdcd336cc7b82f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522038"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Azure Machine Learning におけるモデルの解釈可能性 (プレビュー)


## <a name="model-interpretability-overview"></a>モデルの解釈可能性の概要

データ サイエンティスト、監査者、ビジネスの意思決定者にとって、モデルの解釈可能性は会社のポリシー、業界標準、政府の規制に準拠する上で、次のように重要です。

+ データ サイエンティストは、モデルの結果が生み出す価値と正確性を経営陣や利害関係者が理解できるよう、説明する必要があります。 また、モデルをデバッグし、その改善方法についての情報に基づく決定を行うための解釈可能性も必要です。 

+ 法務監査者は、規制遵守に関してモデルを検証し、モデルの決定が人間にどのような影響を与えるかを監視するツールを必要とします。 

+ ビジネスの意思決定者は、エンド ユーザーに透明性を提供できるという安心感を求めています。 これにより、信頼を獲得して維持することができます。

モデル開発における次の 2 つの主なフェーズでは、機械学習モデルを説明できるようにすることが重要です。

+ トレーニング フェーズでは、モデル設計者と評価者が、モデルの解釈可能性出力を使用して仮説を検証し、利害関係者との信頼を構築できます。 また、モデルの分析情報をデバッグに使用したり、モデルの動作が目標に一致しているか検証したり、モデルの偏りや重要度の低い機能がないか検査したりできます。

+ 推論フェーズでは、透過性があるモデルをデプロイすることで、経営者は、モデルがどのように動作しているか、およびその意思決定が実際のユーザーにどのように作用し、どのように影響を与えているかを把握することができます。 

## <a name="interpretability-with-azure-machine-learning"></a>Azure Machine Learning での解釈可能性

モデルの解釈可能性クラスは、次の SDK パッケージを介して利用可能になります。[Azure Machine Learning 用の SDK パッケージをインストールする](/python/api/overview/azure/ml/install)方法を確認してください。

* `azureml.interpret`。Microsoft でサポートされている機能が含まれます。

一般的な用途には `pip install azureml-interpret` を使用します。

## <a name="how-to-interpret-your-model"></a>モデルを解釈する方法

SDK のクラスとメソッドを使用して、次のことを行うことができます。
+ モデル全体や個々のデータポイントの特徴量の重要度の値を生成し、モデルの予測を説明する。 
+ トレーニングおよび推論中の実際の大規模データセットに関するモデルの解釈可能性を実現する。
+ 対話型の視覚化ダッシュボードを使用して、トレーニング時のデータと説明のパターンを検出する。

機械学習では、**特徴** は、ターゲット データ ポイントの予測に使用されるデータ フィールドです。 たとえば、信用リスクを予測するために、年齢、アカウント サイズ、およびアカウントの有効期間のデータ フィールドが使用されるとします。 この場合、年齢、アカウント サイズ、およびアカウントの有効期間が **特徴** です。 特徴の重要度から、各データ フィールドがモデルの予測にどのような影響を与えたかがわかります。 たとえば、年齢は予測で頻繁に使用されるが、アカウントのサイズと有効期間は予測値に大きな影響を与えない場合があります。 このプロセスにより、データ サイエンティストは結果の予測を説明でき、利害関係者はモデルで最も重要な機能を認識できます。

## <a name="supported-interpretability-techniques"></a>サポートされている解釈可能性の手法

 `azureml-interpret` は、解釈可能なモデルをトレーニングし、ブラックボックス化した AI システムを説明するオープン ソースの python パッケージである [Interpret-Community](https://github.com/interpretml/interpret-community/) で開発された解釈能力の手法を使用します。 [Interpret-Community](https://github.com/interpretml/interpret-community/) は、この SDK でサポートされている Explainer のホストとして機能し、現在、次の解釈可能性の手法をサポートしています。

|解釈可能性の手法|説明|Type|
|--|--|--------------------|
|SHAP Tree Explainer| [SHAP](https://github.com/slundberg/shap) の Tree Explainer は、**ツリーおよびツリーのアンサンブル** に固有の多項式時間高速 SHAP 値推定アルゴリズムに注目します。|モデル固有|
|SHAP Deep Explainer| SHAP による説明に基づくと、Deep Explainer は [SHAP NIPS の論文](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)で説明されている DeepLIFT との接続上に構築されているディープ ラーニング モデルでの SHAP 値に対する高速近似アルゴリズムです。 **TensorFlow** モデルおよび TensorFlow バックエンドを使用する **Keras** モデルがサポートされています (PyTorch の暫定サポートもあります)"。|モデル固有|
|SHAP Linear Explainer| SHAP の Linear Explainer では、**線形モデル** の SHAP 値を計算し、必要に応じて機能間の相関関係を考慮します。|モデル固有|
|SHAP Kernel Explainer| SHAP の Kernel Explainer では、特別に重み付けされたローカル線形回帰を使用して、**任意のモデル** の SHAP 値を推定します。|モデル非依存|
|Mimic Explainer (グローバル サロゲート)| Mimic Explainer は、[グローバル サロゲート モデル](https://christophm.github.io/interpretable-ml-book/global.html)をトレーニングしてブラックボックス モデルを模倣するアイデアに基づいています。 グローバル サロゲート モデルは、**任意のブラック ボックス モデル** の予測をできる限り正確に近似するためにトレーニングされた本質的に解釈可能なモデルです。 データ サイエンティストは、サロゲート モデルを解釈してブラック ボックス モデルについての結論を導き出すことができます。 サロゲート モデルとして、次のいずれかの解釈可能なモデルを使用できます。LightGBM (LGBMExplainableModel)、線形回帰 (LinearExplainableModel)、確率勾配降下説明可能モデル (SGDExplainableModel)、およびデシジョン ツリー (DecisionTreeExplainableModel)。|モデル非依存|
|Permutation Feature Importance Explainer (PFI)| Permutation Feature Importance は、[Breiman のランダム フォレスト論文](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (セクション 10 を参照してください) から着想を得た、分類および回帰モデルの説明に使用される手法です。 概要を説明すると、データセット全体に対して一度に 1 つの特徴のデータをランダムにシャッフルし、対象のパフォーマンス メトリックがどのくらい変化するかを計算するしくみです。 変化が大きいほど、その特徴の重要度は高くなります。 PFI では、**基になる任意のモデル** の全体的な動作を説明できますが、個々の予測については説明しません。 |モデル非依存|

前述の解釈可能性の手法に加えて、`TabularExplainer` と呼ばれる別の SHAP ベースの Explainer がサポートされています。 モデルによっては、`TabularExplainer` はサポートされている SHAP Explainers のいずれかを使用します。

* すべてのツリー ベースのモデル向けの TreeExplainer
* DNN モデル向けの DeepExplainer
* 線形モデル向けの LinearExplainer
* その他のすべてのモデル向けの KernelExplainer

`TabularExplainer` ではまた、Direct SHAP Explainer より大きな機能とパフォーマンスの強化も行われています。

* **初期化データセットの要約作成**。 説明の速度が最も重要な場合、初期化データセットを要約し、代表的なサンプルの小さなセットを生成します。これにより、全体的な特徴と個々の特徴の重要度の値の生成速度が向上します。
* **評価データ セットのサンプリング**。 ユーザーが評価サンプルの大きなセットを渡しても、実際にはそれらのすべてを評価する必要がない場合は、サンプリング パラメーターを true に設定し、全体的なモデルの説明の計算を高速化できます。

次の図は、サポートされている Explainer の現在の構造を示しています。

[![機械学習解釈可能性のアーキテクチャ](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>サポートされている機械学習モデル

SDK の `azureml.interpret` パッケージでは、次のデータセット形式でトレーニングされたモデルがサポートされています。
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

説明関数では、モデルとパイプラインの両方が入力として受け付けられます。 モデルを提供する場合、モデルでは Scikit 規則に準拠する予測関数 `predict` または `predict_proba` が実装されている必要があります。 モデルでこれがサポートされていない場合は、Scikit で `predict` または `predict_proba` と同じ結果を生成する関数でモデルをラップし、選択した Explainer でそのラッパー関数を使用できます。 パイプラインが提供された場合、説明関数では、パイプライン スクリプトの実行から予測が返るものと想定します。 このラップ技法を使用すると、`azureml.interpret` は、PyTorch、TensorFlow、および Keras ディープラーニング フレームワークや従来の機械学習モデルによってトレーニングされたモデルをサポートできます。

## <a name="local-and-remote-compute-target"></a>ローカルとリモートのコンピューティング先

`azureml.interpret` パッケージは、ローカルおよびリモート両方のコンピューティング先で動作するように設計されています。 ローカルで実行した場合、SDK 関数はどの Azure サービスにも接続されません。 

Azure Machine Learning コンピューティング上で説明をリモートで実行し、説明情報を Azure Machine Learning 実行履歴サービスに記録できます。 この情報が記録されると、ユーザーによる分析のため、説明からのレポートや視覚化を、Azure Machine Learning スタジオですぐに使用できます。


## <a name="next-steps"></a>次のステップ

- ローカルと Azure Machine Learning リモート コンピューティング リソースの両方で解釈可能性を有効にし、モデルのトレーニングを行う[方法](how-to-machine-learning-interpretability-aml.md)を参照してください。 
- その他のシナリオについては、[ノートブックのサンプル](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)を参照してください。 
- テキスト シナリオの解釈可能性に関心がある場合、NLP の解釈可能性の手法については、「[Interpret-text](https://github.com/interpretml/interpret-text)」 (「[Interpret-Community](https://github.com/interpretml/interpret-community/)」に関連するオープン ソース リポジトリ) を参照してください。 `azureml.interpret` パッケージでは、現在これらの手法をサポートしていませんが、[テキスト分類でのノートブックの例](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)を使用して開始できます。