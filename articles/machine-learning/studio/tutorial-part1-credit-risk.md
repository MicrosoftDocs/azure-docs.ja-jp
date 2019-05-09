---
title: チュートリアル 1:信用リスクの予測
titleSuffix: Azure Machine Learning Studio
description: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning Studio で作成する方法を詳しく紹介したチュートリアルです。 このチュートリアルは、3 部構成のチュートリアル シリーズの第 1 部です。  ワークスペースの作成方法、データのアップロード方法、実験の作成方法について説明しています。
keywords: 信用リスク, 予測分析ソリューション,リスク評価
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: f69b3f2c8de4cf137583ad7a33e8edfe31373096
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904509"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio"></a>チュートリアル 1:信用リスクの予測 - Azure Machine Learning Studio

このチュートリアルでは、予測分析ソリューションを開発するプロセスについて詳しく説明します。 Machine Learning Studio で簡単なモデルを開発します。  その後、そのモデルを Azure Machine Learning Web サービスとしてデプロイします。  このデプロイ モデルは、新しいデータを使用して予測を行うことができます。 このチュートリアルは、**3 部構成のチュートリアル シリーズの第 1 部**です。

クレジットの申請書に記入する情報に基づいて個人のクレジット リスクを予測する必要があるとします。  

信用リスクの評価は複雑な問題ですが、このチュートリアルでは、それを少し簡略化してみます。 Microsoft Azure Machine Learning Studio を使用して予測分析ソリューションを作成する方法の例として使用してください。 このソリューションには、Azure Machine Learning Studio と Machine Learning Web サービスを使用します。  

この 3 部構成のチュートリアルでは、まず、公表されている信用リスク データを使用します。  その後、予測モデルを開発してトレーニングします。  最後にそのモデルを Web サービスとしてデプロイします。

チュートリアルのこのパートでは、次のことを行います。 
 
> [!div class="checklist"]
> * Machine Learning Studio ワークスペースの作成
> * 既存のデータのアップロード
> * 実験の作成

その後、この実験を使用して[モデルをトレーニング (第 2部)](tutorial-part2-credit-risk-train.md) し、[それらをデプロイ (第 3 部)](tutorial-part3-credit-risk-deploy.md) することができます。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>前提条件

このチュートリアルでは、これまでに少なくとも 1 回は Machine Learning Studio を使用したことがあり、機械学習の概念をある程度理解していることを前提としています。 いずれにしても専門家ではないことを想定しています。

**Azure Machine Learning Studio** をまだ使用したことがない場合は、[Azure Machine Learning Studio で初めてのデータ サイエンス実験を作成する](create-experiment.md)クイック スタートから始めることをお勧めします。 そのクイック スタートでは、Machine Learning Studio を初めて使用する場合の手順を示しながら、 実験にモジュールをドラッグ アンド ドロップして互いに結び付け、実験を実行して結果を確認する方法の基本について説明します。


> [!TIP] 
> [Azure AI Gallery](https://gallery.azure.ai) には、このチュートリアルで開発する実験の作業コピーがあります。 「**[Tutorial - Predict credit risk (チュートリアル - 信用リスクの予測)](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)**」にアクセスし、**[Open in Studio]\(Studio で開く\)** をクリックして Machine Learning Studio ワークスペースに実験のコピーをダウンロードしてください。
> 


## <a name="create-a-machine-learning-studio-workspace"></a>Machine Learning Studio ワークスペースの作成

Machine Learning Studio を使用するには、Microsoft Azure Machine Learning Studio ワークスペースが必要です。 このワークスペースには、実験を管理および公開するのに必要なツールが用意されています。  

ワークスペースを作成するには、「[Azure Machine Learning Studio ワークスペースの作成と共有](create-workspace.md)」を参照してください。

ワークスペースが作成された後、Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)) を開きます。 ワークスペースが複数ある場合は、ウィンドウの右上隅のツールバーでワークスペースを選択できます。

