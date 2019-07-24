---
title: 概要
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio は、そのまま使うことのできるアルゴリズムやモジュールを含んだライブラリから、ドラッグ アンド ドロップでモデルをすばやく構築できるツールです。
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: 7a2bd8ebf2e96cf2251d98501bbf42007d43a660
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683003"
---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning Studio とは
Microsoft Azure Machine Learning Studio は、データを活用した予測分析ソリューションの構築、テスト、デプロイをドラッグ アンド ドロップで行うことができる、コラボレーションに対応したツールです。 Machine Learning Studio でモデルを Web サービスとして公開すれば、カスタム アプリや BI ツール (Excel など) からそのモデルを簡単に利用することができます。

Machine Learning Studio があれば、最新のデータ サイエンスとクラウド リソースを活用して、独自に所有するデータを使った予測分析を実現することができます。


## <a name="the-machine-learning-studio-interactive-workspace"></a>Machine Learning Studio の対話型ワークスペース
一般的な予測分析モデルの作成では、1 種類以上のソースからデータを入手し、さまざまなデータ操作と統計的特徴を適用してデータを変換することにより、一連の結果を生成します。 このようなモデルの作成プロセスは対話型プロセスになります。 十分にトレーニングされた有効なモデルが作成されるまで、さまざまな特徴とパラメーターを繰り返し調整します。

**Azure Machine Learning Studio** では、予測分析モデルの作成、テスト、反復作業を支援する、視覚的操作に対応した対話型ワークスペースが提供されます。 "***データセット***" と分析 "***モジュール***" を対話型のキャンバスにドラッグ アンド ドロップし、それらを相互に接続して "***実験***" を完成させ、Machine Learning Studio で実行できます。 モデルのさまざまな設計を試す際は、実験を編集して必要に応じて保存し、再度実行できます。 準備が整ったら、"***トレーニング実験***" を "***予測実験***" に変換します。さらに、"***Web サービス***" として発行し、第三者が利用できるようにそのモデルを公開します。

データセットとモジュールを視覚的に接続すれば予測分析モデルが完成するため、プログラミングは必要ありません。

