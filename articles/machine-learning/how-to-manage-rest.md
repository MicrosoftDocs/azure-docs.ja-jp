---
title: REST を使用して ML リソースを管理する
titleSuffix: Azure Machine Learning
description: REST API を使用して、ワークスペースなどの Azure Machine Learning リソースを作成、実行、および削除する方法、またはモデルを登録する方法について説明します。
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b61050db29ff960b7923f2211b2f09649608b37e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520321"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>REST を使用して Azure ML リソースの作成、実行、削除を行う



Azure ML リソースを管理するには、いくつかの方法があります。 [ポータル](https://portal.azure.com/)、[コマンド ライン インターフェイス](/cli/azure)、または [Python SDK](/python/api/overview/azure/ml/intro) を使用できます。 または、REST API を選択することもできます。 REST API では、標準的な方法で HTTP 動詞を使用して、リソースの作成、取得、更新、および削除を行います。 REST API は、HTTP 要求を作成できるすべての言語またはツールで使用できます。 REST は構造がわかりやすいため、多くの場合、スクリプト環境や MLOps オートメーションに適しています。 

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * 承認トークンを取得する
> * サービス プリンシパル認証を使用して適切に書式設定された REST 要求を作成する
> * GET 要求を使用して Azure ML の階層型リソースに関する情報を取得する
> * PUT 要求と POST 要求を使用してリソースを作成および変更する
> * DELETE 要求を使用してリソースをクリーンアップする 
> * キーベースの承認を使用してデプロイされたモデルをスコア付けする

## <a name="prerequisites"></a>前提条件

- 管理者権限を持っている **Azure サブスクリプション**。 そのようなサブスクリプションがない場合は、[無料または有料の個人用サブスクリプション](https://aka.ms/AMLFree)をお試しください
- [Azure Machine Learning ワークスペース](./how-to-manage-workspace.md)
- 管理 REST 要求でサービス プリンシパル認証が使用されている。 ワークスペースにサービス プリンシパルを作成するには、[Azure Machine Learning のリソースとワークフローの認証を設定する方法](./how-to-setup-authentication.md#service-principal-authentication)に関する記事に記載されている手順に従ってください
- **curl** ユーティリティ。 **curl** プログラムは、[Linux 用 Windows サブシステム](/windows/wsl/install-win10)または任意の UNIX ディストリビューションで使用できます。 PowerShell では、**curl** は **Invoke-WebRequest** の別名であり、`curl -d "key=val" -X POST uri` は `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` になります。 

## <a name="retrieve-a-service-principal-authentication-token"></a>サービス プリンシパルの認証トークンを取得する

管理 REST 要求は、OAuth2 暗黙的フローで認証されます。 この認証フローでは、サブスクリプションのサービス プリンシパルによって提供されたトークンが使用されます。 このトークンを取得するには、次のものが必要です。

- テナント ID (サブスクリプションが属している組織を識別します)
- クライアント ID (作成されたトークンに関連付けられます)
- クライアント シークレット (これは保護する必要があります)

これらの値は、サービス プリンシパルの作成に対する応答から取得する必要があります。 これらの値の取得については、「[Azure Machine Learning のリソースとワークフローの認証を設定する](./how-to-setup-authentication.md#service-principal-authentication)」で説明しています。 会社のサブスクリプションを使用している場合は、サービス プリンシパルを作成するアクセス許可がない可能性があります。 その場合は、[無料または有料の個人用サブスクリプション](https://aka.ms/AMLFree)を使用する必要があります。

トークンを取得するには:

1. ターミナル ウィンドウを開きます。
1. コマンド ラインで以下のコードを入力します
1. `{your-tenant-id}`、`{your-client-id}`、`{your-client-secret}` は、使用する実際の値に置き換えてください。 この記事では、中かっこで囲まれた文字列は変数を表し、実際の適切な値に置き換える必要があります。
1. コマンドを実行します

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

その応答で、1 時間有効なアクセス トークンが返されるはずです。

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

このトークンは、後続のすべての管理要求を認証するために使用するので、メモしておいてください。 これを行うには、すべての要求に Authorization ヘッダーを設定します。

```bash
curl -h "Authorization:Bearer {your-access-token}" ...more args...
```

この値は文字列 "Bearer " で始まり、トークンを追加する前に 1 つのスペースを入れることに注意してください。

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>サブスクリプションに関連付けられているリソース グループの一覧を取得する

サブスクリプションに関連付けられているリソース グループの一覧を取得するには、次を実行します。

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Azure 全体で、多くの REST API が発行されています。 各サービス プロバイダーは独自の頻度で API を更新しますが、それによって既存のプログラムが損なわれることはありません。 サービス プロバイダーは、`api-version` 引数を使用して互換性を保証します。 `api-version` 引数はサービスによって異なります。 たとえば、Machine Learning サービスの場合、最新の API バージョンは `2019-11-01` です。 ストレージ アカウントの場合は `2019-06-01` です。 キー コンテナーの場合は `2019-09-01` です。 あらゆる REST 呼び出しで、`api-version` 引数を必要な値に設定する必要があります。 API が進化し続けても、指定したバージョンの構文とセマンティクスに依存することができます。 `api-version` 引数を指定せずにプロバイダーに要求を送信すると、その応答には、サポートされている値のユーザーが判読できる一覧が含まれます。 

上記の呼び出しでは、次の形式の圧縮された JSON 応答が返されます。 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>ワークスペースとそのリソースへのドリルダウン

リソース グループ内のワークスペースのセットを取得するには、次を実行します (`{your-subscription-id}`、`{your-resource-group}`、および `{your-access-token}` を置き換えてください)。 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

ここでも JSON リストが返されます。今回は、各項目がワークスペースの詳細を示すリストが含まれています。

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

ワークスペース内のリソースを操作するには、一般的な **management.azure.com** サーバーから、ワークスペースの場所に固有の REST API サーバーに切り替えます。 上記の JSON 応答に含まれる `discoveryUrl` キーの値をメモしてください。 この URL に対して GET を実行すると、次のような応答が返されます。

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

`api` 応答の値は、追加の要求に使用するサーバーの URL です。 たとえば、実験の一覧を表示するには、次のコマンドを送信します。 `regional-api-server` を `api` 応答の値 (たとえば、`centralus.api.azureml.ms`) に置き換えてください。 また、`your-subscription-id`、`your-resource-group`、`your-workspace-name`、および `your-access-token` も通常どおり置き換えてください。

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

同様に、ワークスペースに登録済みのモデルを取得するには、次を送信します。

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

実験の一覧を表示する場合はパスの先頭が `history/v1.0` であり、モデルの一覧を表示する場合はパスの先頭が `modelmanagement/v1.0` であることがわかります。 REST API はいくつかの操作グループに分割され、それぞれが個別のパスを持っています。 

|領域|Path|
|-|-|
|Artifacts|/rest/api/azureml|
|データ ストア|/azure/machine-learning/how-to-access-data|
|ハイパーパラメーターの調整|hyperdrive/v1.0/|
|モデル|modelmanagement/v1.0/|
|実行履歴|execution/v1.0/ および history/v1.0/|

次の一般的なパターンを使用して、REST API を調べることができます。

|URL コンポーネント|例|
|-|-|
| https://| |
| regional-api-server/ | centralus.api.azureml.ms/ |
| operations-path/ | history/v1.0/ |
| subscriptions/{サブスクリプション ID}/ | subscriptions/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{リソース グループ}/ | resourceGroups/MyResourceGroup/ |
| providers/operation-provider/ | providers/Microsoft.MachineLearningServices/ |
| provider-resource-path/ | workspaces/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| operations-endpoint/ | artifacts/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>PUT 要求と POST 要求を使用してリソースを作成および変更する

REST API では、GET 動詞を使用したリソースの取得に加えて、ML ソリューションのトレーニング、デプロイ、および監視に必要なあらゆるリソースの作成がサポートされています。 

ML モデルのトレーニングと実行には、コンピューティング リソースが必要です。 次を使って、ワークスペースのコンピューティング リソースの一覧を表示することができます。 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

名前付きコンピューティング リソースを作成または上書きするには、PUT 要求を使用します。 以下では、おなじみの `your-subscription-id`、`your-resource-group`、`your-workspace-name`、`your-access-token` の置き換えに加えて、`your-compute-name` と、`location`、`vmSize`、`vmPriority`、`scaleSettings`、`adminUserName`、`adminUserPassword` の値を置き換えてください。 [Machine Learning コンピューティング - 作成または更新に関する SDK リファレンス](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)で指定されているように、次のコマンドを実行すると、30 分後にスケールダウンされる、専用の単一ノードの Standard_D1 (基本的な CPU コンピューティング リソース) が作成されます。

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> Windows ターミナルでは、JSON データを送信するときに、二重引用符記号をエスケープすることが必要になる場合があります。 つまり、`"location"` などのテキストが `\"location\"` になります。 

要求が成功すると `201 Created` 応答が返されますが、この応答は、プロビジョニング プロセスが開始されたことを意味するにすぎないことに注意してください。 それが正常に完了したことを確認するには、ポーリングする (またはポータルを使用する) 必要があります。

### <a name="create-an-experimental-run"></a>実験用の実行を作成する

実験内の実行を開始するには、トレーニング スクリプトと関連ファイル、および実行の定義 JSON ファイルを含む zip フォルダーが必要です。 この zip フォルダーのルート ディレクトリには、Python エントリ ファイルを含める必要があります。 例として、次のような単純な Python プログラムを、**train.zip** というフォルダーに zip 圧縮します。

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

次のスニペットを **definition.json** として保存します。 "Script" の値が、先ほど zip 圧縮した Python ファイルの名前と一致していることを確認します。 "Target" の値が、使用可能なコンピューティング リソースの名前と一致していることを確認します。 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

`multipart/form-data` コンテンツを使用して、これらのファイルをサーバーに送信します。

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

POST 要求が成功すると、`200 OK` 状態が生成され、応答本文には作成された実行の識別子が含まれます。

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

おなじみになった RESTful パターンを使用して、実行を監視することができます。

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>不要になったリソースを削除する

一部の (すべてではありません) リソースでは DELETE 動詞がサポートされています。 削除のユース ケース用の REST API に取り組む前に、[API リファレンス](/rest/api/azureml/)を確認してください。 たとえば、モデルを削除するには、次を使用できます。

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>REST を使用してデプロイされたモデルをスコア付けする

サービス プリンシパルで認証されるようにモデルをデプロイすることもできますが、ほとんどのクライアント向けのデプロイでは、キーベースの認証が使用されます。 適切なキーは、Studio の **[エンドポイント]** タブ内にあるデプロイのページで確認できます。 同じ場所に、エンドポイントのスコアリング URI が表示されます。 モデルの入力は、`data` という名前の JSON 配列としてモデル化されている必要があります。

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>REST を使用してワークスペースを作成する 

すべての Azure ML ワークスペースは、他の 4 つの Azure リソースに依存しています。つまり、管理が有効になっているコンテナー レジストリ、キー コンテナー、Application Insights リソース、およびストレージ アカウントです。 これらのリソースがない場合は、ワークスペースを作成することはできません。 このような各リソースの作成の詳細については、REST API リファレンスを参照してください。

ワークスペースを作成するには、次のような呼び出しを `management.azure.com` に PUT します。 この呼び出しでは多数の変数を設定する必要がありますが、構造的には、この記事で説明した他の呼び出しと同じです。 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

`202 Accepted` 応答が返され、返されたヘッダーに `Location` URI が含まれているはずです。 この URI を GET して、デプロイに関する情報を取得できます。これには、依存リソースのいずれかに問題がある場合に役立つデバッグ情報が含まれます (たとえば、コンテナー レジストリで管理者アクセスを有効にすることを忘れた場合など)。 

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="resource-provider-errors"></a>リソース プロバイダーのエラー

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>ワークスペースの移動

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry の削除

Azure Machine Learning ワークスペースでは、一部の操作に対して Azure Container Registry (ACR) が使用されます。 これにより ACR インスタンスは、最初に必要になったときに自動的に作成されます。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>次のステップ

- 完全な [AzureML REST API リファレンス](/rest/api/azureml/)を確認します。
- デザイナーを使用して、[デザイナーを使用して自動車の価格を予測する](./tutorial-designer-automobile-price-train-score.md)方法を学習します。
- [Jupyter ノートブックを使用した Azure Machine Learning](..//machine-learning/samples-notebooks.md) について調べます。
