---
title: チュートリアル:需要予測と AutoML
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio で自動機械学習を使用して需要予測モデルをトレーニングおよびデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 07/10/2020
ms.openlocfilehash: a244372168cb34f190bd584634bf108f2b5215a5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092290"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>チュートリアル:自動機械学習を使用して需要を予測する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

このチュートリアルでは、Azure Machine Learning Studio で自動機械学習 (自動 ML) を使用して、自転車シェアリング サービスのレンタル需要を予測するための時系列予測モデルを作成します。

>[!IMPORTANT]
> Azure Machine learning studio での自動 ML エクスペリエンスは、プレビュー段階にあります。 特定の機能がサポートされないことや、機能が制限されることがあります。

分類モデルの例については、「[チュートリアル: Azure Machine Learning の自動 ML で分類モデルを作成する](tutorial-first-experiment-automated-ml.md)」を参照してください。

このチュートリアルでは、次のタスクを実施する方法について説明します。

> [!div class="checklist"]
> * データセットを作成して読み込む。
> * 自動 ML 実験を構成して実行する。
> * 予測設定を指定する。
> * 実験結果を調べる。
> * 最適なモデルをデプロイする。

## <a name="prerequisites"></a>前提条件

* Enterprise Edition の Azure Machine Learning ワークスペース。 ワークスペースがない場合は、[Enterprise Edition のワークスペースを作成します](how-to-manage-workspace.md)。 
    * Azure Machine Learning Studio での自動機械学習は、Enterprise Edition のワークスペースでのみ使用できます。 
* [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) のデータ ファイルをダウンロードします

## <a name="get-started-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio で開始する

このチュートリアルでは、Azure Machine Learning Studio で自動 ML 実験の実行を作成します。Azure Machine Learning Studio は、あらゆるスキル レベルのデータ サイエンス実務者がデータ サイエンス シナリオを実行するための機械学習ツールを含む統合 Web インターフェイスです。 Internet Explorer ブラウザーでは、Studio はサポートされません。

1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。

1. お使いのサブスクリプションと、作成したワークスペースを選択します。

1. **[Get started]\(作業を開始する\)** を選択します。

1. 左側のウィンドウの **[Author]\(作成者\)** セクションで **[Automated ML]\(自動 ML\)** を選択します。

1. **[+New automated ML run]\(+新しい自動 ML の実行\)** を選択します。 

## <a name="create-and-load-dataset"></a>データセットを作成して読み込む

実験を構成する前に、Azure Machine Learning データセットの形式でデータ ファイルをワークスペースにアップロードします。 そうすることで、データを実験に適した形式にすることができます。

1. **[データセットの選択]** フォームで、 **[+データセットの作成]** ドロップダウンから **[From local files]\(ローカル ファイルから\)** を選択します。 

    1. **[基本情報]** フォームでデータセットに名前を付け、必要に応じて説明を入力します。 現在、Azure Machine Learning Studio の自動 ML でサポートされるデータセットは表形式のみであるため、データセットの種類は既定で**表形式**に設定されます。
    
    1. 左下の **[次へ]** を選択します

    1. **[データストアとファイルの選択]** フォームで、ワークスペースの作成時に自動的に設定された既定のデータストア、**workspaceblobstore (Azure Blob Storage)** を選択します。 これは、データ ファイルをアップロードするストレージの場所です。 

    1. **[参照]** を選択します。 
    
    1. ローカル コンピューター上の **bike-no.csv** ファイルを選択します。 これは、[前提条件](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)としてダウンロードしたファイルです。

    1. **[次へ]** を選択します

       アップロードが完了すると、ファイルの種類に基づいて [Settings and preview]\(設定とプレビュー\) フォームが事前設定されます。 
       
    1. **[Settings and preview]\(設定とプレビュー\)** フォームが次のように設定されていることを確認し、 **[次へ]** を選択します。
        
        フィールド|説明| チュートリアルの値
        ---|---|---
        ファイル形式|ファイルに格納されているデータのレイアウトと種類を定義します。| 区切り記号
        区切り記号|プレーンテキストまたは他のデータ ストリーム内の個別の独立した領域の間の境界を指定するための&nbsp;1 つ以上の文字。 |コンマ
        エンコード|データセットの読み取りに使用する、ビットと文字のスキーマ テーブルを識別します。| UTF-8
        列見出し| データセットの見出しがある場合、それがどのように処理されるかを示します。| 最初のファイルのヘッダーを使用する
        行のスキップ | データセット内でスキップされる行がある場合、その行数を示します。| なし

    1. **[スキーマ]** フォームを使用すると、この実験用にデータをさらに構成できます。 
    
        1. この例では、**casual** と **registered** 列を無視するように選択します。 これらの列は **cnt** 列の内訳であるため、それらを含めません。

        1. また、この例では、**Properties** と **Type** の既定値をそのまま使用します。 
        
        1. **[次へ]** を選択します。

    1. **[詳細の確認]** フォームで、 **[基本情報]** および **[Settings and preview]\(設定とプレビュー\)** のフォームに入力された情報が一致していることを確認します。

    1. **[作成]** を選択して、データセットの作成を完了します。

    1. リストにデータセットが表示されたら、それを選択します。

    1. **[次へ]** を選択します。

