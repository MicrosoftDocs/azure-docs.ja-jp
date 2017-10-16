---
title: "ドキュメント コレクション分析 - Azure | Microsoft Docs"
description: "Azure ML Workbench を使用したフレーズ学習、トピック モデリング、トピック モデル分析など、大量のドキュメント コレクションをまとめ、分析する方法。"
services: machine-learning
documentationcenter: 
author: kehuan
ms.author: kehuan
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 669fc7a9ec5dfb446ef2755919c498fe6f60c9df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="document-collection-analysis"></a>ドキュメント コレクションの分析

このシナリオでは、Azure ML Workbench を使用したフレーズ学習、トピック モデリング、トピック モデル分析など、大量のドキュメント コレクションをまとめ、分析する方法について説明します。 Azure Machine Learning Workbench には、大量のドキュメント コレクションに合わせて簡単にスケール アップできる機能があります。また、ローカルの計算から、データ サイエンス仮想マシン、Spark クラスターまで、多様な計算コンテキスト内でモデルをトレーニングおよびチューニングするメカニズムもあります。 Azure Machine Learning Workbench 内の Jupyter ノートブックを使用すると簡単にデプロイできます。

## <a name="link-to-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリへのリンク

この実際のシナリオに対応するパブリック GitHub リポジトリには、コード サンプルなど、この例に必要なすべての素材が含まれています。

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>概要

毎日大量のデータ (特に構造化されていないテキスト データ) が収集される場合、このような大量のテキストを整理、検索、および理解することは非常に困難です。 このドキュメント コレクション分析シナリオでは、大量のドキュメント コレクションを分析し、ダウンストリーム NLP タスクを有効にする効率的な自動化されたエンドツーエンドのワークフローについて説明します。

このシナリオから、主に次のような成果があります。

1. ドキュメントで目立つ複数単語のフレーズを学習する。

1. ドキュメント コレクションに存在する基礎となるトピックを検出する。

1. トピックの分布でドキュメントを表す。

1. トピックの内容に基づいて、ドキュメントを整理、検索、および要約する方法を提供する。

このシナリオで提示される方法を使用すると、重要な各種工業ワークロードが可能になります。たとえば、トピック傾向の異常の検出、ドキュメント コレクションの要約、似たドキュメントの検索などです。 このシナリオは、政府の法律制定、ニュース記事、製品レビュー、顧客のフィードバック、化学研究記事など、さまざまなドキュメント分析に適用できます。

このシナリオでは、次のような機械学習手法とアルゴリズムが使用されます。

1. テキスト処理とクリーニング

1. フレーズ学習

1. トピックのモデリング

1. コーパス要約

1. トピックの傾向および異常検出

## <a name="prerequisites"></a>前提条件

この例を実行するための前提条件は次のとおりです。

* [インストールと作成のクイックスタート](quickstart-installation.md)に関するページに従って、[Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) が正しくインストールされていることを確認します。

* この例は、人気のコンピューティング コンテキストで実行できます。 ただし、少なくとも 16 GB のメモリと 5 GB のディスク容量を備えたマルチコア マシン上で実行することをお勧めします。

## <a name="create-a-new-workbench-project"></a>新しいワークベンチ プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「Document Collection Analysis」と入力し、テンプレートを選択します
5.  **[作成]**

## <a name="data-description"></a>データの説明

