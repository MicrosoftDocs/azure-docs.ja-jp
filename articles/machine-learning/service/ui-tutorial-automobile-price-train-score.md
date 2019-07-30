---
title: チュートリアル:ビジュアル インターフェイスで自動車価格を予測する
titleSuffix: Azure Machine Learning service
description: ドラッグ アンド ドロップ ビジュアル インターフェイスを使用して、機械学習モデルのトレーニング、スコア付け、およびデプロイを行う方法を学習します。 このチュートリアルは、線形回帰を使用した自動車価格の予測に関する 2 部構成のシリーズのパート 1 です。
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720535"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>チュートリアル:ビジュアル インターフェイスで自動車価格を予測する

このチュートリアルでは、Azure Machine Learning service のビジュアル インターフェイスでの予測分析ソリューションの開発についてさらに見ていきます。 このチュートリアルの目的は、送信の技術仕様に基づいてすべての自動車価格を予測できるソリューションを用意することです。

チュートリアルのパート 1 で学習する内容は次のとおりです。

> [!div class="checklist"]
> * データのインポートとクリーンアップ
> * 機械学習モデルのトレーニング
> * モデルのスコア付けと評価

チュートリアルの[パート 2](ui-tutorial-automobile-price-deploy.md) では、予測モデルを Azure Web サービスとしてデプロイする方法を学習します。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

このチュートリアルの完成版は、実験のサンプルとして使用できます。

