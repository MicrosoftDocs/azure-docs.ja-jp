---
title: "Azure Machine Learning Studio とは | Microsoft Docs"
description: "そのまま使うことのできるアルゴリズムやモジュールを含んだライブラリから、ドラッグ アンド ドロップでモデルをすばやく構築できるツール Azure ML Studio の概要です。"
keywords: "azure machine learning、azure ml、ml studio"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 9e738c4e5f43ae6c939f7c6da90c258498943e73
ms.openlocfilehash: b8115f1fb72b0ba89fd0c8afa3358878a0fab92b
ms.lasthandoff: 12/14/2016


---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning Studio とは
Microsoft Azure Machine Learning Studio は、データを活用した予測分析ソリューションの構築、テスト、デプロイをドラッグ アンド ドロップで行うことができる、コラボレーションに対応したツールです。 Machine Learning Studio でモデルを Web サービスとして公開すれば、カスタム アプリや BI ツール (Excel など) からそのモデルを簡単に利用することができます。

Machine Learning Studio があれば、最新のデータ サイエンスとクラウド リソースを活用して、独自に所有するデータを使った予測分析を実現することができます。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>Machine Learning Studio の対話型ワークスペース
一般的な予測分析モデルの作成では、1 種類以上のソースからデータを入手し、さまざまなデータ操作と統計的特徴を適用してデータを変換することにより、一連の結果を生成します。 このようなモデルの作成プロセスは対話型プロセスになります。 十分にトレーニングされた有効なモデルが作成されるまで、さまざまな特徴とパラメーターを繰り返し調整します。

**Azure Machine Learning Studio** では、予測分析モデルの作成、テスト、反復作業を支援する、視覚的操作に対応した対話型ワークスペースが提供されます。 "***データセット***" と分析 "***モジュール***" を対話型のキャンバスにドラッグ アンド ドロップし、それらを相互に接続して "***実験***" を完成させ、Machine Learning Studio で実行できます。 モデルのさまざまな設計を試す際は、実験を編集して必要に応じて保存し、再度実行できます。 準備が整ったら、"***トレーニング実験***" を "***予測実験***" に変換します。さらに、"***Web サービス***" として発行し、第三者が利用できるようにそのモデルを公開します。

データセットとモジュールを視覚的に接続すれば予測分析モデルが完成するため、プログラミングは必要ありません。

> [!TIP]
> Machine Learning Studio の機能の概要を示す図をダウンロードして印刷する場合は、「 [Azure Machine Learning Studio 機能の概要図](machine-learning-studio-overview-diagram.md)」ご覧ください。
> 
> 

