---
title: アルゴリズムとモジュールのリファレンス
description: 独自の機械学習プロジェクトの作成に使用できる Azure Machine Learning デザイナー モジュールについて説明します。
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/09/2020
ms.openlocfilehash: 89ad9aae7c0d01971bbcfc7e392cb9d455ef85cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94376844"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーのアルゴリズムとモジュールのリファレンス

このリファレンス コンテンツでは、Azure Machine Learning デザイナーで使用可能な各機械学習アルゴリズムとモジュールの技術的な背景について説明します。

各モジュールは、個別に実行可能なコードのセットを表し、必要な入力を取得して機械学習タスクを実行します。 モジュールには、特定のアルゴリズムが含まれているものや、機械学習において重要なタスク (欠損値の置換、統計分析など) を実行するものがあります。

アルゴリズムの選択の詳細については、以下を参照してください。 
* [アルゴリズムの選択方法](../how-to-select-algorithms.md)
* [Azure Machine Learning アルゴリズム チート シート](../algorithm-cheat-sheet.md)

> [!TIP]
> デザイナー内のパイプラインでは、特定のモジュールに関する情報を取得できます。 モジュールの一覧、またはモジュールの右側のウィンドウで、モジュールにカーソルを合わせ、モジュール カードの **[詳細情報]** リンクを選択します。

## <a name="data-preparation-modules"></a>データ準備モジュール


