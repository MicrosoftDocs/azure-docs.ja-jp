---
title: Train Vowpal Wabbit Model (Vowpal Wabbit モデルのトレーニング)
titleSuffix: Azure Machine Learning
description: Train Vowpal Wabbit Model モジュールを使用し、Vowpal Wabbit のインスタンスを使用して機械学習モデルを作成する方法について説明します。
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 34caefba5bae660ca150f6e447b1d59b7174c768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857714"
---
# <a name="train-vowpal-wabbit-model"></a>Train Vowpal Wabbit Model (Vowpal Wabbit モデルのトレーニング)
この記事では、Azure Machine Learning デザイナー (プレビュー) で **Train Vowpal Wabbit Model** モジュールを使用し、Vowpal Wabbit を使用して機械学習モデルを作成する方法について説明します。  

機械学習に Vowpal Wabbit を使用するには、Vowpal Wabbit の要件に従って入力の書式を設定し、必要な形式でデータを準備します。 このモジュールを使用して、Vowpal Wabbit のコマンド ライン引数を指定します。 

パイプラインが実行されると、Vowpal Wabbit のインスタンスが、指定したデータと共に実験のランタイムに読み込まれます。 トレーニングが完了すると、モデルがワークスペースにシリアル化されて戻されます。 モデルをすぐに使用して、データをスコアリングできます。 

新しいデータで既存のモデルを増分的にトレーニングするには、保存されているモデルを **Train Vowpal Wabbit Model** の **[Pre-trained Vowpal Wabbit Model]\(事前トレーニング済み Vowpal Wabbit モデル\)** 入力ポートに接続し、新しいデータを他の入力ポートに追加します。  

## <a name="what-is-vowpal-wabbit"></a>Vowpal Wabbit について  

Vowpal Wabbit (VW) は高速かつ並列に機能する機械学習フレームワークです。これは分散コンピューティング用に Yahoo! Research によって開発されました。 その後、Windows に移植され、並列アーキテクチャでの科学技術計算用に John Langford (Microsoft Research) によって採用されました。  

機械学習に重要とされる Vowpal Wabbit の機能には、継続的な学習 (オンライン学習)、次元の縮小、および対話型学習などがあります。 Vowpal Wabbit はまた、モデル データがメモリに収まらない場合に問題解決の手段となります。  

Vowpal Wabbit を主に使用するのは、分類、回帰、トピック モデリング、行列因子分解などの機械学習タスクに、以前はフレームワークを使用していたデータ サイエンティストです。 Vowpal Wabbit 用の Azure ラッパーは、オンプレミス バージョンと非常によく似たパフォーマンス特性を備えているため、Vowpal Wabbit の強力な機能とネイティブ パフォーマンスを利用し、トレーニング済みのモデルを運用可能なサービスとして簡単に発行できます。  

[Feature Hashing](feature-hashing.md) モジュールにも、Vowpal Wabbit によって提供される機能が含まれており、ハッシュ アルゴリズムを使用してテキスト データセットをバイナリ機能に変換することができます。  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Vowpal Wabbit モデルを構成する方法  

ここでは、新しいモデルをトレーニングする方法と、既存のモデルに新しいデータを追加する方法について説明します。

デザイナーの他のモジュールとは異なり、このモジュールでは、モジュール パラメーターの指定と、モデルのトレーニングの両方が行われます。 既存のモデルがある場合は、それをオプションの入力として追加し、モデルを増分的にトレーニングすることができます。

