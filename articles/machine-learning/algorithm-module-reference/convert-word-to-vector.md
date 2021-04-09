---
title: 'Convert Word to Vector: モジュール リファレンス'
titleSuffix: Azure Machine Learning
description: 提供された 3 つの Word2Vec モデルを使用して、テキストのコーパスからボキャブラリとそれに対応する単語の埋め込みを抽出する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91536734"
---
# <a name="convert-word-to-vector-module"></a>Convert Word to Vector モジュール

この記事では、Azure Machine Learning デザイナーの Convert Word to Vector モジュールを使用して、以下のタスクを行う方法について説明します。

- 入力として指定したテキストのコーパスで、さまざまな Word2Vec モデル (Word2Vec、FastText、GloVe トレーニング済みモデル) を適用します。
- 単語埋め込みを使用してボキャブラリを生成します。

このモジュールでは Gensim ライブラリを使用します。 Gensim の詳細については、[公式 Web サイト](https://radimrehurek.com/gensim/apiref.html)を参照してください。ここにはチュートリアルとアルゴリズムの説明があります。

### <a name="more-about-converting-words-to-vectors"></a>単語をベクトルに変換する方法の詳細

ワードをベクターに変換すること (ワードのベクター化) は、自然言語処理 (NLP) のプロセスです。 このプロセスでは、言語モデルを使用して、ワードをベクター空間にマップします。 ベクター空間は、各ワードを実数のベクターで表現します。 また、類似した意味を持つワードは同様の表現を持つことができます。

ワードの埋め込みは、テキスト分類やセンチメント分析などの NLP ダウンストリーム タスクの初期入力として使用できます。

このモジュールでは、さまざまな単語埋め込みテクノロジの中で、広く使用されている 3 つの方法が実装されています。 Word2Vec と FastText の 2 つはオンライン トレーニング モデルです。 もう 1 つは事前トレーニング済みのモデルの glove-wiki-gigaword-100 です。 

オンライン トレーニング モデルは、ユーザーの入力データでトレーニングされます。 事前トレーニング済みモデルは、通常、約 1,000 億語が含まれる大きなテキスト コーパス (Wikipedia、Google News など) でオフライン トレーニングされます。 その後、単語ベクトル化の間、単語の埋め込みは変わりません。 事前トレーニング済みの単語モデルにより、トレーニング時間の短縮、単語ベクトルのエンコードの向上、全体的なパフォーマンスの向上などの利点がもたらされます。

方法に関するいくつかの情報を次に示します。

+ Word2Vec は、浅いニューラル ネットワークを使用して単語埋め込みを学習するための最も一般的な手法の 1 つです。 この理論について解説された次の論文を PDF でダウンロードできます: [Efficient Estimation of Word Representations in Vector Space](https://arxiv.org/pdf/1301.3781.pdf) このモジュールでの実装は、[Word2Vec 用の Gensim ライブラリ](https://radimrehurek.com/gensim/models/word2vec.html)に基づいています。

+ FastText 理論について説明している次の論文を PDF でダウンロードできます: [Enriching Word Vectors with Subword Information](https://arxiv.org/pdf/1607.04606.pdf) このモジュールでの実装は、[FastText 用の Gensim ライブラリ](https://radimrehurek.com/gensim/models/fasttext.html)に基づいています。

+ GloVe トレーニング済みモデルは glove-wiki-gigaword-100 です。 これは、Wikipedia のテキスト コーパスに基づく事前トレーニング済みベクトルのコレクションであり、56 億個のトークンと 40 万個の小文字化されたボキャブラリ単語が含まれます。 PDF でダウンロードできます。[GloVe:Global Vectors for Word Representation](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Convert word to Vector の構成方法

このモジュールには、テキストの列が含まれるデータセットが必要です。 前処理されたテキストの方が適しています。

1. **Convert word to Vector** モジュールをパイプラインに追加します。

2. モジュールの入力として、1 つまたは複数のテキスト列を含むデータセットを指定します。

3. **[Target column]\(ターゲット列\)** で、処理するテキストが含まれる列を 1 つだけ選択します。

    このモジュールではテキストからボキャブラリが作成されるため、列の内容が異なり、そのためにボキャブラリの内容が異なります。 モジュールで受け入れられるターゲット列が 1 つだけなのはこのためです。

4. **[Word2Vec strategy]\(Word2Vec の戦略\)** では、 **[GloVe pretrained English Model]\(GloVe 事前トレーニング済み英語モデル\)** 、 **[Gensim Word2Vec]** 、 **[Gensim FastText]** から選択します。

5. **[Word2Vec strategy]\(Word2Vec の戦略\)** が **[Gensim Word2Vec]** または **[Gensim FastText]** の場合:

    + **[Word2Vec Training Algorithm]\(Word2Vec トレーニング アルゴリズム\)** で、 **[Skip_gram]** または **[CBOW]** を選択します。 違いは、[元の論文 (PDF)](https://arxiv.org/pdf/1301.3781.pdf) で説明されています。

        既定の方法は **Skip_gram** です。

    + **[Length of word embedding]\(単語埋め込みの長さ\)** では、単語ベクトルの次元を指定します。 この設定は、Gensim の `size` パラメーターに対応します。

        既定の埋め込みサイズは 100 です。

    + **[Context window size]\(コンテキスト ウィンドウ サイズ\)** では、予測される単語と現在の単語の間の最大距離を指定します。 この設定は、Gensim の `window` パラメーターに対応します。

        既定のウィンドウ サイズは 5 です。

    + **[Number of epochs]\(エポックの数\)** では、コーパス上のエポック (イテレーション) の数を指定します。 Gensim の `iter` パラメーターに対応します。

        既定のエポック数は 5 です。

6. **[Maximum vocabulary size]\(最大ボキャブラリ サイズ\)** では、生成されるボキャブラリに含まれる単語の最大数を指定します。

    一意のワードが最大サイズよりも多い場合は、頻度の低いものが取り除かれます。

    既定のボキャブラリ サイズは 10,000 です。

7. **[Minimum word count]\(最小単語数\)** では、単語の最小数を指定します。 頻度がこの値より低い単語はすべて、モジュールで無視されます。

    既定値は 5 です。

8. パイプラインを送信します。

## <a name="examples"></a>例

モジュールには次の 1 つの出力があります。

+ **埋め込みを含むボキャブラリ**:生成されたボキャブラリと共に、各単語の埋め込みが格納されます。 1 つの次元が 1 つの列を占めます。

次の例では、Convert Word to Vector モジュールがどのように動作するかを示します。 ここでは、前処理された Wikipedia SP 500 データセットに対して、Convert Word to Vector が既定の設定で使用されています。

### <a name="source-dataset"></a>ソース データセット

データセットには、カテゴリ列と共に、Wikipedia からフェッチされたフル テキストが含まれています。 代表的な例の一部を次の表に示します。

|Text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

### <a name="output-vocabulary-with-embeddings"></a>埋め込みを含むボキャブラリの出力

次の表には、入力として Wikipedia SP 500 データセットを使用するこのモジュールの出力が含まれています。 左端の列はボキャブラリを示します。 その埋め込みベクトルは、同じ行の残りの列の値によって表されます。

|ボキャブラリ|埋め込み次元 0|埋め込み次元 1|埋め込み次元 2|埋め込み次元 3|埋め込み次元 4|埋め込み次元 5|...|埋め込み次元 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0.375865|0.609234|0.812797|-0.002236|0.319071|-0.591986|...|0.364276
|コンポーネント|0.081302|0.40001|0.121803|0.108181|0.043651|-0.091452|...|0.636587
|s|-0.34355|-0.037092|-0.012167|0.151542|0.601019|0.084501|...|0.149419
|p|-0.133407|0.073244|0.170396|0.326706|0.213463|-0.700355|...|0.530901
foundation|-0.166819|0.10883|-0.07933|-0.073753|0.262137|0.045725|...|0.27487
founder|-0.297408|0.493067|0.316709|-0.031651|0.455416|-0.284208|...|0.22798
location|-0.375213|0.461229|0.310698|0.213465|0.200092|0.314288|...|0.14228
city|-0.460828|0.505516|-0.074294|-0.00639|0.116545|0.494368|...|-0.2403
apple|0.05779|0.672657|0.597267|-0.898889|0.099901|0.11833|...|0.4636
campus|-0.281835|0.29312|0.106966|-0.031385|0.100777|-0.061452|...|0.05978
infinite|-0.263074|0.245753|0.07058|-0.164666|0.162857|-0.027345|...|-0.0525
loop|-0.391421|0.52366|0.141503|-0.105423|0.084503|-0.018424|...|-0.0521

この例では、**Word2Vec の戦略** には既定の **Gensim Word2Vec** を使用し、**トレーニング アルゴリズム** は **Skip-gram** です。 **単語埋め込みの長さ** は 100 なので、100 個の埋め込み列があります。

## <a name="technical-notes"></a>テクニカル ノート

このセクションには、ヒント、よく寄せられる質問への回答が含まれています。

+ オンライン トレーニング モデルと事前トレーニング済みモデルの違い:

    この Convert Word to Vector モジュールでは、3 つの異なる戦略、2 つのオンライン トレーニング モデル、および 1 つの事前トレーニング済みモデルを提供しました。 オンライン トレーニング モデルでは、トレーニング データとして入力データセットが使用され、トレーニングの間にボキャブラリと単語ベクトルが生成されます。 事前トレーニング済みモデルは、Wikipedia や Twitter テキストなどのより大規模なテキスト コーパスによって既にトレーニングされています。 事前トレーニング済みモデルは、実際には単語と埋め込みのペアのコレクションです。  

    Glove 事前トレーニング済みモデルでは、入力データセットからボキャブラリが要約されて、事前トレーニング済みモデルから各ワードの埋め込みベクトルが生成されます。 オンライン トレーニングを行わないと、事前トレーニング済みモデルを使用するときのトレーニング時間を短縮できます。 特に入力データセットのサイズが比較的小さい場合に、パフォーマンスが向上します。

+ 埋め込みサイズ:

    通常は、ワードの埋め込みの長さを数百に設定します。 たとえば、100、200、300 などです。 埋め込みサイズが小さいと、ベクター空間が小さくなり、ワード埋め込みの衝突が発生する可能性があります。  

    事前トレーニング済みモデルでは、ワード埋め込みの長さは固定されています。 この例では、glove-wiki-gigaword-100 の埋め込みサイズは 100 です。


## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 

デザイナー モジュール固有のエラーの一覧については、[Machine Learning のエラー コード](designer-error-codes.md)に関する記事を参照してください。
