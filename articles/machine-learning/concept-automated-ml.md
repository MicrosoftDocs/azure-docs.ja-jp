---
title: 自動 ML とは AutoML
titleSuffix: Azure Machine Learning
description: 指定したパラメーターと条件を使用して、Azure Machine Learning がモデルを自動的に生成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: e7038a4e06a96ac2df1a7f0918877db9c97cb71d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502537"
---
# <a name="what-is-automated-machine-learning-automl"></a>自動機械学習 (AutoML) とは

自動機械学習 (自動 ML または AutoML とも呼ばれます) は、時間のかかる反復的な機械学習モデルの開発タスクを自動化するプロセスです。 これにより、データ サイエンティスト、アナリスト、開発は、モデルの品質を維持しながら、高いスケール、効率性、生産性で ML モデルを構築することができます。 Azure Machine Learning の 自動 ML は、[Microsoft Research 部門](https://www.microsoft.com/research/project/automl/)の最先端技術に基づいています。

機械学習モデルの従来の開発はリソース集約型であり、ドメインに関する広範な知識と多数のモデルを生成して比較するための大量の時間を必要とします。 自動機械学習を使用することで、すぐに実稼働環境で使用できる ML モデルを取得するための時間を、容易にかつ効率的に短縮することができます。

## <a name="when-to-use-automl-classify-regression--forecast"></a>AutoML をいつ使用するか: 分類、回帰、予測

指定したターゲット メトリックを使用して自分の代わりに Azure Machine Learning にモデルのトレーニングと調整を行わせる場合は、自動 ML を適用します。 自動 ML を使うと、誰でも機械学習モデルの開発プロセスを使用でき、ユーザーはデータ サイエンスの専門知識に関係なく、どの問題についてもエンド ツー エンドの機械学習パイプラインを識別することができます。

さまざまな業界のデータ サイエンティスト、アナリスト、開発者が、自動化された ML を使用して次のことを実行できます。
+ 広範なプログラミング知識なしで ML ソリューションを実装する
+ 時間とリソースを節約する
+ データ サイエンスのベスト プラクティスを活用する
+ アジャイルな問題解決を提供する

### <a name="classification"></a>分類

分類は、一般的な機械学習タスクの 1 つです。 分類は、モデルでトレーニング データを使用して学習を行い、得られた知識を新しいデータに適用する、教師あり学習の一種です。 Azure Machine Learning には、これらのタスクに特化した特徴付けが用意されています (分類用のディープ ニューラル ネットワーク テキスト特徴抽出器など)。 「[特徴付けオプション](how-to-configure-auto-features.md#featurization)」を参照してください。 

分類モデルの主な目的は、トレーニング データから学習した知識に基づいて、新しいデータがどのカテゴリに分類されるかを予測することです。 一般的な分類の例には、不正行為の検出、手書き認識、オブジェクトの検出などがあります。 詳細と例については、[Azure Machine Learning の自動 ML での分類モデルの作成](tutorial-first-experiment-automated-ml.md)に関するページで参照できます。

分類と自動機械学習の例については、次の Python ノートブックを参照してください: [不正行為の検出](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)、[マーケティング予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)、[ニュースグループ データの分類](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>回帰

分類と同様に、回帰タスクも一般的な教師あり学習の 1 つです。 Azure Machine Learning には、[これらのタスクに特化した特徴付け](how-to-configure-auto-features.md#featurization)が用意されています。

予測される出力値がカテゴリである分類とは異なり、回帰モデルでは、独立した予測子に基づいて数値の出力値が予測されます。 回帰の目的は、1 つの変数が他の変数にどのように影響するかを推定することによって、独立した予測変数間の関係を確立することです。 たとえば、自動車の価格は、燃費効率や安全性の評価などの特徴に基づいています。 [自動機械学習を使用した回帰](tutorial-auto-train-models.md)に関する記事で、詳細と例を確認してください。

予測のための回帰と自動機械学習の例については、次の Python ノートブックを参照してください: [CPU パフォーマンスの予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb)。 

### <a name="time-series-forecasting"></a>時系列予測

予測を行うことは、収益、在庫、販売、顧客需要にかかわらず、あらゆるビジネスに不可欠です。 自動化された ML を使用してテクニックとアプローチを組み合わせて、推奨される高品質な時系列予測を得ることができます。 詳細については、[時系列予測のための自動機械学習](how-to-auto-train-forecast.md)に関する記事を参照してください。 

自動化された時系列の実験は、多変量回帰問題として扱われます。 過去の時系列値は "ピボット" されて、他の予測因子とともにリグレッサーの追加ディメンションとなります。 このアプローチには、従来の時系列手法と異なり、トレーニング中に複数のコンテキスト変数とその関係を自然に取り込めるという利点があります。 自動 ML では、データセットと予測期間内のすべての項目について、単一ではあるがしばしば内部的に分岐するモデルが学習されます。 したがって、モデルのパラメーターを見積もるために多くのデータを使用でき、目に見えない系列の一般化が可能になります。

高度な予測構成には次のものが含まれます。
* 休日の検出と特性付け
* 時系列と DNN 学習 (自動 ARIMA、Prophet、ForecastTCN)
* グループ化による多くのモデルのサポート
* ローリング オリジン クロス検証
* 構成可能なラグ
* ローリング ウィンドウの集計機能


予測のための回帰と自動機械学習の例については、次の Python ノートブックを参照してください: [売上予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)、[需要予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)、[飲料生産の予測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)。

## <a name="how-automated-ml-works"></a>自動 ML の動作

トレーニング中、Azure Machine Learning は、さまざまなアルゴリズムとパラメーターを試行する多数のパイプラインを並列に作成します。 サービスは、機能選択と組み合わせた ML アルゴリズムを介して反復し、それぞれの反復で、トレーニング スコアを含むモデルを生成します。 このスコアが高いほど、モデルがデータに「適合している」と見なされます。  実験に定義されている終了基準に到達すると停止します。 

**Azure Machine Learning** を利用するとき、次の手順で自動 ML トレーニング実験を設計し、実験できます。

1. 解決すべき **ML 問題を特定します**。分類、予測、または回帰になります。

1. **Python SDK と Studio Web エクスペリエンスのどちらを使用するかを選択します**。[Python SDK と Studio Web エクスペリエンス](#parity)の同等性を確認してください。

   * コードの経験があまりない、またはない場合は、Azure Machine Learning Studio Web エクスペリエンスを [https://ml.azure.com](https://ml.azure.com/) で試してください  
   * Python 開発者は、[Azure Machine Learning Python SDK](how-to-configure-auto-train.md) に関するページを参照してください 
    
1. **ラベルの付いたトレーニング データのソースとフォーマットを指定します**。Numpy 配列または Pandas データフレーム

1. [ローカル コンピューター、Azure Machine Learning コンピューティング、リモート VM、Azure Databricks](how-to-set-up-training-targets.md) など、**モデル トレーニングのためのコンピューティング先を構成します**。  リモート リソースでの自動トレーニングに関する詳細は[こちら](how-to-auto-train-remote.md)にあります。

1. さまざまなモデルでの繰り返しの回数、ハイパーパラメーター設定、前処理/特徴付けの詳細、最良のモデルを決定するときに考慮されるメトリックを決定する **自動化された機械学習のパラメーターを構成します**。  
1. **トレーニング実行を送信します。**

1. **結果を確認します** 

このプロセスを説明する図を次に示します。 
![自動機械学習](./media/concept-automated-ml/automl-concept-diagram2.png)


ログに記録された実行情報を調べることもできます。これには、実行中に収集した[メトリックが含まれています](how-to-understand-automated-ml.md)。 トレーニングを実行すると、モデルおよびデータ前処理を含む Python シリアル化オブジェクト (`.pkl` ファイル) が生成されます。

モデルの構築は自動ですが、生成されたモデルにとって[特徴がどれだけ重要であるか、または関連性があるか](how-to-configure-auto-train.md#explain)を学ぶこともできます。

[リモート コンピューティング先](how-to-auto-train-remote.md)の使用方法を学習します。



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>特徴エンジニアリング

特徴エンジニアリングは、データに関するドメインの知識を活用して、ML アルゴリズムの学習を支援する機能を作成するプロセスです。 Azure Machine Learning では、特徴エンジニアリングを容易にするために、スケーリングと正規化の手法が適用されます。 これらの手法と特徴エンジニアリングは、まとめて特徴量化と呼ばれています。

自動機械学習の実験において、特徴量化は自動的に適用されますが、データに基づいてカスタマイズすることもできます。 含まれる特徴付けに関する詳細は[こちら](how-to-configure-auto-features.md#featurization)から参照してください。  

> [!NOTE]
> 自動化された機械学習の特徴付け手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 このモデルを予測に使用する場合、トレーニング中に適用されたのと同じ特徴付けの手順がご自分の入力データに自動的に適用されます。

### <a name="automatic-featurization-standard"></a>自動特徴量化 (標準)

自動化されたすべての機械学習実験において、アルゴリズムが十分に実行されるよう、自動的にデータの規模が調整され、正規化されます。 モデル トレーニングの間、次のいずれかのスケーリング手法または正規化手法が各モデルに適用されます。 AutoML がモデル内の[オーバーフィットや不均衡データを防止する](concept-manage-ml-pitfalls.md)方法について説明します。

|スケーリング &nbsp;&&nbsp; 処理| 説明 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 中間を取り除き、単位差異に合わせてスケールを変更することで特徴を正規化します  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | その列の最小と最大で各特徴のスケールを変更することで特徴を変換します  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |その最大絶対値で各特徴のスケールを変更します |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |分位範囲で特徴のスケールを変更します |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |データの特異値分解を利用した線形次元削減により、低いほうの次元空間にデータを投影させます |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |このトランスフォーマーは端数を切り捨てた特異値分解 (SVD) によって線形次元削減を実行します。 PCA とは異なり、この推定では、特異値分解を計算する前にデータを中心に置くことはありません。つまり、scipy.sparse マトリックスを効率的に使用できます。 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | その標準 (l1 または l2) が 1 に等しくなるよう、ゼロ以外の要素を少なくとも 1 つ有する各サンプルのスケールが他のサンプルに依存せずに変更されます。 |

### <a name="customize-featurization"></a>特徴量化のカスタマイズ

その他の特徴エンジニアリング手法 (エンコードや変換など) も使用できます。 

この設定は次の方法で有効にできます。

+ Azure Machine Learning Studio:[これらの手順に従って](how-to-use-automated-ml-for-ml-models.md#customize-featurization)、 **[View additional configuration]\(追加構成の表示\)** セクションで **[Automatic featurization]\(自動特性付け\)** を有効にします。

+ Python SDK:[AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) オブジェクトで `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` を指定します。 [特性付けを有効にする](how-to-configure-auto-features.md)方法に関する詳細を参照してください。 

## <a name="ensemble-models"></a><a name="ensemble"></a> アンサンブル モデル

自動機械学習では、既定で有効になっているアンサンブル モデルがサポートされています。 アンサンブル学習では、1 つのモデルを使用するのではなく、複数のモデルを組み合わせることによって、機械学習の結果と予測パフォーマンスが改善されます。 アンサンブル イテレーションは、実行の最終イテレーションとして表示されます。 自動機械学習では、モデルの結合に投票とスタッキングの両方のアンサンブル方法を使用します。

* **投票**: 予測されたクラス確率 (分類タスクの場合) または予測された回帰ターゲット (回帰タスクの場合) の加重平均に基づいて予測します。
* **スタッキング**: スタッキングは異種のモデルを結合し、個々のモデルの出力に基づいてメタモデルをトレーニングします。 現在の既定のメタモデルは、分類タスクの場合は LogisticRegression、回帰/予測タスクの場合は ElasticNet です。

初期アンサンブルが並べ替えられた [Caruana のアンサンブル選択アルゴリズム](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)を使用して、アンサンブル内で使用するモデルを決定します。 大まかに言えば、このアルゴリズムでは、最適な個別スコアを持つ最大 5 つのモデルを使用してアンサンブルを初期化し、これらのモデルが最適なスコアの 5% のしきい値内にあることを確認して、不適切な初期アンサンブルを回避します。 その後、各アンサンブルの繰り返しに対して、新しいモデルが既存のエンティティに追加され、結果のスコアが計算されます。 新しいモデルによって既存のアンサンブル スコアが向上した場合、アンサンブルはその新しいモデルを含むように変更されます。

自動化された機械学習の既定のアンサンブル設定を変更する方法については、[ハウツー](how-to-configure-auto-train.md#ensemble)を参照してください。

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>ローカルおよびリモートで管理されている ML のコンピューティング先に関するガイダンス

自動 ML 用の Web インターフェイスでは、常にリモートの[コンピューティング先](concept-compute-target.md)が使用されます。  ただし、Python SDK を使用する場合は、自動 ML のトレーニング用にローカルのコンピューティング先またはリモートのコンピューティング先のどちらかを選択します。

* **ローカル コンピューティング**:ローカルのノート PC または VM のコンピューティング上でトレーニングが行われます。 
* **リモート コンピューティング**:Machine Learning コンピューティング クラスター上でトレーニングが行われます。  

### <a name="choose-compute-target"></a>コンピューティング先の選択
使用するコンピューティング先を選択するときに、次の要素を考慮してください。

 * **ローカル コンピューティングを選択する**:小規模のデータと短いトレーニング (つまり、子実行あたり数秒または数分) を使用する、初期探索またはデモのためのシナリオの場合は、ローカル コンピューター上でのトレーニングが適している場合があります。  セットアップ時間はかからず、インフラストラクチャ リソース (お使いの PC または VM) を直接使用できます。
 * **リモート ML コンピューティング クラスターを選択する**:より長いトレーニングが必要なモデルを作成する運用環境でのトレーニングのように、より大きなデータセットを使用してトレーニングを行う場合、リモート コンピューティングを選択するとエンド ツー エンドの時間のパフォーマンスが大幅に向上します。`AutoML` によってクラスターのノード間でトレーニングが並列化されるためです。 リモート コンピューティングでは、内部インフラストラクチャの起動時間によって子実行あたり約 1.5 分が加算され、VM がまだ稼働していない場合はクラスター インフラストラクチャ用にさらに数分がかかります。

### <a name="pros-and-cons"></a>長所と短所
ローカルとリモートのどちらを使用するかを選択する場合は、以下の長所と短所を考慮してください。

|  | 長所 (利点)  |短所 (欠点)  |
|---------|---------|---------|---------|
|**ローカル コンピューティング先** |  <li> 環境の起動時間がかからない   | <li>  機能のサブセット<li>  実行を並列化できない <li> 大規模なデータには適さない。 <li>トレーニング中のデータ ストリーミングがない <li>  DNN ベースの特徴量化がない <li> Python SDK のみ |
|**リモート ML コンピューティング クラスター**|  <li> 機能の完全なセット <li> 子実行の並列化 <li>   大規模なデータのサポート<li>  DNN ベースの特徴量化 <li>  必要に応じたコンピューティング クラスターの動的スケーラビリティ <li> コードなしのエクスペリエンス (Web UI) も使用可能  |  <li> クラスター ノードの起動時間 <li> 子実行ごとの起動時間    |

### <a name="feature-availability"></a>使用可能な機能 

 次の表に示すように、リモート コンピューティングを使用するとより多くの機能を使用できます。 

| 機能                                                    | Remote | ローカル | 
|------------------------------------------------------------|--------|-------|
| データ ストリーミング (大規模なデータのサポート、最大 100 GB)          | ✓      |       | 
| DNN-BERT ベースのテキストの特徴量化とトレーニング             | ✓      |       |
| すぐに使用できる GPU サポート (トレーニングと推論)        | ✓      |       |
| 画像の分類とラベル付けのサポート                  | ✓      |       |
| 予測のための自動 ARIMA、Prophet、および ForecastTCN モデル | ✓      |       | 
| 並列での複数の実行/反復                       | ✓      |       |
| AutoML スタジオ Web エクスペリエンス UI で解釈可能なモデルを作成する      | ✓      |       |
| スタジオ Web エクスペリエンス UI での特徴エンジニアリングのカスタマイズ| ✓      |       |
| Azure ML ハイパーパラメーターの調整                             | ✓      |       |
| Azure ML パイプライン ワークフローのサポート                         | ✓      |       |
| 実行の継続                                             | ✓      |       |
| 予測                                                | ✓      | ✓     |
| ノートブックで実験を作成して実行する                    | ✓      | ✓     |
| UI で実験の情報とメトリックを登録して視覚化する | ✓      | ✓     |
| データ ガードレール                                            | ✓      | ✓     |

## <a name="many-models"></a>多数モデル 

[多数モデル ソリューション アクセラレータ](https://aka.ms/many-models) (プレビュー) は Azure Machine Learning 上に構築されており、自動 ML を使用して、数百または数千もの機械学習モデルをトレーニング、操作、管理できます。

たとえば次のシナリオで、__それぞれの事例または個々の対象の__ モデルを構築すると、結果が改善される可能性があります。

* 個々の店舗の売上予測
* 数百基の油井の予測メンテナンス
* 個々のユーザーのエクスペリエンスの調整。

## <a name="automl-in-azure-machine-learning"></a>Azure Machine Learning での自動 ML

Azure Machine Learning には、自動 ML を使用するための 2 つのエクスペリエンスが用意されています。

* コードの経験がある場合は、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) に関する記事を参照してください 

* コードの経験があまりない、またはない場合は、Azure Machine Learning Studio ([https://ml.azure.com](https://ml.azure.com/)) に関する記事を参照してください。  

<a name="parity"></a>

### <a name="experiment-settings"></a>実験の設定 

次の設定を使用して、自動 ML の実験を構成できます。 

| |Python SDK|Studio Web エクスペリエンス|
----|:----:|:----:
|**データをトレーニングおよび検証セットに分割する**| ✓|✓
|**ML タスクのサポート: 分類、回帰、予測**| ✓| ✓
|**プライマリ メトリックに基づいて最適化する**| ✓| ✓
|**Azure ML コンピューティングをコンピューティング ターゲットとしてサポートする** | ✓|✓
|**予測期間、ターゲットのラグ、ローリング期間を構成する**|✓|✓
|**終了条件を設定する** |✓|✓ 
|**コンカレント イテレーションを設定する**| ✓|✓
|**列の削除**| ✓|✓
|**ブロック アルゴリズム**|✓|✓
|**クロス検証** |✓|✓
|**Azure Databricks クラスターでのトレーニングをサポートする**| ✓|
|**エンジニアリング機能の名前を表示する**|✓|
|**特徴付けの概要**| ✓|
|**休日の特徴付け**|✓|
|**ログ ファイルの詳細度**| ✓|

### <a name="model-settings"></a>モデルの設定

これらの設定は、自動 ML 実験の結果として最適なモデルに適用できます。

| |Python SDK|Studio Web エクスペリエンス|
|----|:----:|:----:|
|**最適なモデルの登録、デプロイ、説明可能性**| ✓|✓|
|**投票アンサンブルとスタック アンサンブル モデルを有効にする**| ✓|✓|
|**プライマリ メトリック以外に基づいて最適なモデルを表示する**|✓||
|**ONNX モデルの互換性を有効または無効にする**|✓||
|**モデルのテスト** | ✓| |

### <a name="run-control-settings"></a>コントロール設定を実行する

これらの設定を使用すると、実験の実行とその子の実行を確認し、制御することができます。 

| |Python SDK|Studio Web エクスペリエンス|
|----|:----:|:----:|
|**実行の概要テーブル**| ✓|✓|
|**実行および子の実行を取り消す**| ✓|✓|
|**ガードレールを取得する**| ✓|✓|
|**実行を一時停止および再開する**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Azure Machine Learning では、自動化された ML を使用して Python モデルを構築し、それを ONNX 形式に変換できます。 ONNX 形式になったモデルは、さまざまなプラットフォームやデバイスで実行することができます。 [ONNX での ML モデルの能率化](concept-onnx.md)に関する詳細をご覧ください。

ONNX 形式に変換する方法については、[この Jupyter ノートブックの例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)を参照してください。 [ONNX でサポートされているアルゴリズム](how-to-configure-auto-train.md#select-your-experiment-type)についてご確認ください。

ONNX ランタイムは C# にも対応しています。そのため、コードを書き直す必要がなく、また、REST エンドポイントで発生するネットワークの遅延なく、C# アプリで自動的に構築されたモデルを使用できます。 [ML.NET を使用する .NET アプリケーションでの AutoML ONNX モデルの使用](./how-to-use-automl-onnx-model-dotnet.md)と [ONNX ランタイム C# API を使用した ONNX モデルの推論](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md)に関するページを参照してください。 

## <a name="next-steps"></a>次のステップ

AutoML の使用を開始する方法がわかるリソースが複数あります。 

### <a name="tutorials-how-tos"></a>チュートリアルと方法
チュートリアルでは、AutoML シナリオのサンプルを端から端まで紹介します。
+ **コード ファーストの場合**、[Azure Machine Learning Python SDK で回帰モデルを自動的にトレーニングする](tutorial-auto-train-models.md)方法のチュートリアルを参照してください。

 + **コードの作成経験が少ないか、まったくない場合**、[Azure Machine Learning スタジオで自動 ML 分類モデルを作成する](tutorial-first-experiment-automated-ml.md)方法のチュートリアルを参照してください。

ハウツー記事には、AutoML が備えている機能に関する詳細があります。 たとえば、次のように入力します。 

+ 自動トレーニング実験の設定を構成してください
    + Azure Machine Learning Studio で、[こちらの手順](how-to-use-automated-ml-for-ml-models.md)を使用します。 
    + Python SDK で、[こちらの手順](how-to-configure-auto-train.md)を使用します。

+  [こちらの手順](how-to-auto-train-forecast.md)で、時系列データを使用して自動トレーニングする方法について学習してください。

### <a name="jupyter-notebook-samples"></a>Jupyter Notebook のサンプル 

[GitHub の自動機械学習サンプルのノートブック リポジトリ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)で詳しいコード サンプルやユース ケースを確認してください。

### <a name="python-sdk-reference"></a>Python SDK リファレンス

[AutoML クラス リファレンス ドキュメント](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)では、SDK デザイン パターンとクラス仕様の知識を深めることができます。 

> [!Note]
> 自動機械学習機能は、[ML.NET](/dotnet/machine-learning/automl-overview)、[HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)、[Power BI](/power-bi/service-machine-learning-automated)、[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/) などの他の Microsoft ソリューションでも使用できます