+ [必要な形式のいずれかで入力データを準備する](#prepare-the-input-data)
+ [新しいモデルをトレーニングする](#create-and-train-a-vowpal-wabbit-model)
+ [既存のモデルを増分的にトレーニングする](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>入力データを準備する

このモジュールを使用してモデルをトレーニングするには、入力データセットが、サポートされている次の 2 つの形式のいずれかを使用する 1 つのテキスト列で構成されている必要があります: **SVMLight** または **VW**。 これは、Vowpal Wabbit ではテキスト データのみが分析されるという意味ではなく、必要なテキスト ファイル形式で特徴と値を準備する必要があることを意味するだけです。  

データは、ファイル データセットまたは表形式データセットの 2 種類のデータセットから読み取ることができます。 これらのデータセットはいずれも、SVMLight 形式または VW 形式である必要があります。 Vowpal Wabbit データ形式には、列形式を必要としないためにスパース データを処理する際に領域を節約できる、という利点があります。 この形式の詳細については、[Vowpal Wabbit の wiki ページ](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)を参照してください。  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Vowpal Wabbit モデルを作成してトレーニングする

1. **Train Vowpal Wabbit Model** モジュールを実験に追加します。 
  
2. トレーニング データセットを追加し、**トレーニング データ**に接続します。 トレーニング データセットが、トレーニング データ ファイルが含まれているディレクトリの場合は、 **[Name of the training data file]\(トレーニング データ ファイルの名前\)** を使用してトレーニング データ ファイル名を指定します。 トレーニング データセットが 1 つのファイルの場合は、 **[Name of the training data file]\(トレーニング データ ファイルの名前\)** を空のままにします。

3. **[VW arguments]\(VW 引数\)** テキスト ボックスに、Vowpal Wabbit 実行可能ファイルに対するコマンド ライン引数を入力します。

     たとえば、学習速度を指定するには *`–l`* を追加し、ハッシュ ビットの数を示すには *`-b`* を追加します。  

     詳細については、[Vowpal Wabbit のパラメーター](#supported-and-unsupported-parameters)に関するセクションを参照してください。  

4. **[Name of the training data file]\(トレーニング データ ファイルの名前\)** : 入力データが含まれるファイルの名前を入力します。 この引数は、トレーニング データセットがディレクトリの場合にのみ使用されます。

5. **[Specify file type]\(ファイルの種類を指定する\)** : トレーニング データで使用されている形式を指定します。 Vowpal Wabbit では、次の 2 つの入力ファイル形式がサポートされています。  

    - **[VW]** は、Vowpal Wabbit によって使用される内部形式を表します。 詳細については、[Vowpal Wabbit wiki ページ](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)を参照してください。 
    - **[SVMLight]** は、他のいくつかの機械学習ツールで使用される形式です。 

6. **[Output readable model file]\(読み取り可能なモデル ファイルを出力する\)** : モジュールで読み取り可能なモデルを実行レコードに保存する場合は、このオプションを選択します。 この引数は、VW コマンド ラインの `--readable_model` パラメーターに対応します。  

7. **[Output inverted hash file]\(逆ハッシュ ファイルを出力する\)** : モジュールで、実行レコードの 1 つのファイルに逆ハッシュ関数を保存する場合は、このオプションを選択します。 この引数は、VW コマンド ラインの `--invert_hash` パラメーターに対応します。  

8. パイプラインを送信します。

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>既存の Vowpal Wabbit モデルを再トレーニングする

Vowpal Wabbit では、既存のモデルに新しいデータを追加することによって、増分トレーニングがサポートされています。 再トレーニング用に既存のモデルを取得するには、次の 2 つの方法があります。

+ 同じパイプライン内の別の **Train Vowpal Wabbit Model** モジュールの出力を使用します。  
  
+ デザイナーの左側のナビゲーション ペインにある **[Datasets]\(データセット\)** カテゴリで保存されているモデルを探し、それをパイプラインにドラッグします。  

1. **Train Vowpal Wabbit Model** モジュールをパイプラインに追加します。  
2. 以前にトレーニングしたモデルを、モジュールの **[Pre-trained Vowpal Wabbit Model]\(事前トレーニング済み Vowpal Wabbit モデル\)** 入力ポートに接続します。
3. 新しいトレーニング データをモジュールの **[Training data]\(トレーニング データ\)** 入力ポートに接続します。
4. **Train Vowpal Wabbit Model** のパラメーター ペインで、新しいトレーニング データの形式と、入力データセットがディレクトリの場合はトレーニング データ ファイル名も指定します。
5. 対応するファイルを実行レコードに保存する必要がある場合は、**[Output readable model file]\(読み取り可能なモデル ファイルを出力する\)** オプションと **[Output inverted hash file]\(逆ハッシュ ファイルを出力する\)** オプションを選択します。

6. パイプラインを送信します。  
7. モジュールを選択し、右側のペインの **[Outputs+logs]\(出力 + ログ\)** タブで **[Register dataset]\(データ セットの登録\)** を選択して、更新されたモデルを Azure Machine Learning ワークスペースに保持します。  新しい名前を指定しないと、更新されたモデルにより既存の保存されているモデルが上書きされます。

## <a name="technical-notes"></a>テクニカル ノート

このセクションには、実装の詳細、ヒント、よく寄せられる質問への回答が含まれています。

### <a name="advantages-of-vowpal-wabbit"></a>Vowpal Wabbit の利点

Vowpal Wabbit では、N-gram のような非線形の特徴より非常に高速な学習が実現されます。  

Vowpal Wabbit では、確率的勾配降下 (SGD) などの "*オンライン学習*" 技法を使用して、モデルが一度に 1 つのレコードに収められます。 したがって、生データが非常にすばやく反復処理され、他のほとんどのモデルよりも速く適切な予測子を作成できます。 また、この方法では、すべてのトレーニング データをメモリに読み取る必要がありません。  

Vowpal Wabbit により、すべてのデータがハッシュに変換されます。これには、テキスト データだけでなく他のカテゴリ変数も含まれます。 ハッシュを使用すると、回帰の重みをより効率的に参照できます。このことは、効果的な確率的勾配降下を実現する上で重要です。  

###  <a name="supported-and-unsupported-parameters"></a>サポートされているパラメーターとサポートされていないパラメーター 

このセクションでは、Azure Machine Learning デザイナーでの Vowpal Wabbit コマンド ライン パラメーターのサポートについて説明します。 

一般に、引数の限られたセット以外はすべてサポートされています。 引数の完全な一覧については、[Vowpal Wabbit wiki ページ](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)を参照してください。    

サポートされていないパラメーターは次のとおりです。

-   [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments) で指定されている入力および出力オプション  
  
     これらのプロパティは、モジュールによってあらかじめ自動的に構成されます。  
  
-   また、複数の出力を生成したり、複数の入力を受け取ったりするオプションは許可されていません。 これらには、 *`--cbt`* 、 *`--lda`* 、および *`--wap`* が含まれます。  
  
-   教師あり学習アルゴリズムのみがサポートされています。 したがって、 *`–active`* 、`--rank`、 *`--search`* などのオプションはサポートされていません。 

### <a name="restrictions"></a>制限

サービスの目的は Vowpal Wabbit の経験豊富なユーザーをサポートすることであるため、他のモジュールで使用されるデータセット形式ではなく、Vowpal Wabbit ネイティブ テキスト形式を使用して、事前に入力データが準備されている必要があります。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
