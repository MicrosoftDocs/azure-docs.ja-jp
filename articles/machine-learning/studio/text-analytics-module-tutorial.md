---
title: 感情分析モデルを作成する
titleSuffix: Azure Machine Learning Studio
description: テキストの前処理、N グラム、または特徴ハッシュ用のモジュールを使って Azure Machine Learning Studio でテキスト分析モデルを作成する方法
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 08d62e7a6c9503d415fe144da57eee72ce3bfafd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58088402"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio で感情分析モデルを作成する

Azure Machine Learning Studio を使用して、テキスト分析モデルを構築し、運用可能な状態にすることができます。 こうしたモデルは、たとえば、ドキュメントの分類やセンチメント分析の問題を解決するのに役立ちます。

通常のテキスト分析の実験では、次の作業を行います。

1. テキスト データセットのクリーニングと前処理
2. 前処理されたテキストからの数値特徴ベクトルの抽出
3. 分類モデルまたは回帰モデルのトレーニング
4. モデルのスコア付けと検証
5. 運用環境へのモデルのデプロイ

このチュートリアルでは、Amazon Book Reviews データセットを使用したセンチメント解析モデルを進めながら上記の手順を学習します。John Blitzer、Mark Dredze、Fernando Pereira による研究論文「Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification (伝記、ボリウッド、ラジオカセットレコーダ、ミキサー: 感想の分類に対する領域適応) コンピューター言語学会 (ACL)、2007 年」を参照してください。このデータセットは、レビュー スコア (1-2 または 4-5) と自由形式のテキストから構成されています。 目標は、レビュー スコアが低 (1-2) であるか高 (4-5) であるかを予測することです。

Azure AI ギャラリーで、このチュートリアルで取り上げた実験をご覧になれます。

