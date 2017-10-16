---
title: "生体エンティティ認識 - Team Data Science Process - Azure Machine Learning | Microsoft Docs"
description: "Azure Machine Learning Workbench でディープ ラーニングを使って生体エンティティ認識を行う Team Data Science Process プロジェクトのクイックスタート。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: a13bbd5d32eaab96dfb97e60652dbe9bcbdfb1b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Team Data Science Process (TDSP) テンプレートを使った生体エンティティ認識

この現実に即したシナリオの目的は、Azure Machine Learning Workbench を使って、非構造化テキストからエンティティを抽出するような複雑な自然言語処理 (NLP) タスクを解決する方法に焦点を当てることです。

1. [Spark Word2Vec の実装](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec)を使って、PubMed の約 1,800 万件の要旨のテキスト コーパスに対して、ニューラル単語埋め込みモデルをトレーニングする方法を示します。
2. Azure 上の GPU 対応 Azure Data Science Virtual Machine (GPU DS VM) で、エンティティ抽出のためのディープ Long Short-Term Memory (LSTM) 再帰型ニューラル ネットワーク モデルを構築する方法を示します。
2. エンティティ認識タスクにおいて、汎用の単語埋め込みモデルよりも、ドメイン固有の単語埋め込みモデルの方がパフォーマンスが高いことを示します。 
3. Azure Machine Learning Workbench を使って、ディープ ラーニング モデルをトレーニングして運用化する方法を示します。

4. Azure Machine Learning Workbench 内の次の機能を示します。

    * [Team Data Science Process (TDSP) 構造とテンプレート](how-to-use-tdsp-in-azure-ml.md)のインスタンス化。
    * Jupyter Notebook と Python スクリプトのコードの実行。
    * Python ファイルの実行履歴の追跡。
    * HDInsight Spark 2.1 クラスターを使った、リモート Spark コンピューティング コンテキストでのジョブの実行。
    * Azure 上のリモート GPU VM でのジョブの実行。
    * Azure Container Services での、Web サービスとしてのディープ ラーニング モデルの簡単な運用化。

## <a name="use-case-overview"></a>ユース ケースの概要
生体名前付きエンティティ認識 (固有表現抽出) は、生物医学に関する次のような複雑な NLP タスクを行うための重要なステップです。 
* 電子医療記録から病名や症状を抽出する。
* 創薬
* 薬の相互作用や薬と病気の関係、遺伝子とタンパク質の関係など、異なるエンティティ タイプ間の作用を理解する。

このユース ケースのシナリオでは、Medline PubMed の要旨のような大量の非構造化データ コーパスをどのように分析可能にして、単語埋め込みモデルをトレーニングするかに焦点を当てています。 生成された埋め込みは、ニューラル エンティティ抽出をトレーニングするために自動的に生成された特徴と見なされます。

結果として、汎用的な特徴タイプでモデルをトレーニングするよりも、ドメイン固有の単語埋め込みによる特徴で生体エンティティ抽出モデルをトレーニングした方がパフォーマンスが優れていることがわかりました。 ドメイン固有のモデルでは (9,475 件中) 7,012 件のエントリを正しく検出し、F1 スコアは 0.73 であるのに対し、汎用モデルでは検出エントリ数は 5,274 件、F1 スコアは 0.61 です。

次の図は、データの処理とモデルのトレーニングに使用したアーキテクチャを示しています。