![Azure ML Studio の図: 実験の作成、各種ソースのデータの読み取り、スコア付けされたデータの書き込み、モデルの書き込み][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Machine Learning Studio の概要
[Machine Learning Studio](https://studio.azureml.net) を初めて起動したときには、 **ホーム** ページが表示されます。 そこからドキュメントやビデオ、ウェビナーなど、有益なリソースにアクセスすることができます。

左上のメニュー  ![メニュー](media/machine-learning-what-is-ml-studio/menu.png) をクリックすると、いくつかのオプションが表示されます。

### <a name="cortana-intelligence"></a>Cortana Intelligence
**[Cortana Intelligence]** をクリックすると、[Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite) のホーム ページが表示されます。 Cortana Intelligence Suite は、データをインテリジェントなアクションに変換するための、完全管理型ビッグ データおよび高度な分析スイートです。 顧客事例を含む完全なドキュメントについては、Cortana Intelligence Suite のホーム ページを参照してください。

### <a name="azure-machine-learning"></a>Azure Machine Learning
ここには、**[ホーム]** (最初のページ) と **[Studio]** の&2; つのオプションがあります。

**[Studio]** をクリックすると、**Azure Machine Learning Studio** に移動します。 最初に、Microsoft アカウントを使用するか職場または学校アカウントを使用してサインインするように求められます。 サインインすると、次のタブが左側に表示されます。

* **[PROJECTS (プロジェクト)]** - 単一のプロジェクトを表す実験、データセット、ノートブック、およびその他のリソースがまとめられています。
* **[EXPERIMENTS (実験)]** - 作成済み、実行済み、ドラフトとして保存済みの実験です。
* **[Web サービス]** - 実験からデプロイした Web サービスです。
* **[ノートブック]** - 作成済みの Jupyter ノートブックです。
* **[データセット]** - Studio にアップロード済みのデータセット。
* **[トレーニング済みのモデル]** - 実験でトレーニングし、Studio で保存したモデルです。
* **設定** - アカウントとリソースを構成するための各種設定がまとめられています。

### <a name="gallery"></a>[ギャラリー]
**[ギャラリー]** をクリックすると、**[Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com/)**に移動します。 ギャラリーは、データ サイエンティストや開発者のコミュニティが、Cortana Intelligence Suite のコンポーネントを使用して作成したソリューションを共有する場です。

ギャラリーの詳細については、「 [Cortana Intelligence ギャラリーでソリューションを共有および発見する](machine-learning-gallery-how-to-use-contribute-publish.md)」を参照してください。

## <a name="components-of-an-experiment"></a>実験の構成要素
実験にはデータセットが含まれます。データセットからデータが分析モジュールに提供され、分析モジュールを接続することで予測分析モデルが完成します。 有効な実験に求められる具体的な条件を以下に示します。

* 実験には少なくとも&1; つのデータセットと&1; つのモジュールがある。
* データセットはモジュールにのみ接続できる。
* モジュールはデータセットにも別のモジュールにも接続できる。
* モジュールのすべての入力ポートが、何らかの形でデータ フローに接続されている。
* モジュールの必須パラメーターがすべて設定されている。

実験を最初から作成するか、既にあるサンプル実験をテンプレートとして使用してください。 詳細については、 [サンプル実験を使用した新しい実験の作成](machine-learning-sample-experiments.md)に関するページを参照してください。

簡単な実験を作成する例については、「 [Azure Machine Learning Studio での簡単な実験の作成](machine-learning-create-experiment.md)」をご覧ください。

予測分析ソリューションのより詳しいチュートリアルについては、「 [Azure Machine Learning を使用した予測ソリューションの開発](machine-learning-walkthrough-develop-predictive-solution.md)」をご覧ください。

### <a name="datasets"></a>[データセット]
データセットを Machine Learning Studio にアップロードすることで、これらのデータセットをモデル作成プロセスで使用できるようになります。 Machine Learning Studio には数多くのサンプル データセットが既に含まれているため、これらを実験で試すことができます。また、必要に応じてさらにデータセットをアップロードできます。 提供されるデータセットには以下のようなものがあります。

* **さまざまな自動車の燃費データ** - シリンダー数や馬力などによって分類された、自動車の燃費値 (MPG) です。
* **乳がんデータ** - 乳がんの診断データです。
* **森林火災データ** - ポルトガル北東地域を対象とする森林火災の規模データです。

利用可能なデータセットは、実験を作成するときにキャンバスの左側の一覧から選択できます。

Machine Learning Studio に含まれている一連のサンプル データセットについては、 [Azure Machine Learning Studio でのサンプル データセットの使用](machine-learning-use-sample-datasets.md)に関するページを参照してください。

### <a name="modules"></a>モジュール
モジュールとは、データに対して実行できるアルゴリズムのことです。 Machine Learning Studio には、データの受信機能や、データのトレーニング、スコア付け、検証などに対応したさまざまなモジュールが用意されています。 提供されるモジュールには以下のようなものがあります。

* [ARFF への変換][convert-to-arff] - .NET でシリアル化されたデータセットを属性関係ファイル フォーマット (ARFF) に変換します。
* [基本統計値の計算][elementary-statistics] - 平均や標準偏差などの基本的な統計値を計算します。
* [線形回帰][linear-regression] - オンライン傾斜降下に基づく線形回帰モデルを作成します。
* [モデルのスコア付け][score-model] - トレーニングされた分類か回帰モデルをスコア付けします。

利用可能なモジュールは、実験を作成するときにキャンバスの左側の一覧から選択できます。  

モジュールに一連のパラメーターが含まれている場合、これらを使用してモジュールの内部アルゴリズムを構成することができます。 モジュールのパラメーターは、キャンバスでモジュールを選択するとキャンバス右側の **[プロパティ]** ウィンドウに表示されます。 このウィンドウでパラメーターを変更することにより、モデルを微調整できます。

大規模な機械学習アルゴリズムのライブラリを利用する際のヒントについては、 [Microsoft Azure Machine Learning のアルゴリズムの選択方法](machine-learning-algorithm-choice.md)に関するページを参照してください。

## <a name="deploying-a-predictive-analytics-web-service"></a>予測分析 Web サービスのデプロイ
予測分析モデルの準備が整ったら、それを Machine Learning Studio から Web サービスとしてデプロイすることができます。 このプロセスの詳細については、「 [Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)」を参照してください。

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/

