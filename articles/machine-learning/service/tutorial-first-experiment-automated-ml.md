---
title: 初めての自動機械学習の実験を作成する
titleSuffix: Azure Machine Learning service
description: Azure portal で自動機械学習を使用して分類モデルをトレーニングおよびデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 08/14/2019
ms.openlocfilehash: e53cd92a9dfd8f823918fb38e14c2b73c2ce071f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534394"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>チュートリアル:自動化された機械学習を使用して最初の分類モデルを作成する

このチュートリアルでは、コード行を 1 つも記述せず、Azure portal (プレビュー) で初めての自動機械学習実験を作成する方法について説明します。 この例では、クライアントが金融機関に定期預金を申し込むかどうかを予測する分類モデルを作成します。

Azure Machine Learning service の自動機械学習機能と Azure portal を使用して、自動機械学習プロセスを開始します。 アルゴリズムの選択とハイパーパラメーターのチューニングが自動的に行われます。 自動化された機械学習の手法では、アルゴリズムとハイパーパラメーターの組み合わせを多数イテレーションして、お客様の条件に基づき最高のモデルを見つけます。

このチュートリアルでは、以下のタスクについて学習します。

> [!div class="checklist"]
> * Azure Machine Learning service ワークスペースを構成する。
> * 実験を作成します。
> * 分類モデルを自動トレーニングする。
> * トレーニングの実行の詳細を表示する。
> * モデルをデプロイします。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://aka.ms/AMLFree)を作成してください。

* [**bankmarketing_train.csv** ](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) データ ファイルをダウンロードします。 **[y]** 列では、このチュートリアルの予測対象列として後で識別される定期預金に顧客が申し込んだかどうかが示されます。 

## <a name="create-a-workspace"></a>ワークスペースの作成

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>実験の作成

次の手順では、データの選択から主要なメトリックとモデルの種類の選択まで、実験の設定を段階的に進めます。 

1. ワークスペースの左側のウィンドウに移動します。 **[作成 (プレビュー)]** セクションで、 **[自動 Machine Learning]** を選択します。
これは、自動機械学習を使用する最初の実験であるため、 **[Welcome to Automated Machine Learning]\(自動 Machine Learning へようこそ\)** の画面が表示されます。

    ![Azure portal ナビゲーション ウィンドウ](media/tutorial-1st-experiment-automated-ml/nav-pane.png)



1. **[Create experiment]\(実験の作成\)** を選択します。 次に、実験名として「**my-1st-automl-experiment**」と入力します。

1. **[Create a new compute]\(新しいコンピューティングの作成\)** を選択し、この実験のコンピューティング コンテキストを構成します。

    フィールド| 値
    ---|---
    コンピューティング名| コンピューティング コンテキストを識別する一意名を入力します。 この例では、**automl-compute** を使用します。
    仮想マシンのサイズ| コンピューティングの仮想マシン サイズを選択します。 **Standard_DS12_V2** を使用します。
    追加設定| *Min node (最小ノード)* : 1. データ プロファイルを有効にするには、1 つ以上のノードが必要です。 <br> *Max node (最大ノード)* : 6. 

    新しいコンピューティングを作成するには、 **[作成]** をクリックします。 これにはしばらくかかります。 

    作成が完了したら、ドロップダウン リストから新しいコンピューティングを選択し、 **[次へ]** を選択します。

    >[!NOTE]
    >このチュートリアルでは、新しいコンピューティングで作成された既定のストレージ アカウントとコンテナーを使用します。 フォームに自動的に入力されます。

