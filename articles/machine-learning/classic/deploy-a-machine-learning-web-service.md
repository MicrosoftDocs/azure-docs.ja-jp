---
title: 'ML Studio (classic): Web サービスをデプロイする - Azure'
description: トレーニング実験を予測実験に変換し、そのデプロイを準備してから、Azure Machine Learning Studio (クラシック) Web サービスとしてデプロイする方法。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 018e292abc7050f5d71c6bf9065618fecf8b5668
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520444"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Azure Machine Learning Studio (クラシック) Web サービスをデプロイする

**適用対象:** ![これはチェック マークです。つまり、この記事は Machine Learning Studio (クラシック) を対象としています。](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (クラシック)   ![これは X 印です。つまり、この記事は Azure Machine Learning を対象としています。](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Azure Machine Learning Studio (クラシック) を使用すると、予測分析ソリューションを構築してテストできます。 その後、Web サービスとしてソリューションをデプロイできます。

Machine Learning Studio (クラシック) Web サービスは、アプリケーションと Machine Learning Studio (クラシック) ワークフローのスコア付けモデルの間のインターフェイスを提供します。 外部のアプリケーションは、Machine Learning Studio (クラシック) ワークフローのスコア付けモデルとリアルタイムに通信できます。 Machine Learning Studio (クラシック) Web サービスを呼び出すと、予測結果が外部のアプリケーションに返されます。 Web サービスの呼び出しを実行するには、Web サービスのデプロイ時に作成された API キーを渡します。 Machine Learning Studio (クラシック) Web サービスは、Web プログラミング プロジェクトでの一般的なアーキテクチャ選択である REST に基づいています。

Azure Machine Learning Studio (クラシック) には、2 種類の Web サービスがあります。

* 要求応答サービス (RRS):待ち時間が短くスケーラビリティが高いサービス。1 つのデータ レコードをスコア付けします。
* バッチ実行サービス (BES):非同期のサービス。データ レコードのバッチをスコア付けします。

BES への入力は、RRS で使用されるデータ入力と似ています。 主な違いは、BES が、Azure Blob Storage、Azure Table Storage、Azure SQL Database、HDInsight (Hive クエリ)、HTTP ソースなど、さまざまなソースからレコードのブロックを読み取る点です。

モデルのデプロイは、大まかに次の 3 つの手順で行われます。

* **[トレーニング実験を作成する]** - Studio (クラシック) では、供給するトレーニング データを活用した予測分析モデルを、大規模な組み込みの機械学習アルゴリズムを使用して、トレーニングおよびテストできます。
* **[予測実験に変換する]** - 既存のデータでモデルが学習され、それを使用して新しいデータをスコア付けする準備ができると、予測用に実験を用意し、合理化します。
* これを **[新しい Web サービス]** または **[従来の Web サービス]** として **デプロイ** する - Azure Web サービスとして予測実験をデプロイすると、ユーザーはデータをモデルに送信して、モデルの予測を受信できるようになります。

## <a name="create-a-training-experiment"></a>トレーニング実験を作成する

予測分析モデルをトレーニングするには、Azure Machine Learning Studio (クラシック) を使用してトレーニング実験を作成します。この実験には、トレーニング データを読み込み、必要に応じてデータを用意し、機械学習アルゴリズムを適用して結果を評価するための、さまざまなモジュールを含めることができます。 実験を繰り返し、別の機械学習アルゴリズムを試して結果を比較したり評価したりできます。

トレーニング実験を作成したり管理したりするプロセスはあらゆる場所でより包括的に使用できます。 詳細と例については、次の記事をご覧ください。

* [Azure Machine Learning Studio (クラシック) で単純な実験を作成する](create-experiment.md)
* [Azure Machine Learning Studio (クラシック) で予測ソリューションを開発する](tutorial-part1-credit-risk.md)
* [Azure Machine Learning Studio (クラシック) にトレーニング データをインポートする](import-data.md)
* [Azure Machine Learning Studio (クラシック) で実験イテレーションを管理する](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>トレーニング実験を予測実験に変換する

モデルのトレーニングが終了したら、トレーニング実験を予測実験に変換して、新しいデータのスコア付けを行うことができます。

予測実験に変換することで、トレーニングされたモデルをスコア付け Web サービスとしてデプロイできるようになります。 Web サービスのユーザーがモデルに入力データを送信し、モデルは予測結果を返します。 予測実験を変換する際は、モデルが他のユーザーに使用されることを想定しておいてください。

トレーニング実験を予測実験に変換するプロセスには、次の 3 つの手順が含まれます。

1. 機械学習アルゴリズムのモジュールをトレーニング済みのモデルに置き換えます。
2. スコア付けに必要なモジュールのみに実験を絞り込みます。 トレーニング実験にはトレーニングに必要な多数のモジュールが含まれますが、モデルがトレーニングされると、それらのモジュールは不要になります。
3. モデルが Web サービス ユーザーからのデータを受け入れる方法と、どのデータを返すかを定義します。

> [!TIP]
> トレーニング実験では、独自のデータを使用してモデルのトレーニングとスコア付けに取り組みました。 ただしいったんデプロイすると、ユーザーから新しいデータがモデルに送信され、モデルは予測結果を返します。 このため、トレーニング実験を予測実験に変換してデプロイの準備をするときには、モデルが他のユーザーにどのように使用されるかに留意してください。

![スコア付け実験に変換する](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>[Web サービスの設定] ボタン
(実験キャンバスの下部にある **[実行]** をクリックして) 実験を実行したら、(**[予測 Web サービス]** オプションを選択して)**[Web サービスの設定]** ボタンをクリックします。 **[Web サービスの設定]** によって、トレーニング実験を予測実験に変換する次の 3 つの手順が実行されます。

1. トレーニング済みのモデルを、(実験キャンバスの左側にある) モジュール パレットの **[トレーニング済みのモデル]** セクションに保存します。 次に、機械学習アルゴリズムと[モデルのトレーニング][train-model] モジュールを、保存したトレーニング済みのモデルに置き換えます。
2. 実験が分析され、明らかにトレーニングのためにのみ使用されて不要になったモジュールが削除されます。
3. _Web サービスの入力_ および _出力_ モジュールを、実験の既定の場所に挿入します (これらのモジュールはユーザー データを受け入れて返します)。

たとえば、次の実験では、サンプルの国勢調査のデータを使用した 2 クラスのブースト デシジョン ツリー モデルをトレーニングします。

![トレーニング実験](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

この実験のモジュールは、基本的に 4 つの異なる機能を実行します。

![モジュール関数](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

トレーニング実験を予測実験に変換すると、これらのモジュールの一部は不要になったり、別の役割を果たすようになります。

* **データ**- このサンプル データセット内のデータは、スコア付け中には使用されません。Web サービスのユーザーは、スコア付けするデータを指定します。 ただし、データ型など、このデータセットからのメタデータは、トレーニング済みのモデルで使用されます。 このため、このメタデータを提供できるように、予測実験のデータセットを保持する必要があります。

* **準備** -スコア付けのために送信されるユーザー データによっては、これらのモジュールが受信データの処理に必要な場合と必要でない場合があります。 **[Web サービスの設定]** ボタンではこれらは処理されないため、ユーザーがどのように処理するかを決める必要があります。
  
    たとえばこの例では、サンプル データセットで値が不足している可能性があるため、これに対処するために[見つからないデータのクリーンアップ][clean-missing-data] モジュールが含まれています。 また、サンプル データセットには、モデルのトレーニングには不要な列が含まれています。 このため、これらの余分な列をデータ フローから除外するための[データセット内の列の選択][select-columns]モジュールが含まれています。 Web サービスを通じてスコア付けのために送信されるデータに不足している値がないことがわかっている場合は、[見つからないデータのクリーンアップ][clean-missing-data] モジュールを削除できます。 ただし、[データセット内の列の選択][select-columns]モジュールは、トレーニング済みのモデルが必要とするデータ列の定義に役立つため、このモジュールは残す必要があります。

* **トレーニング** - これらのモジュールは、モデルのトレーニングに使用します。 **[Web サービスの設定]** をクリックすると、これらのモジュールはトレーニング済みのモデルを含む 1 つのモジュールに置き換えられます。 この新しいモジュールは、モジュール パレットの **[Trained Models (トレーニング済みのモデル)]** セクションに保存されます。

* **スコア** - この例では、データ ストリームをテスト データとトレーニング データに分割するために、[データの分割][split]モジュールが使用されます。 予測実験ではもうトレーニングを行わないため、[データの分割][split]を削除できます。 同様に、2 番目の[モデルのスコア付け][score-model]モジュールと[モデルの評価][evaluate-model]モジュールは、テスト データの結果を比較するために使用されるため、これらのモジュールも予測実験では不要です。 ただし、残りの[モデルのスコア付け][score-model]モジュールは、Web サービスを通じてスコア結果を返すために必要です。

**[Web サービスの設定]** をクリックした後で、この例がどのようになるのかを次に示します。

![変換された予測実験](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

**[Web サービスの設定]** によって実行された作業は、実験を Web サービスとしてデプロイするための準備として十分な場合があります。 ただし、実験に合わせて追加の作業を実行する可能性があります。

#### <a name="adjust-input-and-output-modules"></a>入力と出力のモジュールを調整する
トレーニング実験では、トレーニング データのセットを使用して、機械学習アルゴリズムが必要とする形式でデータを取得するいくつかの処理を実行しました。 Web サービスを通じて受信することを想定しているデータでこの処理が不要な場合は、**Web サービスの入力モジュール** の出力を実験の別のノードに接続することで、これを回避できます。 この場所のモデルにユーザーのデータが届くようになります。

たとえば、既定では、上の図で示すように、**[Web サービスの設定]** によって、**Web サービスの入力** モジュールがデータ フローの上部に配置されます。 ただし、**Web サービスの入力** をデータ処理モジュールの先に手動で配置することができます。

![Web サービス入力の移動](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Web サービスを通じて提供される入力データが、これで処理なしでモデルのスコア付けモジュールに直接渡されるようになりました。

同様に、既定では **Web サービスの設定** によって、Web サービスの出力モジュールがデータ フローの下部に置かれます。 この例では、Web サービスはユーザーに、完全な入力データのベクトルとスコア付けの結果を含む[モデルのスコア付け][score-model]モジュールの出力を返します。
ただし、別のものを返したい場合は、**Web サービスの出力** モジュールの前に別のモジュールを追加することができます。 

たとえば、入力データのベクトル全体ではなく、スコア付けの結果のみを返すようにする場合は、[データセット内の列の選択][select-columns]モジュールを追加して、スコア付けの結果以外のすべての列を除外できます。 次に、**Web サービスの出力** モジュールを [データセット内の列の選択][select-columns]モジュールの出力に移動します。 実験は次のようになります。

![Web サービス出力の移動](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>データ処理モジュールを追加または削除する
スコア付け中に必要ないことがわかっているモジュールが、他にも実験に存在する場合は、それらを削除できます。 たとえば、**Web サービスの入力** モジュールをデータ処理モジュールの後の地点に移動したため、[見つからないデータのクリーンアップ][clean-missing-data] モジュールを予測実験から削除できます。

これで、予測実験が次のようになります。

![その他のモジュールの取り外し](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>オプションの Web サービス パラメーターを追加する
場合によっては、Web サービスのユーザーがサービスにアクセスしたときに、モジュールの動作を変更できるようにすることもあります。 *Web サービスのパラメーター* を使えば変更できます。

一般的な例として、デプロイされた Web サービスのユーザーが Web サービスにアクセスしたときに別のデータ ソースを指定できるように、[データのインポート][import-data] モジュールを設定する場合があります。 または、別の宛先を指定できるように[データのエクスポート][export-data] モジュールを構成します。

Web サービスのパラメーターを定義して、1 つまたは複数のモジュール パラメーターに関連付け、必須かオプションかを指定することができます。 Web サービスのユーザーは、サービスにアクセスするときにこれらのパラメーターの値を指定することができ、モジュールの動作はそれに応じて変更されます。

Web サービス パラメーターとその使用方法の詳細については、「[Azure Machine Learning Web サービスのパラメーターの使用][webserviceparameters]」を参照してください。

次の手順では、新しい Web サービスとして予測実験をデプロイする方法を説明します。 実験は、従来の Web サービスとしてデプロイすることもできます。

## <a name="deploy-it-as-a-new-web-service"></a>新しい Web サービスとしてデプロイする

予測実験の準備ができたので、新しい (Resource Manager ベースの) Azure Web サービスとしてこれをデプロイできます。 Web サービスを使用してユーザーはデータをモデルに送信でき、モデルは予測を返します。

予測実験をデプロイするには、実験キャンバスの下部にある **[実行]** をクリックします。 実験の実行が終了したら **[Web サービスのデプロイ]** をクリックし、**Web サービスのデプロイ [新規]** を選択します。  Machine Learning Studio (クラシック) Web サービス ポータルのデプロイ ページが開きます。

> [!NOTE] 
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳しくは、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」をご覧ください。 

### <a name="web-service-portal-deploy-experiment-page"></a>Web サービス ポータルの [実験のデプロイ] ページ

[Deploy Experiment (実験のデプロイ]) ページで、Web サービスの名前を入力します。
料金プランを選択します。 既存の料金プランがある場合はそのプランを選択できます。ない場合は、サービス用に新しい料金プランを作成する必要があります。

1. **[Price Plan (料金プラン)]** ドロップダウンで、既存のプランを選択するか、 **[Select new plan (新しいプランを選択する)]** オプションを選択します。
2. **[プラン名]** に、請求書でプランを識別する名前を入力します。
3. **[Monthly Plan Tiers (月額プラン レベル)]** のいずれか 1 つを選択します。 プラン レベルは既定では既定のリージョンのプランになり、Web サービスはそのリージョンにデプロイされます。

**[デプロイ]** をクリックすると、Web サービスの **[クイック スタート]** ページが開きます。

Web サービスの [クイック スタート] ページでは、新しい Web サービスの作成後に実行するほとんどの一般的なタスクにアクセスし、そのガイダンスを得ることができます。 ここから、[テスト] ページと [Consume (使用)] ページの両方に簡単にアクセスできます。

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>新しい Web サービスをテストする

新しい Web サービスをテストするには、一般的なタスクの下の **[Test web service (Web サービスのテスト)]** をクリックします。 [テスト] ページでは、Web サービスを要求応答サービス (RRS) またはバッチ実行サービス (BES) としてテストできます。

RRS テストのページには、入力、出力、および実験用に定義したすべてのグローバル パラメーターが表示されます。 Web サービスをテストするには、手動で適切な入力値を入力するか、テスト値を含むコンマ区切り値 (CSV) の書式設定されたファイルを指定できます。

RRS を使用してテストするには、リスト ビュー モードから適切な入力値を入力し、 **[Test Request-Response (要求応答のテスト)]** をクリックします。 予測結果は、左側の出力列に表示されます。

![ご自分の Web サービスをテストするための適切な値を入力する](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

BES をテストするには、 **[バッチ]** をクリックします。 バッチ テストのページで、入力の下の [参照] をクリックし、適切なサンプル値を含む CSV ファイルを選択します。 CSV ファイルがなく、Machine Learning Studio (クラシック) を使用して予測実験を作成した場合は、予測実験用のデータ セットをダウンロードし、それを使用できます。

データ セットをダウンロードするには、Machine Learning Studio (クラシック) を開きます。 予測実験を開き、実験の入力を右クリックします。 コンテキスト メニューの **[データセット]** を選択し、 **[ダウンロード]** を選択します。

![Studio (クラシック) キャンバスからデータ セットをダウンロードする](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

**[Test]** をクリックします。 バッチ実行ジョブの進行状況は、 **[Test Batch Jobs (バッチ ジョブのテスト)]** の右下に表示されます。

![Web サービス ポータルを使用してご自分のバッチ実行ジョブをテストする](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

**[構成]** ページでは、Web サービスの説明やタイトルの変更、ストレージ アカウント キーの更新、およびサンプル データの有効化を行うことができます。

![ご自分の Web サービスを構成する](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>新しい Web サービスにアクセスする

Machine Learning Studio (クラシック) から Web サービスをデプロイしたら、プログラムでそのサービスにデータを送信したり、応答を受信したりできます。

**[Consume (使用)]** ページでは、Web サービスへのアクセスに必要なすべての情報が提供されます。 たとえば、API キーは、サービスへの承認済みアクセスを許可するために提供されます。

Machine Learning Studio (クラシック) Web サービスへのアクセスの詳細については、[Azure Machine Learning Studio (クラシック) Web サービスを使用する方法](consume-web-services.md)に関するページを参照してください。

### <a name="manage-your-new-web-service"></a>新しい Web サービスを管理する

Machine Learning Studio (クラシック) Web サービス ポータルを使用して新しい Web サービスを管理できます。 [メイン ポータル ページ](https://services.azureml.net/) で **[Web サービス]** をクリックします。 Web サービスのページでは、サービスを削除したり、コピーしたりすることができます。 特定のサービスを監視するには、サービスをクリックし、 **[ダッシュボード]** をクリックします。 Web サービスに関連付けられたバッチ ジョブを監視するには、 **[Batch Request Log (バッチ要求ログ)]** をクリックします。

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a>複数のリージョンへの新しい Web サービスのデプロイ

複数のサブスクリプションやワークスペースがなくても、複数のリージョンに新しい Web サービスを簡単にデプロイできます。

料金は、リージョンに固有なので、Web サービスをデプロイするリージョンごとに課金プランを定義する必要があります。

#### <a name="create-a-plan-in-another-region"></a>別のリージョンにプランを作成する

1. [Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/)にサインインします。
2. **[プラン]** メニュー オプションをクリックします。
3. [プラン] 概要ページで **新規** をクリックします。
4. **[サブスクリプション]** ドロップダウンで、新しいプランを配置するサブスクリプションを選択します。
5. **[地域]** ドロップダウンで、新しいプランのリージョンを選択します。 選択したリージョンのプラン オプションが、ページの **[Plan Options (プラン オプション)]** セクションに表示されます。
6. **[リソース グループ]** ドロップダウンで、プランのリソース グループを選択します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/management/overview.md)」をご覧ください。
7. **[プラン名]** にプランの名前を入力します。
8. **[Plan Options (プラン オプション)]** で、新しいプランの課金レベルをクリックします。
9. **Create** をクリックしてください。

#### <a name="deploy-the-web-service-to-another-region"></a>別のリージョンに Web サービスをデプロイする

1. Microsoft Azure Machine Learning Web サービスのページで、 **[Web サービス]** メニュー オプションをクリックします。
2. 新しいリージョンにデプロイする Web サービスを選択します。
3. **[コピー]** をクリックします。
4. **[Web サービス名]** に Web サービスの新しい名前を入力します。
5. **[Web service description (Web サービスの説明)]** に Web サービスの説明を入力します。
6. **[サブスクリプション]** ドロップダウンで、新しい Web サービスを配置するサブスクリプションを選択します。
7. **[リソース グループ]** ドロップダウンで、Web サービスのリソース グループを選択します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/management/overview.md)」をご覧ください。
8. **[地域]** ドロップダウンで、Web サービスをデプロイするリージョンを選択します。
9. **[ストレージ アカウント]** ドロップダウンで、Web サービスを格納するためのストレージ アカウントを選択します。
10. **[Price Plan (価格プラン)]** ドロップダウンで、手順 8 で選択したリージョンのプランを選択します。
11. **[コピー]** をクリックします。

## <a name="deploy-it-as-a-classic-web-service"></a>従来の Web サービスとしてデプロイする

予測実験の準備を十分に実行したので、従来の Azure Web サービスとしてデプロイできます。 Web サービスを使用してユーザーはデータをモデルに送信でき、モデルは予測を返します。

予測実験をデプロイするには、実験キャンバスの下部にある **[実行]** をクリックし、 **[Web サービスのデプロイ]** をクリックします。 Web サービスがセットアップされ、Web サービスのダッシュ ボードに配置されます。

![Studio (クラシック) から Web サービスをデプロイする](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>従来の Web サービスをテストする

Web サービスは、Machine Learning Studio (クラシック) Web サービス ポータルまたは Machine Learning Studio (クラシック) のどちらかでテストできます。

要求 - 応答 Web サービスをテストするには、Web サービス ダッシュボードで **[テスト]** をクリックします。 ダイアログ ボックスが表示され、サービスへのデータの入力が促されます。 これらはスコア付け実験で想定される列になります。 データのセットを入力し、 **[OK]** をクリックします。 Web サービスによって生成された結果がダッシュボードの下部に表示されます。

「新しい Web サービス」セクションで説明したように、**[テスト]** プレビュー リンクをクリックして、サービスを Azure Machine Learning Studio (クラシック) Web サービス ポータルでテストできます。

バッチ実行サービスをテストするには、 **[テスト]** プレビュー リンクをクリックします。 バッチ テストのページで、入力の下の [参照] をクリックし、適切なサンプル値を含む CSV ファイルを選択します。 CSV ファイルがなく、Machine Learning Studio (クラシック) を使用して予測実験を作成した場合は、予測実験用のデータ セットをダウンロードし、それを使用できます。

![Web サービスをテストする](./media/publish-a-machine-learning-web-service/figure-3.png)

**[構成]** ページで、サービスの表示名を変更したり、説明を付けたりできます。 この名前と説明は、Web サービスを管理する [Azure Portal](https://portal.azure.com/) に表示されます。

**INPUT SCHEMA**、**OUTPUT SCHEMA**、**Web SERVICE PARAMETER** の各列に文字列を入力し、入力データ、出力データ、Web サービス パラメーターの説明を追加できます。 これらの説明は、Web サービスのサンプル コードのドキュメントで使用されます。

ログ記録を有効にすれば、Web サービスのアクセスで発生するすべてのエラーを診断できます。 詳細については、[Machine Learning Studio (クラシック) Web サービスのログ記録の有効化](web-services-logging.md)に関するページを参照してください。

![Web サービス ポータル内でログ記録を有効にする](./media/publish-a-machine-learning-web-service/figure-4.png)

「新しい Web サービス」セクションで説明した手順と同様に、Web サービスのエンドポイントを Azure Machine Learning Web サービス ポータルで構成することもできます。 オプションは異なりますが、サービスの説明の追加または変更、ログの有効化、テスト用サンプル データの有効化を行うことができます。

### <a name="access-your-classic-web-service"></a>従来の Web サービスにアクセスする

Azure Machine Learning Studio (クラシック) から Web サービスをデプロイしたら、プログラムでそのサービスにデータを送信したり、応答を受信したりできます。

ダッシュ ボードでは、Web サービスへのアクセスが必要なすべての情報が提供されます。 たとえば、サービスへの承認済みアクセスを許可する API キーや、コードの記述に役立つ API のヘルプ ページが提供されます。

Machine Learning Studio (クラシック) Web サービスへのアクセスの詳細については、[Azure Machine Learning Studio (クラシック) Web サービスを使用する方法](consume-web-services.md)に関するページを参照してください。

### <a name="manage-your-classic-web-service"></a>従来の Web サービスを管理する

Web サービスを監視するために実行できるさまざまな操作があります。 更新および削除することもできます。 デプロイ時に作成される既定のエンドポイントに加え、従来の Web サービスにさらにエンドポイントを追加することもできます。

詳細については、[Azure Machine Learning Studio (クラシック) ワークスペースの管理](manage-workspace.md)および [Azure Machine Learning Studio (クラシック) Web サービス ポータルを使用した Web サービスの管理](manage-new-webservice.md)に関するページを参照してください。

## <a name="update-the-web-service"></a>Web サービスを更新する
別のトレーニング データでのモデルの更新など、Web サービスに変更を加えたり、元の Web サービスを上書きしてもう一度デプロイしたりできます。

Web サービスを更新するには、Web サービスのデプロイに使用した元の予測実験を開き、 **[名前を付けて保存]** をクリックして編集可能なコピーを作成します。 必要な変更を加えて **[WEB サービスのデプロイ]** をクリックします。

この実験は前にデプロイ済みであるため、既存のサービスを上書きするか (従来の Web サービス)、更新するか (新しい Web サービス) 確認されます。 **[はい]** または **[更新]** をクリックすると、既存の Web サービスが停止され、新しい予測実験が代わりにデプロイされます。

> [!NOTE]
> たとえば元の Web サービスの構成に変更を加えた場合や新しい表示名または説明を入力した場合、それらの値をもう一度入力する必要が生じます。

Web サービスを更新するオプションの 1 つに、モデルをプログラムによって再トレーニングする方法があります。 詳細については、[プログラムによる Machine Learning Studio (クラシック) のモデルの再トレーニング](./retrain-machine-learning-model.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* デプロイのしくみに関する技術的な詳細については、[Machine Learning Studio (クラシック) モデルが実験から運用可能な Web サービスになるまでの過程](model-progression-experiment-to-web-service.md)に関するページを参照してください。

* デプロイのためにモデルを準備する方法の詳細については、[Azure Machine Learning Studio (クラシック) でのデプロイのためにモデルを準備する方法](deploy-a-machine-learning-web-service.md)に関するページを参照してください。

* REST API を使用して Web サービスにアクセスするには、いくつかの方法があります。 [Azure Machine Learning Studio (クラシック) Web サービスを使用する方法](consume-web-services.md)に関するページを参照してください。

<!-- internal links -->
[トレーニング実験を作成する]: #create-a-training-experiment
[予測実験に変換する]: #convert-the-training-experiment-to-a-predictive-experiment
[新しい Web サービス]: #deploy-it-as-a-new-web-service
[従来の Web サービス]: #deploy-it-as-a-classic-web-service
[新規]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data