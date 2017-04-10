---
title: "手順 4: 予測分析モデルをトレーニングして評価する | Microsoft Docs"
description: "予測ソリューションの開発に関するチュートリアルの中の、Azure Machine Learning Studio で複数のモデルのトレーニング、スコア付け、評価するステップの 4 番目の手順です。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: a9ebbbdc431a34553de04e920efbbc8c2496ce5f
ms.openlocfilehash: 1ef11386d9040c3929546ea05cd15237defd8a28
ms.lasthandoff: 12/19/2016


---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>チュートリアル手順 4: 予測分析モデルをトレーニングして評価する
このトピックに含まれるのは、「[チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](machine-learning-walkthrough-develop-predictive-solution.md)」の 4 番目の手順です。

1. [Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3. [新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4. **モデルをトレーニングして評価する**
5. [Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6. [Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

- - -
Azure Machine Learning Studio を使用して機械学習モデルを作成することの利点の 1 つは、1 つの実験で複数種類のモデルを同時に試してそれらの結果を比較できることです。 このような実験は、問題の最善の解決策を見つけるのに役立ちます。

このチュートリアルで開発している実験では、2 種類の異なるモデルを作成し、それらのスコア結果を比較することで、最終的な実験で使用するアルゴリズムを決定します。  

選択できるモデルは数多くあります。 これらのモデルを表示するには、モジュール パレットの **[機械学習]** ノードを展開して、**[モデルの初期化]** とその下にあるノードを展開します。 実験の目的に合わせて、ここでは、[2 クラス サポート ベクター マシン (SVM)][two-class-support-vector-machine] モジュールと [2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールを選択します。    

> [!TIP]
> 解決しようとしている特定の問題に最適な機械学習アルゴリズムを決定するには、「 [Microsoft Azure Machine Learning のアルゴリズムの選択方法](machine-learning-algorithm-choice.md)」を参照してください。
> 
> 

## <a name="train-the-models"></a>モデルをトレーニングする

[2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールと [2 クラス サポート ベクター マシン][two-class-support-vector-machine] モジュールの両方を実験に追加します。

### <a name="two-class-boosted-decision-tree"></a>2 クラス ブースト デシジョン ツリー

まず、ブースト デシジョン ツリー モデルを以下の手順で設定します。

1. モジュール パレットで [2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールを見つけ、キャンバスにドラッグします。

2. [モデルのトレーニング][train-model] モジュールを見つけてキャンバスにドラッグします。その後、[2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールの出力を、[モデルのトレーニング][train-model] モジュールの左側の入力ポート に接続します。
   
   [2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールは汎用モデルを初期化し、[トレーニング モデル][train-model] モジュールはトレーニング データを使用してモデルをトレーニングします。 

3. [R スクリプトの実行][execute-r-script]モジュールの左側の出力を、[モデルのトレーニング][train-model] モジュールの右側の入力ポートに接続します (このチュートリアルの[手順 3](machine-learning-walkthrough-3-create-new-experiment.md) で、データの分割モジュールの左側から出力されるデータをトレーニング用に使用することを決定しています)。
   
   > [!TIP]
   > この実験では、[R スクリプトの実行][execute-r-script]モジュールの入力 2 つと出力 1 つは必要ないため、そのままにしておきます。 
   > 
   > 

実験の一部は以下のようになっているはずです。  

![Training a model][1]

次に、[モデルのトレーニング][train-model] モジュールに対して、信用リスク値を予測するように通知する必要があります。

1. [モデルのトレーニング][train-model] モジュールを選択します。 **[プロパティ]** ウィンドウで、**[列セレクターの起動]** をクリックします。

2. **[1 つの列の選択]** ダイアログで、**[使用可能な列]** の下の検索フィールドに「Credit risk」と入力し、右矢印ボタン (**>**) をクリックして [Credit risk] を **[選択した列]** に移動します。 

    ![モデルのトレーニング モジュールの Credit Risk列の選択][0]

3. **OK** チェック マークをクリックします。

### <a name="two-class-support-vector-machine"></a>2 クラス サポート ベクター マシン

次に SVM モデルを設定します。  

最初に、SVM について少し説明されます。 ブースト デシジョン ツリーはあらゆる種類の特徴で問題なく使うことができます。 ただし、SVM モジュールは線形分類器を生成するため、これによって生成されるモデルでは、すべての数値特徴のスケールが同一である場合に最適なテスト エラーが得られます。 すべての数値特徴を同じスケールに変換するには、Tanh 変換 ([データの正規化][normalize-data]モジュール) を使用します。 これによって特徴が [0,1] の範囲に変換されます。 文字列特徴は SVM モジュールによってカテゴリ特徴に変換され、バイナリ 0/1 特徴に変換されます。このため文字列特徴を手動で変換する必要はありません。 また、[Credit Risk] 列 (21 列目) は変換しません。これは数値ですが、トレーニングによってモデルが予測する値です。このため、この値はそのままにしておきます。  

SVM モデルを設定するには、次の操作を行います。

1. モジュール パレットで [2 クラス サポート ベクター マシン][two-class-support-vector-machine] モジュールを見つけ、キャンバスにドラッグします。

2. [モデルのトレーニング][train-model] モジュールを右クリックして **[コピー]** を選択し、キャンバスを右クリックして **[貼り付け]** を選択します。 [モデルのトレーニング][train-model] モジュールのコピーでも、元のモジュールと同じ列が選択されています。

3. [2 クラス サポート ベクター マシン][two-class-support-vector-machine] モジュールの出力を、2 つ目の[モデルのトレーニング][train-model] モジュールの左側の入力ポート に接続します。

4. [データの正規化][normalize-data]モジュールを見つけ、キャンバスにドラッグします。

5. 左側の [R スクリプトの実行][execute-r-script]モジュールの左側の出力を、このモジュールの入力に接続します (モジュールの出力ポートは複数のモジュールに接続できます)。

6. [データの正規化][normalize-data]モジュールの左側の出力ポートを、2 つ目の[モデルのトレーニング][train-model] モジュールの右側の入力ポートに接続します。

実験の一部は以下のようになっているはずです。  

![Training the second model][2]  

次に、[データの正規化][normalize-data]モジュールを構成します。

1. [データの正規化][normalize-data]モジュールをクリックして選択します。 **[プロパティ]** ウィンドウで、**[変換メソッド]** パラメーターに **[Tanh]** を選択します。

2. **[列セレクターの起動]** をクリックし、**[が次の値で始まる]** で [列なし] を選択し、最初のドロップダウンで **[含める]** を選択し、2 つ目のドロップダウンで **[列の型]** を選択します。3 つ目のドロップダウンで **[数値]** を選択します。 これによって、すべての数値列が (そして数値列のみが) 変換されます。

3. 行の右側にあるプラス記号 (+) をクリックします。ドロップダウン リストの行が作成されます。 最初のドロップダウンで **[除外]** を選択し、2 つ目のドロップダウンで **[列名]** を選択し、テキスト フィールドに「Credit risk」と入力します。 これで [Credit Risk] 列を無視することが指定されます (この列は数値であり、指定がないと変換されてしまうため、この操作を行います)。

4. **OK** チェック マークをクリックします。  

    ![データの正規化モジュール用の列の選択][5]

これで、[データの正規化][normalize-data]モジュールが、[Credit Risk] 列以外のすべての数値列で Tanh 変換を実行するように設定されました。  

## <a name="score-and-evaluate-the-models"></a>モデルにスコアを付け、評価する

[データの分割][split]モジュールによって分離されたテスト データを使用して、トレーニング済みのモデルにスコアを付けます。 その後、2 つのモデルの結果を比較して、どちらのモデルがより適した結果を生成したかを判断します。  

### <a name="add-the-score-model-modules"></a>モデルのスコア付けモジュールを追加する

1. [モデルのスコア付け][score-model]モジュールを見つけ、キャンバスにドラッグします。

2. [2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree] モジュールに接続されている[モデルのトレーニング][train-model] モジュールを、[モデルのスコア付け][score-model]モジュールの左側の入力ポートに接続します。

3. 右側の [R スクリプトの実行][execute-r-script]モジュール (テスト データ) を、[モデルのスコア付け][score-model]モジュールの右側の入力ポートに接続します。

    ![モデルのスコア付けモジュールの接続][6]
   
   これで、[モデルのスコア付け][score-model]モジュールがテスト データから信用情報を取得し、モデルで実行し、テスト データの実際の信用リスク列を使用して生成されたモデルの予測と比較することができます。

4. [モデルのスコア付け][score-model]モジュールをコピーして貼り付けることで、2 つ目のコピーを作成します。

5. SVM モデルの出力 (つまり、[2 クラス サポートベクター マシン][two-class-support-vector-machine] モジュールに接続された[モデルのトレーニング][train-model] モジュールの出力ポート) を、2 つ目の[モデルのスコア付け][score-model]モジュールの入力ポートに接続します。

6. SVM モデルでは、トレーニング用データに対する変換と同様の変換を、テスト用データにも実行する必要があります。 このため、[データの正規化][normalize-data]モジュールをコピーして貼り付けることで 2 つ目のコピーを作成し、それを [R スクリプトの実行][execute-r-script]モジュールの右側に接続します。

7. 2 つ目の[データの正規化][normalize-data]モジュールの左側の出力を、2 つ目の[モデルのスコア付け][score-model]モジュールの右側の入力ポートに接続します。

    ![両方のモデルのスコア付け モジュールの接続][7]

### <a name="add-the-evaluate-model-module"></a>モデルの評価モジュールを追加する

2 つのスコア付けの結果を評価して比較するには、[モデルの評価][evaluate-model]モジュールを使用します。  

1. [モデルの評価][evaluate-model]モジュールを見つけ、キャンバスにドラッグします。

2. ブースト デシジョン ツリー モデルに関連付けられた[モデルのスコア付け][score-model]モジュールの出力ポートを、[モデルの評価][evaluate-model]モジュールの左側の入力ポートに接続します。

3. もう一方の[モデルのスコア付け][score-model]モジュールを、右側の入力ポートに接続します。  

    ![モデルの評価モジュールの接続][8]

### <a name="run-the-experiment-and-check-the-results"></a>実験を実行して結果を確認する

実験を実行するには、キャンバスの下の **[実行]** ボタンをクリックします。 これには数分かかることがあります。 実行中であることを示す各モジュールのインジケーターが回転して、モジュールが完了すると、緑色のチェック マークが表示されます。 すべてのモジュールにチェック マークが付いたら、実験の実行は完了しています。

実験は以下のようになっているはずです。  

![両方のモデルの評価][3]

結果を確認するには、[モデルの評価][evaluate-model]モジュールの出力ポートをクリックし、**[視覚化]** を選択します。  

[モデルの評価][evaluate-model]モジュールが生成する曲線と測定値のペアを使用して、スコア付けされた 2 つのモデルの結果を比較できるようになります。 結果は、Receiver Operator Characteristic (ROC) 曲線、Precision/Recall 曲線、または Lift 曲線で表示できます。 表示されるその他の情報には、混同行列、累積の AUC (曲線下面積) 値、その他の測定値があります。 スライダーを左右に移動してしきい値を変更することで、変更が測定値に与える影響を確認することもできます。  

グラフの右側の **[スコア付けされたデータセット]** または **[スコア付けされた比較対象のデータセット]** をクリックすると、関連付けられた曲線が強調表示され、関連する測定値が下に表示されます。 曲線の凡例では、[スコア付けされたデータセット] は、[モデルの評価][evaluate-model]モジュールの左側の入力ポート (この場合、ブースト デシジョン ツリー モデル) に対応しています。 [スコア付けされた比較対象のデータセット] は、右側の入力ポート (この場合、SVM モデル) に対応しています。 いずれかのラベルをクリックすると、そのモデルの曲線が強調表示され、次の図に示すように、関連する測定値が表示されます。  

![モデルの ROC 曲線][4]

これらの値を検証することで、求めている結果に最も近い結果が得られるモデルを判断することができます。 モデルとパラメーター値を変えて実験を再度実行することもできます。 

結果の解釈とモデルのパフォーマンスの調整に関するサイエンスとアートは、このチュートリアルの範囲外です。 詳細については、次の記事を参照してください。
- [Azure Machine Learning でモデルのパフォーマンスを評価する方法](machine-learning-evaluate-model-performance.md)
- [Azure Machine Learning でアルゴリズムを最適化するためにパラメーターを選ぶ](machine-learning-algorithm-parameters-optimize.md)
- [Azure Machine Learning でモデル結果を解釈する](machine-learning-interpret-model-results.md)

> [!TIP]
> 実験を実行するたびに、イテレーションの記録が実行履歴に保存されます。 キャンバスの下にある **[実行履歴を表示]** をクリックすると、これらのイテレーションをいつでも表示し、いつでも実験に戻ることができます。 また、**[プロパティ]** ウィンドウの **[前回の実行]** をクリックすると、現在開いているイテレーションの前のイテレーションがすぐに表示されます。
> 
> キャンバスの下にある **[名前を付けて保存]** をクリックすることで、実験のイテレーションのコピーを作成できます。 
> 実験の **[概要]** と **[説明]** のプロパティを使用して、実験のイテレーションで行った内容を記録します。
> 
> 詳細については、「 [Azure Machine Learning Studio での実験イテレーションの管理](machine-learning-manage-experiment-iterations.md)」をご覧ください。  
> 
> 

- - -
**次: [eb サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)**

[0]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

