---
title: Machine Learning Studio Web サービスをデプロイする
titleSuffix: Azure Machine Learning Studio
description: トレーニング実験を予測実験に変換する方法、デプロイメントの準備をする方法、Azure Machine Learning Studio Web サービスとしてデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 2ffc9055f23b8221a6f711f741b6146545ff0821
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895731"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service"></a>Azure Machine Learning Studio Web サービスをデプロイする

Azure Machine Learning Studio では、予測分析ソリューションをビルドしてテストできます。 その後、Web サービスとしてソリューションをデプロイできます。

Machine Learning Studio Web サービスは、アプリケーションと Machine Learning Studio ワークフローのスコア付けモデルの間のインターフェイスを提供します。 外部のアプリケーションは、Machine Learning Studio ワークフローのスコア付けモデルとリアルタイムで通信できます。 Machine Learning Studio Web サービスを呼び出すと、予測結果が外部のアプリケーションに返されます。 Web サービスの呼び出しを実行するには、Web サービスのデプロイ時に作成された API キーを渡します。 Machine Learning Studio Web サービスは、Web プログラミング プロジェクトで広く使われているアーキテクチャの REST に基づいています。

Azure Machine Learning Studio には、2 種類の Web サービスがあります。

* 要求応答サービス (RRS):待ち時間が短くスケーラビリティが高いサービス。1 つのデータ レコードをスコア付けします。
* バッチ実行サービス (BES):非同期のサービス。データ レコードのバッチをスコア付けします。

BES への入力は、RRS で使用されるデータ入力と似ています。 主な違いは、BES が、Azure Blob Storage、Azure Table Storage、Azure SQL Database、HDInsight (Hive クエリ)、HTTP ソースなど、さまざまなソースからレコードのブロックを読み取る点です。

モデルのデプロイは、大まかに次の 3 つの手順で行われます。

* **[トレーニング実験を作成する]** - Studio では、供給するトレーニング データを活用した予測分析モデルを、大規模な組み込みの機械学習アルゴリズムを使用して、トレーニングおよびテストできます。
* **[予測実験に変換する]** - 既存のデータでモデルが学習され、それを使用して新しいデータをスコア付けする準備ができると、予測用に実験を用意し、合理化します。
* これを**[新しい Web サービス]** または**[従来の Web サービス]** として**デプロイ**する - Azure Web サービスとして予測実験をデプロイすると、ユーザーはデータをモデルに送信して、モデルの予測を受信できるようになります。

## <a name="create-a-training-experiment"></a>トレーニング実験を作成する

予測分析モデルを学習するには、Azure Machine Learning Studio を使用してトレーニング実験を作成します。ここではトレーニング データを読み込み、必要に応じてデータを用意し、機械学習アルゴリズムに適用し、結果を評価するさまざまなモジュールを含めることができます。 実験を繰り返し、別の機械学習アルゴリズムを試して結果を比較したり評価したりできます。

トレーニング実験を作成したり管理したりするプロセスはあらゆる場所でより包括的に使用できます。 詳細と例については、次の記事をご覧ください。

