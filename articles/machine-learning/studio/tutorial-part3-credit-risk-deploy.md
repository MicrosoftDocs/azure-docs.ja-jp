---
title: チュートリアル 3:信用リスク モデルをデプロイする
titleSuffix: Azure Machine Learning Studio
description: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning Studio で作成する方法を詳しく紹介したチュートリアルです。 このチュートリアルは、3 部構成のチュートリアル シリーズのパート 3 です。 モデルを Web サービスとしてデプロイする方法を示します。
keywords: 信用リスク, 予測分析ソリューション, リスク評価, デプロイ, Web サービス
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 6cdccd54546296c85864f1588b71109ed8b8f79f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620516"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio"></a>チュートリアル 3:信用リスク モデルをデプロイする - Azure Machine Learning Studio

このチュートリアルでは、予測分析ソリューションを開発するプロセスについて詳しく説明します。 Machine Learning Studio で簡単なモデルを開発します。  その後、そのモデルを Azure Machine Learning Web サービスとしてデプロイします。  このデプロイ モデルは、新しいデータを使用して予測を行うことができます。 このチュートリアルは、**3 部構成のチュートリアル シリーズのパート 3** です。

クレジットの申請書に記入する情報に基づいて個人のクレジット リスクを予測する必要があるとします。  

信用リスクの評価は複雑な問題ですが、このチュートリアルでは、それを少し簡略化してみます。 Microsoft Azure Machine Learning Studio を使用して予測分析ソリューションを作成する方法の例として使用してください。 このソリューションには、Azure Machine Learning Studio と Machine Learning Web サービスを使用します。 

この 3 部構成のチュートリアルでは、まず、公表されている信用リスク データを使用します。  その後、予測モデルを開発してトレーニングします。  最後にそのモデルを Web サービスとしてデプロイします。

[チュートリアルのパート 1](tutorial-part1-credit-risk.md) では、Machine Learning Studio ワークスペースを作成し、データをアップロードし、実験を作成しました。

[チュートリアルのパート 2](tutorial-part2-credit-risk-train.md) では、モデルをトレーニングして評価しました。

チュートリアルのこのパートでは、次のことを行います。

> [!div class="checklist"]
> * デプロイの準備をする
> * Web サービスをデプロイする
> * Web サービスをテストする
> * Web サービスを管理する
> * Web サービスにアクセスする

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>前提条件

[チュートリアルのパート 2](tutorial-part2-credit-risk-train.md) を完了していること。

## <a name="prepare-for-deployment"></a>デプロイの準備をする
このチュートリアルで作成した予測モデルを他のユーザーが使用できるように、Web サービスとして Azure にデプロイできます。

これまでは、モデルのトレーニングを実験してきました。 ただし、デプロイするサービスのトレーニングはこれ以上行われません。サービスは、モデルに基づいてユーザーの入力をスコア付けすることで新しい予測を生成します。 このため、この実験を "***トレーニング***" 実験から "***予測***" 実験に変換するための準備をします。 

デプロイの準備は、3 ステップのプロセスです。  

1. モデルのいずれかを削除する
1. 作成した "*トレーニング実験*" を "*予測実験*" に変換する
1. Web サービスとして予測実験をデプロイする

### <a name="remove-one-of-the-models"></a>モデルのいずれかを削除する

最初に、この実験を少しスリム化する必要があります。 現在、実験には 2 つの異なるモデルがありますが、Web サービスとしてデプロイするサービスでは、モデルは 1 つだけ使用します。  

ブースト ツリー モデルの方が SVM モデルより優れていると判断したものとします。 このため、最初に実行するのは、[2 クラス サポート ベクター マシン][two-class-support-vector-machine] モジュールと、それをトレーニングするために使用したモジュールの削除です。 まずは実験キャンバスの下にある **[名前を付けて保存]** をクリックして実験のコピーを作成します。

次のモジュールを削除する必要があります。  

* [2 クラス サポート ベクター マシン][two-class-support-vector-machine]
* 上記に接続されている[モデルのトレーニング][train-model] モジュールと[モデルのスコア付け][score-model]モジュール
* [データの正規化][normalize-data] (両方)
* [モデルの評価][evaluate-model] (モデルの評価は終了したため)

各モジュールを選択して Delete キーを押すか、モジュールを右クリックして **[削除]** を選択します。 