![アーキテクチャ](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>データの説明

### <a name="1-word2vec-model-training-data"></a>1.Word2Vec モデルのトレーニング データ
まず、[MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html) から未加工の MEDLINE 要旨データをダウンロードしました。 このデータは、MEDLINE の [FTP サーバー](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline)で XML ファイルとして一般公開されています。 サーバーには 892 個の XML ファイルがあり、各ファイルに 30,000 件の論文の情報があります。 データの収集手順については、プロジェクト構成のセクションで詳しく説明しています。 各ファイルのフィールドは次のようになっています。 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2.LSTM モデルのトレーニング データ

ニューラル エンティティ抽出モデルは、一般公開されているデータセットでトレーニングおよび評価されています。 これらのデータセットに関する詳しい説明については、次のソースを参照してください。
 * [Bio-Entity Recognition Task at BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR task corpus](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - Task 9.1 (Drug Recognition)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Azure ギャラリーの GitHub リポジトリへのリンク
次に示すのは、コードとより詳細な説明を含む、現実に即したシナリオを集めた一般公開されている GitHub リポジトリへのリンクです。 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>前提条件 

* Azure [サブスクリプション](https://azure.microsoft.com/free/)。
* Azure Machine Learning Workbench。 [インストール ガイド](quickstart-installation.md)をご覧ください。 現在、Azure Machine Learning Workbench をインストールできるのは次のオペレーティング システムのみです。 
    * Windows 10 または Windows Server 2016
    * macOS Sierra (またはそれ以降)


### <a name="azure-services"></a>Azure サービス
* Linux (HDI 3.6) 上の [HDInsight Spark クラスター](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) バージョン Spark 2.1 (コンピューティング能力のスケール アウトのため)。 この後説明する MEDLINE の要旨全体を処理するには、最低限以下の構成が必要です。 
    * ヘッド ノード: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) サイズ。
    * ワーカー ノード: [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/) サイズを少なくとも 4 つ。 このシナリオでは D12_V2 サイズのワーカー ノードを 11 個使用しました。
* [NC6 Data Science Virtual Machine (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) (コンピューティング能力のスケールアップのため)。

### <a name="python-packages"></a>Python パッケージ

必要な依存関係はすべて、シナリオのプロジェクト フォルダーの aml_config/conda_dependencies.yml ファイルに定義されています。 このファイルに定義されている依存関係は、Docker、VM、および HDI クラスターのターゲットに対して実行するよう自動的にプロビジョニングされます。 Conda 環境ファイルのフォーマットの詳細については、[こちら](https://conda.io/docs/using/envs.html#create-environment-file-by-hand)をご覧ください。

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Azure Machine Learning (AML) Workbench の基本的な使用方法
* [概要](overview-what-is-azure-ml.md)
* [インストール](quickstart-installation.md)
* [TDSP の使用](how-to-use-tdsp-in-azure-ml.md)
* [ファイルを読み書きする方法](how-to-read-write-files.md)
* [Jupyter Notebook の使用方法](how-to-use-jupyter-notebooks.md)
* [GPU の使用方法](how-to-use-gpu.md)

## <a name="scenario-structure"></a>シナリオの構成
このシナリオでは、TDSP プロジェクト構造とドキュメント テンプレート (図 1) を使用します。これは、[TDSP のライフサイクル](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)に従ったものです。 プロジェクトは、[こちら](./how-to-use-tdsp-in-azure-ml.md)の手順を基に作成されています。


![プロジェクト情報の入力](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

詳細なデータ サイエンス ワークフローは次のとおりです。

### <a name="1-data-acquisition-and-understanding"></a>1.データの取得と理解

「[データの取得と理解](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/Code/01_Data_Acquisition_and_Understanding/ReadMe.md)」を参照してください。

未加工の MEDLINE コーパスには合計で 2,700 万件の要旨があり、約 1,000 万件の論文の要旨フィールドは空白になっています。 Azure HDInsight Spark を使って、単一マシンのメモリにロードできないビッグ データを [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) として処理します。 まず、データを Spark クラスターにダウンロードします。 次に、[Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) で次の手順を実行します。 
* Medline XML Parser を使って XML ファイルを解析する。
* センテンス分割、トークン化、ケース (大文字小文字) の正規化など、要旨のテキストを前処理する。
* 要旨フィールドのテキストが空または短い論文を除外する。 
* トレーニングする要旨からワード ボキャブラリを作成する。
* 単語埋め込みニューラル モデルをトレーニングする。 詳しくは、[GitHub のコード](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/Code/01_DataPreparation/ReadMe.md)を参照してください。


XML ファイルの解析後、データの形式は次のようになります。 

![サンプル データ](./media/scenario-tdsp-biomedical-recognition/datasample.png)

ニューラル エンティティ抽出モデルは、一般公開されているデータセットでトレーニングおよび評価されています。 これらのデータセットに関する詳しい説明については、次のソースを参照してください。
 * [Bio-Entity Recognition Task at BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR task corpus](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - Task 9.1 (Drug Recognition)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2.モデリング

「[モデリング](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling)」を参照してください。

モデリングの段階では、前のセクションで単語埋め込みモデルのトレーニング用にダウンロードしたデータの使用方法を示し、これを他のダウンストリーム タスクに使用します。 ここでは PubMed のデータを使用していますが、埋め込みを生成するパイプラインは汎用的であるため、他のドメインの単語埋め込みのトレーニングにも利用することができます。 データを正確に表す埋め込みを作成するには、word2vec が大量のデータでトレーニングされている必要があります。
単語埋め込みの準備が整ったら、学習済みの埋め込みを使用するディープ ニューラル ネットワーク モデルをトレーニングして、埋め込み層を初期化することができます。 ここでは埋め込み層をトレーニング不可としてマークしていますが、必須ではありません。 単語埋め込みモデルのトレーニングは教師ありの学習ではないので、ラベル付けされていないテキストを利用することができます。 ただし、エンティティ認識モデルのトレーニングは教師ありの学習タスクであり、その精度は手動で注釈が付けられたデータの量と質に依存します。 


#### <a name="21-feature-generation"></a>2.1. 特徴の生成

「[特徴の生成](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling/01_FeatureEngineering)」を参照してください。

Word2Vec は、ラベル付けされていないトレーニング コーパスでニューラル ネットワーク モデルをトレーニングする、単語埋め込み学習アルゴリズムです。 コーパス内の各単語について、そのセマンティック情報を表す連続ベクトルを生成します。 これらのモデルは、1 つの隠れ層を持つ単純なニューラル ネットワークです。 単語ベクトル/埋め込みの学習は、誤差逆伝播法と確率的勾配降下法によって行われます。 word2vec モデルには、Skip-Gram と Continuous-Bag-of-Words という 2 つの種類があります (詳細は[こちら](https://arxiv.org/pdf/1301.3781.pdf))。 ここでは Skip-Gram をサポートする word2vec の MLlib の実装を使用しているので、Skip-Gram モデルについて下の図で簡単に説明します (図の出典への[リンク](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/))。 

![Skip Gram モデル](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

このモデルでは、パフォーマンスを最適化するために階層的ソフトマックスとネガティブ サンプリングを使用します。 階層的ソフトマックス (H-SoftMax) はバイナリ ツリーに似ています。 H-SoftMax は、基本的にフラットなソフトマックス層を単語のリーフ (葉) を持つ階層に置換します。 これにより、1 つ 1 つの単語の確率計算を連続する確率計算に分解することができ、コストがかかる正規化をすべての単語に対して計算する必要がなくなります。 均衡の取れたバイナリ ツリーの深さは log2(|V|) (V はボキャブラリ) であるため、最大でも log2(|V|) のノードを評価すれば、単語の最終的な確率が得られます。 c というコンテキストで w という単語が出現する確率は、単純にそのリーフ ノードに至るために右に曲がる確率と左に曲がる確率の積になります。 頻出する単語をより短く表現できるように、データセット内の単語の出現頻度に基づいて Huffman ツリーを構築することができます。 詳しくは、[こちらのリンク](http://sebastianruder.com/word-embeddings-softmax/)を参照してください。
図の出典は[こちら](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)です。

##### <a name="visualization"></a>グラフ

埋め込みを作成したので、それを視覚化し、意味的に類似した単語間の関係を確認します。 

![W2V 類似性](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

埋め込みを視覚化する 2 つの方法を示しました。 上にあるのが、PCA を使って高次元ベクトルを 2 次元ベクトル空間に投影する方法です。 この方法では情報が大幅に欠落するため、正確な視覚化はできません。 下にあるのが、PCA を [t-SNE](https://distill.pub/2016/misread-tsne/) と使用する方法です。 t-SNE は非線形の次元削減手法で、高次元データを 2 次元または 3 次元の空間に埋め込み、散布図で視覚化するのに最も適した方法です。  高次元のオブジェクトをそれぞれ 2 次元または 3 次元のポイントでモデル化するのですが、その際に類似オブジェクトは近くのポイントに、異なるオブジェクトは離れたポイントに、といった方法でモデル化します。 このプロセスは 2 段階に分かれており、 まず、高次元空間のペアに対して確率分布を作成します。類似オブジェクトは選択さる確率が高く、異なるポイントは選択される確率が低いというやり方です。 次に、低次元マップ上のポイントに対して同様の確率分布を定義して、マップ上のポイントの場所に関する 2 つの分布間の KL ダイバージェンスを最小化します。 低次元マップ上のポイントの場所は、勾配降下法を使って KL ダイバージェンスを最小化することで取得します。 ただし、t-SNE は常に信頼できるとは限りません。 実装の詳細は[こちら](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling/01_FeatureEngineering)を参照してください。 


次の図に示すように、t-SNE による 視覚化では、単語ベクトルと潜在的なクラスタリング パターンの分離がより大きくなります。 


* PCA による視覚化

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* t-SNE による視覚化

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* "Cancer" という単語に最も近いポイント (すべて "Cancer" のサブタイプ)

!["Cancer" に最も近いポイント](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. ニューラル エンティティ抽出のトレーニング

「[ニューラル エンティティ抽出のトレーニング](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling/02_ModelCreation/ReadMe.md)」を参照してください。

フィード フォワード ニューラル ネットワーク アーキテクチャには、各入出力を他の入出力とは独立したものとして扱うという問題があります。 このアーキテクチャでは、機械翻訳やエンティティ抽出のような、シーケンスからシーケンスにラベリングするタスクをモデル化することができません。 再帰型ニューラル ネットワーク モデルは現在までに計算された情報を次のノードに渡すことができるので、この問題を解決できます。 これは、次の図に示すように、以前に計算された情報を利用できることから、ネットワークにメモリを持っているという言い方もされます。

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Vanilla RNN では、以前に見たすべての情報を利用することができないため、[勾配消失の問題](https://en.wikipedia.org/wiki/Vanishing_gradient_problem)が起こります。 この問題が見られるのは、予測のために大量のコンテキストが必要になる場合だけです。 ただし、LSTM のようなモデルにこのような問題はありません。実際、LSTM モデルは長期的な依存関係を記憶するよう設計されています。 ニューラル ネットワークが 1 つだけの Vanilla RNN とは異なり、LSTM では各セル 4 つのニューラル ネットワーク間でやり取りがあります。 LSTM の詳しい説明については、[こちらの記事](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)を参照してください。

![LSTM セル](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

私たちが作った LSTM ベースの再帰型ニューラル ネットワークを使って、PubMed データから薬、病名、症状の言及などのエンティティ タイプを抽出してみましょう。 まずは大量のラベル付きデータを取得するのですが、この手順はご想像通り簡単ではありません。 ほとんどの医療データは慎重な取り扱いを要する個人情報を含んでいるため、一般公開されていません。 ここでは、一般公開されている 2 つのデータセットを組み合わせて使用します。 1 つは Semeval 2013 - Task 9.1 (Drug Recognition) のデータセットで、もう 1 つは BioCreative V CDR task のデータセットです。 この 2 つのデータセットを組み合わせて自動的にラベル付けを行い、メディカル テキストから薬と病名の両方を検出して、単語埋め込みを評価します。 実装の詳細については、[GitHub のコード](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling/02_ModelCreation)を参照してください。

以下に、すべてのコードにまたがって比較のために使用したモデルのアーキテクチャを示します。 さまざまなデータセットに対して変化するパラメーターは、シーケンス長の最大値 (ここでは 613) です。

![LSTM モデル](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. モデルの評価
共有タスク [Bio-Entity Recognition Task at Bio NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) の評価スクリプトを使って、モデルの精度、再現率、および F1 スコアを評価します。 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>ドメイン内の単語埋め込みモデルと汎用的な単語埋め込みモデルの比較

次に示すのは、2 つの特徴タイプ (1) PubMed の要旨でトレーニングした単語埋め込みと (2) Google News でトレーニングした単語埋め込みの精度を比較したものです。 明らかに、ドメイン内の単語埋め込みモデルの方が汎用モデルよりも高いパフォーマンスを発揮することがわかります。 このため、汎用的な単語埋め込みモデルではなく特定の単語埋め込みモデルを使う方が理にかなっています。 

* タスク #1: 薬と病名の検出

![モデルの比較 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

同様の方法で、他のデータセットで単語埋め込みの評価を行い、ドメイン内モデルが常に優位であるかを確認します。

* タスク #2: タンパク質、細胞株、細胞型、DNA、RNA の検出

![モデルの比較 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* タスク #3: 化学薬品と病名の検出

![モデルの比較 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* タスク #4: 薬の検出

![モデルの比較 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* タスク #5: 遺伝子の検出

![モデルの比較 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow と CNTK の比較
報告されたモデルはすべて、バックエンドが TensorFlow の Keras を使ってトレーニングされています。 トレーニングの時点では、バックエンドが CNTK の Keras では "リバース" がサポートされていませんでした。 したがって、ここでは比較のため、CNTK バックエンドを使った一方向の LSTM モデルをトレーニングし、これを TensorFlow バックエンドを使った一方向の LSTM モデルと比較しています。 Keras 向けの CNTK 2.0 は[こちら](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)からインストールできます。 

![モデルの比較 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

CNTK は、エポックあたりのトレーニング時間 (CNTK は 60 秒、Tensorflow は 75 秒) と検出したテスト エンティティの数において、Tensorflow と同等のパフォーマンスを記録しました。 評価のため、ここでは一方向の層を使用しています。


### <a name="3-deployment"></a>3.デプロイ

「[デプロイ](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/03_Deployment)」を参照してください。

ここでは、[Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/) のクラスター上に Web サービスをデプロイしました。 運用環境では、クラスターで Docker と Kubernetes をプロビジョニングして、Web サービスのデプロイを管理します。 運用化のプロセスについて詳しくは、[こちら](model-management-service-deploy.md )を参照してください。


## <a name="conclusion"></a>まとめ

Spark 上で Word2Vec アルゴリズムを使って単語埋め込みをトレーニングする方法について詳しく説明し、抽出された埋め込みを特徴として使用して、エンティティ抽出のディープ ニューラル ネットワークをトレーニングしました。 また、生体ドメインでトレーニング パイプラインを適用しました。 ただし、このパイプラインは汎用的であるため、他のドメインのカスタム エンティティ タイプの検出にも適用できます。 十分な量のデータがあれば、別のドメインであっても、ここで説明したワークフローを簡単に採用することができます。

## <a name="references"></a>参照

* Tomas Mikolov, Kai Chen, Greg Corrado, and Jeffrey Dean. 2013a. Efficient estimation of word representations in vector space. In Proceedings of ICLR.
* Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S Corrado, and Jeff Dean. 2013b. Distributed representations of words and phrases and their compositionality. In Proceedings of NIPS, pp. 3111–3119.
* Billy Chiu, Gamal Crichton, Anna Korhonen and Sampo Pyysalo. 2016. [How to Train Good Word Embeddings for Biomedical NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), In Proceedings of the 15th Workshop on Biomedical Natural Language Processing, pp. 166–174.
* [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec)
* [Recurrent Neural Networks](https://www.tensorflow.org/tutorials/recurrent)
* [Problems encountered with Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: lessons learned](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