## <a name="configure-experiment-run"></a>実験の実行を構成する

データを読み込んで構成したら、リモート コンピューティング先を設定し、予測するデータの列を選択します。

1. 次のように **[Configure run]\(実行の構成\)** フォームに入力します。
    1. 実験の名前として「`automl-bikeshare`」と入力します。

    1. 予測するターゲット列として、 **[cnt]** を選択します。 この列は、自転車シェアリング レンタルの合計数を示します。

    1. **[Create a new compute]\(新しいコンピューティングの作成\)** を選択し、コンピューティング先を構成します。 自動 ML では、Azure Machine Learning コンピューティングのみがサポートされます。 

        フィールド | 説明 | チュートリアルの値
        ----|---|---
        コンピューティング名 |コンピューティング コンテキストを識別する一意名。|bike-compute
        仮想マシンの種類&nbsp;&nbsp;|コンピューティング用の仮想マシンの種類を選択します。|CPU (中央処理装置)
        仮想マシンのサイズ&nbsp;&nbsp;| コンピューティングの仮想マシン サイズを選択します。|Standard_DS12_V2
        最小および最大ノード数| データをプロファイリングするには、1 つ以上のノードを指定する必要があります。|最小ノード: 1<br>最大ノード: 6
        スケールダウンする前のアイドル時間 (秒) | クラスターが自動的に最小ノード数にスケールダウンされるまでのアイドル時間。|120 (既定値)
        詳細設定 | 実験用の仮想ネットワークを構成および承認するための設定。| なし
  
        1. **[作成]** を選択して、コンピューティング先を取得します。 

            **完了するまでに数分かかります。** 

        1. 作成後、ドロップダウン リストから新しいコンピューティング先を選択します。

    1. **[次へ]** を選択します。

## <a name="select-forecast-settings"></a>予測設定を選択する

機械学習のタスクの種類と構成設定を指定して、自動 ML 実験の設定を完了します。

1. **[Task type and settings]\(タスクの種類と設定\)** フォームで、機械学習のタスクの種類として **[時系列の予測]** を選択します。

1. **[時刻列]** として **[date]** を選択し、 **[時系列識別子]** を空白のままにします。 

1. **予測期間**とは、予測対象となる将来の時間の長さです。  [自動検出] の選択を解除し、フィールドに「14」と入力します。 

