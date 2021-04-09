---
title: テキストからの N gram 特徴抽出モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーで N-gram の抽出モジュールを使用して、テキスト データの特徴を抽出する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/08/2019
ms.openlocfilehash: 37a10d90fa0e277fbe45d9f1377e365cb3d42996
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96861462"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>テキストからの N gram 特徴抽出モジュール リファレンス

この記事では Azure Machine Learning デザイナーのモジュールについて説明します。 テキストからの N-gram 特徴抽出モジュールを使用して、非構造化テキスト データの "*特徴を抽出*" します。 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>テキストからの N-gram 特徴抽出モジュールの構成

このモジュールでは、N-gram 辞書を使用するための次のシナリオがサポートされています。

* フリー テキストの列から[新しい N-gram 辞書を作成する](#create-a-new-n-gram-dictionary)。

* [既存のテキストの特徴のセットを使用](#use-an-existing-n-gram-dictionary)して、フリー テキスト列の特徴を抽出する。

* n-gram を使用する[モデルのスコア付けまたはデプロイ](#build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint)を行う。

### <a name="create-a-new-n-gram-dictionary"></a>新しい N-gram 辞書を作成する

1.  テキストからの N-gram 特徴抽出モジュールをパイプラインに追加し、処理するテキストが含まれているデータセットを接続します。

1.  **テキスト列** を使用して、抽出するテキストを含む string 型の列を選択します。 結果は詳細であるため、一度に処理できるのは 1 列だけです。

1. **[Vocabulary mode]\(ボキャブラリ モード\)** を **[Create]\(作成\)** に設定して、新しい N-gram の特徴リストを作成していることを示します。 

1. **[N-Grams size]\(N-gram のサイズ\)** を設定して、抽出して格納する N-gram の *最大* サイズを示します。 

    たとえば、3 を入力すると、unigram、bigram、trigram が作成されます。

1. **[Weighting function]\(重み付け関数\)** は、ドキュメントの特徴ベクトルを作成する方法、およびドキュメントからボキャブラリを抽出する方法を指定します。

    * **Binary Weight (バイナリ ウェイト)** :抽出された N-gram にバイナリ プレゼンス値を割り当てます。 各 N-gram の値は、ドキュメントに存在する場合は 1 になり、そうでない場合は 0 になります。

    * **TF ウェイト (TF Weight)** :抽出された N-gram に、用語頻度 (TF) スコアを割り当てます。 各 N-gram の値は、ドキュメント内の出現頻度です。

    * **IDF ウェイト (IDF Weight)** :抽出された N-gram に、逆ドキュメント頻度 (IDF) スコアを割り当てます。 各 N-gram の値は、コーパス全体の出現頻度で割ったコーパス サイズのログです。
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF ウェイト (TF-IDF Weight)** :抽出された N-gram に、用語頻度/逆ドキュメント頻度 (TF/IDF) スコアを割り当てます。 各 N-gram の値は、その TF スコアを IDF スコアで乗算したものです。

1. **[Minimum word length]\(単語の最小長\)** を、N-gram 内の任意の *1 つの単語* に使用できる最小文字数に設定します。

1. **[Maximum word length]\(単語の最大長\)** を使用して、N-gram 内の任意の *1 つの単語* に使用できる最大文字数を設定します。

    既定では、単語またはトークンごとに最大 25 文字を使用できます。

1. **[Minimum n-gram document absolute frequency]\(N-gram ドキュメント絶対頻度の最小値\)** を使用して、N-gram が N-gram 辞書に含まれるために必要な最小出現回数を設定します。 

    たとえば、既定値の 5 を使用した場合、N-gram が N-gram 辞書に含まれるには、コーパスに 5 回以上出現する必要があります。 

1.  **[Maximum n-gram document ratio]\(N-gram ドキュメントの最大比率\)** を、コーパス全体の行数に対して特定の N-gram を含む行数の最大比率に設定します。

    たとえば、比率が 1 の場合は、特定の N-gram がすべての行に存在する場合でも、その N-gram を N-gram 辞書に追加できます。 通常は、すべての行に出現する単語はノイズ ワードと見なされて削除されます。 ドメインに依存するノイズ ワードを除外するには、この比率を小さくしてみてください。

    > [!IMPORTANT]
    > 特定の単語の発生率は一様ではありません。 ドキュメントごとに異なります。 たとえば、特定の製品に関する顧客のコメントを分析している場合、製品名の出現頻度は非常に高く、ノイズ ワードに近くなる可能性がありますが、他のコンテキストでは重要な用語になります。

1. 特徴ベクトルを正規化するには、 **[Normalize n-gram feature vectors]\(N-gram の特徴ベクトルの正規化\)** を選択します。 このオプションが有効になっている場合、各 N-gram の特徴ベクトルは L2 ノルムで除算されます。

1. パイプラインを送信します。

### <a name="use-an-existing-n-gram-dictionary"></a>既存の N-gram 辞書を使用する

1.  テキストからの N-gram 特徴抽出モジュールをパイプラインに追加し、処理するテキストが含まれているデータセットを **[データセット]** ポートに接続します。

1.  **[Text column]\(テキスト列\)** を使用して、特徴を抽出するテキストを含むテキスト列を選択します。 既定では、モジュールでは **string** 型のすべての列が選択されます。 最良の結果を得るためには、一度に 1 列ずつ処理します。

1. 以前に生成した N-gram 辞書を含む保存済みデータセットを追加して、 **[Input vocabulary]\(入力ボキャブラリ\)** ポートに接続します。 また、テキストからの N-gram 特徴抽出モジュールの上流インスタンスの **[Result vocabulary]\(結果のボキャブラリ\)** 出力も接続できます。

1. **[Vocabulary mode]\(ボキャブラリ モード\)** に対して、ドロップダウン リストから **[ReadOnly]\(読み取り専用\)** 更新オプションを選択します。

   **[ReadOnly]\(読み取り専用\)** オプションは、入力ボキャブラリの入力コーパスを表します。 新しいテキスト データセット (左側の入力) から用語の頻度を計算するのではなく、入力ボキャブラリの N-gram の重みがそのまま適用されます。

   > [!TIP]
   > このオプションは、テキスト分類器のスコアを付けるときに使用します。

1.  他のすべてのオプションについては、[前のセクション](#create-a-new-n-gram-dictionary)にあるプロパティの説明を参照してください。

1.  パイプラインを送信します。

### <a name="build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint"></a>n-gram を使用してリアルタイム エンドポイントをデプロイする推論パイプラインを構築する

テスト データセットに対して予測を行うための **[Extract N-Grams Feature From Text]\(テキストから N-Grams 特徴を抽出する\)** および **[モデルのスコア付け]** が含まれるトレーニング パイプラインは、以下のような構造で構築されています。

:::image type="content" source="./media/module/extract-n-gram-training-pipeline-score-model.png" alt-text="N-Gram の抽出のトレーニング パイプラインの例" border="true":::

囲まれている **[Extract N-Grams Feature From Text]\(テキストから N-Grams 特徴を抽出する\)** モジュールの **[Vocabulary mode]\(ボキャブラリ モード\)** は **[Create]\(作成\)** であり、 **[モデルのスコア付け]** モジュールに接続されているモジュールの **[Vocabulary mode]\(ボキャブラリ モード\)** は **[ReadOnly]\(読み取り専用\)** です。

上記のトレーニング パイプラインを正常に送信した後、囲まれたモジュールの出力をデータセットとして登録できます。

:::image type="content" source="./media/module/extract-n-gram-output-voc-register-dataset.png" alt-text="データセットの登録" border="true":::

次に、リアルタイムの推論パイプラインを作成できます。 推論パイプラインを作成したら、次のように手動で推論パイプラインを調整する必要があります。

:::image type="content" source="./media/module/extract-n-gram-inference-pipeline.png" alt-text="推論パイプライン" border="true":::

次に、推論パイプラインを送信し、リアルタイム エンドポイントをデプロイします。

## <a name="results"></a>結果

テキストからの N-gram 特徴抽出モジュールでは、次の 2 つの種類の出力が作成されます。 

* **結果データセット**: この出力は、抽出された N-gram と結合された分析済みテキストの概要です。 **[Text column]\(テキスト列\)** オプションで選択しなかった列は、出力にパススルーされます。 分析するテキストの列ごとに、モジュールによって次の列が生成されます。

  * **Matrix of n-gram occurrences (N-gram の出現のマトリックス)** :モジュールは、全コーパスで見つかった N-gram ごとに列を生成し、各列にスコアを追加して、その行の N-gram の重みを示します。 

* **Result vocabulary (結果のボキャブラリ)** :ボキャブラリには、実際の N-gram 辞書と、分析の一部として生成される用語の頻度スコアが含まれています。 データセットは、別の入力セットで利用したり、後で更新したりするために保存できます。 また、モデル化とスコアリングのためにボキャブラリを再利用することもできます。

### <a name="result-vocabulary"></a>結果のボキャブラリ

ボキャブラリには、N-gram 辞書と、分析の一部として生成される用語の頻度スコアが含まれています。 DF スコアと IDF スコアは、他のオプションに関係なく生成されます。

+ **[ID]** :一意の N-gram ごとに生成される識別子。
+ **NGram**:N-gram。 スペースやその他の単語の区切り文字は、アンダースコア文字に置き換えられます。
+ **DF**:元のコーパスの N-gram の用語頻度スコア。
+ **IDF**:元のコーパスの N-gram の逆ドキュメント頻度スコア。

このデータセットは手動で更新できますが、エラーが発生する可能性があります。 次に例を示します。

* 入力ボキャブラリで同じキーを使用している重複行がモジュールによって検出されると、エラーが発生します。 ボキャブラリ内の 2 つの行に同じ単語が含まれていないことを確認してください。
* ボキャブラリ データセットの入力スキーマは、列名と列の型を含め、完全に一致している必要があります。 
* **ID** 列と **DF** 列は、integer 型でなければなりません。 
* **IDF** 列は、float 型でなければなりません。

> [!Note]
> データ出力をモデルのトレーニング モジュールに直接接続しないでください。 モデルのトレーニングに取り込まれる前に、フリー テキスト列を削除する必要があります。 そうしないと、フリー テキスト列はカテゴリ別の特徴として扱われます。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。
