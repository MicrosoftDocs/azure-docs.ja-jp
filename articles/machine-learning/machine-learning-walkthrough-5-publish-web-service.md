---
title: "手順 5: Machine Learning Web サービスをデプロイする | Microsoft Docs"
description: "予測ソリューションの開発チュートリアルの 5 番目の手順: Machine Learning Studio で予測実験を Web サービスとしてデプロイする。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 065e84e8ddee3466834e04fb5d1929652533265a
ms.lasthandoff: 03/25/2017


---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>チュートリアル手順 5: Azure Machine Learning Web サービスをデプロイする
これは、「 [チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](machine-learning-walkthrough-develop-predictive-solution.md)

1. [Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3. [新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4. [モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. **Web サービスをデプロイする**
6. [Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

- - -
このチュートリアルで作成した予測モデルを他のユーザーが使用できるように、Web サービスとして Azure にデプロイします。

これまでは、モデルのトレーニングを実験してきました。 ただし、デプロイするサービスのトレーニングはこれ以上行われません。サービスは、モデルに基づいてユーザーの入力をスコア付けすることで新しい予測を生成します。 このため、この実験を "***トレーニング***" 実験から "***予測***" 実験に変換するための準備をします。 

これは 3 段階のプロセスです。  

1. モデルのいずれかを削除する
2. 作成した "*トレーニング実験*" を "*予測実験*" に変換
3. Web サービスとして予測実験をデプロイする

## <a name="remove-one-of-the-models"></a>モデルのいずれかを削除する

最初に、この実験を少しスリム化する必要があります。 現在、実験には 2 つの異なるモデルがありますが、Web サービスとしてデプロイするサービスでは、モデルは 1 つだけ使用します。  

使用にあたってはブースト ツリー モデルの方が優れていると判断したとします。 このため、最初に実行するのは、[2 クラス サポート ベクター マシン][two-class-support-vector-machine] モジュールと、それをトレーニングするために使用したモジュールの削除です。 まずは実験キャンバスの下にある **[名前を付けて保存]** をクリックして実験のコピーを作成します。

次のモジュールを削除する必要があります。  

* [2 クラス サポート ベクター マシン][two-class-support-vector-machine]
* 上記に接続されている[モデルのトレーニング][train-model] モジュールと[モデルのスコア付け][score-model]モジュール
* [データの正規化][normalize-data] (両方)
* [モデルの評価][evaluate-model] (モデルの評価は終了したため)

各モジュールを選択して Delete キーを押すか、モジュールを右クリックして **[削除]**を選択します。 

![SVM モデルを削除][3a]

これでモデルは次のようになるはずです。

![SVM モデルを削除][3]

これで、[2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree]を使用するモデルをデプロイする準備ができました。

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>トレーニング実験を予測実験に変換する

このモデルをデプロイできるよう準備するには、このトレーニング実験を予測実験に変換する必要があります。 これを行うには、3 つの手順を実行します。

1. トレーニングしたモデルを保存し、トレーニング モジュールを置き換えます。
2. 実験をトリミングしてトレーニングのためのみに必要だったモジュールを削除します。
3. Web サービスが入力を受け取る場所と出力を生成する場所を定義します

この 3 つの手順は手動でも実行できますが、幸いにも、実験キャンバスの下部にある **[Web サービスの設定]** をクリックすることですべての手順を完了できます (**[予測 Web サービス]** オプションを選択します)。

> [!TIP]
> トレーニング実験を予測実験に変換するときの動作の詳細については、「[Machine Learning のトレーニング実験から予測実験に変換する](machine-learning-convert-training-experiment-to-scoring-experiment.md)」を参照してください。

**[Web サービスの設定]**をクリックすると、次の動作が行われます。

* トレーニング済みのモデルが 1 つの**トレーニング済みのモデル** モジュールに変換され、実験キャンバスの左側にあるモジュール パレットに保存されます (**[トレーニング済みのモデル]** で見つけることができます)。
* トレーニングに使用したモジュールは削除されます。具体的には次のモジュールが削除されます。
  * [2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree]
  * [モデルのトレーニング][train-model]
  * [データの分割][split]
  * テスト データ用に使用した 2 つ目の [R スクリプトの実行][execute-r-script]モジュール
* 保存したトレーニング済みのモデルが実験に追加されます。
* **Web サービスの入力**モジュールと **Web サービスの出力**モジュールが追加されます (これらは、ユーザーのデータがどこでモデルに入力されるか、どのようなデータが返されるか、いつ Web サービスにアクセスされるかを識別します)

> [!NOTE]
> 実験は、実験キャンバスの上部に追加された 2 つのタブに分かれて保存されます。 元のトレーニング実験は **[トレーニング実験]** タブに、新しく作成された予測実験は **[予測実験]** タブに配置されます。 Web サービスとしてデプロイするのは予測実験です。

この特定の実験では、手順を 1 つ追加で実行する必要があります。
データに重み関数を提供するために、2 つの [R スクリプトの実行][execute-r-script]モジュールが追加されています。 この追加はトレーニングとテストを行うために必要な操作であるため、これらのモジュールは最終モデルから削除できます。
Machine Learning Studio は、[分割][split]モジュールを削除する際に、[R スクリプトの実行][execute-r-script]モジュールを 1 つ削除しています。 ここで、もう 1 つのモジュールを削除し、[メタデータ エディター][metadata-editor]を[モデルのスコア付け][score-model]に直接接続できます。    

現在、実験は以下のようになっています。  

![Scoring the trained model][4]  

> [!NOTE]
> UCI のドイツ語のクレジット カード データ データセットはどうして予測実験に残されたか疑問に思われるでしょう。 このサービスでは、元のデータセットではなくユーザーのデータを使用します。それにもかかわらずなぜ元のデータセットを残すのでしょうか?
> 
> サービスで元のクレジット カード データを必要としないのは本当です。 しかし、列がいくつあり、どの列が数値型であるかなどの情報を含むこのデータのスキーマが必要になります。 このスキーマ情報は、ユーザーのデータを解釈するうえで必要になります。 そこで、これらのコンポーネントを接続したままにして、サービスが実行されているときにスコア付けモジュールがデータセット スキーマを利用できるようにしています。 データは使用されません。スキーマだけが使用されます。  
> 
> 

最後にもう一度実験を実行します ( **[実行]**をクリックします)。モデルがまだ機能していることを確認するには、[モデルのスコア付け][score-model]モジュールをクリックし、**[結果の表示]** を選択します。 元のデータが、信用リスク値 ("スコア付けラベル") とスコア付け確率値 ("スコア付け確率") と共に表示されます。 

## <a name="deploy-the-web-service"></a>Web サービスをデプロイする
従来の Web サービスまたは Azure Resource Manager に基づく新しい Web サービスのどちらかとして実験をデプロイできます。

### <a name="deploy-as-a-classic-web-service"></a>従来の Web サービスとしてデプロイする
実験から派生する従来の Web サービスをデプロイするには、キャンバスの下にある **[Web サービスのデプロイ]** をクリックし、**[Deploy Web Service (Classic) (Web サービスのデプロイ [従来])]** を選択します。 Machine Learning Studio によって実験が Web サービスとしてデプロイされ、その Web サービス用のダッシュボードに移動します。 このページから実験に戻って (**[スナップショットの表示]** または **[最新の表示]** をクリック)、Web サービスの簡単なテストを実行できます (以下の「**Web サービスをテストする**」セクションを参照してください)。 この Web サービスにアクセスできるアプリケーションを作成するための情報も表示されます (詳細はこのチュートリアルの次の手順を参照してください)。

![Web サービス ダッシュボード][6]

サービスを構成するには、 **[構成]** タブをクリックします。 このタブでは、サービス名 (既定で実験名が付けられます) を変更したり、説明を追加したりできます。 また、入力データと出力データにわかりやすいラベルを付けることもできます。  

![Web サービスを構成する][5]  

### <a name="deploy-as-a-new-web-service"></a>新しい Web サービスとしてデプロイする

> [!NOTE] 
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳細については、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](machine-learning-manage-new-webservice.md)」を参照してください。 

実験から派生した新しい Web サービスをデプロイするには:

1. キャンバスの下部の **[Web サービスのデプロイ]** をクリックし、**[Deploy Web Service (New) (Web サービスのデプロイ (新規))]** を選択します。 Machine Learning Studio から Azure Machine Learning Web サービスの **[Deploy Experiment (実験のデプロイ)]** ページに移動します。

2. Web サービスの名前を入力します。 

3. **[料金プラン]** で、既存の価格プランを選択するか、[新規作成] を選択し、新しいプランに名前を付け、月額プラン オプションを選択します。 プラン レベルは既定では既定のリージョンのプランになり、Web サービスはそのリージョンにデプロイされます。

4. **[デプロイ]**をクリックします。

数分後に、Web サービスの **[クイック スタート]** ページが開きます。

**[構成]** タブをクリックしてサービスを構成できます。 ここで、サービスのタイトルを変更し、説明を入力できます。 

Web サービスをテストするには、**[テスト]** タブをクリックします (以下の「**Web サービスをテストする**」を参照してください)。 Web サービスにアクセスできるアプリケーションの作成については、**[Consume (使用)]** タブをクリックします (詳細については、このチュートリアルの次の手順を参照してください)。

> [!TIP]
> Web サービスをデプロイした後で更新できます。 たとえばモデルを変更する場合は、トレーニング実験を編集し、モデルのパラメーターを調整した後で **[Web サービスのデプロイ]** をクリックし、**[Deploy Web Service (Classic) \(Web サービスのデプロイ (クラシック))]** または **[Deploy Web Service (New) \(Web サービスのデプロイ (新規))]**.を選択します。 もう一度実験をデプロイすると、Web サービスが置き換えられ、更新済みのモデルが使用されるようになります。  
> 
> 

## <a name="test-the-web-service"></a>Web サービスをテストする

Web サービスがアクセスされると、ユーザーのデータは **Web サービスの入力**モジュールに入力され、そこから[モデルのスコア付け][score-model]モジュールに渡されてスコアが付けられます。 設定した予測実験の方法では、モデルのデータは、元の信用リスクデータ セットと同じ形式であることが期待されています。
結果は、Web サービスから **Web サービスの出力**モジュール経由でユーザーに返されます。

> [!TIP]
> 構成した予測実験の方法では、[モデルのスコア付け][score-model]モジュールから結果全体が返されます。 これには、すべての入力データに加え、信用リスク値とスコア付け確率が含まれます。 ただし、必要に応じて、これとは異なる結果、たとえば信用リスク値のみを返すことができます。 それには、[列の投影][project-columns]モジュールを、[モデルのスコア付け][score-model]と **Web サービスの出力**の間に挿入して、Web サービスから返さない列を排除します。 
> 
> 

従来の Web サービスは、**Machine Learning Studio** または **Azure Machine Learning Web サービス** ポータルでテストできます。
新しい Web サービスは、**Machine Learning Web サービス** ポータルでのみテストできます。

> [!TIP]
> Azure Machine Learning Web サービス ポータルでテストするとき、要求 - 応答型のサービスをテストするために使用できるサンプル データをポータルに作成させることができます。 **[構成]** ページで、**[Sample Data Enabled? (サンプル データを有効にしますか?)]** で [はい] を選択します。 **[テスト]** ページの [要求 - 応答] タブを開くと、ポータルによって元の信用リスクデータセットから取得されたサンプル データが入力されます。

### <a name="test-a-classic-web-service"></a>従来の Web サービスをテストする

従来の Web サービスは、Machine Learning Studio または Machine Learning Web サービス ポータルでテストできます。 

#### <a name="test-in-machine-learning-studio"></a>Machine Learning Studio でのテストでテストする

1. Web サービスの **[ダッシュボード]** ページで、**[既定のエンドポイント]** の **[テスト]** ボタンをクリックします。 サービスの入力データを要求するダイアログが表示されます。 これらは、元の信用リスク データセットに含まれるのと同じ列です。  

2. データのセットを入力し、 **[OK]**をクリックします。 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Machine Learning Web サービス ポータルでテストする

1. Web サービスの **[ダッシュボード]** ページで、**[既定のエンドポイント]** の **[Test preview (プレビューのテスト)]** リンクをクリックします。 Azure Machine Learning Web サービス ポータルで、Web サービス エンドポイント用のテスト ページが開き、サービスの入力データが求められます。 これらは、元の信用リスク データセットに含まれるのと同じ列です。

2. **[Test Request-Response (要求応答のテスト)]** をクリックします。 

### <a name="test-a-new-web-service"></a>新しい Web サービスをテストする

新しい Web サービスは、Machine Learning Web サービス ポータルでのみテストできます。

1. [Azure Machine Learning Web サービス](https://services.azureml.net/quickstart) ポータルで、ページ上部の **[テスト]** をクリックします。 **[テスト]** ページが開かれ、サービスのデータを入力することができます。 表示される入力フィールドは、元の信用リスク データセットに含まれる列に対応しています。 

2. データのセットを入力し、 **[Test Request-Response (要求応答のテスト)]**をクリックします。

テストの結果は、ページの右側の出力列に表示されます。 


## <a name="manage-the-web-service"></a>Web サービスを管理する

### <a name="manage-a-classic-web-service-in-the-azure-classic-portal"></a>Azure クラシック ポータルで従来の Web サービスを管理する

デプロイした従来の Web サービスは、[Azure クラシック ポータル](https://manage.windowsazure.com)から管理できます。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. Microsoft Azure サービス パネルで、**[Machine Learning]** をクリックします。
3. 自分のワークスペースをクリックします。
4. **[Web サービス]** タブをクリックします。
5. 作成した Web サービスをクリックします
6. "既定の" エンドポイントをクリックします。

そこから、Web サービスの動作の監視や、サービスが処理できる同時呼び出し数の変更によるパフォーマンスの微調整などを実行できます。

詳細については、次のリンクを参照してください。

* [エンドポイントを作成する](machine-learning-create-endpoint.md)
* [Web サービスのスケーリング](machine-learning-scaling-webservice.md)

### <a name="manage-a-classic-or-new-web-service-in-the-azure-machine-learning-web-services-portal"></a>Azure Machine Learning Web サービス ポータルで従来の Web サービスまたは新しい Web サービスを管理する

デプロイした Web サービスは、従来の Web サービスでも新しい Web サービスでも、[Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/quickstart) ポータルから管理できます。

Web サービスのパフォーマンスを監視するには:

1. [Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/quickstart) ポータルにサインインします
2. **[Web サービス]** をクリックします。
3. Web サービスをクリックします
4. **[ダッシュボード]** をクリックします。

- - -
**次:[ Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)**

[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