![サポート ベクター マシン モデルを削除するために、削除するモジュールを強調表示](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

これでモデルは次のようになるはずです。

![サポート ベクター マシン モデルが削除されたときの結果の実験](./media/tutorial-part3-credit-risk-deploy/publish3.png)

これで、[2 クラス ブースト デシジョン ツリー][two-class-boosted-decision-tree]を使用するモデルをデプロイする準備ができました。

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>トレーニング実験を予測実験に変換する

このモデルをデプロイできるよう準備するには、このトレーニング実験を予測実験に変換する必要があります。 これを行うには、3 つの手順を実行します。

1. トレーニングしたモデルを保存し、トレーニング モジュールを置き換えます。
1. 実験をトリミングしてトレーニングのためのみに必要だったモジュールを削除します。
1. Web サービスが入力を受け取る場所と出力を生成する場所を定義します

この 3 つの手順は手動でも実行できますが、幸いにも、実験キャンバスの下部にある **[Set Up Web Service]\(Web サービスの設定\)** をクリックすることですべての手順を完了できます (**[Predictive Web Service]\(予測 Web サービス\)** オプションを選択します)。

> [!TIP]
> トレーニング実験を予測実験に変換するときの動作の詳細については、[Azure Machine Learning Studio でのデプロイのためにモデルを準備する方法](convert-training-experiment-to-scoring-experiment.md)に関するページを参照してください。

**[Web サービスの設定]** をクリックすると、次の動作が行われます。

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

![Scoring the trained model](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> UCI のドイツ語のクレジット カード データ データセットはどうして予測実験に残されたか疑問に思われるでしょう。 このサービスでは、元のデータセットではなくユーザーのデータを使用します。それにもかかわらずなぜ元のデータセットを残すのでしょうか?
> 
> サービスで元のクレジット カード データを必要としないのは本当です。 しかし、列がいくつあり、どの列が数値型であるかなどの情報を含むこのデータのスキーマが必要になります。 このスキーマ情報は、ユーザーのデータを解釈するうえで必要になります。 そこで、これらのコンポーネントを接続したままにして、サービスが実行されているときにスコア付けモジュールがデータセット スキーマを利用できるようにしています。 データは使用されません。スキーマだけが使用されます。  
> 
>注意すべき重要な点の 1 つとして、元のデータセットにラベルが含まれている場合は、Web 入力からの必要なスキーマにもラベル付きの列が必要です。 これを回避するには、Web 入力とトレーニング データセットを共通モジュールに接続する前に、ラベル、およびトレーニング データセットにあった (しかし、Web 入力に含まれることのない) 他のデータを削除します。 
> 

最後にもう一度実験を実行します ( **[実行]** をクリックします)。モデルがまだ機能していることを確認するには、[モデルのスコア付け][score-model]モジュールをクリックし、**[結果の表示]** を選択します。 元のデータが、信用リスク値 ("スコア付けラベル") とスコア付け確率値 ("スコア付け確率") と共に表示されます。 

## <a name="deploy-the-web-service"></a>Web サービスをデプロイする
従来の Web サービスまたは Azure Resource Manager に基づく新しい Web サービスのどちらかとして実験をデプロイできます。

### <a name="deploy-as-a-classic-web-service"></a>従来の Web サービスとしてデプロイする
実験から派生する従来の Web サービスをデプロイするには、キャンバスの下にある **[Web サービスのデプロイ]** をクリックし、**[Deploy Web Service (Classic) (Web サービスのデプロイ [従来])]** を選択します。 Machine Learning Studio によって実験が Web サービスとしてデプロイされ、その Web サービス用のダッシュボードに移動します。 このページから実験に戻って (**[View snapshot]\(スナップショットの表示\)** または **[View latest]\(最新の表示\)** をクリック)、Web サービスの簡単なテストを実行できます (以下の「**Web サービスをテストする**」セクションを参照してください)。 この Web サービスにアクセスできるアプリケーションを作成するための情報も表示されます (詳細はこのチュートリアルの次の手順を参照してください)。

![Web サービス ダッシュボード](./media/tutorial-part3-credit-risk-deploy/publish6.png)


サービスを構成するには、 **[構成]** タブをクリックします。このタブでは、サービス名 (既定で実験名が付けられます) を変更したり、説明を追加したりできます。 また、入力データと出力データにわかりやすいラベルを付けることもできます。  

![Web サービスを構成する](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>新しい Web サービスとしてデプロイする

> [!NOTE] 
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳細については、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」を参照してください。 

実験から派生した新しい Web サービスをデプロイするには:

1. キャンバスの下部の **[Web サービスのデプロイ]** をクリックし、**[Deploy Web Service (New) (Web サービスのデプロイ (新規))]** を選択します。 Machine Learning Studio から Azure Machine Learning Web サービスの **[Deploy Experiment (実験のデプロイ)]** ページに移動します。

1. Web サービスの名前を入力します。 

1. **[料金プラン]** で、既存の価格プランを選択するか、[新規作成] を選択し、新しいプランに名前を付け、月額プラン オプションを選択します。 プラン レベルは既定では既定のリージョンのプランになり、Web サービスはそのリージョンにデプロイされます。

1. **[デプロイ]** をクリックします。

数分後に、Web サービスの **[クイック スタート]** ページが開きます。

**[構成]** タブをクリックしてサービスを構成できます。ここで、サービスのタイトルを変更し、説明を入力できます。 

Web サービスをテストするには、**[テスト]** タブをクリックします (以下の「**Web サービスをテストする**」を参照してください)。 Web サービスにアクセスできるアプリケーションの作成については、**[Consume]\(使用\)** タブをクリックします (詳細については、このチュートリアルの次の手順を参照してください)。

> [!TIP]
> Web サービスをデプロイした後で更新できます。 たとえばモデルを変更する場合は、トレーニング実験を編集し、モデルのパラメーターを調整した後で **[Web サービスのデプロイ]** をクリックし、**[Deploy Web Service (Classic) \(Web サービスのデプロイ (クラシック))]** または **[Deploy Web Service (New) \(Web サービスのデプロイ (新規))]**.を選択します。 もう一度実験をデプロイすると、Web サービスが置き換えられ、更新済みのモデルが使用されるようになります。  
> 
> 

## <a name="test-the-web-service"></a>Web サービスをテストする

Web サービスがアクセスされると、ユーザーのデータは **Web サービスの入力**モジュールに入力され、そこから[モデルのスコア付け][score-model]モジュールに渡されてスコアが付けられます。 設定した予測実験の方法では、モデルのデータは、元の信用リスク データセットと同じ形式であることが期待されています。
結果は、Web サービスから **Web サービスの出力**モジュール経由でユーザーに返されます。

> [!TIP]
> 構成した予測実験の方法では、[モデルのスコア付け][score-model]モジュールから結果全体が返されます。 これには、すべての入力データに加え、信用リスク値とスコア付け確率が含まれます。 ただし、必要に応じて、これとは異なる結果、たとえば信用リスク値のみを返すことができます。 それには、[列の選択][select-columns]モジュールを、[モデルのスコア付け][score-model]と **Web サービスの出力**の間に挿入して、Web サービスから返さない列を排除します。 
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

1. データのセットを入力し、 **[OK]** をクリックします。 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Machine Learning Web サービス ポータルでテストする

1. Web サービスの **[ダッシュボード]** ページで、**[既定のエンドポイント]** の **[Test preview (プレビューのテスト)]** リンクをクリックします。 Azure Machine Learning Web サービス ポータルで、Web サービス エンドポイント用のテスト ページが開き、サービスの入力データが求められます。 これらは、元の信用リスク データセットに含まれるのと同じ列です。

2. **[Test Request-Response (要求応答のテスト)]** をクリックします。 

### <a name="test-a-new-web-service"></a>新しい Web サービスをテストする

新しい Web サービスは、Machine Learning Web サービス ポータルでのみテストできます。

1. [Azure Machine Learning Web サービス](https://services.azureml.net/quickstart) ポータルで、ページ上部の **[テスト]** をクリックします。 **[テスト]** ページが開かれ、サービスのデータを入力することができます。 表示される入力フィールドは、元の信用リスク データセットに含まれる列に対応しています。 

1. データのセットを入力し、 **[Test Request-Response (要求応答のテスト)]** をクリックします。

テストの結果は、ページの右側の出力列に表示されます。 


## <a name="manage-the-web-service"></a>Web サービスを管理する

デプロイした Web サービスは、従来の Web サービスでも新しい Web サービスでも、[Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/quickstart) ポータルから管理できます。

Web サービスのパフォーマンスを監視するには:

1. [Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/quickstart) ポータルにサインインします
1. **[Web サービス]** をクリックします。
1. Web サービスをクリックします
1. **[ダッシュボード]** をクリックします。

## <a name="access-the-web-service"></a>Web サービスにアクセスする

このチュートリアルで前の手順では、信用リスク予測モデルを使用する Web サービスをデプロイしました。 これにより、ユーザーは、Web サービスにデータを送信し、結果を受信できます。 

Web サービスは、次の 2 つの方法のいずれかで、REST API を使用してデータを受信して返すことができる Azure Web サービスです。  

* **要求/応答** - ユーザーが HTTP プロトコルを使用して 1 行以上のクレジット データをサービスに送信し、サービスが 1 つ以上の結果のセットを返します。
* **バッチ実行** - ユーザーが Azure BLOB に 1 行以上のクレジット データを格納し、そのBLOB の場所をサービスに送信します。 サービスは、入力 BLOB 内のデータのすべての行にスコアを付け、結果を別の BLOB に格納し、この BLOB のURL を返します。  

従来の Web サービスにアクセスする最も迅速で簡単な方法は、[Azure ML 要求応答サービス Web アプリ](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)または[Azure ML バッチ実行サービス Web アプリ テンプレート](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)を使用することです。

このような Web アプリ テンプレートを使用すると、Web サービスの入力データとサービスで返される内容を認識するカスタム Web アプリを構築できます。 必要な操作は、Web サービスおよびデータへのアクセスを許可することだけで、後の処理はテンプレートによって行われます。

Web アプリ テンプレートの使用方法の詳細については、「[Web アプリケーション テンプレートによる Azure Machine Learning Web サービスの使用](/azure/machine-learning/studio/consume-web-services)」をご覧ください。



## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を完了しました。

> [!div class="checklist"]
> * デプロイの準備をする
> * Web サービスをデプロイする
> * Web サービスをテストする
> * Web サービスを管理する
> * Web サービスにアクセスする

R、C#、Python プログラミング言語で提供されるスターター コードを使用して、Web サービスにアクセスするカスタム アプリケーションを開発することもできます。

> [!div class="nextstepaction"]
> [Azure Machine Learning Web サービスを使用する](consume-web-services.md)

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
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
