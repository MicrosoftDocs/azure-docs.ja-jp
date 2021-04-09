---
title: Latent Dirichlet Allocation:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Latent Dirichlet Allocation モジュールを使用して、他の方法では分類されないテキストを、カテゴリにグループ化する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: f9f239ea69aaf71e591a447feb300c13a45ba1a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907852"
---
# <a name="latent-dirichlet-allocation-module"></a>Latent Dirichlet Allocation モジュール

この記事では Azure Machine Learning デザイナーの Latent Dirichlet Allocation モジュールを使用して、他の方法では分類されないテキストをカテゴリにグループ化する方法について説明します。 

Latent Dirichlet Allocation (LDA) は、類似したテキストを検索するために自然言語処理でよく使用されます。 もう 1 つの一般的な用語は "*トピック モデリング*" です。

このモジュールでは、テキストの列を受け取り、次の出力を生成します。

+ ソース テキストと、各カテゴリのスコア

+ 各カテゴリの抽出された用語と係数を含む機能マトリックス

+ 保存し、入力として使用される新しいテキストに再適用することができる変換

このモジュールでは、scikit-learn ライブラリを使用します。 scikit-learn の詳細については、チュートリアルとアルゴリズムの説明が含まれる [GitHub リポジトリ](https://github.com/scikit-learn/scikit-learn)を参照してください。

## <a name="more-about-latent-dirichlet-allocation"></a>Latent Dirichlet Allocation の詳細

LDA は、通常、分類の方法ではありません。 しかし、生成的アプローチを使用しているため、既知のクラス ラベルを指定してからパターンを推論する必要はありません。  代わりに、アルゴリズムによって、トピックのグループを識別するために使用される確率モデルが生成されます。 確率モデルを使用して、既存のトレーニング ケースまたはモデルに入力として提供する新しいケースを分類することができます。

生成モデルは、テキストとカテゴリ間のリレーションシップについての強い仮説を作成しないようにするため、こちらを好む人もいるでしょう。 これは単語の分布のみを使用して数学的にトピックをモデル化します。

この理論について解説された次の論文を PDF でダウンロードできます: [Latent Dirichlet Allocation: Blei, Ng, and Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf).

このモジュールでの実装は、LDA 用の [scikit-learn ライブラリ](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py)に基づいています。

詳しくは、「[テクニカル ノート](#technical-notes)」セクションをご覧ください。

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Latent Dirichlet Allocation を構成する方法

このモジュールには、未加工または前処理済みのテキスト列を含むデータセットが必要です。

1. **Latent Dirichlet Allocation** モジュールをパイプラインに追加します。

2. モジュールの入力として、1 つまたは複数のテキスト列を含むデータセットを指定します。

3. **[Target columns]\(ターゲット列\)** で、分析するテキストを含む 1 つまたは複数の列を選択します。

    複数の列を選択できますが、列は **文字列** データ型である必要があります。

    LDA ではテキストから大きな特徴マトリックスが作成されるため、通常は 1 つのテキスト列を分析します。

4. **[Number of topics to model]\(モデル化するトピックの数\)** に、入力テキストから生成するカテゴリまたはトピックの数を示す 1 から 1,000 の整数を入力します。

    既定では、5 つのトピックが作成されます。

5. **[N-grams]\(N-gram\)** では、ハッシュ中に生成される N グラムの最大長を指定します。

    既定値は 2 で、バイグラムとユニグラムの両方が生成されることを意味します。

6. **[Normalize]\(正規化\)** オプションを選択すると、出力値が確率に変換されます。 

    変換された値が整数として表されるのではなく、出力と特徴データセットの値が次のように変換されます。

    + データセット内の値は、`P(topic|document)` の確率として表されます。

    + 特徴トピック マトリックス内の値は、`P(word|topic)` の確率として表されます。

    > [!NOTE] 
    > Azure Machine Learning デザイナーの scikit-learn ライブラリは、バージョン 0.19 からの正規化されていない *doc_topic_distr* 出力をサポートしなくなりました。 このモジュールでは、**Normalize** パラメーターは "*機能トピック マトリックス*" 出力にのみ適用できます。 "*変換されたデータセット*" 出力は常に正規化されます。

7. 次の高度なパラメーターを設定する場合は、 **[Show all options]\(すべてのオプションを表示\)** オプションを選択し、**TRUE** に設定します。

    これらのパラメーターは、LDA の scikit-learn 実装に固有のものです。 scikit-learn には LDA に関する良いチュートリアルがいくつかあります。また、公式の [scikit-learn ドキュメント](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html)もあります。

    + **[Rho parameter]\(Rho パラメーター\)** 。 トピック分布の低密度の事前確率を指定します。 このパラメーターは、sklearn の `topic_word_prior` パラメーターに対応します。 単語の分布がフラットであると予想される場合、つまりすべての単語が等確率であると想定される場合は、値 **1** を使用します。 ほとんどの単語が少ないと考えられる場合は、小さい値に設定します。

    + **[Alpha parameter]\(アルファ パラメーター\)** 。 ドキュメントごとのトピックの重みの低密度の事前確率を指定します。 このパラメーターは、sklearn の `doc_topic_prior` パラメーターに対応します。

    + **[Estimated number of documents]\(推定ドキュメント数\)** 。 処理されるドキュメント (行) の数の最良推定値を表す値を入力します。 このパラメーターを使用すると、モジュールで十分なサイズのハッシュ テーブルを割り当てることができます。 これは scikit-learn の `total_samples` パラメーターに対応します。

    + **[Size of the batch]\(バッチのサイズ\)** 。 LDA モデルに送信される各テキストのバッチに含める行数を入力します。 このパラメーターは、scikit-learn の `batch_size` パラメーターに対応します。

    + **[Initial value of iteration used in learning update schedule]\(学習更新スケジュールで使用される反復の初期値\)** 。 オンライン学習での早期反復に対する学習速度の重みを下げる開始値を指定します。 このパラメーターは、scikit-learn の `learning_offset` パラメーターに対応します。

    + **[Power applied to the iteration during updates]\(更新中に反復に適用する指数\)** 。 オンライン更新中の学習速度を制御するために、反復数に適用する指数のレベルを示します。 このパラメーターは、scikit-learn の `learning_decay` パラメーターに対応します。

    + **[Number of passes over the data]\(データのパス数\)** 。 アルゴリズムがデータを循環する最大回数を指定します。 このパラメーターは、scikit-learn の `max_iter` パラメーターに対応します。

8. テキストを分類する前に、最初のパスで ngram のリストを作成する場合は、 **[Build dictionary of ngrams]\(ngram の辞書を作成する\)** または **[Build dictionary of ngrams prior to LDA]\(LDA の前に ngram の辞書を作成する\)** オプションをオンにします。

    初期辞書を事前に作成した場合は、後でモデルを確認するときに辞書を使用できます。 数値インデックスではなくテキストに結果をマップできるようにすると、一般に解釈が容易になります。 ただし、ディクショナリの保存には長い時間がかかり、追加のストレージが使用されます。

9. **[Maximum size of ngram dictionary]\(ngram 辞書の最大サイズ\)** には、n-gram 辞書に作成できる行の合計数を入力します。

    このオプションは、辞書のサイズを制御する場合に便利です。 ただし、入力の ngram の数がこのサイズを超えると、競合が発生する可能性があります。

10. パイプラインを送信します。 LDA モジュールではベイズの定理を使用して、個々の単語に関連付けられている可能性のあるトピックが判別されます。 単語は、トピックやグループに排他的には関連付けられません。 代わりに、各 n-gram には、検出されたいずれかのクラスに関連付けられる学習された確率が設定されます。

## <a name="results"></a>結果

モジュールには次の 2 つの出力があります。

+ **変換されたデータセット**: この出力には、入力テキスト、指定された数の検出されたカテゴリ、および各カテゴリに対する各テキスト例のスコアが含まれています。

+ **特徴トピック マトリックス**: 左端の列には抽出されたテキストの特徴が含まれています。 各カテゴリの列には、そのカテゴリの特徴のスコアが含まれます。


### <a name="lda-transformation"></a>LDA 変換

このモジュールでは、データセットに LDA を適用する "*LDA 変換*" も出力されます。

この変換を保存し、他のデータセットに再利用することができます。 この手法は、大規模なコーパスでトレーニングを行い、係数またはカテゴリを再利用したい場合に役立ちます。

この変換を再利用するには、Latent Dirichlet Allocation モジュールの右パネルにある **[データセットの登録]** アイコンを選択して、モジュール リストの **[データセット]** カテゴリの下にモジュールを保持します。 その後、このモジュールを[変換の適用](apply-transformation.md)モジュールに接続すると、この変換を再利用できるようになります。

### <a name="refining-an-lda-model-or-results"></a>LDA のモデルまたは結果の改良

通常、すべてのニーズを満たす単一の LDA モデルを作成することはできません。 1 つのタスク用に設計されたモデルでも、精度向上のために多くのイテレーションが必要になる場合があります。 モデルを改善するために、次のすべての方法を試してみることをお勧めします。

+ モデルのパラメーターを変更する
+ 視覚化を使用して結果を理解する
+ 分野の専門家のフィードバックを得て、生成されたトピックが役に立つかどうかを判断する

定性的メジャーは、結果の評価にも役立ちます。 トピックのモデリングの結果を評価するには、次の点を考慮します。

+ 精度。 似ている項目が実際に似ているか。
+ 多様性。 ビジネス上の問題に必要な場合、モデルで類似する項目を区別できるか。
+ スケーラビリティ。 幅広いテキスト カテゴリで動作するか、または狭いターゲット ドメインでのみ動作するか。

多くの場合、自然言語処理を使用して、テキストのクリーンアップ、要約、単純化、または分類を行うことによって、LDA に基づくモデルの精度を向上させることができます。 たとえば、Azure Machine Learning ですべてサポートされている次の手法でにより、分類の精度が向上します。

+ ストップ ワードの削除

+ 大文字と小文字の正規化

+ レンマ化またはステミング

+ 名前付きエンティティの認識

詳細については、「[Preprocess Text (テキストの前処理)](preprocess-text.md)」を参照してください。

デザイナーでは、テキスト処理に R または Python ライブラリを使用することもできます。「[R スクリプトの実行](execute-r-script.md)」、「[Python スクリプトの実行](execute-python-script.md)」。



## <a name="technical-notes"></a>テクニカル ノート

このセクションには、実装の詳細、ヒント、よく寄せられる質問への回答が含まれています。

### <a name="implementation-details"></a>実装の詳細

既定では、変換されたデータセットと特徴トピック マトリックスの出力の分布は確率として正規化されます。

+ 変換されたデータセットは、あるドキュメントでのトピックの条件付き確率として正規化されます。 この場合、各行の合計は 1 になります。

+ 特徴トピック マトリックスは、あるトピックでの単語の条件付き確率として正規化されます。 この場合、各列の合計は 1 になります。

> [!TIP]
> 場合によっては、モジュールが空のトピックを返すことがあります。 ほとんどの場合、原因はアルゴリズムの擬似ランダム初期化です。 これが発生した場合は、関連するパラメーターの変更を試すことができます。 たとえば、N-gram 辞書の最大サイズ、または特徴ハッシュに使用するビット数を変更します。

### <a name="lda-and-topic-modeling"></a>LDA とトピック モデリング

Latent Dirichlet Allocation は、多くの場合、"*コンテンツベースのトピック モデリング*" に使用されます。これは、基本的に、未分類のテキストからのカテゴリの学習を意味します。 コンテンツベースのトピック モデリングでは、トピックは単語に対する分布です。

たとえば、多くの製品を含む顧客レビューのコーパスを提供しているとします。 顧客が長期間にわたって送信したレビューのテキストには、多くの用語が含まれており、その中には複数のトピックで使用されるものがあります。

LDA プロセスで識別される "*トピック*" は、個々の製品のレビューを表している場合もあれば、製品レビューのグループを表している場合もあります。 LDA では、トピック自体は、一連の単語についての、時間の経過に伴う確率分布にすぎません。

用語は、1 つの製品に限定されることはほとんどありません。 他の製品を指すこともあれば、あらゆることに適用される一般的な用語 ("すばらしい"、"非常に高い") である場合もあります。 その他の用語は、ノイズ ワードの可能性があります。 ただし、LDA メソッドでは、同時生起の確率は別にして、宇宙のすべての単語をキャプチャしようとしたり、単語がどのように関連しているかを理解しようとしたりされることはありません。 できるのは、ターゲット ドメインで使用されている単語をグループ化することだけです。

用語のインデックスが計算された後、距離ベースの類似性メジャーによって、テキストの各行が比較され、2 つのテキストが類似しているかどうかが判断されます。 たとえば、製品には厳密に相関する複数の名前があることがわかることがあります。 または、非常に否定的な語句が特定の製品に関連付けられていることがわかる場合もあります。 類似性メジャーは、関連する用語の識別と、推奨事項の作成の両方に使用できます。

###  <a name="module-parameters"></a>モジュールのパラメーター

|名前|種類|Range|省略可能|Default|説明|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Target column(s) (ターゲット列)|列の選択||必須|StringFeature|ターゲット列の名前またはインデックス。|  
|Number of topics to model (モデル化するトピックの数)|Integer|[1;1000]|必須|5|N 個のトピックに対してドキュメントの分布をモデル化します。|  
|N-grams (N-gram)|Integer|[1;10]|必須|2|ハッシュ化の間に生成される N-gram のオーダー。|  
|Normalize (正規化)|Boolean|True または False|必須|true|出力を確率に正規化します。  変換されたデータセットは P(topic&#124;document)、特徴トピック マトリックスは P(word&#124;topic) になります。|  
|Show all options (すべてのオプションを表示)|Boolean|True または False|必須|False|scikit-learn オンライン LDA に固有の追加のパラメーターを表示します。|  
|Rho parameter (Rho パラメーター)|Float|[0.00001;1.0]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンのときに適用されます|0.01|トピックの単語の事前分布。|  
|Alpha parameter (アルファ パラメーター)|Float|[0.00001;1.0]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンのときに適用されます|0.01|ドキュメントのトピックの事前分布。|  
|Estimated number of documents (推定ドキュメント数)|Integer|[1;int.MaxValue]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンのときに適用されます|1000|推定ドキュメント数。 `total_samples` パラメーターに対応します。|  
|Size of the batch (バッチのサイズ)|Integer|[1;1024]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンのときに適用されます|32|バッチのサイズ。|  
|Initial value of iteration used in learning rate update schedule (学習速度更新スケジュールで使用される反復の初期値)|Integer|[0;int.MaxValue]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンのときに適用されます|0|早期反復に対する学習速度の重みを下げる初期値。 `learning_offset` パラメーターに対応します。|  
|Power applied to the iteration during updates (更新中に反復に適用する指数)|Float|[0.0;1.0]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンのときに適用されます|0.5|学習速度を制御するために反復数に適用する指数。 `learning_decay` パラメーターに対応します。 |  
|トレーニングの反復数|Integer|[1;1024]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンのときに適用されます|25|トレーニングの反復数。|  
|Build dictionary of ngrams (ngram の辞書を作成する)|Boolean|True または False|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスが "*オフ*" のときに適用されます|True|LDA を計算する前に ngram の辞書を構築します。 モデルの検査と解釈に役立ちます。|  
|Maximum size of ngram dictionary (ngram 辞書の最大サイズ)|Integer|[1;int.MaxValue]|**[Build dictionary of ngrams]\(ngram の辞書を作成する\)** オプションが **True** の場合に適用されます|20000|ngram 辞書の最大サイズ。 入力のトークンの数がこのサイズを超えると、競合が発生する可能性があります。|  
|Number of bits to use for feature hashing (特徴ハッシュに使用するビット数)|Integer|[1;31]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスが "*オフ*" で、 **[Build dictionary of ngrams]\(ngram の辞書を作成する\)** が **False** のときに適用されます|12|Number of bits to use for feature hashing (特徴ハッシュに使用するビット数)| 
|Build dictionary of ngrams prior to LDA (LDA の前に ngram の辞書を作成する)|Boolean|True または False|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンのときに適用されます|True|LDA の前に ngram の辞書を作成します。 モデルの検査と解釈に役立ちます。|  
|Maximum number of ngrams in dictionary (辞書内の ngram の最大数)|Integer|[1;int.MaxValue]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンで、 **[Build dictionary of ngrams]\(ngram の辞書を作成する\)** が **True** のときに適用されます|20000|辞書の最大サイズ。 入力のトークンの数がこのサイズを超えると、競合が発生する可能性があります。|  
|Number of hash bits (ハッシュ ビットの数)|Integer|[1;31]|**[Show all options]\(すべてのオプションを表示\)** チェック ボックスがオンで、 **[Build dictionary of ngrams]\(ngram の辞書を作成する\)** が **False** のときに適用されます|12|特徴ハッシュの間に使用するビット数。|   


## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 

モジュール固有のエラーの一覧については、「[デザイナーの例外とエラー コード](designer-error-codes.md)」を参照してください。