これを見つけるには、 **[実験] ページ**から **[新規追加]** を選択し、**サンプル 1 - 回帰:Automobile Price Prediction(Basic)** (自動車価格の予測 (Basic)) を選択します。

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning service ワークスペースがある場合は、[次のセクション](#open-the-visual-interface-webpage)に進みます。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>ビジュアル インターフェイスの Web ページを開く

1. [Azure Portal](https://portal.azure.com/) でワークスペースを開きます。

1. ワークスペースで、 **[ビジュアル インターフェイス]** を選択します。 次に、 **[ビジュアル インターフェイスを起動する]** を選択します。 

    ![Machine Learning service ワークスペースからビジュアル インターフェイスにアクセスする方法を示す Azure portal のスクリーンショット](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>初めての実験を作成する

ビジュアル インターフェイス ツールでは、予測分析モデルを作成するための視覚的な対話型の操作に対応した場所が提供されます。 データセットと分析モジュールを対話型のキャンバスにドラッグ アンド ドロップし、それらを相互に接続して "*実験*" を作成することができます。

1. ビジュアル インターフェイス ウィンドウの下部にある **[+ 新規]** を選択して、新しい実験を作成します。

    ![新しい実験を追加する](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. **[Blank Experiment]\(空の実験\)** を選択します。

1. キャンバスの上部にある既定の実験名 "**Experimented Created on ...** " を選択し、わかりやすい名前に変更します。 たとえば、**Automobile price prediction** (自動車価格の予測) です。 名前は一意でなくてもかまいません。

## <a name="add-data"></a>データの追加

機械学習の実行にはまずデータが必要です。 このインターフェイスには、使用できるいくつかのサンプル データセットが含まれています。 既存のソースからデータをインポートすることもできます。 このチュートリアルでは、**Automobile price data (Raw)** というサンプル データセットを使用します。 

1. 実験キャンバスの左側には、データセットとモジュールのパレットがあります。 **[Saved Datasets]\(保存されたデータセット\)** を選択し、次に **[Samples]\(サンプル\)** を選択して、利用可能なサンプル データセットを表示します。

1. **Automobile price data (raw)** データセットを選択し、キャンバスにドラッグします。

   ![データをキャンバスにドラッグする](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>Select columns

使用するデータの列を選択します。 最初にモジュールを構成して、使用可能なすべての列を表示します。

> [!TIP]
> 必要なデータまたはモジュールの名前がわかっている場合は、パレットの上部にある検索バーを使用してすばやく検索します。 このチュートリアルの残りの部分では、このショートカットを使用します。


1. 検索ボックスに「**Select**」と入力して、**Select Columns in Dataset (データセットの列を選択する)** モジュールを見つけます。

1. **Select Columns in Dataset (データセットの列を選択する)** をクリックし、キャンバスにドラッグします。 先ほど追加したデータセットの下のモジュールを削除します。

1. データセットを **Select Columns in Dataset (データセットの列を選択する)** に接続する: データセットの出力ポートをクリックし、**Select Columns in Dataset (データセットの列を選択する)** の入力ポートにドラッグした後、マウス ボタンを離します。 これで、マウスをキャンバス上でどこに移動しても、データセットとモジュールが接続されたままになります。

    > [!TIP]
    > データセットとモジュールには、小さな円で表される入力ポートと出力ポートがあります (入力ポートは上部、出力ポートは下部)。 1 つのモジュールの出力ポートを別のモジュールの入力ポートに接続するときに、実験を通じてデータのフローを作成することになります。
    >

    ![モジュールの接続](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    赤色の感嘆符は、モジュールのプロパティがまだ設定されていないことを示します。

1. **Select Columns in Dataset (データセットの列を選択する)** モジュールを選択します。

1. キャンバスの右側にある **[プロパティ]** ウィンドウで、 **[列の編集]** を選択します。

    **[Select columns]\(列の選択\)** ダイアログで、 **[ALL COLUMNS]\(すべての列\)** を選択し、 **[all features]\(すべてのフィーチャー\)** を含めます。 ダイアログは次のようになります。

     ![列セレクター](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. 右下の **[OK]** を選択して列セレクターを閉じます。

## <a name="run-the-experiment"></a>実験を実行する

いつでもデータセットまたはモジュールの出力ポートをクリックすると、データ フローのその時点でデータがどのようになっているかを確認できます。 **[Visualize]\(可視化\)** オプションが無効になっている場合、最初に実験を実行する必要があります。

実験はコンピューティング先 (ワークスペースにアタッチされているコンピューティング リソース) で実行されます。 コンピューティング先を作成すると、将来の実行でそれを再利用できます。

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

コンピューティング ターゲットが使用できるようになった後、実験が実行されます。 実行が完了したら、各モジュールに緑色のチェック マークが表示されます。


## <a name="preview-the-data"></a>データをプレビューする

最初の実験を実行したので、次にデータを視覚化して、作業する必要があるデータセットについてさらに理解することができます。

1. **Select Columns in Dataset (データセットの列を選択する)** の下部にある出力ポートを選択し、 **[Visualize]\(可視化\)** を選択します。

1. データ ウィンドウで別の列をクリックして、その列についての情報を表示します。

    このデータセットでは、各行が自動車を表していて、各自動車に関連付けられている変数は列として表示されます。 このデータセット内には、205 の行と 26 の列があります。

     データの列をクリックするたびに、その列の **[Statistics]\(統計\)** 情報と **[Visualization]\(視覚化\)** の画像が左側に表示されます。 たとえば、**num-of-doors** をクリックすると、一意の値が 2 つ、不足している値が 2 つあることがわかります。 下にスクロールして 2 ドアと 4 ドアの値を表示します。

     ![データをプレビューする](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. 各列をクリックしてデータセットの詳細を把握し、自動車の価格を予測する際にこれらの列が役立つかどうかを考えます。

## <a name="prepare-data"></a>データを準備する

通常、データセットには、分析前にある程度の前処理が必要です。 データセットを視覚化するときに、いくつか不足値があることに気付いたかもしれません。 モデルがデータを正しく分析するには、これらの不足値を整理する必要があります。 値が不足している行をすべて削除します。 また、不足している値の大部分は、**normalized-losses** 列にあります。したがって、モデルからこの列を完全に除外します。

> [!TIP]
> 不足している値を入力データから整理することが、ほとんどのモジュールを使用するための前提条件となっています。

### <a name="remove-column"></a>列を削除する

最初に、**normalized-losses** 列を完全に削除します。

1. **Select Columns in Dataset (データセットの列を選択する)** モジュールを選択します。

1. キャンバスの右側にある **[プロパティ]** ウィンドウで、 **[列の編集]** を選択します。

    * **[With rules]\(規則を使用\)** と **[ALL COLUMNS]\(すべての列\)** は選択したままにします。

    * ドロップダウンから **[Exclude]\(除外\)** と **[column names]\(列名\)** を選択し、テキスト ボックスの内側をクリックします。 「**normalized-losses**」と入力します。

    * 右下の **[OK]** を選択して列セレクターを閉じます。

    ![列を除外する](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    これで、Select Columns in Dataset (データセットの列を選択する) のプロパティ ウィンドウに、**normalized-losses** 以外のデータセットのすべての列がフィルターを通過することが示されます。
        
    "**normalized-losses**" 列が除外されたことを示すプロパティ ウィンドウ。
        
    ![プロパティ ウィンドウ](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    モジュールをダブルクリックして、テキストを入力すると、モジュールにコメントを追加できます。 これで、実験でモジュールがどのような処理をするのかがひとめでわかります。 

1. **Select Columns in Dataset (データセットの列を選択する)** モジュールをダブルクリックして、「Exclude normalized losses」(normalized losses を除外する) というコメントを入力します。 
    
    コメントを入力した後、モジュールの外側をクリックします。 モジュールにコメントが含まれていることを示す下向き矢印が表示されます。

1. コメントを表示するには、下向き矢印をクリックします。

    今度は、コメントを非表示にする上向きの矢印がモジュールに表示されます。
        
    ![説明](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>見つからないデータのクリーンアップ

モデルをトレーニングする際は、見つからないデータに対処する必要があります。 この場合、見つからないデータを含む残りの行を削除するためのモジュールを追加します。

1. 検索ボックスに「**Clean**」と入力して、**Clean Missing Data (不足データのクリーンアップ)** モジュールを見つけます。

1. **Clean Missing Data (不足データのクリーンアップ)** モジュールを実験キャンバスにドラッグして、**Select Columns in Dataset (データセットの列を選択する)** モジュールに接続します。 

1. [Properties]\(プロパティ\) ウィンドウで、 **[Cleaning mode]\(整理モード\)** の **[Remove entire row]\(行全体を削除\)** を選択します。

    これらのオプションにより、**Clean Missing Data (不足データのクリーンアップ)** では値が不足している行が削除され、データが整理されます。

1. モジュールをダブルクリックして、「Remove missing value rows」(値が不足している行を削除する) というコメントを入力します。
 
    ![行を削除する](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    実験は以下のようになっているはずです。
    
    ![select-column](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>結果を視覚化する

実験でモジュールに変更を加えたため、モジュール状態が "ドラフト" に変更されました。  新しい整理されたデータを視覚化するには、最初に実験をもう一度実行する必要があります。

1. 下部の **[Run]\(実行\)** を選択して実験を実行します。

    今回は、先ほど作成したコンピューティング ターゲットを再利用できます。

1. ダイアログ内の **[Run]\(実行\)** を選択します。

   ![実験を実行する](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. 実行が完了したら、**Clean Missing Data (不足データのクリーンアップ)** モジュールを右クリックし、新しい整理されたデータを視覚化します。

    ![整理されたデータを視覚化する](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. 整理されたデータ ウィンドウで別の列をクリックし、データがどのように変更されたかを確認します。

    ![整理されたデータを視覚化する](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    193 の行と 25 の列があります。

    **num-of-doors** をクリックすると、一意の値はまだ 2 つありますが、不足している値がは 0 であることがわかります。 残りの列をクリックスルーし、データセットに欠損値がないことを確認します。 

## <a name="train-the-model"></a>モデルをトレーニングする

これでデータを用意できたので、予測モデルを構築できます。 データをモデルのトレーニングに使用します。 その後、モデルをテストして、価格をどの程度の精度で予測できるかを確認します。

"**分類**" と "**回帰**" は、2 種類の教師あり機械学習アルゴリズムです。 **分類**は、色 (赤、青、または緑) のような定義された一連のカテゴリから答えを予測するものです。 **回帰**は、数値を予測する目的で使用されます。

予測したい価格は数値であるため、回帰アルゴリズムを使用できます。 この例では、線形回帰モデルを使用します。

価格が含まれた一連のデータを指定して、モデルをトレーニングします。 モデルによってデータがスキャンされ、自動車のフィーチャーと価格の相関関係が検出されます。 次に、よく知られている自動車のフィーチャーのセットをモデルに与えることで、モデルのテストを行い、モデルがその既知の価格をどの程度の精度で予測できるかを確認します。

データを別個のトレーニング データセットとテスト データセットに分割して、モデルのトレーニングとテストの両方に使用します。

1. 検索ボックスに「**split data**」と入力して、**Split Data** (データの分割) モジュールを見つけ、**Clean Missing Data** (見つからないデータのクリーンアップ) モジュールの左側のポートに接続します。

1. 接続した **Split Data** (データの分割) モジュールを選択します。 [Properties]\(プロパティ\) ウィンドウで、[Fraction of rows in the first output dataset]\(最初の出力データセットにおける列の割合\) を 0.7 に設定します。 このようにして、データの 70% をモデルのトレーニングに使用し、30% をテスト用に保持しておきます。

    ![[Properties]\(プロパティ\) ウィンドウの正しい構成を示すスクリーンショット "Split Data" (データの分割) の各値は、"Split Rows" (行の分割)、0.7、Randomized split (ランダム分割)、0、False である必要があります。](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. **Split Data** (データの分割) をダブルクリックし、「Split the dataset into training set(0.7) and test set(0.3)」(データセットをトレーニング セット (0.7) とテスト セット (0.3) に分割する) というコメントを入力します。

1. 学習アルゴリズムを選択するには、モジュール パレットの検索ボックスをオフにします。

1. **[Machine Learning]** を展開し、 **[Initialize Model]\(モデルの初期化\)** を展開します。 これにより、機械学習アルゴリズムの初期化に使用できるモジュールのカテゴリが複数表示されます。

1. この実験では、 **[Regression]\(回帰\)**  > **Linear Regression** (線形回帰) モジュールを選択し、実験キャンバスにドラッグします。

    ![[Properties]\(プロパティ\) ウィンドウの正しい構成を示すスクリーンショット "Split Data" (データの分割) の各値は、"Split Rows" (行の分割)、0.7、Randomized split (ランダム分割)、0、False である必要があります。](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. **Train Model** (モデルのトレーニング) モジュールを見つけて、実験にドラッグします。 Linear Regression (線形回帰) モジュールの出力を、Train Model (モデルのトレーニング) モジュールの左側の入力に接続します。次に、**Split Data** (データの分割) モジュールのトレーニング データ出力 (左側のポート) を、**Train Model** (モデルのトレーニング) モジュールの右側の入力に接続します。

    ![Train Model (モデルのトレーニング) モジュールの正しい構成を示すスクリーンショット。 Linear Regression (線形回帰) モジュールが、Train Model (モデルのトレーニング) モジュールの左側のポートに接続され、Split Data (データの分割) モジュールが、Train Model (モデルのトレーニング) モジュールの右側のポートに接続されています。](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. **Train Model** (モデルのトレーニング) モジュールを選択します。 [Properties]\(プロパティ\) ウィンドウで [Launch column selector]\(列セレクターの起動\) を選択し、 **[Include]\(含める\)、[column names]\(列名\)** の横にあるボックスに「**price**」と入力します。 price (価格) は、モデルで予測する値です。

    ![列セレクター モジュールの正しい構成を示すスクリーンショット。 [With rules]\(規則を使用\) > [Include]\(含める\)、[column names]\(列名\) > "price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    実験は以下のようになります。

    ![Train Model (モデルのトレーニング) モジュールを追加した後の実験の正しい構成を示すスクリーンショット。](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>トレーニング実験を実行する

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>モデルにスコアを付け、評価する

これまでにデータの 70% を使用してモデルをトレーニングしました。ここからは残りの 30% のデータにスコアを付け、モデルの精度を確認します。

1. 検索ボックスに「**score model**」と入力して、**Score Model** (モデルのスコア付け) モジュールを見つけ、このモジュールを実験キャンバスにドラッグします。 **Train Model** (モデルのトレーニング) モジュールの出力を、**Score Model** (モデルのスコア付け) の左側の入力ポートに接続します。 **Split Data** (データの分割) モジュールのテスト データの出力 (右側のポート) を、**Score Model** (モデルのスコア付け) の右側の入力ポートに接続します。

1. 検索ボックスに「**evaluate**」と入力して、**Evaluate Model** (モデルの評価) を見つけ、このモジュールを実験キャンバスにドラッグします。 **Score Model** (モデルのスコア付け) モジュールの出力を、**Evaluate Model** (モデルの評価) の左側の入力に接続します。 最終的な実験は次のようになります。

    ![実験の最終的な正しい構成を示すスクリーンショット。](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. 以前に使用したのと同じコンピューティング ターゲットを使用して、実験を実行します。

1. **Score Model** (モデルのスコア付け) の出力ポートをクリックし、 **[Visualize]\(視覚化\)** を選択すると、**Score Model** (モデルのスコア付け) モジュールの出力が表示されます。 出力に、予測された価格の値と、テスト データからの既知の値が表示されます。

    !["Scored Label" (スコア付けラベル) 列が強調表示された状態の出力の視覚化のスクリーンショット](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Evaluate Model (モデルの評価) モジュールの出力を表示するには、出力ポートを選択し、[Visualize]\(視覚化\) を選択します。

    ![最終的な実験の評価結果を示すスクリーンショット。](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

作成したモデルに対して、以下の統計値が表示されます。

* **Mean Absolute Error** (MAE、平均絶対誤差):絶対誤差の平均です (誤差とは、予測された値と実際の値との差です)。
* **Root Mean Squared Error** (RMSE、二乗平均平方根誤差):テスト データセットに対して実行した予測の二乗誤差平均の平方根です。
* **Relative Absolute Error** (相対絶対誤差):実際の値とすべての実際の値の平均との絶対差を基準にした絶対誤差の平均です。
* **Relative Squared Error** (相対二乗誤差):実際の値とすべての実際の値の平均との二乗差を基準にした二乗誤差の平均です。
* **Coefficient of Determination** (決定係数):R-2 乗値ともいいます。どの程度モデルが高い精度でデータと適合するかを示す統計指標です。

この誤差の統計情報は、それぞれ小さいほど良いとされます。 値が小さいほど、予測が実際の値により近いことを示します。 Coefficient of Determination (決定係数) では、値が 1 (1.0) に近づくほど、予測の精度が高くなります。

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Azure Machine Learning service ワークスペースで実験を管理する

ビジュアル インターフェイスで作成した実験は、Azure Machine Learning service ワークスペースから管理できます。 ワークスペースを使用して、個別の実験の実行、診断ログ、実行グラフなど、より詳細な情報を参照してください。

1. [Azure Portal](https://portal.azure.com/) でワークスペースを開きます。  

1. ワークスペースで、 **[実験]** を選択します。 次に、作成した実験を選択します。

    ![Azure portal で実験に移動する方法を示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    このページに、実験とその最新の実行の概要が表示されます。

    ![Azure portal での実験の統計情報の概要を示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. 特定の実行に関する詳細情報を表示するには、実行番号を選択します。

    ![詳細な実行レポートを示すスクリーンショット](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    実行レポートはリアルタイムで更新されます。 実験で **Execute Python Script** (Python スクリプトの実行) モジュールを使用した場合は、 **[ログ]** タブで出力にスクリプト ログを指定できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルのパート 1 では、次の手順を完了しました。

* 実験を作成する
* データを準備する
* モデルをトレーニングする
* モデルにスコアを付け、評価する

パート 2 では、モデルを Azure Web サービスとしてデプロイする方法を学習します。

> [!div class="nextstepaction"]
> [モデルのデプロイに進む](ui-tutorial-automobile-price-deploy.md)
