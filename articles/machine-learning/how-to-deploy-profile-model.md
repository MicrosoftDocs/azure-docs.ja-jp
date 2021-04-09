---
title: モデルのメモリと CPU の使用状況をプロファイルする
titleSuffix: Azure Machine Learning
description: デプロイ前にモデルをプロファイルする方法について説明します。 プロファイルによって、モデルのメモリと CPU の使用率が決まります。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: b9ae40b3d2673961f9b84ed702f18b25b79b6d0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93320405"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>モデルをプロファイルしてリソース使用状況を判断する

この記事では、モデルに機械学習をプロファイルして、Web サービスとしてモデルをデプロイするときにモデルに割り当てる必要がある CPU とメモリの量を判断する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、Azure Machine Learning でモデルをトレーニングして登録したことを想定しています。 Azure Machine Learning での scikit-learn モデルのトレーニングと登録の例については、[こちらのサンプル チュートリアル](how-to-train-scikit-learn.md)を参照してください。

## <a name="limitations"></a>制限事項

* お使いのワークスペースの Azure Container Registry (ACR) が仮想ネットワーク内にある場合、プロファイルは機能しません。

## <a name="run-the-profiler"></a>プロファイラーの実行

モデルを登録して、そのデプロイに必要な他のコンポーネントを準備したら、デプロイされたサービスで必要とされる CPU とメモリを決定できます。 モデルを実行して CPU 使用率、メモリ使用率、応答の待機時間などの情報を返す、サービスのプロファイル テストを行います。 また、リソースの使用状況に基づいて CPU とメモリに関する推奨事項も提示されます。

モデルをプロファイリングするには、以下が必要になります。
* 登録済みのモデル。
* エントリ スクリプトと推論環境の定義に基づく推論構成。
* 単一列の表形式のデータセット。各行には、サンプルの要求データを表す文字列が含まれています。

> [!IMPORTANT]
> 現時点では、要求データが文字列であることを期待するサービスのプロファイリングのみがサポートされています。たとえば、文字列のシリアル化された JSON、テキスト、文字列のシリアル化された画像などです。データセット (文字列) の各行の内容は、HTTP 要求の本文に置かれ、スコアリングのためにモデルをカプセル化するサービスに送信されます。

> [!IMPORTANT]
> ChinaEast2 リージョンと USGovArizona リージョンでは、最大 2 つの CPU のプロファイルのみがサポートされています。

以下に、入力データセットを作成して、受信する要求データにシリアル化された JSON が含まれていることを想定するサービスをプロファイルできる方法の例を示します。 ここでは、要求データの内容が同じである 100 個のインスタンスに基づくデータセットを作成しました。 実際のシナリオでは、モデル リソースの使用状況/動作が入力に依存している場合は特に、さまざまな入力を含むより規模の大きいデータセットを使用することをお勧めします。

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

サンプル要求データを含むデータセットを準備したら、推論構成を作成します。 推論構成は、score.py と環境定義に基づきます。 次の例に、推論構成を作成してプロファイリングを実行する方法を示します。

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


次のコマンドでは、CLI を使用してモデルをプロファイルする方法を示します。

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> プロファイリングによって返された情報を保持するには、モデルのタグまたはプロパティを使用します。 タグまたはプロパティを使用すると、データがモデルと共にモデル レジストリに格納されます。 次の例は、`requestedCpu` および `requestedMemoryInGb` 情報を含む新しいタグを追加する方法を示しています。
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>次のステップ

* [失敗したデプロイのトラブルシューティング](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを使用するクライアント アプリケーションを作成する](how-to-consume-web-service.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
* [モデル デプロイのイベント アラートおよびトリガーを作成する](how-to-use-event-grid.md)