![Azure Machine Learning Studio の図:実験の作成、各種ソースのデータの読み取り、スコア付けされたデータの書き込み、モデルの書き込み](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Machine Learning Studio 概要図のダウンロード
**Microsoft Azure Machine Learning Studio 機能の概要** 図をダウンロードして、Machine Learning Studio の機能の概要を確認します。 図をタブロイド サイズ (11 x 17 インチ) で印刷し、手元に置いておくことができます。

**ここから図をダウンロード:[Microsoft Azure Machine Learning Studio の機能の概要](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Microsoft Azure Machine Learning Studio の機能の概要](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Machine Learning Studio の概要
[Machine Learning Studio](https://studio.azureml.net) を初めて起動したときには、 **ホーム** ページが表示されます。 そこからドキュメントやビデオ、ウェビナーなど、有益なリソースにアクセスすることができます。

左上のメニュー  ![メニュー](./media/what-is-ml-studio/menu.png) をクリックすると、いくつかのオプションが表示されます。
### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
ここには、**[ホーム]** (最初のページ) と **[Studio]** の 2 つのオプションがあります。

**[Studio]** をクリックすると、**Azure Machine Learning Studio** に移動します。 最初に、Microsoft アカウントを使用するか職場または学校アカウントを使用してサインインするように求められます。 サインインすると、次のタブが左側に表示されます。

* **[PROJECTS (プロジェクト)]** - 単一のプロジェクトを表す実験、データセット、ノートブック、およびその他のリソースがまとめられています。
* **[EXPERIMENTS (実験)]** - 作成済み、実行済み、ドラフトとして保存済みの実験です。
* **[Web サービス]** - 実験からデプロイした Web サービスです。
* **[ノートブック]** - 作成済みの Jupyter ノートブックです。
* **[データセット]** - Studio にアップロード済みのデータセット。
* **[トレーニング済みのモデル]** - 実験でトレーニングし、Studio で保存したモデルです。
* **設定** - アカウントとリソースを構成するための各種設定がまとめられています。

### <a name="gallery"></a>[ギャラリー]
**[ギャラリー]** をクリックすると、**[Azure AI ギャラリー](https://gallery.azure.ai/)** に移動します。 ギャラリーは、データ サイエンティストや開発者のコミュニティが、Cortana Intelligence Suite のコンポーネントを使用して作成したソリューションを共有する場です。

ギャラリーの詳細については、[Azure AI ギャラリーでのソリューションの共有と発見](gallery-how-to-use-contribute-publish.md)に関するページを参照してください。

## <a name="components-of-an-experiment"></a>実験の構成要素
実験にはデータセットが含まれます。データセットからデータが分析モジュールに提供され、分析モジュールを接続することで予測分析モデルが完成します。 有効な実験に求められる具体的な条件を以下に示します。

* 実験には少なくとも 1 つのデータセットと 1 つのモジュールがある。
* データセットはモジュールにのみ接続できる。
* モジュールはデータセットにも別のモジュールにも接続できる。
* モジュールのすべての入力ポートが、何らかの形でデータ フローに接続されている。
* モジュールの必須パラメーターがすべて設定されている。

実験を最初から作成するか、既にあるサンプル実験をテンプレートとして使用してください。 詳細については、「[サンプル実験をコピーして新しい機械学習実験を作成する](sample-experiments.md)」を参照してください。

簡単な実験を作成する例については、「 [Azure Machine Learning Studio での簡単な実験の作成](create-experiment.md)」をご覧ください。

予測分析ソリューションのより詳しいチュートリアルについては、「[Azure Machine Learning Studio を使用した予測ソリューションの開発](tutorial-part1-credit-risk.md)」をご覧ください。

### <a name="datasets"></a>データセット
データセットを Machine Learning Studio にアップロードすることで、これらのデータセットをモデル作成プロセスで使用できるようになります。 Machine Learning Studio には数多くのサンプル データセットが既に含まれているため、これらを実験で試すことができます。また、必要に応じてさらにデータセットをアップロードできます。 提供されるデータセットには以下のようなものがあります。

* **さまざまな自動車の燃費データ** - シリンダー数や馬力などによって分類された、自動車の燃費値 (MPG) です。
* **乳がんデータ** - 乳がんの診断データです。
* **森林火災データ** - ポルトガル北東地域を対象とする森林火災の規模データです。

利用可能なデータセットは、実験を作成するときにキャンバスの左側の一覧から選択できます。

Machine Learning Studio に含まれている一連のサンプル データセットについては、 [Azure Machine Learning Studio でのサンプル データセットの使用](use-sample-datasets.md)に関するページを参照してください。

### <a name="modules"></a>モジュール
モジュールとは、データに対して実行できるアルゴリズムのことです。 Machine Learning Studio には、データの受信機能や、データのトレーニング、スコア付け、検証などに対応したさまざまなモジュールが用意されています。 提供されるモジュールには以下のようなものがあります。

* [ARFF への変換][convert-to-arff] - .NET でシリアル化されたデータセットを属性関係ファイル フォーマット (ARFF) に変換します。
* [基本統計値の計算][elementary-statistics] - 平均や標準偏差などの基本的な統計値を計算します。
* [線形回帰][linear-regression] - オンライン傾斜降下に基づく線形回帰モデルを作成します。
* [モデルのスコア付け][score-model] - トレーニングされた分類か回帰モデルをスコア付けします。

利用可能なモジュールは、実験を作成するときにキャンバスの左側の一覧から選択できます。

モジュールに一連のパラメーターが含まれている場合、これらを使用してモジュールの内部アルゴリズムを構成することができます。 モジュールのパラメーターは、キャンバスでモジュールを選択するとキャンバス右側の **[プロパティ]** ウィンドウに表示されます。 このウィンドウでパラメーターを変更することにより、モデルを微調整できます。

大規模な機械学習アルゴリズムのライブラリを利用する際のヒントについては、[Microsoft Azure Machine Learning Studio のアルゴリズムの選択方法](algorithm-choice.md)に関する記事を参照してください。

## <a name="deploying-a-predictive-analytics-web-service"></a>予測分析 Web サービスのデプロイ
予測分析モデルの準備が整ったら、それを Machine Learning Studio から Web サービスとしてデプロイすることができます。 このプロセスの詳細については、「 [Azure Machine Learning Web サービスをデプロイする](publish-a-machine-learning-web-service.md)」を参照してください。

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Machine Learning Studio と Azure Machine Learning service の違い

[Azure Machine Learning service](../service/overview-what-is-azure-ml.md) には、機械学習モデルのデータ準備、トレーニング、デプロイを迅速に行うための SDK とサービスが用意されています。 自動スケーリング コンピューティングとパイプラインを使用して、生産性とコストを改善します。 PyTorch、TensorFlow、scikit-learn など、オープンソースの Python フレームワークと共にこれらの機能を使用します。 

Machine Learning Studio を使用するのは、機械学習モデルをすばやく簡単に試したいときで、組み込みの機械学習アルゴリズムで十分な場合です。

Machine Learning サービスを使用するのは、Python 環境で作業していて機械学習アルゴリズムを詳細に制御したい場合、またはオープンソースの機械学習ライブラリを使用したい場合です。

> [!NOTE]
> Azure Machine Learning Studio で作成されたモデルは、Azure Machine Learning service でデプロイまたは管理することはできません。

## <a name="free-trial"></a>無料試用版

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>次の手順
[ステップ バイ ステップ クイック スタート](create-experiment.md)を使用し、[サンプル上に構築](sample-experiments.md)することによって、予測分析と機械学習の基礎について学習できます。

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