1. **[View additional configuration settings]\(追加の構成設定を表示\)** を選択し、次のようにフィールドを設定します。 これらは、トレーニング ジョブをより細かく制御し、予測の設定を指定するための設定です。 設定しない場合、実験の選択とデータに基づいて既定値が適用されます。

    追加の構成&nbsp;|説明|チュートリアル用の値&nbsp;&nbsp;
    ------|---------|---
    主要メトリック| 機械学習アルゴリズムを測定される評価メトリック。|正規化された平均平方二乗誤差
    最適なモデルの説明| 自動 ML で作成された最適なモデルの説明を自動的に表示します。| 有効化
    ブロックされたアルゴリズム | トレーニング ジョブから除外するアルゴリズム| 極端なランダム ツリー
    その他の予測設定| これらの設定は、モデルの精度を向上させるのに役立ちます <br><br> _**[Forecast target lags]\(予測ターゲットのラグ\)**_: ターゲット変数のラグをどの程度さかのぼって作成するかを指定します <br> _**ターゲットのローリング ウィンドウ**_: *max、min*、*sum* などの特徴が生成されるローリング ウィンドウのサイズを指定します。 | <br><br>予測&nbsp;ターゲットの&nbsp;ラグ:なし <br> ターゲットの&nbsp;ローリング&nbsp;ウィンドウ&nbsp;サイズ:なし
    終了条件| 条件が満たされると、トレーニング ジョブが停止します。 |トレーニング&nbsp;ジョブ時間 (時間):&nbsp;3 <br> メトリック&nbsp;スコアしきい値:&nbsp;なし
    検証 | クロス検証タイプとテストの回数を選択します。|検証タイプ:<br>&nbsp;k 分割交差検証&nbsp; <br> <br> 検証の数: 5
    コンカレンシー| イテレーションごとに実行される並列イテレーションの最大数| &nbsp;最大同時イテレーション数:&nbsp;6
    
    **[保存]** を選択します。

## <a name="run-experiment"></a>実験を実行する

実験を実行するには、 **[終了]** を選択します。 **[実行の詳細]** 画面が開き、上部の実行番号の横に **[実行の状態]** が表示されます。 この状態は、実験の進行に応じて更新されます。

>[!IMPORTANT]
> 実験の実行の準備に、**10 から 15 分**かかります。
> 実行の開始後、**各イテレーションのためにさらに 2、3 分**かかります。  <br> <br>
> 実稼働環境では、このプロセスには時間がかかるため、しばらくお待ちください。 待機している間に、アルゴリズムのテストが終わり次第、 **[モデル]** タブで調査を開始することをお勧めします。 

##  <a name="explore-models"></a>モデルを調査する

**[モデル]** タブに移動し、テストされたアルゴリズム (モデル) を確認します。 既定では、モデルは完了時のメトリック スコアで並べ替えられます。 このチュートリアルでは、選択した**正規化された平均平方二乗誤差**メトリックに基づいて最も高いスコアを獲得したモデルがリストの一番上に表示されます。

すべての実験モデルが終了するのを待っている間に、完了したモデルの **[アルゴリズム名]** を選択して、そのパフォーマンスの詳細を調査します。 

次の例では、 **[詳細]** タブと **[メトリック]** タブを移動して、選択したモデルのプロパティ、メトリック、およびパフォーマンス グラフを表示しています。 