* [Azure Machine Learning Studio での簡単な実験の作成](create-experiment.md)
* [Azure Machine Learning Studio を使用した予測ソリューションの開発](tutorial-part1-credit-risk.md)
* [Azure Machine Learning Studio への学習データのインポート](import-data.md)
* [Azure Machine Learning Studio での繰り返し学習の管理](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>トレーニング実験を予測実験に変換する

モデルのトレーニングが終了したら、トレーニング実験を予測実験に変換して、新しいデータのスコア付けを行うことができます。

予測実験に変換することで、トレーニングされたモデルをスコア付け Web サービスとしてデプロイできるようになります。 Web サービスのユーザーがモデルに入力データを送信し、モデルは予測結果を返します。 予測実験を変換する際は、モデルが他のユーザーに使用されることを想定しておいてください。

トレーニング実験を予測実験に変換するには、実験キャンバスの下部にある **[実行]** をクリックし、**[Web サービスの設定]** をクリックして、**[予測 Web サービス]** を選択します。

![スコア付け実験に変換する](./media/publish-a-machine-learning-web-service/figure-1.png)

この変換を実行する方法については、[Azure Machine Learning Studio でのデプロイのためにモデルを準備する方法](convert-training-experiment-to-scoring-experiment.md)に関するページを参照してください。

次の手順では、新しい Web サービスとして予測実験をデプロイする方法を説明します。 実験は、従来の Web サービスとしてデプロイすることもできます。

## <a name="deploy-it-as-a-new-web-service"></a>新しい Web サービスとしてデプロイする

予測実験の準備ができたので、新しい (Resource Manager ベースの) Azure Web サービスとしてこれをデプロイできます。 Web サービスを使用してユーザーはデータをモデルに送信でき、モデルは予測を返します。

予測実験をデプロイするには、実験キャンバスの下部にある **[実行]** をクリックします。 実験の実行が終了したら **[Web サービスのデプロイ]** をクリックし、**Web サービスのデプロイ [新規]** を選択します。  Machine Learning Studio Web サービス ポータルの [デプロイ] ページが開きます。

> [!NOTE] 
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳しくは、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」をご覧ください。 

### <a name="machine-learning-studio-web-service-portal-deploy-experiment-page"></a>Machine Learning Web サービス ポータルの [Deploy Experiment (実験のデプロイ)] ページ

[Deploy Experiment (実験のデプロイ]) ページで、Web サービスの名前を入力します。
料金プランを選択します。 既存の料金プランがある場合はそのプランを選択できます。ない場合は、サービス用に新しい料金プランを作成する必要があります。

1. **[Price Plan (料金プラン)]** ドロップダウンで、既存のプランを選択するか、**[Select new plan (新しいプランを選択する)]** オプションを選択します。
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

BES をテストするには、 **[バッチ]** をクリックします。 バッチ テストのページで、入力の下の [参照] をクリックし、適切なサンプル値を含む CSV ファイルを選択します。 CSV ファイルがなく、Machine Learning Studio を使用して予測実験を作成した場合は、予測実験用のデータ セットをダウンロードし、それを使用できます。

データ セットをダウンロードするには、Machine Learning Studio を開きます。 予測実験を開き、実験の入力を右クリックします。 コンテキスト メニューの **[データセット]** を選択し、**[ダウンロード]** を選択します。

![Studio キャンバスからデータセットをダウンロードする](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

**[Test]** をクリックします。 バッチ実行ジョブの進行状況は、**[Test Batch Jobs (バッチ ジョブのテスト)]** の右下に表示されます。

![Web サービス ポータルを使用してご自分のバッチ実行ジョブをテストする](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

**[構成]** ページでは、Web サービスの説明やタイトルの変更、ストレージ アカウント キーの更新、およびサンプル データの有効化を行うことができます。

![ご自分の Web サービスを構成する](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>新しい Web サービスにアクセスする

Machine Learning Studio から Web サービスをデプロイすると、サービスにデータを送信し、プログラムからの応答が得られます。

**[Consume (使用)]** ページでは、Web サービスへのアクセスに必要なすべての情報が提供されます。 たとえば、API キーは、サービスへの承認済みアクセスを許可するために提供されます。

Machine Learning Studio Web サービスへのアクセスの詳細については、「[Azure Machine Learning Studio Web サービスを使用する方法](consume-web-services.md)」を参照してください。

### <a name="manage-your-new-web-service"></a>新しい Web サービスを管理する

新しい Web サービスの Machine Learning Studio Web サービス ポータルを管理することができます。 [メイン ポータル ページ](https://services.azureml-test.net/) で **[Web サービス]** をクリックします。 Web サービスのページでは、サービスを削除したり、コピーしたりすることができます。 特定のサービスを監視するには、サービスをクリックし、 **[ダッシュボード]** をクリックします。 Web サービスに関連付けられたバッチ ジョブを監視するには、 **[Batch Request Log (バッチ要求ログ)]** をクリックします。

### <a id="multi-region"></a>複数のリージョンへの新しい Web サービスのデプロイ

複数のサブスクリプションやワークスペースがなくても、複数のリージョンに新しい Web サービスを簡単にデプロイできます。

料金は、リージョンに固有なので、Web サービスをデプロイするリージョンごとに課金プランを定義する必要があります。

#### <a name="create-a-plan-in-another-region"></a>別のリージョンにプランを作成する

1. [Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/)にサインインします。
2. **[プラン]** メニュー オプションをクリックします。
3. [プラン] 概要ページで **[新規]** をクリックします。
4. **[サブスクリプション]** ドロップダウンで、新しいプランを配置するサブスクリプションを選択します。
5. **[地域]** ドロップダウンで、新しいプランのリージョンを選択します。 選択したリージョンのプラン オプションが、ページの **[Plan Options (プラン オプション)]** セクションに表示されます。
6. **[リソース グループ]** ドロップダウンで、プランのリソース グループを選択します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md)」をご覧ください。
7. **[プラン名]** にプランの名前を入力します。
8. **[Plan Options (プラン オプション)]** で、新しいプランの課金レベルをクリックします。
9. **Create** をクリックしてください。

#### <a name="deploy-the-web-service-to-another-region"></a>別のリージョンに Web サービスをデプロイする

1. Microsoft Azure Machine Learning Web サービスのページで、**[Web サービス]** メニュー オプションをクリックします。
2. 新しいリージョンにデプロイする Web サービスを選択します。
3. **[コピー]** をクリックします。
4. **[Web サービス名]** に Web サービスの新しい名前を入力します。
5. **[Web service description (Web サービスの説明)]** に Web サービスの説明を入力します。
6. **[サブスクリプション]** ドロップダウンで、新しい Web サービスを配置するサブスクリプションを選択します。
7. **[リソース グループ]** ドロップダウンで、Web サービスのリソース グループを選択します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md)」をご覧ください。
8. **[地域]** ドロップダウンで、Web サービスをデプロイするリージョンを選択します。
9. **[ストレージ アカウント]** ドロップダウンで、Web サービスを格納するためのストレージ アカウントを選択します。
10. **[Price Plan (価格プラン)]** ドロップダウンで、手順 8 で選択したリージョンのプランを選択します。
11. **[コピー]** をクリックします。

## <a name="deploy-it-as-a-classic-web-service"></a>従来の Web サービスとしてデプロイする

予測実験の準備を十分に実行したので、従来の Azure Web サービスとしてデプロイできます。 Web サービスを使用してユーザーはデータをモデルに送信でき、モデルは予測を返します。

予測実験をデプロイするには、実験キャンバスの下部にある **[実行]** をクリックし、**[Web サービスのデプロイ]** をクリックします。 Web サービスがセットアップされ、Web サービスのダッシュ ボードに配置されます。

![Studio からご自分の Web サービスをデプロイする](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>従来の Web サービスをテストする

Web サービスは、Machine Learning Studio Web サービス ポータルまたは Machine Learning Studio のいずれかでテストできます。

要求 - 応答 Web サービスをテストするには、Web サービス ダッシュボードで **[テスト]** をクリックします。 ダイアログ ボックスが表示され、サービスへのデータの入力が促されます。 これらはスコア付け実験で想定される列になります。 データのセットを入力し、 **[OK]** をクリックします。 Web サービスによって生成された結果がダッシュボードの下部に表示されます。

「新しい Web サービス」セクションで説明したように、**[テスト]** プレビュー リンクをクリックして、サービスを Azure Machine Learning Studio Web サービス ポータルでテストできます。

バッチ実行サービスをテストするには、**[テスト]** プレビュー リンクをクリックします。 バッチ テストのページで、入力の下の [参照] をクリックし、適切なサンプル値を含む CSV ファイルを選択します。 CSV ファイルがなく、Machine Learning Studio を使用して予測実験を作成した場合は、予測実験用のデータ セットをダウンロードし、それを使用できます。

![Web サービスをテストする](./media/publish-a-machine-learning-web-service/figure-3.png)

**[構成]** ページで、サービスの表示名を変更したり、説明を付けたりできます。 この名前と説明は、Web サービスを管理する [Azure Portal](https://portal.azure.com/) に表示されます。

**INPUT SCHEMA**、**OUTPUT SCHEMA**、**Web SERVICE PARAMETER** の各列に文字列を入力し、入力データ、出力データ、Web サービス パラメーターの説明を追加できます。 これらの説明は、Web サービスのサンプル コードのドキュメントで使用されます。

ログ記録を有効にすれば、Web サービスのアクセスで発生するすべてのエラーを診断できます。 詳細については、「[Machine Learning Studio Web サービスのログ記録を有効にする](web-services-logging.md)」をご覧ください。

![Web サービス ポータル内でログ記録を有効にする](./media/publish-a-machine-learning-web-service/figure-4.png)

「新しい Web サービス」セクションで説明した手順と同様に、Web サービスのエンドポイントを Azure Machine Learning Web サービス ポータルで構成することもできます。 オプションは異なりますが、サービスの説明の追加または変更、ログの有効化、テスト用サンプル データの有効化を行うことができます。

### <a name="access-your-classic-web-service"></a>従来の Web サービスにアクセスする

Machine Learning Studio から Web サービスをデプロイすると、サービスにデータを送信し、プログラムからの応答が得られます。

ダッシュ ボードでは、Web サービスへのアクセスが必要なすべての情報が提供されます。 たとえば、サービスへの承認済みアクセスを許可する API キーや、コードの記述に役立つ API のヘルプ ページが提供されます。

Machine Learning Studio Web サービスへのアクセスの詳細については、「[Azure Machine Learning Studio Web サービスを使用する方法](consume-web-services.md)」を参照してください。

### <a name="manage-your-classic-web-service"></a>従来の Web サービスを管理する

Web サービスを監視するために実行できるさまざまな操作があります。 更新および削除することもできます。 デプロイ時に作成される既定のエンドポイントに加え、従来の Web サービスにさらにエンドポイントを追加することもできます。

詳細については、「[Azure Machine Learning Studio ワークスペースの管理](manage-workspace.md)」と「[Azure Machine Learning Studio Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」を参照してください。

## <a name="update-the-web-service"></a>Web サービスを更新する
別のトレーニング データでのモデルの更新など、Web サービスに変更を加えたり、元の Web サービスを上書きしてもう一度デプロイしたりできます。

Web サービスを更新するには、Web サービスのデプロイに使用した元の予測実験を開き、 **[名前を付けて保存]** をクリックして編集可能なコピーを作成します。 必要な変更を加えて **[WEB サービスのデプロイ]** をクリックします。

この実験は前にデプロイ済みであるため、既存のサービスを上書きするか (従来の Web サービス)、更新するか (新しい Web サービス) 確認されます。 **[はい]** または **[更新]** をクリックすると、既存の Web サービスが停止され、新しい予測実験が代わりにデプロイされます。

> [!NOTE]
> たとえば元の Web サービスの構成に変更を加えた場合や新しい表示名または説明を入力した場合、それらの値をもう一度入力する必要が生じます。

Web サービスを更新するオプションの 1 つに、モデルをプログラムによって再トレーニングする方法があります。 詳細については、「[プログラムによる Machine Learning Studio のモデルの再トレーニング](/azure/machine-learning/studio/retrain-machine-learning-model)」をご覧ください。

## <a name="next-steps"></a>次の手順

* デプロイのしくみに関する技術的な詳細については、「[Machine Learning Studio モデルが実験から運用可能な Web サービスになるまでの過程](model-progression-experiment-to-web-service.md)」をご覧ください。

* デプロイのためにモデルを準備する方法については、「[Azure Machine Learning Studio でのデプロイのためにモデルを準備する方法](convert-training-experiment-to-scoring-experiment.md)」を参照してください。

* REST API を使用して Web サービスにアクセスするには、いくつかの方法があります。 「[Azure Machine Learning Studio Web サービスを使用する方法](consume-web-services.md)」をご覧ください。

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
