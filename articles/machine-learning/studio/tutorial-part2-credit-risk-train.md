---
title: チュートリアル 2:信用リスク モデルをトレーニングする
titleSuffix: Azure Machine Learning Studio
description: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning Studio で作成する方法を詳しく紹介したチュートリアルです。 このチュートリアルは、3 部構成のチュートリアル シリーズのパート 2 です。 モデルをトレーニングして評価する方法について説明します。
keywords: 信用リスク, 予測分析ソリューション,リスク評価
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 45407e183c70fe67e6bd59e3fd86a50a31844c47
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453483"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio"></a>チュートリアル 2:信用リスク モデルをトレーニングする - Azure Machine Learning Studio

このチュートリアルでは、予測分析ソリューションを開発するプロセスについて詳しく説明します。 Machine Learning Studio で簡単なモデルを開発します。  その後、そのモデルを Azure Machine Learning Web サービスとしてデプロイします。  このデプロイ モデルは、新しいデータを使用して予測を行うことができます。 このチュートリアルは、**3 部構成のチュートリアル シリーズのパート 2** です。

クレジットの申請書に記入する情報に基づいて個人のクレジット リスクを予測する必要があるとします。  

信用リスクの評価は複雑な問題ですが、このチュートリアルでは、それを少し簡略化してみます。 Microsoft Azure Machine Learning Studio を使用して予測分析ソリューションを作成する方法の例として使用してください。 このソリューションには、Azure Machine Learning Studio と Machine Learning Web サービスを使用します。  

この 3 部構成のチュートリアルでは、まず、公表されている信用リスク データを使用します。  その後、予測モデルを開発してトレーニングします。  最後にそのモデルを Web サービスとしてデプロイします。

[チュートリアルのパート 1](tutorial-part1-credit-risk.md) では、Machine Learning Studio ワークスペースを作成し、データをアップロードし、実験を作成しました。

チュートリアルのこのパートでは、次のことを行います。
 
> [!div class="checklist"]
> * 複数のモデルをトレーニングする
> * モデルにスコアを付け、評価する


[チュートリアルのパート 3](tutorial-part3-credit-risk-deploy.md) では、モデルを Web サービスとしてデプロイします。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>前提条件

[このチュートリアルのパート 1](tutorial-part1-credit-risk.md) を完了しました。

## <a name="train"></a>複数のモデルをトレーニングする

Azure Machine Learning Studio を使用して機械学習モデルを作成することの利点の 1 つは、1 つの実験で複数種類のモデルを同時に試してそれらの結果を比較できることです。 このような実験は、問題の最善の解決策を見つけるのに役立ちます。

このチュートリアルで開発している実験では、2 種類の異なるモデルを作成し、それらのスコア結果を比較することで、最終的な実験で使用するアルゴリズムを決定します。  