![実行の詳細](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>モデルをデプロイする

Azure Machine Learning Studio で自動機械学習を使用すると、わずかなステップで最良のモデルを Web サービスとしてデプロイすることができます。 デプロイとは、新しいデータを予測したり、潜在的な機会領域を特定したりできるようにモデルを統合することです。 

この実験では、Web サービスへのデプロイは、自転車シェアリングのレンタル需要を予測するための反復的でスケーラブルな Web ソリューションを自転車シェアリング会社が持つことを意味します。 

実行が完了したら、画面の上部にある **[Run 1]\(実行 1\)** を選択して、親の実行ページに戻ります。

**正規化された平均平方二乗誤差**メトリックに基づいて、 **[Best model summary]\(最適なモデルの概要\)** セクションの **StackEnsemble** が、この実験のコンテキストで最適なモデルと見なされます。  

このモデルをデプロイしますが、デプロイには完了まで約 20 分かかることにご留意ください。 デプロイ プロセスには、モデルを登録したり、リソースを生成したり、Web サービス用にそれらを構成したりすることを含む、いくつかの手順が伴います。

1. **[StackEnsemble]** を選択して、モデル固有のページを開きます。

1. 画面の左上の領域にある **[デプロイ]** ボタンを選択します。

1. **[Deploy a model]\(モデルのデプロイ\)** ペインに次のように入力します。

    フィールド| 値
    ----|----
    デプロイ名| bikeshare-deploy
    デプロイの説明| 自転車シェアリング需要のデプロイ
    コンピューティングの種類 | Azure のコンピューティング インスタンス (ACI) を選択します。
    認証を有効にする| 無効。 
    カスタム デプロイ アセットを使用する| 無効。 無効化によって、既定のドライバー ファイル (スコアリング スクリプト) と環境ファイルが自動的に生成されます。 
    
    この例では、 *[Advanced]\(詳細\)* メニューに指定されている既定値を使用します。 

1. **[デプロイ]** を選択します。  

    デプロイが正常に開始されたことを示す緑色の成功メッセージが **[実行]** 画面の上部に表示されます。 デプロイの進行状況は、 **[モデルの概要]** ペインの **[Deploy status]\(デプロイの状態\)** で確認できます。
    
デプロイに成功すると、予測を生成するための実稼働 Web サービスが作成されます。 

新しい Web サービスの使い方、Azure Machine Learning サポートに組み込まれている Power BI を使用した予測のテスト方法の詳細については、[**次のステップ**](#next-steps)に進みます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

デプロイ ファイルはデータ ファイルと実験ファイルよりも大きいため、格納コストは高くなります。 アカウントのコストを最小限に抑える場合、またはワークスペースと実験ファイルを保持する場合は、デプロイ ファイルだけを削除します。 それ以外の場合で、いずれのファイルも使用する予定がない場合は、リソース グループ全体を削除します。  

### <a name="delete-the-deployment-instance"></a>デプロイ インスタンスの削除

他のチュートリアルや探索用にリソース グループとワークスペースを維持する場合は、Azure Machine Learning Studio からデプロイ インスタンスだけを削除します。 

1. [Azure Machine Learning Studio](https://ml.azure.com/) に移動します。 お使いのワークスペースに移動し、左側の **[アセット]** ウィンドウの下の **[エンドポイント]** を選択します。 

1. 削除するデプロイを選択し、 **[削除]** を選択します。 

1. **[続行]** を選択します。

### <a name="delete-the-resource-group"></a>リソース グループを削除します

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Machine Learning Studio で自動 ML を使用して、自転車シェアリングのレンタル需要を予測する時系列予測モデルを作成してデプロイしました。 

新しくデプロイされた Web サービスの使用を容易にするために、Power BI でサポートされているスキーマを作成する手順については、こちらの記事を参照してください。

> [!div class="nextstepaction"]
> [Web サービスを使用する](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ [自動機械学習](concept-automated-ml.md)についてさらに理解を深める。
+ 分類メトリックとグラフの詳細については、「[自動化機械学習の結果の概要](how-to-understand-automated-ml.md#classification)」の記事を参照してください。
+ [特徴付け](how-to-configure-auto-features.md#featurization)についてさらに理解を深める。
+ [データ プロファイル](how-to-use-automated-ml-for-ml-models.md#profile)についてさらに理解を深める。

>[!NOTE]
> この自転車シェアリング データセットは、このチュートリアル用に変更されています。 このデータセットは、[Kaggle コンテスト](https://www.kaggle.com/c/bike-sharing-demand/data)の一部として提供されており、もともとは [Capital Bikeshare](https://www.capitalbikeshare.com/system-data) を通じて入手可能でした。 また、[UCI Machine Learning データベース](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)にもあります。<br><br>
> ソース:Fanaee-T, Hadi, and Gama, Joao、「Event labeling combining ensemble detectors and background knowledge」、Progress in Artificial Intelligence (2013): pp. 1-15、Springer Berlin Heidelberg。
