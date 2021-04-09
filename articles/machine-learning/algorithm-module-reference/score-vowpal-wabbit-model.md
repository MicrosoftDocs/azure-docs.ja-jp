---
title: Vowpal Wabbit モデルのスコアリング
titleSuffix: Azure Machine Learning
description: Vowpal Wabbit モデルのスコアリング モジュールを使用して、既存のトレーニング済み Vowpal Wabbit モデルを使用して入力データ セットのスコアを生成する方法について説明します。
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898401"
---
# <a name="score-vowpal-wabbit-model"></a>Vowpal Wabbit モデルのスコアリング
この記事では、Azure Machine Learning デザイナーで **Vowpal Wabbit モデルのスコアリング** モジュールを使用して、既存のトレーニングされた Vowpal Wabbit モデルを使用して入力データ セットのスコアを生成する方法について説明します。  

このモジュールは、Vowpal Wabbit フレームワークの最新バージョンであるバージョン 8.8.1 を提供します。 このモジュールを使用して、VW バージョン 8 形式で保存されたトレーニング済みのモデルを使用してデータをスコア付けします。  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Vowpal Wabbit モデルのスコアリングを構成する方法

1.  **Vowpal Wabbit モデルのスコアリング** モジュールを実験に追加します。  
  
2.  トレーニング済みの Vowpal Wabbit モデルを追加し、左側の入力ポートに接続します。 同じ実験で作成されたトレーニング済みのモデルを使用することも、デザイナーの左側のナビゲーション ウィンドウにある **[データセット]** カテゴリで、保存済みのモデルを見つけることもできます。 ただし、このモデルは Azure Machine Learning デザイナーで使用できる必要があります。  
  
    > [!NOTE]
    > Vowpal Wabbit 8.8.1 モデルのみがサポートされています。他のアルゴリズムを使用してトレーニングされた保存済みのモデルを接続することはできません。
  
3.  テスト データセットを追加し、右側の入力ポートに接続します。 テスト データセットが、テスト データ ファイルが含まれているディレクトリの場合は、 **[テスト データ ファイルの名前]** を使用してテスト データ ファイル名を指定します。 テスト データセットが 1 つのファイルの場合は、 **[テスト データ ファイルの名前]** を空のままにします。

4. **[VW 引数]** テキスト ボックスに、Vowpal Wabbit 実行可能ファイルに有効なコマンドライン引数のセットを入力します。  

    Azure Machine Learning でサポートされている Vowpal Wabbit 引数とサポートされていない引数の詳細については、「[テクニカル ノート](#technical-notes)」セクションを参照してください。  

5.  **テスト データ ファイルの名前**: 入力データを含むファイルの名前を入力します。 この引数は、テスト データセットがディレクトリである場合にのみ使用されます。

6. **Specify file type (ファイルの種類を指定)** : トレーニング データで使用する形式を指定します。 Vowpal Wabbit では、次の 2 つの入力ファイル形式がサポートされています。  

   - **[VW]** は、Vowpal Wabbit によって使用される内部形式を表します。 詳細については、[Vowpal Wabbit wiki ページ](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)を参照してください。 
   - **SVMLight** は、他のいくつかの機械学習ツールで使用される形式です。 

7. スコアと一緒にラベルを出力する場合は、 **[ラベルを含む余分な列を含める]** オプションを選択します。  

   通常、テキスト データを処理する場合は、Vowpal Wabbit はラベルを必要とせず、データの各行のスコアのみを返します。  

8. 結果と一緒に生のスコアを出力する場合は、 **[Include an extra column containing raw scores] (生のスコアを含む余分な列を含める)** オプションを選択します。  

9. パイプラインを送信します。

## <a name="results"></a>結果

トレーニングの完了後:

+ 結果を視覚化するには、[[Vowpal Wabbit モデルのスコアリング]](score-vowpal-wabbit-model.md) モジュールの出力を右クリックします。 出力には、0 ～ 1 の正規化された予測スコアが示されます。 

+ 結果を評価するには、出力データセットに、モデルの評価モジュールの要件を満たす特定のスコア列の名前が含まれている必要があります。

  + 回帰タスクの場合は、評価するデータセットに、スコア付けされたラベルを表す `Regression Scored Labels` という名前の 1 つの列が含まれている必要があります。
  + 二項分類タスクの場合は、評価するデータセットに `Binary Class Scored Labels`、`Binary Class Scored Probabilities` という名前の 2 つの列があります。これらはそれぞれ、スコア付けラベルと確率を表します。
  + マルチ分類タスクの場合は、評価するデータセットに、スコア付けされたラベルを表す `Multi Class Scored Labels` という名前の 1 つの列が含まれている必要があります。

  Vowpal Wabbit モデルのスコアリング モジュールの結果を直接評価することはできないことに注意してください。 評価する前に、上記の要件に従ってデータセットを変更する必要があります。

##  <a name="technical-notes"></a>テクニカル ノート

このセクションには、実装の詳細、ヒント、よく寄せられる質問への回答が含まれています。

### <a name="parameters"></a>パラメーター

Vowpal Wabbit には、アルゴリズムを選択およびチューニングするためのコマンドライン オプションが多数あります。 これらのオプションについて、ここで詳細に説明することはできません。[Vowpal Wabbit wiki ページ](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)を参照することをお勧めします。  

次のパラメーターは Azure Machine Learning Studio (classic) ではサポートされていません。  

-   [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments) で指定された入力/出力オプション  
  
     これらのプロパティは、モジュールによってあらかじめ自動的に構成されます。  
  
-   また、複数の出力を生成したり、複数の入力を受け取ったりするオプションは許可されていません。 これらには、 *`--cbt`* 、 *`--lda`* 、および *`--wap`* が含まれます。  
  
-   教師あり学習アルゴリズムのみがサポートされています。 これにより、 *`–active`* 、`--rank`、 *`--search`* などのオプションが禁止されます。  

上記以外のすべての引数は許可されます。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 