1. **[アップロード]** を選択し、ローカル コンピューターから **bankmarketing_train.csv** ファイルを選択して、既定のコンテナーにアップロードします。 パブリック プレビューでは、ローカル ファイルのアップロードと Azure Blob Storage アカウントのみがサポートされています。 アップロードが完了したら、一覧からファイルを選択します。 

    [![データ ファイルの選択](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. **[プレビュー]** タブでは、この実験用にデータをさらに構成することができます。

    **[プレビュー]** タブに、データにヘッダーが含まれていることが示されます。 サービスには既定でトレーニング用のすべての機能 (列) が含まれます。 この例では、右にスクロールして、**day_of_week** 機能を **無視**にします。

    ![[プレビュー] タブの構成](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > 最小ノード数が 0 の計算では、データ プロファイルは使用できません。

1. 予測タスクとして **[分類]** を選択します。

1. 予測を実行するターゲット列として、 **[y]** を選択します。 この列には、クライアントが定期預金を申し込むかどうかが示されます。

1. **[詳細設定]** を展開し、次のようにフィールドを設定します。

    詳細設定|値
    ------|------
    主要メトリック| AUC_weighted 
    終了基準| これらの基準のどれかが満たされると、トレーニング ジョブは完全に完了する前に終了します。 <br> *Training job time (minutes) (トレーニング ジョブ時間 (分))* : 5  <br> *イテレーションの最大数*: 10 
    前処理| 自動機械学習による前処理を有効にします。 これには、合成的特徴を生成するための自動データ クレンジング、準備、変換が含まれます。
    検証| K フォールド クロス検証を選択し、クロス検証の数に **2** を選択します。 
    コンカレンシー| 同時実行反復処理の最大数に **5** を選択します。

   >[!NOTE]
   > この実験では、メトリックまたはイテレーションあたりの最大コア数のしきい値は設定しません。 また、アルゴリズムのテストをブロックしません。

1. **[開始]** を選択して、実験を実行します。

   実験が開始されると、空の **[実行の詳細]** 画面が表示され、上部に次のステータスが示されます。 

      ![実行の準備中](media/tutorial-1st-experiment-automated-ml/run-preparing.png)
      
実験の準備プロセスは数分かかります。 プロセスが完了すると、ステータス メッセージが **[Run is Running]\(実行が実行中\)** に変わります。

##  <a name="view-experiment-details"></a>実験の詳細の表示

実験が進むにつれて、 **[実行の詳細]** 画面では、実行されたさまざまなイテレーション (モデル) でイテレーション グラフとリストが更新されます。 イテレーション リストは、メトリック スコア順に並べられています。 既定では、**AUC_weighted** メトリックに基づいて最も高いスコアを獲得したモデルがリストの一番上に表示されます。

>[!TIP]
> トレーニング ジョブで各パイプラインの実行を完了するには、数分かかります。

[![詳細ダッシュボードの実行](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>モデルをデプロイする

Azure portal で自動機械学習を使用すると、最良のモデルを Web サービスとしてデプロイし、新しいデータを予測したり、潜在的な機会領域を特定したりできます。 この実験では、デプロイは、定期預金の潜在顧客を特定するためのスケーラブルな反復ソリューションを金融機関が持つことを意味します。

この実験では、**VotingEnsemble** は **AUC_weighted** メトリックに基づいて最適なモデルと見なされます。  このモデルをデプロイしますが、デプロイには完了まで約 20 分かかることにご留意ください。

1. **[実行の詳細]** ページで、 **[Deploy Best Model]\(最適なモデルのデプロイ\)** ボタンを選択します。

1. **[Deploy Best Model]\(最適なモデルのデプロイ\)** ウィンドウに次のように入力します。

    フィールド| 値
    ----|----
    デプロイ名| my-automl-deploy
    デプロイの説明| 初めての自動機械学習実験のデプロイ
    スコアリング スクリプト| 自動生成
    環境スクリプト| 自動生成
    
1. **[デプロイ]** を選択します。

    デプロイが正常に完了すると、次のメッセージが表示されます。

    ![デプロイ完了](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    これで、予測を生成するための実稼働 Web サービスが作成されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

デプロイ ファイルはデータ ファイルと実験ファイルよりも大きいため、格納コストは高くなります。 アカウントのコストを最小限に抑える場合、またはワークスペースと実験ファイルを保持する場合は、デプロイ ファイルだけを削除します。 それ以外の場合で、いずれのファイルも使用する予定がない場合は、リソース グループ全体を削除します。  

### <a name="delete-the-deployment-instance"></a>デプロイ インスタンスの削除

他のチュートリアルや探索用にリソース グループとワークスペースを維持する場合は、Azure portal からデプロイ インスタンスだけを削除します。 

1. 左側の **[アセット]** ウィンドウに移動し、 **[デプロイ]** を選択します。 

1. 削除するデプロイを選択し、 **[削除]** を選択します。 

1. **[続行]** を選択します。

### <a name="delete-the-resource-group"></a>リソース グループを削除します

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

この自動機械学習チュートリアルでは、Azure portal を使用して分類モデルの作成とデプロイを行いました。 詳細と次の手順については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Web サービスを使用する](how-to-consume-web-service.md)


+ [前処理](how-to-create-portal-experiments.md#preprocess)についてさらに理解を深める。
+ [データ プロファイル](how-to-create-portal-experiments.md#profile)についてさらに理解を深める。
+ [自動機械学習](concept-automated-ml.md)についてさらに理解を深める。

>[!NOTE]
> この Bank Marketing データセットは、[クリエイティブ コモンズ (CCO:パブリック ドメイン) ライセンス](https://creativecommons.org/publicdomain/zero/1.0/)により利用できます。 データベースの個々のコンテンツに含まれる権限は、[データベース コンテンツ ライセンス](https://creativecommons.org/publicdomain/zero/1.0/)によりライセンス供与され、[Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) で入手できます。 このデータセットのオリジナルは、[UCI Machine Learning データベース](https://archive.ics.uci.edu/ml/datasets/bank+marketing)から入手できます。<br><br>
> 出典: <br> [Moro その他、2014 年] S. Moro、P. Cortez、Rita。 銀行のテレマーケティングの成功を予測するためのデータドリブン アプローチ。 意思決定支援システム、Elsevier、62:22-31、2014 年 6 月。
