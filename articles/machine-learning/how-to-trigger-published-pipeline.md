---
title: 新しいデータに対して ML パイプラインをトリガーする
titleSuffix: Azure Machine Learning
description: Azure Logic Apps を使用して ML パイプラインの実行をトリガーする方法について説明します。
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4
ms.openlocfilehash: 6d54945ce026f91b27ce31ba82ada1b0cc49a260
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324252"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Logic Apps から Machine Learning パイプラインの実行をトリガーする

新しいデータが出現したときに、Azure Machine Learning パイプラインの実行をトリガーします。 たとえば、BLOB ストレージ アカウントに新しいデータが出現したときに、新しいモデルをトレーニングするパイプラインをトリガーすることができます。 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用してトリガーを設定します。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

* 発行された Machine Learning パイプライン用の REST エンドポイント。 [パイプラインを作成して発行します](how-to-create-your-first-pipeline.md)。 次に、パイプライン ID を使用して、PublishedPipeline の REST エンドポイントを見つけます。
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* データを格納する [Azure BLOB ストレージ](../storage/blobs/storage-blobs-overview.md)。
* BLOB ストレージ アカウントの詳細を格納する、ワークスペース内の[データストア](how-to-access-data.md)。

## <a name="create-a-logic-app"></a>Logic Apps の作成

ここで [Azure Logic Apps](../logic-apps/logic-apps-overview.md) インスタンスを作成します。 必要に応じて、[統合サービス環境 (ISE) を使用](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)して、Logic Apps で使用するための[カスタマー マネージド キーを設定](../logic-apps/customer-managed-keys-integration-service-environment.md)します。

Logic Apps がプロビジョニングされたら、次の手順に従って、パイプラインのトリガーを構成します。

1. [システム割り当てマネージド ID](../logic-apps/create-managed-service-identity.md) を作成し、アプリに Azure Machine Learning ワークスペースへのアクセス権を付与します。

1. Logic Apps デザイナー ビューに移動し、[空の Logic Apps] テンプレートを選択します。 
    > [!div class="mx-imgBorder"]
    > ![空のテンプレート](media/how-to-trigger-published-pipeline/blank-template.png)

1. デザイナーで **BLOB** を検索します。 **[BLOB が追加または変更されたとき (プロパティのみ)]** トリガーを選択して、このトリガーを Logic Apps に追加します。
    > [!div class="mx-imgBorder"]
    > ![トリガーの追加](media/how-to-trigger-published-pipeline/add-trigger.png)

1. BLOB の追加または変更を監視するための BLOB ストレージ アカウントの接続情報を入力します。 監視するコンテナーを選択します。 
 
    自動で機能する、更新をポーリングする**間隔**と**頻度**を選択します。  

    > [!NOTE]
    > このトリガーでは、選択したコンテナーは監視されますが、サブフォルダーは監視されません。

1. 新しい BLOB または変更された BLOB が検出されたときに実行される HTTP アクションを追加します。 **[+ 新しいステップ]** を選択してから、HTTP アクションを検索して選択します。

  > [!div class="mx-imgBorder"]
  > ![HTTP アクションを検索する](media/how-to-trigger-published-pipeline/search-http.png)

  以下の設定を使用してアクションを構成します。

  | 設定 | 値 | 
  |---|---|
  | HTTP アクション | POST |
  | URI |[前提条件](#prerequisites)として確認した、発行されたパイプラインへのエンドポイント |
  | 認証モード | マネージド ID |

1. 使用する可能性のあるすべての [DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) の値を設定するためのスケジュールを設定します。

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    ワークスペースに追加した `DataStoreName` を[前提条件](#prerequisites)として使用します。
     
    > [!div class="mx-imgBorder"]
    > ![HTTP 設定](media/how-to-trigger-published-pipeline/http-settings.png)

1. **[保存]** を選択するとスケジュールの準備ができます。