このシナリオで使用するデータセットには、米国議会が採用した各法的措置のテキストの要約と関連するメタデータが含まれています。 このデータは [GovTrack.us](https://www.govtrack.us/) から収集されています。米国議会の活動を追跡したデータであり、米国人が国の立法プロセスに参加するために役立ちます。 一括データは、手動スクリプト (このシナリオには含まれていません) を使用して[このリンク](https://www.govtrack.us/data/congress/)からダウンロードできます。 データの詳細なダウンロード方法については、[GovTrack API のドキュメント](https://www.govtrack.us/developers/api)を参照してください。

### <a name="data-source"></a>データ ソース

このシナリオで収集される生データは、1973 年から 2017 年 6 月までに米国議会 (第 93 回から第 115 回会議まで) が導入した一連の法的措置です。 収集されたデータは JSON 形式であり、法的措置に関するさまざまな情報が含まれています。 データ フィールドの詳細な説明については、[こちらの GitHub リンク](https://github.com/unitedstates/congress/wiki/bills)を参照してください。 このシナリオ内では、デモの目的から、データ フィールドのごく一部のみを JSON ファイルから抽出しました。 このシナリオでは、こうした法的措置のレコードを含むコンパイル済みの TSV ファイル `CongressionalDataAll_Jun_2017.tsv` を提供します。 この TSV ファイルは、ノートブック フォルダー以下のノートブック `1_Preprocess_Text.ipynb`、または Python パッケージの `preprocessText.py` に自動的にダウンロードできます。

### <a name="data-structure"></a>データ構造

データ ファイルには、9 個のデータ フィールドがあります。 データ フィールドの名前と説明は以下のとおりです。

| フィールド名 | 型 | Description | 欠損値を含む |
|------------|------|-------------|---------------|
| `ID` | String | 法案/決議の ID。 このフィールドの形式は、[法案の種類][番号]-[会議] です。 たとえば、"hconres1-93" は、法案の種類は "hconres" (両院一致決議を表します。詳細については[こちらのドキュメント](https://github.com/unitedstates/congress/wiki/bills#basic-information)を参照してください)、法案番号は "1"、会議番号は "93" です。 | いいえ |
| `Text` | String | 法案/決議の内容。 | いいえ |
| `Date` | String | 法案/決議が最初に提案された日付。 'yyyy-mm-dd' の形式です。 | いいえ |
| `SponsorName` | String | 法案/決議を提案した主要起草者の名前。 | あり |
| `Type` | String | 主要起草者の肩書きの種類。'rep' (下院議員) または 'sen' (上院議員) です。 | あり |
| `State` | String | 主要起草者の状態。 | あり |
| `District` | 整数 | 起草者の肩書きが下院議員の場合は、主要起草者の地域番号。 | あり |
| `Party` | String | 主要起草者の党。 | あり |
| `Subjects` | String | 米国議会図書館が法案に累積的に追加している主題語。 主題語はコンマ区切りで連結されています。 主題語は、米国議会図書館の人員が記入しており、法案に関する情報が最初に公開されるときは、通常存在しません。 また、主題語は常に追加される可能性があります。 そのため、法案の有効期間が終了するまでに、一部の主題語は関連しなくなることがあります。 | あり |

## <a name="scenario-structure"></a>シナリオの構造

ドキュメント コレクション分析例は、2 種類の成果物に整理されます。 1 つ目は、ワークフロー全体の詳細な説明を示す一連の iPython ノートブックです。 2 つ目は、Python パッケージと、そのパッケージを使用する方法のいくつかのコード例です。 Python パッケージは汎用的なので、多くのユース ケースに対応できます。

この例のファイルは、次のように整理されます。

| ファイル名 | 型 | Description |
|-----------|------|-------------|
| `aml_config` | フォルダー | Azure Machine Learning Workbench 構成フォルダー。詳細な実験の実行構成については、[こちらのドキュメント](./experiment-execution-configuration-reference.md)を参照してください |
| `Code` | フォルダー | Python スクリプトと Python パッケージを保存するために使用されるコード フォルダー |
| `Data` | フォルダー | 中間ファイルを保存するために使用されるデータ フォルダー |
| `notebooks` | フォルダー | Jupyter ノートブックのフォルダー |
| `Code/documentAnalysis/__init__.py` | Python ファイル | Python パッケージの init ファイル |
| `Code/documentAnalysis/configs.py` | Python ファイル | 定義済みの定数を含む、ドキュメント分析 Python パッケージで使用される構成ファイル |
| `Code/documentAnalysis/preprocessText.py` | Python ファイル | ダウンストリーム タスクのデータの前処理に使用される Python ファイル |
| `Code/documentAnalysis/phraseLearning.py` | Python ファイル | データからフレーズを学習し、生データを変換するために使用される Python ファイル |
| `Code/documentAnalysis/topicModeling.py` | Python ファイル | Latent Dirichlet Allocation (LDA) トピック モデルのトレーニングに使用される Python ファイル |
| `Code/step1.py` | Python ファイル | ドキュメント コレクション分析の手順 1: テキストの前処理 |
| `Code/step2.py` | Python ファイル | ドキュメント コレクション分析の手順 2: フレーズ学習 |
| `Code/step3.py` | Python ファイル | ドキュメント コレクション分析の手順 3: LDA トピック モデルのトレーニングと評価 |
| `Code/runme.py` | Python ファイル | すべての手順を 1 つのファイルで実行する例 |
| `notebooks/1_Preprocess_Text.ipynb` | iPython Notebook | テキストの前処理と生データの変換 |
| `notebooks/2_Phrase_Learning.ipynb` | iPython Notebook | (データの変換後に) テキスト データのフレーズを学習する |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython Notebook | LDA トピック モデルのトレーニング |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython Notebook | トレーニングされた LDA トピック モデルに基づいてドキュメント コレクションの内容を要約する |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython Notebook | テキスト ドキュメントのコレクションのトピックの内容を分析し、トピックの情報を、ドキュメント コレクションに関連付けられた他のメタデータと関連付ける |
| `notebooks/winprocess.py` | Python ファイル | ノートブックに使用されるマルチプロセッシングの Python スクリプト |
| `README.md` | マークダウン ファイル | README マークダウン ファイル |

### <a name="data-ingestion-and-transformation"></a>データの取り込みと変換

コンパイル済みのデータセット `CongressionalDataAll_Jun_2017.tsv` は、BLOB ストレージに保存されます。ノートブックと Python スクリプトの両方からアクセスできます。 データの取り込みと変換には、テキスト データの前処理とフレーズ学習という 2 つの手順があります。

#### <a name="preprocess-text-data"></a>テキスト データの前処理

自然言語処理手法に前処理手順を適用して、生テキスト データがクリーニングされ、準備されます。 この前処理は、教師なしのフレーズ学習と潜在的なトピック モデリングの前段階として機能します。 詳細な手順の説明については、ノートブックの `1_Preprocess_Text.ipynb` を参照してください。 このノートブックに対応する Python スクリプト `step1.py` もあります。

この手順では、TSV データ ファイルは、Azure Blob Storage からダウンロードされ、Pandas DataFrame としてインポートされます。 DataFrame の各行要素は、テキストまたは 'document' の単一でまとまりのある長い文字列です。 各ドキュメントは、テキストのチャンクに分割されます。多くの場合、文、フレーズ、またはサブフレーズに分割されます。 分割は、フレーズの学習時に、フレーズ学習プロセスが文をまたぐ、またはフレーズをまたぐ単語文字列を使用することを禁止するように設計されています。

ノートブックと Python パッケージの両方の前処理手順に定義されている関数が複数あります。 ジョブの大部分は、こうした 2 行のコードを呼び出して実行できます。

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>フレーズ学習

フレーズ学習手順では、大規模なドキュメント コレクション全体のキー フレーズを学習する基本的なフレームワークを実装します。 フレーズ学習については、口語テクノロジに関する 2012 年の IEEE ワークショップで最初に発表された、「[Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)」という論文で説明されています。 フレーズ学習手順の詳細な実装については、iPython Notebook `2_Phrase_Learning.ipynb` と Python スクリプト `step2.py` を参照してください。

この手順では、クリーニング済みのテキストを入力として使用し、大量のドキュメント コレクションに存在する最も目立つフレーズを学習します。 フレーズ学習は、n グラムのカウント、構成単語の加重点別相互情報 (Weighted Pointwise Mutual Information) によるフレーズ候補のランク付け、およびフレーズのテキストへの書き換えという、3 つのタスクに分割できる反復処理です。 これら 3 つのタスクは、指定されたフレーズが学習されるまで、複数の反復処理で順番に実行されます。

ドキュメント分析 Python パッケージでは、Python クラス `PhraseLearner` は `phraseLearning.py` ファイルで定義されています。 以下は、フレーズ学習に使用されるコード スニペットです。

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> フレーズ学習手順は、マルチプロセッシングで実装されています。 ただし、CPU コア数を増やしても、実行時間が高速になるとは**限りません**。 Microsoft のテストでは、マルチプロセッシングのオーバーヘッドがあるため、8 コアを超えてもパフォーマンスは改善されません。 8 コア (3.6 GHz) が搭載されたコンピューターの場合、25,000 フレーズの学習に約 2.5 時間かかります。
>

### <a name="topic-modeling"></a>トピックのモデリング

潜在的なトピック モデルで LDA を使用する学習は、このシナリオの 3 つ目の手順です。 [LDA トピック モデル](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation)を学習するには、この手順で [gensim](https://radimrehurek.com/gensim/) Python パッケージが必要です。 この手順に対応するノートブックは `3_Topic_Model_Training.ipynb` です。 また、ドキュメント分析パッケージを使用する方法については、`step3.py` も参照してください。

この手順の主なタスクは、LDA トピック モデルを学習し、ハイパー パラメーターをチューニングすることです。 LDA モデルをトレーニングするときにチューニングが必要なパラメーターは複数ありますが、最も重要なパラメーターはトピック数です。 トピック数が少なすぎると、ドキュメント コレクションを洞察できません。また、トピック数が多すぎると、コーパスが多数のよく似た小さなトピックに "過度にクラスタリング" される結果になります。 このシナリオの目的は、トピック数をチューニングする方法を示すことです。 Azure Machine Learning Workbench では、パラメーター構成とコンピューティング コンテキストを変えながら、自由に実験を実行できます。

ドキュメント分析 Python パッケージには、ユーザーが最適なトピック数を判断することができる関数がいくつか定義されていました。 最初のアプローチは、トピック モデルの一貫性を評価することです。 サポートされる評価メトリックは、`u_mass`、`c_v`、`c_uci`、および `c_npmi` の 4 つです。 これら 4 つのメトリックの詳細については、[こちらのドキュメント](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf)で説明されています。 2 つ目のアプローチは、ヘルドアウト コーパスに関してパープレキシティを評価することです。

パープレキシティ評価の場合、'U' 字型の曲線は、最適な数のトピックが判明したことが期待されます。 屈曲している位置が最適な数です。 異なるランダム シードを使用して複数回評価し、平均値を取ることが推奨されます。 一貫性の評価は、'n' 字型になると期待されます。つまり、トピック数の増加に合わせて一貫性が向上してから低下します。 パープレキシティと `c_v` の一貫性のプロット例を次に示します。

![パープレキシティ](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v の一貫性](./media/scenario-document-collection-analysis/c_v_Coherence.png)

このシナリオでは、パープレキシティは 200 トピックを超えた後に大幅に増えますが、一貫性の値は 200 トピックを超えた後に大幅に低下します。 これらのグラフに基づき、クラスター化されたトピックよりも一般的なトピックを優先することで、200 トピックが適切な選択になります。

1 回の実験実施で 1 つの LDA トピック モデルをトレーニングするか、1 回の実験実施でトピック数の構成を変えて複数の LDA モデルを評価することができます。 1 つの構成について複数回実行し、平均の一貫性やパープレキシティの評価を取得することが推奨されます。 ドキュメント分析パッケージを使用する方法の詳細については、`step3.py` ファイルを参照してください。 コード スニペット例を次に示します。

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> LDA トピック モデルをトレーニングする実行時間は、コーパスのサイズ、ハイパー パラメーターの構成、マシンのコア数などね複数の要因によって変わります。 複数の CPU コアを使用すると、モデルを高速にトレーニングできます。 ただし、同じハイパー パラメーター設定の場合、コア数が多いと、トレーニング中の更新が少なくなります。 **収束された LDA モデルをトレーニングするには、少なくとも 100 回の更新**を行うことをお勧めします。 更新回数とハイパー パラメーターの関係については、[こちらの投稿](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4)と[こちらの投稿](http://miningthedetails.com/blog/python/lda/GensimLDA/)を参照してください。 Microsoft のテストでは、16 コア (2.0 GHz) が搭載されたマシンで `workers=15`、`passes=10`、`chunksize=1000` の構成を使用して、トピック数が 200 の LDA モデルをトレーニングに約 3 時間かかりました。
>

### <a name="topic-summarization-and-analysis"></a>トピックの要約と分析

トピックの要約と分析は、2 つのノートブックで構成されますが、ドキュメント分析パッケージに対応する関数はありません。

`4_Topic_Model_Summarization.ipynb` は、トレーニングされた LDA トピック モデルに基づいてドキュメントの内容を要約する方法を示しています。 要約は、手順 3 で学習した LDA トピック モデルに適用されます。 これは、ドキュメントの純度測定のトピックを使用して、トピックの重要度または品質を測定する方法を示します。 この純度測定では、出現するドキュメントを特徴付ける潜在的なトピックの方が、多数のドキュメントに弱く分散している潜在的なトピックよりも意味的に重要であると想定しています。 この概念は、「[Latent Topic Modeling for Audio Corpus Summarization](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)」ドキュメントで紹介されました。

ノートブック `5_Topic_Model_Analysis.ipynb` は、テキスト ドキュメントのコレクションのトピックの内容を分析し、トピックの情報を、ドキュメント コレクションに関連付けられた他のメタデータと関連付ける方法を示しています。 このノートブックには、ユーザーが学習したトピックとドキュメント コレクションの理解を深められるように、いくつかのプロットが導入されています。

## <a name="conclusion"></a>まとめ

この実際のシナリオでは、既知のテキスト分析手法 (この例ではフレーズ学習と LDA トピック モデリング) を使用して堅牢なモデルを生成する方法と、Azure Machine Learning Workbench でモデル パフォーマンスを追跡し、高いスケールで学習ツールをシームレスに実行する方法を取り上げました。 詳細:

* フレーズ学習とトピック モデリングを使用して、ドキュメント コレクションを生成し、堅牢なモデルを構築します。 ドキュメント コレクションが膨大な場合でも、Azure Machine Learning Workbench はスケール アップとスケール アウトが簡単です。さらに、Azure Machine Learning Workbench 内から簡単に、異なるコンピューティング コンテキストで実験を自由に実行できます。

* Azure Machine Learning Workbench は、手順ごとにノートブックを実行するオプションと、Python スクリプトで実験全体を実行するオプションの両方が用意されています。

* Azure Machine Learning Workbench を使用したハイパーパラメーターのチューニングによって、学習に必要な最適なトピック数を見つけます。 Azure Machine Learning Workbench を使用して、モデルのパフォーマンスを追跡し、高いスケールで異なる学習ツールをシームレスに実行できます。

* Azure Machine Learning Workbench は、実行履歴と学習済みのモデルを管理できます。 データ サイエンティストは、最適なパフォーマンスのモデルをすばやく特定し、生成に使用されるスクリプトとデータを見つけることができます。

## <a name="references"></a>参照

* **Timothy J. Hazen、Fred Richardson**、[_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)。 Spoken Language Technology Workshop (SLT)、2012 IEEE。 IEEE、2012。

* **Timothy J. Hazen**、[_Latent Topic Modeling for Audio Corpus Summarization_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)。 12th Annual Conference of the International Speech Communication Association。 2011。

* **Michael Roder、Andreas Both、Alexander Hinneburg**、[_Exploring the Space of Topic Coherence Measures_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf)。 Proceedings of the eighth ACM international conference on Web search and data mining。 ACM、2015。