[Predict Book Reviews (書評の予測)](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[Predict Book Reviews - Predictive Experiment (書評の予測 - 予測実験)](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>手順 1: テキスト データセットのクリーニングと前処理
実験ではまず、レビュー スコアを低と高のカテゴリーに分類し、問題を 2 クラス分類として形式化します。 [メタデータの編集](https://msdn.microsoft.com/library/azure/dn905986.aspx)モジュールと [Group Categorical Values (カテゴリ値のグループ化)](https://msdn.microsoft.com/library/azure/dn906014.aspx) モジュールを使用します。

![ラベルの作成](./media/text-analytics-module-tutorial/create-label.png)

次に、 [Preprocess Text (テキストの前処理)](https://msdn.microsoft.com/library/azure/mt762915.aspx) モジュールを使用してテキストをクリーニングします。 クリーニングによりデータセットのノイズが減少するため、最も重要な特徴を見つけて最終的なモデルの精度を上げることができます。 ストップワード ("the" または "a" などの一般的な単語)、数字、特殊文字、重複している文字、電子メール アドレス、および URL を削除します。 また、テキストを小文字に変換し、語句を見出語化するとともに、前処理されたテキスト中の "|||" 記号で示された文の境界を検出します。

![Preprocess Text (テキストの前処理)](./media/text-analytics-module-tutorial/preprocess-text.png)

ストップワードのカスタム リストを使用するにはどうすればいいでしょうか。 このリストは、オプションの入力として渡すことができます。 また、C# 構文のカスタム正規表現を使用して、部分文字列を置き換えたり、品詞 (名詞、動詞、形容詞) ごとに単語を削除したりすることもできます。

前処理が完了したら、データをトレーニング セットとテスト セットに分割します。

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>手順 2: 前処理されたテキストからの数値特徴ベクトルの抽出
通常、テキスト データのモデルを構築するには、自由形式のテキストを数値特徴ベクトルに変換する必要があります。 この例では、 [Extract N-Gram Features from Text (テキストからの N グラム特徴の抽出)](https://msdn.microsoft.com/library/azure/mt762916.aspx) モジュールを使って、テキスト データをそのような形式に変換します。 このモジュールは空白文字で区切られた単語の列を受け取り、データセット中に出現する単語 (単語の N グラム) の辞書を計算します。 次に、レコードごとの各単語 (N グラム) の出現回数をカウントし、そのカウントから特徴ベクトルを作成します。 このチュートリアルでは、特徴ベクトルに単一の単語と後続の 2 つの単語の組み合わせが含まれるように、N グラムのサイズを 2 に設定します。

![N グラムの抽出](./media/text-analytics-module-tutorial/extract-ngrams.png)

N グラムのカウントに、TF*IDF (単語の出現頻度と逆文書頻度の積) の重み付けを適用します。 この方法では、単一レコード内には頻出するがデータセット全体ではほとんど出現しない単語に重みが付けられます。 他にも、バイナリ、TF、グラフによる重み付けを使用できます。

一般的に、こうしたテキストの特徴は非常に高次元です。 たとえば、コーパスに 100,000 個の一意の単語があるとすると、特徴空間は 100,000、あるいは N グラムが使われていればさらに多くの次元を持ちます。 Extract N-Gram Features from Text (N グラム特徴の抽出) モジュールでは、次元を削減する各種オプションを使用できます。 有意な予測値を得るために、短い単語や長い単語、また珍しすぎる単語や頻度が高すぎる単語を除外することもできます。 このチュートリアルでは、登場するレコードの数が 5 個未満か 80% 超の N グラムを除外します。

特徴選択を使用して、予測のターゲットとの関連性が最も高い特徴だけを選択することもできます。 ここでは、1000 個の特徴を選択するために、カイ 2 乗特徴選択を使用します。 Extract N-Gram Features from Text (N グラム特徴の抽出) モジュールの右側の出力内容をクリックすると、選択された単語や N グラムのボキャブラリを表示できます

N グラム特徴抽出に代わる方法として、Feature Hashing (特徴ハッシュ) モジュールを利用できます。 ただし、 [特徴ハッシュ](https://msdn.microsoft.com/library/azure/dn906018.aspx) には、特徴選択機能や TF*IDF の重み付け機能は組み込まれていない点に注意してください。

## <a name="step-3-train-classification-or-regression-model"></a>手順 3: 分類モデルまたは回帰モデルのトレーニング
テキストが数値の特徴列に変換されました。 データセットにはまだ前の段階からの文字列があるため、データセット内の列選択を使ってこれらを除外します。

その後、 [2 クラスのロジスティック回帰](https://msdn.microsoft.com/library/azure/dn905994.aspx) を使ってターゲット、つまりレビュー スコアの高低を予測します。 この時点で、テキスト分析の問題は、通常の分類問題に変換されています。 Azure Machine Learning Studio で使用できるツールを使って、モデルを改良できます。 たとえば、実験の精度を確認するために異なる分類子を試したり、ハイパーパラメーター チューニングを使用して精度を向上させたりすることができます。

![トレーニングとスコア付け](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>手順 4: モデルのスコア付けと検証
トレーニング済みのモデルを検証するにはどうすればよいでしょうか。 ここでは、テスト データセットに対してスコアを付けて、精度を評価します。 ただし、モデルでは、トレーニング データセットから N グラムのボキャブラリとそれらの重みを学習しています。 したがって、テスト データから特徴を抽出するときには、ボキャブラリを新しく作成するのではなく、これらのボキャブラリと重みを使用する必要があります。 このため、実験のスコア付けブランチに Extract N-Gram Features (N グラム特徴の抽出モジュール) を追加して、トレーニング ブランチから出力ボキャブラリを接続し、ボキャブラリのモードを読み取り専用に設定します。 また頻度による N グラムのフィルタリングを、最小値を 1 インスタンス、最大値を 100% に設定することで無効にし、特徴選択をオフにします。

テスト データのテキスト列が数値の特徴列に変換された後で、トレーニング ブランチなどにある前の段階からの文字列を除外します。 次に、Score Model (モデルのスコア付け) モジュールを使用して予測を行い、Evaluate Model (モデルの評価) モジュールを使用して精度を評価します。

## <a name="step-5-deploy-the-model-to-production"></a>手順 5: 運用環境へのモデルのデプロイ
これで、モデルを運用環境にデプロイする準備がほぼ整いました。 モデルは Web サービスとしてデプロイされると、自由形式のテキスト文字列を入力として受け取り、"高" または "低" の予測を返します。 学習した N グラム ボキャブラリを使用してテキストを特徴に変換し、トレーニング済みのロジスティック回帰モデルを使用してそれらの特徴から予測を行います。 

予測実験をセットアップするために、最初に N グラム ボキャブラリをデータセットとして保存し、実験のトレーニング ブランチからトレーニング済みのロジスティック回帰モデルを保存します。 次に、[名前を付けて保存] で実験を保存し、予測実験の実験グラフを作成します。 実験から Split Data (データの分割) モジュールとトレーニング ブランチを削除します。 次に、以前に保存した N グラム ボキャブラリとモデルを、それぞれ Extract N-Gram Features (N グラム特徴の抽出) モジュールと Score Model (モデルのスコア付け) モジュールに接続します。 Evaluate Model (モデルの評価) モジュールも削除します。

ラベル列を削除するために、Select Columns in Dataset (データセット内の列の選択) モジュールを Preprocess Text (テキストの前処理) モジュールの前に挿入し、Score Model (モデルのスコア付け) モジュールの [Append score column to dataset (データセットにスコア列を追加)] オプションの選択を解除します。 これにより、Web サービスでは予測対象のラベルが要求されず、応答で入力特徴がエコーされなくなります。

![予測実験](./media/text-analytics-module-tutorial/predictive-text.png)

これで、Web サービスとして発行し、要求応答またはバッチ実行 API を使用して呼び出すことができる実験が完成しました。

## <a name="next-steps"></a>次の手順
[MSDN ドキュメント](https://msdn.microsoft.com/library/azure/dn905886.aspx)でテキスト分析モジュールの詳細を確認してください。

