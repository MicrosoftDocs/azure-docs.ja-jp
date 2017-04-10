---
title: "手順 3: Machine Learning の新しい実験を作成する | Microsoft Docs"
description: "予測ソリューションの開発に関するチュートリアルの中の、Azure Machine Learning Studio で新しいトレーニング実験を作成するステップの 3 番目の手順です。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 660e3c27-55ef-4c33-a4e9-dff4d1224630
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: cd410316910bce76f5c915c06e83b24c034481b7
ms.lasthandoff: 03/25/2017


---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>チュートリアル手順 3: Azure Machine Learning の新しい実験を作成する
これは、「 [チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](machine-learning-walkthrough-develop-predictive-solution.md)

1. [Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3. **新しい実験を作成する**
4. [モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6. [Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

- - -
このチュートリアルでの次の手順は、アップロードしたデータセットを使用する実験を Machine Learning Studio で作成することです。  

1. Studio で、ウィンドウの下部にある **[+新規]** をクリックします。
2. **[実験]**を選択して、[空の実験] を選択します。 

    ![新しい実験を作成する][0]

2. キャンバスの上部にある既定の実験名を選択し、わかりやすい名前に変更します。

    ![実験名の変更][5]
   
   > [!TIP]
   > 実験のため、**[プロパティ]** ウィンドウの **[概要]** と **[説明]** に入力することをお勧めします。 これらのプロパティを指定しておくと、実験を文書に残すことができるので、誰でも後から見て目標と手法を理解できます。
   > 
   > ![実験のプロパティ][6]
   > 
3. 実験キャンバスの左側にあるモジュール パレットの **[保存されたデータセット]**を展開します。
4. 作成したデータセットを **[マイ データセット]** から見つけて、キャンバスにドラッグします。 パレットの上にある **[検索]** ボックスに名前を入力してデータセットを見つけることもできます。  

    ![実験キャンバスへのデータセットの追加][7]

## <a name="prepare-the-data"></a>データを準備する
データの最初の 100 行とデータセット全体の統計情報を表示できます。データセットの出力ポート (下部の小さな円) をクリックし、**[視覚化]**を選択します。  

データ ファイルには列見出しがないため、ため、Studio では汎用の見出し (Col1、Col2 **など) が付けられます。 適切な見出しはモデルを作成するために絶対に必要なものではありませんが、実験のデータを操作する際に便利です。 また、最終的にこのモデルを Web サービスに発行する際に、見出しが設定されていることによってサービスのユーザーが列を見つけやすくなります。  

列見出しを追加するには、[メタデータの編集][edit-metadata]モジュールを使用します。
[メタデータの編集][edit-metadata]モジュールを使用して、データセットに関連付けられたメタデータを変更します。 ここでは、それを使用して、列見出しに分かりやすい名前を付けます。 

[メタデータの編集][edit-metadata]を使用するには、まず変更する列 (このケースではすべての列) を指定します。次に、それらの列に対して実行するアクション (このケースでは列見出しの変更) を指定します。

1. モジュール パレットの **[検索]** ボックスに「メタデータ」と入力します。 モジュールの一覧に[メタデータの編集][edit-metadata]が表示されます。

2. [メタデータの編集][edit-metadata]モジュールをクリックしてキャンバスにドラッグし、先ほど追加したデータセットの下にドロップします。

3. データセットを[メタデータの編集][edit-metadata]に接続します。データセットの出力ポート (データセット下部の小さな円) をクリックし、[メタデータの編集][edit-metadata]の入力ポート (モジュール上部の小さな円) の上にドラッグしてからマウス ボタンを放します。 これで、マウスをキャンバス上でどこに移動しても、データセットとモジュールが接続されたままになります。
   
   実験は以下のようになっているはずです。  
   
   ![メタデータの編集の追加][1]
   
   赤色の感嘆符は、このモジュールのプロパティがまだ設定されていないことを示します。 これは次の手順で実行します。
   
   > [!TIP]
   > モジュールをダブルクリックして、テキストを入力すると、モジュールにコメントを追加できます。 これで、実験でモジュールがどのような処理をするのかがひとめでわかります。 ここでは、[メタデータの編集][edit-metadata]モジュールをダブルクリックし、「列見出しの追加」というコメントを入力します。 テキスト ボックスを閉じるには、キャンバスの任意の場所をクリックします。 コメントを表示するには、モジュールの下向き矢印をクリックします。
   > 
   > ![コメントが追加されたメタデータの編集モジュール][8]
   > 
4. [メタデータの編集][edit-metadata]を選択し、キャンバスの右側にある **[プロパティ]** ウィンドウで **[列セレクターの起動]** をクリックします。

5. **[列の選択]** ダイアログで、**[使用可能な列]** のすべての列を選択して [>] をクリックし、**[選択した列]** に移します。
   ダイアログは次のようになります。

   ![すべての列が選択された列セレクター][2]

6. **OK** チェック マークをクリックします。

7. **[プロパティ]** ウィンドウに戻り、**[新しい列名]** パラメーターを探します。 このフィールドには、コンマ区切りで列の順番どおりに、データセットの 21 列分の名前を入力します。 列名は、UCI Web サイトのデータセットに関するドキュメントから入手できます。または、次の一覧をコピーして貼り付けることができます。  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   [プロパティ] ウィンドウは次のようになります。
   
   ![メタデータの編集のプロパティ][3]

> [!TIP]
> 列見出しを検証する場合は、実験を実行します (実験キャンバスの下の **[Run (実行)]** をクリックします)。 実行が終了したら ([メタデータの編集][edit-metadata]上に緑のチェック マークが表示されたら)、[メタデータの編集][edit-metadata]モジュールの出力ポートをクリックし、**[視覚化]** を選択します。 すべてのモジュールで、この方法によって実験データの進捗状況を確認することができます。
> 
> 

## <a name="create-training-and-test-datasets"></a>トレーニング用およびテスト用のデータセットを作成する
モデルのトレーニング用とテスト用に、データが必要です。
実験の次の手順では、データセットを 2 つの別々のデータセットに分割します。1 つはモデルのトレーニング用、もう 1 つはテスト用です。

これには、[データの分割][split]モジュールを使用します。  

1. [データの分割][split]モジュールを見つけてキャンバスにドラッグし、[メタデータの編集][edit-metadata]モジュールに接続します。

2. 既定では、分割比が 0.5 で、 **[ランダム分割]** パラメーターが設定されます。 これは、データの半分がランダムに抽出されて[データの分割][split]モジュールの 1 つのポートから出力され、残りの半分がもう 1 つのポートから出力されることを意味します。 これらのパラメーターと **[ランダム シード]** パラメーターを調整して、トレーニング データとテスト データの分割比を変更できます。 この例では設定はそのままにしておきます。
   
   > [!TIP]
   > *左の*出力ポートから出力されるデータの量は、**[Fraction of rows in the first output dataset]** (最初の出力データセットにおける列の割合) プロパティによって決まります。 たとえば、比率を 0.7 に設定すると、データの 70% が左側のポートから、30% が右側のポートから出力されます。  
   > 
   > 

3. [データの分割][split]モジュールをダブルクリックし、「トレーニング/テスト データの分割 50%」とコメントを入力します。 

[データの分割][split]モジュールの出力の用途は自由ですが、ここでは左側の出力をトレーニング データ、右側の出力をテスト データとして使用します。  

[前の手順](machine-learning-walkthrough-2-upload-data.md)での説明のとおり、高い信用リスクを誤って低リスクと分類すると、低い信用リスクを誤って高リスクと分類した場合の 5 倍のコストが発生します。 これに対応するため、このコストの特徴を反映した新しいデータセットを生成します。 この新しいデータセットでは、高リスクの例を 5 回重複させ、低リスクの例は重複させません。   

この重複は、R コードを使用して以下の手順で実行します。  

1. [R スクリプトの実行][execute-r-script]モジュールを見つけて、実験キャンバスにドラッグします。 

2. [データの分割][split]モジュールの左側の出力ポートを [R スクリプトの実行][execute-r-script]モジュールの 1 つ目の入力ポート ("Dataset1") に接続します。

3. [R スクリプトの実行][execute-r-script]モジュールをダブルクリックし、「コスト調整の設定」というコメントを入力します。

4. **[プロパティ]** ウィンドウの **[R スクリプト]** パラメーターの既定の文字列を削除し、以下のスクリプトを入力します。
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R スクリプトの実行モジュール内の R スクリプト][9]

[データの分割][split]モジュールの各出力について同様の重複操作を設定する必要があります。これによって、トレーニング データとテスト データのコスト調整が等しくなります。 これを簡単に実行する方法は、作成したばかりの [R スクリプトの実行][execute-r-script]モジュールを複製して、[データの分割][split]モジュールのもう 1 つの出力ポートに接続することです。

1. [R スクリプトの実行][execute-r-script]モジュールを右クリックし、**[コピー]** を選択します。

2. 実験キャンバスを右クリックして **[貼り付け]**を選択します。

3. 新しいモジュールを適切な位置にドラッグし、[データの分割][split]モジュールの右側の出力ポートを、この新しい [ R スクリプトの実行][execute-r-script]モジュールの 1 つ目の入力ポートに接続します。 

4. キャンバスの下部で、**[実行]** をクリックします。 

> [!TIP]
> R スクリプトの実行モジュールのコピーには、元のモジュールと同じスクリプトが格納されています。 モジュールをコピーしてキャンバスに貼り付けた場合、モジュールのコピーには元のモジュールのすべてのプロパティが保持されています。  
> 
> 

現在、実験は以下のようになっているはずです。

![Adding Split module and R scripts][4]

実験での R スクリプトの使用に関する詳細については、「[R を使用した実験の拡張](machine-learning-extend-your-experiment-with-r.md)」をご覧ください。

**次: [モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)**

[0]: ./media/machine-learning-walkthrough-3-create-new-experiment/create-new-experiment.png
[5]: ./media/machine-learning-walkthrough-3-create-new-experiment/rename-experiment.png
[6]: ./media/machine-learning-walkthrough-3-create-new-experiment/experiment-properties.png
[7]: ./media/machine-learning-walkthrough-3-create-new-experiment/add-dataset-to-experiment.png
[8]: ./media/machine-learning-walkthrough-3-create-new-experiment/edit-metadata-with-comment.png
[9]: ./media/machine-learning-walkthrough-3-create-new-experiment/execute-r-script.png
[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/experiment-with-edit-metadata-module.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/select-columns.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/edit-metadata-properties.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/experiment.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