![Studio でのワークスペースの選択](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> 自分がワークスペースの所有者になっている場合は、自分以外のユーザーをワークスペースに招待することで、作業中の実験を共有することができます。 これは、Machine Learning Studio の **[設定]** ページから実行でき、 必要な情報は各ユーザーの Microsoft アカウントまたは組織アカウントだけです。
> 
> **[設定]** ページで **[ユーザー]** をクリックします。次に、ウィンドウの下部にある **[INVITE MORE USERS]** をクリックします。
> 

## <a name="upload"></a>既存のデータのアップロード

信用リスクの予測モデルを作成するには、トレーニングとその後のモデルのテストに使用できるデータが必要です。 このチュートリアルでは、UC Irvine Machine Learning Repository の "UCI Statlog (German Credit Data) Data Set" (UCI Statlog (ドイツの信用貸付データ) データ セット) を使用します。 詳細についてはこちらを参照してください:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

**german.data** という名前のファイルを使用します。 このファイルをローカル ハード ドライブにダウンロードします。  

この **german.data** データセットの行には、信用貸付の過去 1,000 人の申請者に関する 20 の変数が含まれています。 これらの 20 の変数は、データセットの特徴セット (*特徴ベクトル*) を表し、それぞれの信用貸付申請者を識別する特徴を提供します。 各行の追加の列は、申請者について計算された信用リスクを表し、700 人の申請者が低リスクとして、300 人が高リスクとして識別されています。

UCI Web サイトは、このデータの特徴ベクトルの属性についての詳細を提供します。 このデータには、財務情報、クレジット履歴、雇用状況、個人情報が含まれます。 各申請者について、申請者の信用リスクの高低を示す二項評価が与えられています。 

このデータを使用して、予測分析モデルをトレーニングします。 完了したら、このモデルで新しい個人の特徴ベクトルを受け入れて、信用リスクの高低を予測できるようになります。  

ここで興味深い展開になります。

UCI Web サイト上のデータセットの説明では、個人の信用リスクを誤って分類した場合のコストについて触れています。
モデルが実際には信用リスクが低い人を信用リスクが高いと予測すると、誤分類をしたことになります。

しかし逆の誤分類、つまり、モデルが実際には信用リスクが高い人を信用リスクが低いと予測する場合は、金融機関に 5 倍のコストがかかります。

そこで、モデルをトレーニングして、後者の場合の誤分類コストがもう一方の誤分類に比べて 5 倍になるようにします。

実験でモデルをトレーニングするときにこれを実行する簡単な方法の 1 つは、信用リスクが高い人を示すエントリを重複 (5 回) させることです。 

そうすれば、実際には信用リスクが高い人を信用リスクが低い人としてモデルが誤分類を行うと、重複エントリごとに同じ誤分類が 5 回発生することになります。 これにより、トレーニング結果でこのエラーのコストが増加します。


### <a name="convert-the-dataset-format"></a>データセットの形式の変換

元のデータセットは、空白で区切られた形式を使用しています。 Machine Learning Studio で使用するにはコンマ区切り値 (CSV) ファイルの方が適しているため、空白をコンマに置き換えてデータセットを変換します。  

このデータを変換する方法は多数存在します。 1 つは、次の Windows PowerShell コマンドを使用する方法です。   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

他に、次の Unix の sed コマンドを使用する方法もあります。  

    sed 's/ /,/g' german.data > german.csv  

いずれの場合も、コンマ区切りに変換されたデータが、**german.csv** という名前のファイルに作成されます。このファイルは実験で使用できます。

### <a name="upload-the-dataset-to-machine-learning-studio"></a>Machine Learning Studio にデータセットをアップロードする

データを CSV 形式に変換したら、それを Machine Learning Studio にアップロードする必要があります。 

1. Machine Learning Studio のホーム ページ ([https://studio.azureml.net](https://studio.azureml.net)) を開きます。 

2. ウィンドウの左上隅にある ![[メニュー]](./media/tutorial-part1-credit-risk/menu.png) をクリックします。**[Azure Machine Learning]** をクリックし、**[Studio]** を選択してサインインします。

3. ウィンドウの下部にある **[+新規]** をクリックします。

4. **[データセット]** を選択します。

5. **[ローカル ファイルから]** を選択します。

    ![ローカル ファイルからのデータセットの追加](./media/tutorial-part1-credit-risk/add-dataset.png)

6. **[新しいデータセットをアップロードする]** ダイアログで、[参照] をクリックし、作成した **german.csv** ファイルを検索します。

7. データセットの名前を入力します。 このチュートリアルでは、「UCI German Credit Card Data」とします。

8. データ型として、 **[ヘッダーなしの汎用 CSV ファイル (.nh.csv)]** を選択します。

9. 必要に応じて説明を追加します。

10. **OK** チェック マークをクリックします。  

    ![データセットのアップロード](./media/tutorial-part1-credit-risk/upload-dataset.png)

これにより、データは、実験で使用できるデータセット モジュールにアップロードされます。

Studio にアップロードしたデータセットは、Studio ウィンドウの左側にある **[データセット]** タブをクリックすることで管理できます。

![データセットの管理](./media/tutorial-part1-credit-risk/dataset-list.png)

その他の種類のデータを実験にインポートする方法の詳細については、[Azure Machine Learning Studio へのトレーニング データのトレーニング データのインポート](import-data.md)に関するページを参照してください。

## <a name="create-an-experiment"></a>実験の作成

このチュートリアルでの次の手順では、アップロードしたデータセットを使用する実験を Machine Learning Studio で作成します。  

1. Studio で、ウィンドウの下部にある **[+新規]** をクリックします。
1. **[実験]** を選択して、[空の実験] を選択します。 

    ![新しい実験を作成する](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. キャンバスの上部にある既定の実験名を選択し、わかりやすい名前に変更します。

    ![実験名の変更](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > 実験のため、**[プロパティ]** ウィンドウの **[概要]** と **[説明]** に入力することをお勧めします。 これらのプロパティを指定しておくと、実験を文書に残すことができるので、誰でも後から見て目標と手法を理解できます。
   > 
   > ![実験のプロパティ](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. 実験キャンバスの左側にあるモジュール パレットの **[保存されたデータセット]** を展開します。
1. 作成したデータセットを **[マイ データセット]** から見つけて、キャンバスにドラッグします。 パレットの上にある **[検索]** ボックスに名前を入力してデータセットを見つけることもできます。  

    ![実験キャンバスへのデータセットの追加](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>データを準備する

データの最初の 100 行とデータセット全体の統計情報を表示できます。データセットの出力ポート (下部の小さな円) をクリックし、**[視覚化]** を選択します。  

データ ファイルには列見出しがないため、ため、Studio では汎用の見出し (Col1、Col2 "*など*") が付けられます。 適切な見出しはモデルを作成するために絶対に必要なものではありませんが、実験のデータを操作する際に便利です。 また、最終的にこのモデルを Web サービスに発行する際に、見出しは、サービスのユーザーが列を特定するのに役立ちます。  

列見出しを追加するには、[メタデータの編集][edit-metadata]モジュールを使用します。

[メタデータの編集][edit-metadata]モジュールを使用して、データセットに関連付けられたメタデータを変更します。 この場合は、それを使用して、列見出しによりわかりやすい名前を付けます。 

[メタデータの編集][edit-metadata]を使用するには、まず変更する列 (このケースではすべての列) を指定します。次に、それらの列に対して実行するアクション (このケースでは列見出しの変更) を指定します。

1. モジュール パレットの **[検索]** ボックスに「メタデータ」と入力します。 モジュールの一覧に[メタデータの編集][edit-metadata]が表示されます。

1. [メタデータの編集][edit-metadata]モジュールをクリックしてキャンバスにドラッグし、先ほど追加したデータセットの下にドロップします。

1. データセットを[メタデータの編集][edit-metadata]に接続します。データセットの出力ポート (データセット下部の小さな円) をクリックし、[メタデータの編集][edit-metadata]の入力ポート (モジュール上部の小さな円) の上にドラッグしてからマウス ボタンを放します。 これで、マウスをキャンバス上でどこに移動しても、データセットとモジュールが接続されたままになります。
 
    実験は以下のようになっているはずです。  

    ![メタデータの編集の追加](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    赤色の感嘆符は、このモジュールのプロパティがまだ設定されていないことを示します。 これは次に行います。

    > [!TIP]
    > モジュールをダブルクリックして、テキストを入力すると、モジュールにコメントを追加できます。 これで、実験でモジュールがどのような処理をするのかがひとめでわかります。 ここでは、[メタデータの編集][edit-metadata]モジュールをダブルクリックし、「列見出しの追加」というコメントを入力します。 テキスト ボックスを閉じるには、キャンバスの任意の場所をクリックします。 コメントを表示するには、モジュールの下向き矢印をクリックします。
    > 
    > ![コメントが追加されたメタデータの編集モジュール](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. [メタデータの編集][edit-metadata]を選択し、キャンバスの右側にある **[プロパティ]** ウィンドウで **[列セレクターの起動]** をクリックします。

1. **[列の選択]** ダイアログで、**[使用可能な列]** のすべての列を選択して [>] をクリックし、**[選択した列]** に移します。
   ダイアログは次のようになります。

   ![すべての列が選択された列セレクター](./media/tutorial-part1-credit-risk/select-columns.png)


1. **OK** チェック マークをクリックします。

1. **[プロパティ]** ウィンドウに戻り、**[新しい列名]** パラメーターを探します。 このフィールドには、コンマ区切りで列の順番どおりに、データセットの 21 列分の名前を入力します。 列名は、UCI Web サイトのデータセットに関するドキュメントから入手できます。または、次の一覧をコピーして貼り付けることができます。  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   [プロパティ] ウィンドウは次のようになります。

   ![メタデータの編集のプロパティ](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > 列見出しを検証する場合は、実験を実行します (実験キャンバスの下の **[Run (実行)]** をクリックします)。 実行が終了したら ([メタデータの編集][edit-metadata]上に緑のチェック マークが表示されたら)、[メタデータの編集][edit-metadata]モジュールの出力ポートをクリックし、**[視覚化]** を選択します。 すべてのモジュールで、この方法によって実験データの進捗状況を確認することができます。
   > 
   > 

### <a name="create-training-and-test-datasets"></a>トレーニング用およびテスト用のデータセットを作成する

モデルのトレーニング用とテスト用にデータが必要です。
そのため、実験の次の手順では、データセットを 2 つの別々のデータセットに分割します。1 つはモデルのトレーニング用、もう 1 つはテスト用です。

これには、[データの分割][split]モジュールを使用します。  

1. [データの分割][split]モジュールを見つけてキャンバスにドラッグし、[メタデータの編集][edit-metadata]モジュールに接続します。

1. 既定では、分割比が 0.5 で、 **[ランダム分割]** パラメーターが設定されます。 これは、データの半分がランダムに抽出されて[データの分割][split]モジュールの 1 つのポートから出力され、残りの半分がもう 1 つのポートから出力されることを意味します。 これらのパラメーターと **[ランダム シード]** パラメーターを調整して、トレーニング データとテスト データの分割比を変更できます。 この例では、これらをそのままにしておきます。
   
   > [!TIP]
   > *左の*出力ポートから出力されるデータの量は、**[Fraction of rows in the first output dataset]** (最初の出力データセットにおける列の割合) プロパティによって決まります。 たとえば、比率を 0.7 に設定すると、データの 70% が左側のポートから、30% が右側のポートから出力されます。  
   > 
   > 

1. [データの分割][split]モジュールをダブルクリックし、「トレーニング/テスト データの分割 50%」とコメントを入力します。 

[データの分割][split]モジュールの出力の用途は自由ですが、ここでは左側の出力をトレーニング データ、右側の出力をテスト データとして使用します。  

[前の手順](tutorial-part1-credit-risk.md#upload)での説明のとおり、高い信用リスクを誤って低リスクと分類すると、低い信用リスクを誤って高リスクと分類した場合の 5 倍のコストが発生します。 これに対応するため、このコストの特徴を反映した新しいデータセットを生成します。 この新しいデータセットでは、高リスクの例を 5 回重複させ、低リスクの例は重複させません。   

この重複は、R コードを使用して実行できます。  

1. [R スクリプトの実行][execute-r-script]モジュールを見つけて、実験キャンバスにドラッグします。 

1. [データの分割][split]モジュールの左側の出力ポートを [R スクリプトの実行][execute-r-script]モジュールの 1 つ目の入力ポート ("Dataset1") に接続します。

1. [R スクリプトの実行][execute-r-script]モジュールをダブルクリックし、「コスト調整の設定」というコメントを入力します。

1. **[プロパティ]** ウィンドウの **[R スクリプト]** パラメーターの既定の文字列を削除し、以下のスクリプトを入力します。
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R スクリプトの実行モジュール内の R スクリプト](./media/tutorial-part1-credit-risk/execute-r-script.png)

[データの分割][split]モジュールの各出力について同様の重複操作を設定する必要があります。これによって、トレーニング データとテスト データのコスト調整が等しくなります。 これを実行する最も簡単方法は、作成したばかりの [R スクリプトの実行][execute-r-script]モジュールを複製して、[データの分割][split]モジュールのもう 1 つの出力ポートに接続することです。

1. [R スクリプトの実行][execute-r-script]モジュールを右クリックし、**[コピー]** を選択します。

1. 実験キャンバスを右クリックして **[貼り付け]** を選択します。

1. 新しいモジュールを適切な位置にドラッグし、[データの分割][split]モジュールの右側の出力ポートを、この新しい [ R スクリプトの実行][execute-r-script]モジュールの 1 つ目の入力ポートに接続します。 

1. キャンバスの下部で、**[実行]** をクリックします。 

> [!TIP]
> R スクリプトの実行モジュールのコピーには、元のモジュールと同じスクリプトが格納されています。 モジュールをコピーしてキャンバスに貼り付けた場合、モジュールのコピーには元のモジュールのすべてのプロパティが保持されています。  
> 
>

現在、実験は以下のようになっているはずです。

![Adding Split module and R scripts](./media/tutorial-part1-credit-risk/experiment.png)

実験での R スクリプトの使用に関する詳細については、「[R を使用した実験の拡張](extend-your-experiment-with-r.md)」をご覧ください。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を完了しました。 
 
> [!div class="checklist"]
> * Machine Learning Studio ワークスペースの作成
> * ワークスペースへの既存のデータのアップロード
> * 実験の作成

これで、このデータのモデルをトレーニングして評価する準備が整いました。

> [!div class="nextstepaction"]
> [チュートリアル 2 - モデルをトレーニングして評価する](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/