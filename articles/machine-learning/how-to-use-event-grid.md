---
title: ML ワークフロー内でイベントをトリガーする (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で、イベント ドリブンなアプリケーション、プロセス、または CI/CD 機械学習ワークフローを設定します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 9dc7c0a476f57048c7caefb6001918d314c991ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102209698"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Azure Machine Learning イベントに基づいてアプリケーション、プロセス、または CI/CD ワークフローをトリガーする (プレビュー)

この記事では、[Azure Event Grid](../event-grid/index.yml) によって特定の条件が検出された場合に、Azure Machine Learning イベント (失敗通知メールや ML パイプラインの実行など) に基づいて、イベント ドリブンなアプリケーション、プロセス、または CI/CD ワークフローを設定する方法について説明します。

Azure Machine Learning は、モデルのトレーニング、モデルのデプロイ、監視など、機械学習プロセスのライフサイクル全体を管理します。 Event Grid を使用して、最新のサーバーレス アーキテクチャを使用することで、Azure Machine Learning イベント (トレーニングの実行の完了、モデルの登録とデプロイ、データ ドリフトの検出など) に対応できます。 その後、ワークスペースにおける実行状態の変更、実行の完了、モデルの登録、モデルのデプロイ、データ ドリフトの検出などのイベントをサブスクライブして使用できます。

イベント ドリブン アクションに Event Grid を使用する場合:
* 実行失敗および実行完了に関するメールを送信する
* モデルの登録後に Azure 関数を使用する
* Azure Machine Learning からさまざまなエンドポイントへのイベントのストリーミング
* 誤差が検出されたときに ML パイプラインをトリガーする

## <a name="prerequisites"></a>前提条件
Event Grid を使用するには、イベントを作成する Azure Machine Learning ワークスペースへの共同作成者または所有者アクセスが必要です。

## <a name="the-event-model--types"></a>イベントのモデルと種類

Azure Event Grid は、Azure Machine Learning やその他の Azure サービスなどのソースからイベントを読み取ります。 これらのイベントは、その後 Azure Event Hubs、Azure Functions、Logic Apps などのイベント ハンドラーに送信されます。 次の図は、Event Grid がソースとハンドラーをどのように接続するかを示すもので、サポートされる統合の包括的な一覧ではありません。

![Azure Event Grid の機能モデル](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

イベント ソースとイベント ハンドラーの詳細については、「[Event Grid とは](../event-grid/overview.md)」を参照してください。

### <a name="event-types-for-azure-machine-learning"></a>Azure Machine Learning のイベントの種類

Azure Machine Learning は、機械学習のライフサイクルのさまざまな時点でイベントを提供します。 

| イベントの種類 | 説明 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 機械学習実験の実行が完了したときに発生します |
| `Microsoft.MachineLearningServices.ModelRegistered` | 機械学習モデルがワークスペースに登録されたときに発生します |
| `Microsoft.MachineLearningServices.ModelDeployed` | 1 つ以上のモデルを持つ推論サービスのデプロイが完了したときに発生します |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 2 つのデータセットのデータ ドリフト検出ジョブが完了したときに発生します |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 実行状態が変更されたときに発生します。現時点では、実行状態が 'failed' の場合にのみ発生します |

### <a name="filter--subscribe-to-events"></a>イベントをフィルター処理およびサブスクライブする

これらのイベントは Azure Event Grid を通じて発行されます。 お客様は Azure portal、PowerShell、または Azure CLI を使用して、[1 つまたは複数のイベントの種類およびフィルター条件を指定する](../event-grid/event-filtering.md)ことによってイベントを簡単にサブスクライブできます。 

イベントを設定するときに、フィルターを適用して、特定のイベント データでのみトリガーするようにできます。 実行状態変更イベントについての次の例では、実行の種類でフィルター処理できます。 イベントは、条件が満たされた場合にのみトリガーされます。 フィルター処理できるイベン トデータの詳細については、[Azure Machine Learning イベント グリッド スキーマ](../event-grid/event-schema-machine-learning.md)に関する記事を参照してください。 

Azure Machine Learning イベントのサブスクリプションは、Azure ロールベースのアクセス制御 (Azure RBAC) によって保護されています。 ワークスペースの[共同作成者または所有者](how-to-assign-roles.md#default-roles)だけが、イベント サブスクリプションの作成、更新、削除を行うことができます。  イベント サブスクリプションの[作成](/cli/azure/eventgrid/event-subscription)中、または作成後に、イベント サブスクリプションをフィルター処理することができます。 


1. Azure portal にアクセスし、新しいサブスクリプションまたは既存のサブスクリプションを選択します。 

1. [フィルター] タブを選択し、下にスクロールして [高度なフィルター] に移動します。 **[キー]** と **[値]** で、フィルター処理するプロパティの型を指定します。 ここでは、実行の種類がパイプラインの実行またはパイプライン ステップの実行の場合にのみ、イベントがトリガーされることがわかります。  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="イベントのフィルター処理":::


+ **イベントの種類でフィルター処理する:** イベント サブスクリプションでは、1 つまたは複数の Azure Machine Learning イベントの種類を指定できます。

+ **イベントの件名でフィルター処理する:** Azure Event Grid は、__次で始まる__ と __次で終わる__ という一致に基づく件名フィルターをサポートするため、件名が一致するイベントはサブスクライバーに配信されます。 機械学習イベントが異なると、件名の形式も異なります。

  | イベントの種類 | 件名の形式 | 件名の例 |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **高度なフィルター処理**:Azure Event Grid は、発行されたイベント スキーマに基づく高度なフィルター処理もサポートしています。 Azure Machine Learning のイベント スキーマの詳細については、「[Azure Machine Learning の Azure Event Grid イベント スキーマ](../event-grid/event-schema-machine-learning.md)」を参照してください。  実行できる高度なフィルター処理の例をいくつか次に示します。

  `Microsoft.MachineLearningServices.ModelRegistered` イベントの場合、モデルのタグ値をフィルター処理するには、次のようにします。

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  フィルターを適用する方法の詳細については、「[Event Grid のイベントのフィルター処理](../event-grid/how-to-filter-events.md)」をご覧ください。

## <a name="consume-machine-learning-events"></a>Machine Learning イベントを使用する

Machine Learning イベントを処理するアプリケーションは、いくつかの推奨される手法に従う必要があります。

> [!div class="checklist"]
> * 複数のサブスクリプションが同じイベント ハンドラーにイベントをルーティングするように構成される場合があるため、イベントが特定のソースからのものであると見なすのではなく、メッセージのトピックをチェックして、予測している Machine Learning ワークスペースから来ていることを確認することが重要です。
> * 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
> * メッセージは順不同で到着したり、少し遅れて到着する可能性があるので、etag フィールドを使って、オブジェクトに関する情報がまだ最新の状態かどうかを確認します。  また、sequencer フィールドを使って、特定のオブジェクトに対するイベントの順序を確認します。
> * わからないフィールドは無視します。 この手法に従うと、将来追加されるかもしれない新しい機能に弾力的に対応できます。
> * 失敗またはキャンセルされた Azure Machine Learning 操作はイベントをトリガーしません。 たとえば、モデル デプロイが失敗した場合、Microsoft.MachineLearningServices.ModelDeployed はトリガーされません。 アプリケーションを設計するときは、このような障害モードを考慮してください。 Azure Machine Learning SDK、CLI、またはポータルをいつでも使用して、操作の状態を確認して失敗の詳しい理由を理解することができます。

Azure Event Grid を使用すると、Azure Machine Learning イベントによってトリガー可能な、分離されたメッセージ ハンドラーを構築できます。 メッセージ ハンドラーの主要な例を次に示します。
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory パイプライン
* 汎用 Webhook (Azure プラットフォームまたは他の場所でホストされる場合があります)

## <a name="set-up-in-azure-portal"></a>Azure Portal での設定

1. [Azure portal](https://portal.azure.com) を開き、Azure Machine Learning ワークスペースに移動します。

1. 左側のバーで __[イベント]__ を選択し、 **[イベント サブスクリプション]** を選択します。 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. 使用するイベントの種類を選択します。 たとえば、次のスクリーンショットでは、 __[Model registered]\(モデルが登録された\)__ 、 __[Model deployed]\(モデルがデプロイされた\)__ 、 __[Run completed]\(実行の完了\)__ 、 __[Dataset drift detected]\(データセットのドリフトの検出\)__ が選択されています。

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. イベントの発行先のエンドポイントを選択します。 次のスクリーンショットでは、 __[イベント ハブ]__ が選択されたエンドポイントであることを示しています。

    ![[イベント ハブの選択] が開いた [イベント サブスクリプションの作成] ペインを示すスクリーンショット。](./media/how-to-use-event-grid/select-event-handler.png)

選択内容を確認したら、 __[作成]__ をクリックします。 構成の後、これらのイベントはエンドポイントにプッシュされます。


### <a name="set-up-with-the-cli"></a>CLI を使用した設定

最新の [Azure CLI](/cli/azure/install-azure-cli) をインストールするか、Azure サブスクリプションの一部として提供されている Azure Cloud Shell を使用することができます。

Event Grid 拡張機能をインストールするには、CLI から次のコマンドを使用します。

```azurecli-interactive
az add extension --name eventgrid
```

次の例では、Azure サブスクリプションの選択方法が示されており、Azure Machine Learning の新しいイベント サブスクリプションが作成されます。

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>例

### <a name="example-send-email-alerts"></a>例:メール アラートを送信する

[Azure Logic Apps](../logic-apps/index.yml) を使用して、すべてのイベントのメールを構成します。 条件を使用してカスタマイズし、共同作業を行うチーム全体でコラボレーションと意識を高める受信者を指定します。

1. Azure portal で、Azure Machine Learning ワークスペースに移動し、左側のバーの [イベント] タブを選択します。 ここで、 __[ロジック アプリ]__ を選択します。 

    ![Logic Apps の Machine Learning ワークスペース イベント ページを示すスクリーンショット。](./media/how-to-use-event-grid/select-logic-ap.png)

1. ロジック アプリの UI にサインインし、トピックの種類として Machine Learning サービスを選択します。 

    ![リソースの種類として機械学習が選択された状態の [リソース イベントが発生したとき] ダイアログ ボックスを示すスクリーンショット。](./media/how-to-use-event-grid/select-topic-type.png)

1. 通知するイベントを選択します。 たとえば、次のスクリーンショットで __[RunCompleted]__ を選択します。

    ![イベントの種類が選択された [リソース イベントが発生したとき] ダイアログ ボックスを示すスクリーンショット。](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 上のセクションのフィルター処理方法を使用するか、フィルターを追加して、イベントの種類のサブセットでのみロジック アプリをトリガーすることができます。 次のスクリーンショットでは、 __/datadriftID/runs/__ の __プレフィックス フィルター__ が使用されています。

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. 次に、このイベントを使用して電子メールを検索するステップを追加します。 イベントの受信に使用できるメール アカウントはいくつかあります。 また、電子メール アラートを送信する条件を構成することもできます。

    ![検索行にメールアドレスが入力された [アクションの選択] ダイアログ ボックスを示すスクリーンショット。](./media/how-to-use-event-grid/select-email-action.png)

1. __[メールの送信]__ を選択し、パラメーターを入力します。 件名には、イベントのフィルター処理に役立つ __[Event Type]\(イベントの種類\)__ と __[Topic]\(トピック\)__ を含めることができます。 メッセージ本文で実行するために、ワークスペース ページへのリンクを含めることもできます。 

    ![件名行のリストから右側にトピックとイベントの種類が追加された [メールを送る] ダイアログ ボックスを示すスクリーンショット。](./media/how-to-use-event-grid/configure-email-body.png)

1. この操作を保存するには、ページの左端にある **[名前を付けて保存]** を選択します。 表示される右側のバーで、このアクションの作成を確認します。

    ![Logic Apps デザイナーの [名前を付けて保存] および [作成] ボタンを示すスクリーンショット。](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>例:データ ドリフトが再トレーニングをトリガーする

モデルは時間の経過と共に古くなり、実行されているコンテキストでは役に立たなくなります。 モデルの再トレーニングを行う時期であることを通知する方法の 1 つは、データ ドリフトを検出することです。 

この例では、Azure Logic App でイベント グリッドを使用して再トレーニングをトリガーする方法を示します。 この例では、モデルのトレーニングとサービス提供データセットの間でデータ ドリフトが発生したときに、Azure Data Factory パイプラインをトリガーします。

開始する前に、次の操作を行います。

* データセット モニターを設定して、ワークスペース内の[データの誤差を検出する](how-to-monitor-datasets.md)
* 発行される [Azure Data Factory パイプライン](../data-factory/index.yml)を作成する。

この例では、単純な Data Factory パイプラインを使用してファイルを blob ストアにコピーし、発行された Machine Learning パイプラインを実行します。 このシナリオの詳細については、[Azure Data Factory の Machine Learning の手順](../data-factory/transform-data-machine-learning-service.md)を設定する方法を参照してください。

![ML 実行 Pipeline1 を供給する data1 のコピーがあるファクトリ リソースのトレーニング パイプラインを示すスクリーンショット。](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. ロジック アプリの作成を開始します。 [Azure portal](https://portal.azure.com) にアクセスし、ロジック アプリを検索して、[作成] を選択します。

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 必要な情報を入力します。 エクスペリエンスを単純化するには、Azure Data Factory パイプラインと Azure Machine Learning ワークスペースと同じサブスクリプションおよびリソース グループを使用します。

    ![ロジック アプリの [作成] ペインを示すスクリーンショット。](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. ロジック アプリを作成したら、 __[Event Grid のリソース イベントが発生するとき]__ を選択します。 

    ![Event Grid リソース イベントが発生したときなど、一般的なトリガー オプションを使用して開始した Logic Apps デザイナーを示すスクリーンショット。](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. ログインし、イベントの詳細を入力します。 __[リソース名]__ をワークスペース名に設定します。 __[イベントの種類]__ を __[DatasetDriftDetected]__ に設定します。

    ![[イベントの種類] 項目が選択された [リソース イベントが発生したとき] を示すスクリーンショット。](./media/how-to-use-event-grid/login-and-add-event.png)

1. 新しいステップを追加し、__Azure Data Factory__ を検索します。 __[パイプラインの実行の作成]__ を選択します。 

    ![[パイプラインの実行の作成] が選択された [アクションの選択] ペインを示すスクリーンショット。](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. ログインし、実行する公開された Azure Data Factory パイプラインを指定します。

    ![さまざまな値がある [パイプラインの実行の作成] ペインを示すスクリーンショット。](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. ページの左上にある **[保存]** ボタンを使用して、ロジック アプリを保存して作成します。 アプリを表示するには、[Azure portal](https://portal.azure.com) のワークスペースにアクセスし、 **[イベント]** をクリックします。

    ![ロジック アプリが強調表示されているイベントを示すスクリーンショット。](./media/how-to-use-event-grid/show-logic-app-webhook.png)

これで、ドリフトが発生したときに Data Factory パイプラインがトリガーされます。 [新しいワークスペース ポータル](https://ml.azure.com)で、データ ドリフトの実行と機械学習パイプラインの詳細を確認します。 

![パイプライン エンドポイントを示すスクリーンショット。](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>例:タグに基づいてモデルをデプロイする

Azure Machine Learning モデルオブジェクトには、モデルの名前、バージョン、タグ、プロパティなど、デプロイをピボットできるパラメーターが含まれています。 モデル登録イベントはエンドポイントをトリガーでき、Azure 関数を使用して、これらのパラメーターの値に基づいてモデルを配置できます。

例については、[https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) リポジトリを参照し、**readme** ファイルの手順に従ってください。

## <a name="next-steps"></a>次のステップ

Event Grid の詳細について理解し、Azure Machine Learning のイベントを試してみてください。

- [Event Grid について](../event-grid/overview.md)

- [Azure Machine Learning 用のイベント スキーマ](../event-grid/event-schema-machine-learning.md)
