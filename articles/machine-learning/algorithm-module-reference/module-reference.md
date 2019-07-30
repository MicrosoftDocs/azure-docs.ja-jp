---
title: アルゴリズムとモジュールのリファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning のビジュアル インターフェイスで使用可能なモジュールについて説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e73d4ebd3eb05f7cf217573d8112e3dbbe6d3a37
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514082"
---
# <a name="algorithm--module-reference-overview"></a>アルゴリズムとモジュールのリファレンスの概要

このリファレンス コンテンツでは、Azure Machine Learning service のビジュアル インターフェイス (プレビュー) で使用可能な各機械学習アルゴリズムとモジュールの技術的な背景について説明します。

各モジュールは、個別に実行可能なコードのセットを表し、必要な入力を取得して機械学習タスクを実行します。 モジュールには、特定のアルゴリズムが含まれているものや、機械学習において重要なタスク (欠損値の置換、統計分析など) を実行するものがあります。

> [!TIP]
> ビジュアル インターフェイス内の実験では、特定のモジュールに関する情報を取得できます。 モジュールを選択し、 **[Quick Help]\(クイック ヘルプ\)** ウィンドウの **[more help]\(さらにヘルプを見る\)** リンクを選択します。

## <a name="modules"></a>モジュール

モジュールは、機能別に整理されています。

| 機能 | 説明 | モジュール |
| --- |--- | ---- |
| データ形式の変換 | 機械学習で使用されるさまざまなファイル形式にデータを変換します。 | [CSV への変換](convert-to-csv.md) |
| データの入力と出力 | クラウド ソースのデータを実験に移動します。 実験の実行中に、結果や中間データを Azure Storage、SQL データベース、または Hive に書き込みます。また、クラウド ストレージを使用して、複数の実験間でデータを交換します。  | [データのインポート](import-data.md)<br/>[データのエクスポート](export-data.md)<br/>[データの手動入力](enter-data-manually.md) |
| データの変換 | 機械学習に固有のデータに対する操作 (データの正規化やビン分割、特徴選択、次元削減など)。| [データセット内の列の選択](select-columns-in-dataset.md) <br/> [メタデータの編集](edit-metadata.md) <br/> [見つからないデータのクリーンアップ](clean-missing-data.md) <br/> [列の追加](add-columns.md) <br/> [行の追加](add-rows.md) <br/> [重複する行の削除](remove-duplicate-rows.md) <br/> [データの結合](join-data.md) <br/> [データの分割](split-data.md) <br/> [データの正規化](normalize-data.md) <br/> [パーティションとサンプル](partition-and-sample.md) |
| Python および R モジュール | コードを記述し、それをモジュール内に埋め込み、Python および R と実験を統合します。 | [Python スクリプトの実行](execute-python-script.md)   <br/> [Python モデルの作成](create-python-model.md) <br/> [R スクリプトの実行](execute-r-script.md)
|  | **機械学習のアルゴリズム**: | |
| 分類 | クラスを予測します。  バイナリ (2 クラス) または多クラスのアルゴリズムを選択します。| [多クラス デシジョン フォレスト](multiclass-decision-forest.md) <br/> [多クラス ロジスティック回帰](multiclass-logistic-regression.md)  <br/> [多クラス ニューラル ネットワーク](multiclass-neural-network.md)  <br/>  [2 クラス ロジスティック回帰](two-class-logistic-regression.md)  <br/>[2 クラス平均化パーセプトロン](two-class-averaged-perceptron.md) <br/> [2 クラス &nbsp;ブースト &nbsp;デシジョン &nbsp;ツリー](two-class-boosted-decision-tree.md)  <br/> [2 クラス デシジョン フォレスト](two-class-decision-forest.md)  <br/> [2 クラス ニューラル ネットワーク](two-class-neural-network.md)  <br/> [2 クラス &nbsp;サポート &nbsp;ベクター &nbsp;マシン](two-class-support-vector-machine.md) 
| クラスタリング | データをグループ化します。| [K-Means クラスタリング](k-means-clustering.md)
| 回帰 | 値を予測します。 | [線形回帰](linear-regression.md)  <br/> [ニューラル ネットワーク回帰](neural-network-regression.md)  <br/> [デシジョン フォレスト回帰](decision-forest-regression.md)  <br/> [ブースト &nbsp;デシジョン &nbsp;ツリー&nbsp;回帰](boosted-decision-tree-regression.md)
|  | **モデルのビルドおよび評価**: | |
| トレーニング   | アルゴリズムを介してデータを実行します。 | [モデルのトレーニング](train-model.md)  <br/> [クラスタリング モデルのトレーニング](train-clustering-model.md)    |
| モデルの評価 | トレーニング済みモデルの正確性を測定します。 |  [モデルの評価](evaluate-model.md)
| Score | トレーニングしたモデルから予測を取得します。 | [変換の適用](apply-transformation.md)<br/>[クラスター&nbsp;への&nbsp;データの&nbsp;割り当て](assign-data-to-clusters.md) <br/>[モデルのスコア付け](score-model.md)

## <a name="error-messages"></a>エラー メッセージ

Azure Machine Learning service のビジュアル インターフェイスでモジュールを使用しているときに発生する可能性のある[エラー メッセージと例外コード](machine-learning-module-error-codes.md)について説明します。