| 機能 | 説明 | Module |
| --- |--- | --- |
| データの入力と出力 | クラウド ソースのデータをパイプラインに移動します。 パイプラインの実行中に、結果や中間データを Azure Storage、SQL Database、または Hive に書き込みます。また、クラウド ストレージを使用して、パイプライン間でデータを交換します。  | [データの手動入力](enter-data-manually.md) <br/> [データのエクスポート](export-data.md) <br/> [データのインポート](import-data.md) |
| [データの変換] | 機械学習に固有のデータに対する操作 (データの正規化やビン分割、次元削減、さまざまなファイル形式の間でのデータの変換など)。| [列の追加](add-columns.md) <br/> [行の追加](add-rows.md) <br/> [算術演算の適用](apply-math-operation.md) <br/> [SQL 変換の適用](apply-sql-transformation.md) <br/> [見つからないデータのクリーンアップ](clean-missing-data.md) <br/> [クリップの値](clip-values.md) <br/> [CSV への変換](convert-to-csv.md) <br/> [データセットへの変換](convert-to-dataset.md) <br/> [インジケーター値への変換](convert-to-indicator-values.md) <br/> [メタデータの編集](edit-metadata.md) <br/> [データをビンにグループ化する](group-data-into-bins.md) <br/> [データの結合](join-data.md) <br/> [データの正規化](normalize-data.md) <br/> [パーティションとサンプル](partition-and-sample.md)  <br/> [重複する行の削除](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [列変換の選択](select-columns-transform.md) <br/> [データセット内の列の選択](select-columns-in-dataset.md) <br/> [データの分割](split-data.md) |
| 特徴選択 | 分析モデルの構築で使用する、関連する有用な特徴のサブセットを選択します。 | [フィルターに基づく特徴選択](filter-based-feature-selection.md) <br/> [順列の特徴量の重要度](permutation-feature-importance.md) |
| 統計関数 | データ サイエンスに関連するさまざまな統計的方法を提供します。 | [データの集計](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>機械学習のアルゴリズム

| 機能 | 説明 | Module |
| --- |--- | --- |
| 回帰 | 値を予測します。 | [ブースト デシジョン ツリー回帰](boosted-decision-tree-regression.md) <br/> [デシジョン フォレスト回帰](decision-forest-regression.md) <br/> [高速フォレスト分位点回帰](fast-forest-quantile-regression.md)  <br/> [線形回帰](linear-regression.md)  <br/> [ニューラル ネットワーク回帰](neural-network-regression.md)  <br/> [ポワソン回帰](poisson-regression.md)  <br/>|
| クラスタリング | データをグループ化します。| [K-Means クラスタリング](k-means-clustering.md)
| 分類 | クラスを予測します。  バイナリ (2 クラス) または多クラスのアルゴリズムを選択します。| [多クラスの増幅デシジョン ツリー](multiclass-boosted-decision-tree.md) <br/> [多クラス デシジョン フォレスト](multiclass-decision-forest.md) <br/> [多クラス ロジスティック回帰](multiclass-logistic-regression.md)  <br/> [多クラス ニューラル ネットワーク](multiclass-neural-network.md) <br/> [1 対全多クラス](one-vs-all-multiclass.md) <br/> [One vs.One Multiclass](one-vs-one-multiclass.md) <br/>[2 クラス平均化パーセプトロン](two-class-averaged-perceptron.md) <br/>  [2 クラス ブースト デシジョン ツリー](two-class-boosted-decision-tree.md)  <br/> [2 クラス デシジョン フォレスト](two-class-decision-forest.md) <br/>  [2 クラス ロジスティック回帰](two-class-logistic-regression.md) <br/> [2 クラス ニューラル ネットワーク](two-class-neural-network.md) <br/> [2 クラス サポート ベクター マシン](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>モデルを構築および評価するためのモジュール

| 機能 | 説明 | Module |
| --- |--- | --- |
| モデル トレーニング | アルゴリズムを介してデータを実行します。 |  [クラスタリング モデルのトレーニング](train-clustering-model.md) <br/> [モデルのトレーニング](train-model.md) <br/> [PyTorch モデルのトレーニング](train-pytorch-model.md) <br/> [モデルのハイパーパラメーターの調整](tune-model-hyperparameters.md) |
| モデルのスコアリングと評価 | トレーニング済みモデルの正確性を測定します。 | [変換の適用](apply-transformation.md) <br/> [クラスターへのデータの割り当て](assign-data-to-clusters.md) <br/> [モデルのクロス検証](cross-validate-model.md) <br/> [モデルの評価](evaluate-model.md) <br/> [画像モデルのスコア付け](score-image-model.md) <br/> [モデルのスコア付け](score-model.md) |
| Python 言語 | コードを記述し、それをモジュール内に埋め込んで、Python をパイプラインと統合します。 | [Python モデルの作成](create-python-model.md) <br/> [Python スクリプトの実行](execute-python-script.md) |
| R 言語 | コードを記述し、それをモジュール内に埋め込んで、R をパイプラインと統合します。 | [R スクリプトの実行](execute-r-script.md) |
| Text Analytics | 構造化テキストと非構造化テキストの両方を操作するための特別な計算ツールを提供します。 |  [単語からベクトルへの変換](convert-word-to-vector.md) <br/> [テキストからの N Gram 特徴抽出](extract-n-gram-features-from-text.md) <br/> [特徴ハッシュ](feature-hashing.md) <br/> [テキストの前処理](preprocess-text.md) <br/> [Latent Dirichlet Allocation](latent-dirichlet-allocation.md) <br/> [Score Vowpal Wabbit Model (Vowpal Wabbit モデルのスコアリング)](score-vowpal-wabbit-model.md) <br/> [Train Vowpal Wabbit Model (Vowpal Wabbit モデルのトレーニング)](train-vowpal-wabbit-model.md)|
| Computer Vision | 画像データの前処理と画像認識関連のモジュール。 |  [イメージ変換の適用](apply-image-transformation.md) <br/> [イメージ ディレクトリへの変換](convert-to-image-directory.md) <br/> [イメージ変換の初期化](init-image-transformation.md) <br/> [イメージ ディレクトリの分割](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| 推奨 | 推奨モデルを構築します。 | [レコメンダーの評価](evaluate-recommender.md) <br/> [SVD レコメンダーのスコア付け](score-svd-recommender.md) <br/> [ワイドかつディープなレコメンダーのスコア付け](score-wide-and-deep-recommender.md)<br/> [SVD レコメンダーのトレーニング](train-SVD-recommender.md) <br/> [ワイドかつディープなレコメンダーのトレーニング](train-wide-and-deep-recommender.md)|
| 異常検出 | 異常検出モデルを構築します。 | [PCA ベースの異常検出](pca-based-anomaly-detection.md) <br/> [異常検出モデルのトレーニング](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Web サービス

Azure Machine Learning デザイナーでリアルタイムの推論を行うために必要な [Web サービス モジュール](web-service-input-output.md)について説明します。

## <a name="error-messages"></a>エラー メッセージ

Azure Machine Learning デザイナーでモジュールを使用しているときに発生する可能性のある[エラー メッセージと例外コード](designer-error-codes.md)について説明します。

## <a name="next-steps"></a>次のステップ

* [チュートリアル:デザイナーで自動車の価格を予測するモデルを構築する](../tutorial-designer-automobile-price-train-score.md)
