---
title: Azure ストレージ サービスのデータにアクセスする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service でトレーニング中にデータストアを使用して Azure ストレージ サービスにアクセスする方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 545860a394c7eac953c1cbacc9dd05fc3737f6c1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856171"
---
# <a name="access-data-in-azure-storage-services"></a>Azure ストレージ サービスのデータにアクセスする

 この記事では、Azure Machine Learning データストアを使用して Azure ストレージ サービスのデータに簡単にアクセスする方法について説明します。 データストアを使用すると、サブスクリプション ID やトークン承認など接続情報を格納することができ、ストレージにアクセスするためにそのような情報をスクリプトにハードコーディングする必要がなくなります。

ここでは、次のタスクの例を示します。
* [データストアを登録する](#access)
* [ワークスペースからデータストアを取得する](#get)
* [データストアを使用してデータをアップロードおよびダウンロードする](#up-and-down)
* [トレーニング中にデータにアクセスする](#train)

## <a name="prerequisites"></a>前提条件

データストアを使用するには、まず[ワークスペース](concept-workspace.md)が必要です。

まず、[新しいワークスペースを作成する](how-to-manage-workspace.md)か、既存のワークスペースを取得します。

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>データストアを登録する

すべての登録メソッドは [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) クラス上にあり、register_azure_* という形式があります。

次の例では、Azure BLOB コンテナーまたは Azure ファイル共有のデータストアとしての登録を示しています。

+ **Azure BLOB コンテナー データストア**の場合、[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) を使用します。

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ **Azure ファイル共有データストア**の場合、[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) を使用します。 例: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>ストレージのガイダンス

Azure BLOB コンテナーをお勧めします。 BLOB では Standard ストレージと Premium ストレージの両方を使用できます。 お勧めは Premium ストレージです。より高価になりますが、スループットの速度が上がるため、特に大規模なデータ セットに対するトレーニングでは、トレーニングの実行速度が向上する可能性があります。 ストレージ アカウントのコストの情報については、[Azure の料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)に関するページをご覧ください。

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>ワークスペースからデータストアを取得する

現在のワークスペースに登録されている特定のデータストアを取得するには、Datastore クラスで [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) 静的メソッドを使用します。

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
特定のワークスペースに登録されているデータストアの一覧を取得するには、ワークスペース オブジェクトに対して `datastores` プロパティを使用します。

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

ワークスペースを作成すると、Azure Blob コンテナーと Azure ファイル共有がワークスペースに登録されて、それぞれに `workspaceblobstore` と `workspacefilestore` という名前が付けられます。 これらには、ワークスペースにアタッチされているストレージ アカウントにプロビジョニングされる、Blob コンテナーとファイル共有の接続情報が格納されます。 `workspaceblobstore` は既定のデータストアとして設定されます。

ワークスペースの既定のデータストアを取得するには、次のコマンドを使用します。

```Python
datastore = ws.get_default_datastore()
```

異なる既定のデータストアを現在のワークスペースに定義するには、ワークスペース オブジェクトに対して [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) メソッドを使用します。

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>データのアップロードとダウンロード
次の例に示されている [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) と [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) メソッドは、[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) クラスと [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) クラスに固有のものであり、これらのクラスに対して同様に動作します。

### <a name="upload"></a>アップロード

 Python SDK を使用して、データストアにディレクトリまたは個々のファイルをアップロードします。

データストア `datastore` にディレクトリをアップロードするには、次のコマンドを使用します。

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` パラメーターでは、アップロードするファイル共有 (または BLOB コンテナー) 内の場所が指定されます。 既定値は `None` で、その場合はデータがルートにアップロードされます。 `overwrite=True` の場合、`target_path` のすべての既存のデータが上書きされます。

または、`upload_files()` メソッドを使用して、データストアに個々のファイルの一覧をアップロードします。

### <a name="download"></a>ダウンロード

同様に、データストアからローカル ファイル システムにデータをダウンロードします。

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` パラメーターは、データのダウンロード先となるローカル ディレクトリの場所です。 ダウンロードするファイル共有 (または BLOB コンテナー) 内のフォルダーのパスを指定するには、`prefix` にそのパスを指定します。 `prefix` が `None` である場合は、ファイル共有 (または BLOB コンテナー) のすべてのコンテンツがダウンロードされます。

<a name="train"></a>
## <a name="access-your-data-during-training"></a>トレーニング中にデータにアクセスする

トレーニング中にデータにアクセスするには、データストアを使用して Azure ストレージ サービスからコンピューティング先にデータをダウンロードするかマウントします。

次の表では、実行中にデータストアの使用方法をコンピューティング先に指示するメソッドの一覧を示します。 

方法|Method|説明|
----|-----|--------
マウントする| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| コンピューティング先にデータストアをマウントするために使用します。
ダウンロード|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|データストアの内容を `path_on_compute` によって指定された場所にダウンロードするために使用します。 <br> このダウンロードは実行前に行われます。
アップロード|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| `path_on_compute` によって指定された場所からデータストアにファイルをアップロードするために使用します。 <br> このアップロードは実行後に行われます。

データストア内の特定のフォルダーまたはファイルを参照し、コンピューティング先で使用できるようにするには、データストアの [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) メソッドを使用します。

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> すべての `datastore` または `datastore.path` オブジェクトは、コンピューティング先でのマウント/ダウンロード パスを表す値を持つ、`"$AZUREML_DATAREFERENCE_XXXX"` という形式の環境変数名に解決されます。 コンピューティング先でのデータストア パスは、トレーニング スクリプトの実行パスと同じであるとは限りません。

### <a name="examples"></a>例 

次のコード例は、トレーニング中に自分のデータにアクセスするための [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスに固有のものです。 

`script_params` は、entry_script に対するパラメーターを含むディクショナリです。 これを使用してデータストアを渡し、コンピューティング先でデータを使用できるようにする方法を記述することができます。 [チュートリアル](tutorial-train-models-with-aml.md)ではさらに詳しく説明しています。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

また、Estimator コンストラクターの `inputs` パラメーターにデータストアのリストを渡して、データストアとの間でデータのマウントまたはコピーを行うこともできます。 コード例は次のとおりです。
* トレーニング スクリプト `train.py` が実行される前に、`datastore1` 内のコンテンツをすべて、コンピューティング先にダウンロードします
* `train.py` が実行される前に、`datastore2` 内のフォルダー `'./foo'` をコンピューティング先にダウンロードします
* スクリプトが実行された後、コンピューティング先から `datastore3` にファイル `'./bar.pkl'` をアップロードします

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
### <a name="compute-and-datastore-matrix"></a>コンピューティングとデータストアのマトリックス

現在、データストアは、次のマトリックスに示すストレージ サービスに対する接続情報の格納をサポートしています。 このマトリックスでは、コンピューティング先とデータストアの異なる組み合わせのシナリオで使用可能なデータ アクセス機能を示します。 詳細については、[Azure Machine Learning のコンピューティング先](how-to-set-up-training-targets.md#compute-targets-for-training)に関する記事を参照してください。

|Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| ローカル|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|該当なし         |該当なし                                                                         |
| Azure Machine Learning コンピューティング |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、[ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、[ML&nbsp;pipelines](concept-ml-pipelines.md)|該当なし         |該当なし                                                                         |
| 仮想マシン               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、[as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |該当なし         |該当なし                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |該当なし         |該当なし                                                                         |
| データ転送                  |[ML&nbsp;パイプライン](concept-ml-pipelines.md)                                               |該当なし                                           |[ML&nbsp;パイプライン](concept-ml-pipelines.md)            |[ML&nbsp;パイプライン](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;パイプライン](concept-ml-pipelines.md)                                              |該当なし                                           |[ML&nbsp;パイプライン](concept-ml-pipelines.md)             |該当なし                                                                         |
| Azure Batch                    |[ML&nbsp;パイプライン](concept-ml-pipelines.md)                                               |該当なし                                           |該当なし         |該当なし                                                                         |
| Azure Data Lake Analytics       |該当なし                                           |該当なし                                           |[ML&nbsp;パイプライン](concept-ml-pipelines.md)             |該当なし                                                                         |

> [!NOTE]
> `as_mount()` ではなく `as_download()` を使用して、高度に反復的で大規模なデータ処理を高速で実行するシナリオがある場合があります。これは実験的に検証することができます。

## <a name="access-data-during-scoring"></a>スコアリング中にデータにアクセスする

Azure Machine Learning service には、スコアリングにモデルを使用する方法が複数用意されています。 これらの方法の一部では、データストアへのアクセスは提供されていません。 次の表を使用して、スコアリング中にデータストアへのアクセスが許可されるのはどの方法かを理解します。

| Method | データストア アクセス | 説明 |
| ----- | :-----: | ----- |
| [バッチ予測](how-to-run-batch-predictions.md) | ✔ | 大量のデータの予測を非同期的に行います。 |
| [Web サービス](how-to-deploy-and-where.md) | &nbsp; | モデルを Web サービスとしてデプロイします。 |
| [IoT Edge モジュール](how-to-deploy-and-where.md) | &nbsp; | モデルを IoT Edge デバイスにデプロイします。 |

データストアへのアクセスが SDK から提供されない場合は、関連する Azure SDK を使用してデータにアクセスするカスタム コードを作成できる場合があります。 たとえば、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) を使用して、BLOB に格納されているデータにアクセスします。


## <a name="next-steps"></a>次の手順

* [モデルをトレーニングする](how-to-train-ml-models.md)

* [モデルのデプロイ](how-to-deploy-and-where.md)