選択できるモデルは数多くあります。 これらのモデルを表示するには、モジュール パレットの **[機械学習]** ノードを展開して、**[モデルの初期化]** とその下にあるノードを展開します。 実験の目的に合わせて、ここでは、[2 クラス サポート ベクター マシン (SVM)][two-class-support-vector-machine] モジュールと [2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールを選択します。

> [!TIP]
> 解決しようとしている特定の問題に最適な機械学習アルゴリズムを決定するには、[Microsoft Azure Machine Learning Studio のアルゴリズムの選択方法](algorithm-choice.md)に関する記事をご覧ください。
> 
> 

[2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールと [2 クラス サポート ベクター マシン][two-class-support-vector-machine] モジュールの両方を実験に追加します。

### <a name="two-class-boosted-decision-tree"></a>2 クラス ブースト デシジョン ツリー

まず、ブースト デシジョン ツリー モデルを以下の手順で設定します。

1. モジュール パレットで [2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールを見つけ、キャンバスにドラッグします。

1. [モデルのトレーニング][train-model] モジュールを見つけてキャンバスにドラッグします。その後、[2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールの出力を、[モデルのトレーニング][train-model] モジュールの左側の入力ポート に接続します。
   
   [2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールは汎用モデルを初期化し、[トレーニング モデル][train-model] モジュールはトレーニング データを使用してモデルをトレーニングします。 

1. [R スクリプトの実行][execute-r-script]モジュールの左側の出力を、[モデルのトレーニング][train-model] モジュールの右側の入力ポートに接続します (このチュートリアルでは、データの分割モジュールの[左側から出力されるデータをトレーニング用に使用](#train)しました)。
   
   > [!TIP]
   > この実験では、[R スクリプトの実行][execute-r-script]モジュールの入力 2 つと出力 1 つは必要ないため、そのままにしておきます。 
   > 
   > 

実験の一部は以下のようになっているはずです。  

![Training a model](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

次に、[モデルのトレーニング][train-model] モジュールに対して、信用リスク値を予測するように通知する必要があります。

1. [モデルのトレーニング][train-model] モジュールを選択します。 **[プロパティ]** ウィンドウで、**[列セレクターの起動]** をクリックします。

1. **[1 つの列の選択]** ダイアログで、**[使用可能な列]** の下の検索フィールドに「Credit risk」と入力し、右矢印ボタン (**>**) をクリックして [Credit risk] を **[選択した列]** に移動します。 

    ![モデルのトレーニング モジュールの Credit Risk列の選択](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. **OK** チェック マークをクリックします。

### <a name="two-class-support-vector-machine"></a>2 クラス サポート ベクター マシン

次に SVM モデルを設定します。  

最初に、SVM について少し説明されます。 ブースト デシジョン ツリーはあらゆる種類の特徴で問題なく使うことができます。 ただし、SVM モジュールは線形分類子を生成するため、これによって生成されるモデルでは、すべての数値特徴のスケールが同一である場合に最適なテスト エラーが得られます。 すべての数値特徴を同じスケールに変換するには、Tanh 変換 ([データの正規化][normalize-data]モジュール) を使用します。 これによって特徴が [0,1] の範囲に変換されます。 文字列特徴は SVM モジュールによってカテゴリ特徴に変換され、バイナリ 0/1 特徴に変換されます。このため文字列特徴を手動で変換する必要はありません。 また、[Credit Risk] 列 (21 列目) は変換しません。これは数値ですが、トレーニングによってモデルが予測する値です。このため、この値はそのままにしておきます。  

SVM モデルを設定するには、次の操作を行います。

1. モジュール パレットで [2 クラス サポート ベクター マシン][two-class-support-vector-machine] モジュールを見つけ、キャンバスにドラッグします。

1. [モデルのトレーニング][train-model] モジュールを右クリックして **[コピー]** を選択し、キャンバスを右クリックして **[貼り付け]** を選択します。 [モデルのトレーニング][train-model] モジュールのコピーでも、元のモジュールと同じ列が選択されています。

1. [2 クラス サポート ベクター マシン][two-class-support-vector-machine] モジュールの出力を、2 つ目の[モデルのトレーニング][train-model] モジュールの左側の入力ポート に接続します。

1. [データの正規化][normalize-data]モジュールを見つけ、キャンバスにドラッグします。

1. 左側の [R スクリプトの実行][execute-r-script]モジュールの左側の出力を、このモジュールの入力に接続します (モジュールの出力ポートは複数のモジュールに接続できます)。

1. [データの正規化][normalize-data]モジュールの左側の出力ポートを、2 つ目の[モデルのトレーニング][train-model] モジュールの右側の入力ポートに接続します。

実験の一部は以下のようになっているはずです。  

![Training the second model](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

次に、[データの正規化][normalize-data]モジュールを構成します。

1. [データの正規化][normalize-data]モジュールをクリックして選択します。 **[プロパティ]** ウィンドウで、**[変換メソッド]** パラメーターに **[Tanh]** を選択します。

1. **[列セレクターの起動]** をクリックし、**[が次の値で始まる]** で [列なし] を選択し、最初のドロップダウンで **[含める]** を選択し、2 つ目のドロップダウンで **[列の型]** を選択します。3 つ目のドロップダウンで **[数値]** を選択します。 これによって、すべての数値列が (そして数値列のみが) 変換されます。

1. 行の右側にあるプラス記号 (+) をクリックします。ドロップダウン リストの行が作成されます。 最初のドロップダウンで **[除外]** を選択し、2 つ目のドロップダウンで **[列名]** を選択し、テキスト フィールドに「Credit risk」と入力します。 これで [Credit Risk] 列を無視することが指定されます (この列は数値であり、指定がないと変換されてしまうため、この操作を行います)。

1. **OK** チェック マークをクリックします。  

    ![データの正規化モジュール用の列の選択](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


これで、[データの正規化][normalize-data]モジュールが、[Credit Risk] 列以外のすべての数値列で Tanh 変換を実行するように設定されました。  

## <a name="score-and-evaluate-the-models"></a>モデルにスコアを付け、評価する

[データの分割][split]モジュールによって分離されたテスト データを使用して、トレーニング済みのモデルにスコアを付けます。 その後、2 つのモデルの結果を比較して、どちらのモデルがより適した結果を生成したかを判断します。  

### <a name="add-the-score-model-modules"></a>モデルのスコア付けモジュールを追加する

1. [モデルのスコア付け][score-model]モジュールを見つけ、キャンバスにドラッグします。

1. [2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールに接続されている[モデルのトレーニング][train-model] モジュールを、[モデルのスコア付け][score-model]モジュールの左側の入力ポートに接続します。

1. 右側の [R スクリプトの実行][execute-r-script]モジュール (テスト データ) を、[モデルのスコア付け][score-model]モジュールの右側の入力ポートに接続します。

    ![モデルのスコア付けモジュールの接続](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   これで、[モデルのスコア付け][score-model]モジュールがテスト データから信用情報を取得し、モデルで実行し、テスト データの実際の信用リスク列を使用して生成されたモデルの予測と比較することができます。

1. [モデルのスコア付け][score-model]モジュールをコピーして貼り付けることで、2 つ目のコピーを作成します。

1. SVM モデルの出力 (つまり、[2 クラス サポートベクター マシン][two-class-support-vector-machine] モジュールに接続された[モデルのトレーニング][train-model] モジュールの出力ポート) を、2 つ目の[モデルのスコア付け][score-model]モジュールの入力ポートに接続します。

1. SVM モデルでは、トレーニング用データに対する変換と同様の変換を、テスト用データにも実行する必要があります。 このため、[データの正規化][normalize-data]モジュールをコピーして貼り付けることで 2 つ目のコピーを作成し、それを [R スクリプトの実行][execute-r-script]モジュールの右側に接続します。

1. 2 つ目の[データの正規化][normalize-data]モジュールの左側の出力を、2 つ目の[モデルのスコア付け][score-model]モジュールの右側の入力ポートに接続します。

    ![両方のモデルのスコア付け モジュールの接続](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>モデルの評価モジュールを追加する

2 つのスコア付けの結果を評価して比較するには、[モデルの評価][evaluate-model]モジュールを使用します。  

1. [モデルの評価][evaluate-model]モジュールを見つけ、キャンバスにドラッグします。

1. ブースト デシジョン ツリー モデルに関連付けられた[モデルのスコア付け][score-model]モジュールの出力ポートを、[モデルの評価][evaluate-model]モジュールの左側の入力ポートに接続します。

1. もう一方の[モデルのスコア付け][score-model]モジュールを、右側の入力ポートに接続します。  

    ![モデルの評価モジュールの接続](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>実験を実行して結果を確認する

実験を実行するには、キャンバスの下の **[実行]** ボタンをクリックします。 これには数分かかることがあります。 実行中であることを示す各モジュールのインジケーターが回転して、モジュールが完了すると、緑色のチェック マークが表示されます。 すべてのモジュールにチェック マークが付いたら、実験の実行は完了しています。

実験は以下のようになっているはずです。  

![両方のモデルの評価](./media/tutorial-part2-credit-risk-train/final-experiment.png)


結果を確認するには、[モデルの評価][evaluate-model]モジュールの出力ポートをクリックし、**[視覚化]** を選択します。  

[モデルの評価][evaluate-model]モジュールが生成する曲線と測定値のペアを使用して、スコア付けされた 2 つのモデルの結果を比較できるようになります。 結果は、Receiver Operator Characteristic (ROC) 曲線、Precision/Recall 曲線、または Lift 曲線で表示できます。 表示されるその他の情報には、混同行列、累積の AUC (曲線下面積) 値、その他の測定値があります。 スライダーを左右に移動してしきい値を変更することで、変更が測定値に与える影響を確認することもできます。  

グラフの右側の **[スコア付けされたデータセット]** または **[スコア付けされた比較対象のデータセット]** をクリックすると、関連付けられた曲線が強調表示され、関連する測定値が下に表示されます。 曲線の凡例では、[スコア付けされたデータセット] は、[モデルの評価][evaluate-model]モジュールの左側の入力ポート (この場合、ブースト デシジョン ツリー モデル) に対応しています。 [スコア付けされた比較対象のデータセット] は、右側の入力ポート (この場合、SVM モデル) に対応しています。 いずれかのラベルをクリックすると、そのモデルの曲線が強調表示され、次の図に示すように、関連する測定値が表示されます。  

![モデルの ROC 曲線](./media/tutorial-part2-credit-risk-train/roc-curves.png)

これらの値を検証することで、求めている結果に最も近い結果が得られるモデルを判断することができます。 モデルとパラメーター値を変えて実験を再度実行することもできます。 

結果の解釈とモデルのパフォーマンスの調整に関するサイエンスとアートは、このチュートリアルの範囲外です。 詳細については、次の記事を参照してください。
- [Azure Machine Learning Studio でモデルのパフォーマンスを評価する方法](evaluate-model-performance.md)
- [Azure Machine Learning Studio でアルゴリズムを最適化するためにパラメーターを選ぶ](algorithm-parameters-optimize.md)
- [Azure Machine Learning Studio 内でモデル結果を解釈する](interpret-model-results.md)

> [!TIP]
> 実験を実行するたびに、イテレーションの記録が実行履歴に保存されます。 キャンバスの下にある **[実行履歴を表示]** をクリックすると、これらのイテレーションをいつでも表示し、いつでも実験に戻ることができます。 また、**[プロパティ]** ウィンドウの **[前回の実行]** をクリックすると、現在開いているイテレーションの前のイテレーションがすぐに表示されます。
> 
> キャンバスの下にある **[名前を付けて保存]** をクリックすることで、実験のイテレーションのコピーを作成できます。 
> 実験の **[概要]** と **[説明]** のプロパティを使用して、実験のイテレーションで行った内容を記録します。
> 
> 詳細については、[Azure Machine Learning Studio での実験イテレーションの管理](manage-experiment-iterations.md)に関するページを参照してください。  
> 
> 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を完了しました。 
 
> [!div class="checklist"]
> * 実験の作成
> * 複数のモデルをトレーニングする
> * モデルにスコアを付け、評価する

これで、このデータのモデルをデプロイする準備が整いました。

> [!div class="nextstepaction"]
> [チュートリアル 3 - モデルをデプロイする](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/