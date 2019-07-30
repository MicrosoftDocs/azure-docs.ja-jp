---
title: トレーニングのためにデータストア/BLOB 内のデータにアクセスする
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service でトレーニング中にデータストアを使用して BLOB データ ストレージにアクセスする方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 638d7bfb0e396874415c1055c4b707a65caffa4e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269306"
---
# <a name="access-data-from-your-datastores"></a>データストアからデータにアクセスする

 Azure Machine Learning service では、ソース コードを変更する必要なくストレージにアクセスできるよう、データストアはコンピューティングの場所に依存しないメカニズムになっています。 パラメーターとしてパスを受け取るようにトレーニング コードを記述するか、またはエスティメーターに直接データストアを提供するかに関係なく、Azure Machine Learning のワークフローでは、コンピューティング コンテキストからデータストアの場所にアクセスして使用できることが保証されます。

ここでは、次のタスクの例を示します。
* [データストアの選択](#access)
* [データの取得](#get)
* [データのデータストアへのアップロードとダウンロード](#up-and-down)
* [トレーニング中のデータストアへのアクセス](#train)

## <a name="prerequisites"></a>前提条件

データストアを使用するには、まず[ワークスペース](concept-workspace.md)が必要です。

まず、[新しいワークスペースを作成する](setup-create-workspace.md#sdk)か、既存のワークスペースを取得します。

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>データストアの選択

既存のデータストアを使用するか、独自のデータストアを使用することができます。

### <a name="use-the-default-datastore-in-your-workspace"></a>ワークスペースで既存のデータストアを使用する

 各ワークスペースには、すぐに使用できる登録済みの既定のデータストアがあります。

ワークスペースの既定のデータストアを取得するには、次のコマンドを使用します。

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>ワークスペースで独自のデータストアを登録する

既存の Azure Storage がある場合は、ワークスペース上のデータストアとして登録できます。 

<a name="store"></a>

####  <a name="storage-guidance"></a>ストレージのガイダンス

Blob Storage と BLOB データストアをお勧めします。 BLOB では Standard ストレージと Premium ストレージの両方を使用できます。 お勧めは Premium ストレージです。より高価になりますが、スループットの速度が上がるため、特に大規模なデータ セットに対するトレーニングでは、トレーニングの実行速度が向上する可能性があります。 ストレージ アカウントのコストの情報については、[Azure の料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)に関するページをご覧ください。

>[!NOTE]
> Azure Machine Learning service では、特定のシナリオで役立つ可能性のあるその他の種類のデータストアもサポートされています。 たとえば、データベースに格納されたデータを使用してトレーニングする必要がある場合、AzureSQLDatabaseDatastore または AzurePostgreSqlDatastore を使用できます。 使用可能なデータストアの種類については、[こちらのテーブル](#matrix)をご覧ください。

#### <a name="register-your-datastore"></a>データストアの登録
すべての登録メソッドは [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) クラス上にあり、register_azure_* という形式があります。

次の例では、Azure BLOB コンテナーまたは Azure ファイル共有のデータストアとしての登録を示しています。

+ **Azure BLOB コンテナー データストア**の場合、[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) を使用します。

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ **Azure ファイル共有データストア**の場合、[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) を使用します。 例: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>データストアの検索と定義

現在のワークスペースに登録されている特定のデータストアを取得するには、[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) を使用します。

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

指定したワークスペースにあるすべてのデータストアの一覧を取得するには、次のコードを使用します。

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

現在のワークスペースに異なる既定のデータストアを定義するには、[`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) を使用します。

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>データのアップロードとダウンロード
次の例に示されている [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) と [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) メソッドは、[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) クラスと [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) クラスに固有のものであり、これらのクラスに対して同様に動作します。

### <a name="upload"></a>アップロード

 Python SDK を使用して、データストアにディレクトリまたは個々のファイルをアップロードします。

データストア `ds` にディレクトリをアップロードするには、次のコマンドを使用します。

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` では、アップロードするファイル共有 (または BLOB コンテナー) 内の場所が指定されます。 既定値は `None` で、その場合はデータがルートにアップロードされます。 `overwrite=True` では、`target_path` のすべての既存のデータが上書きされます。

または、データストアの `upload_files()` メソッドを使用して、データストアに個々のファイルの一覧をアップロードします。

### <a name="download"></a>ダウンロード
同様に、データストアからローカル ファイル システムにデータをダウンロードします。

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` は、データのダウンロード先となるローカル ディレクトリの場所です。 ダウンロードするファイル共有 (または BLOB コンテナー) 内のフォルダーのパスを指定するには、`prefix` にそのパスを指定します。 `prefix` が `None` である場合は、ファイル共有 (または BLOB コンテナー) のすべてのコンテンツがダウンロードされます。

<a name="train"></a>
## <a name="access-datastores-during-training"></a>トレーニング中のデータストアへのアクセス

自分のデータストアをトレーニング コンピューティング先で使用できるようにすると、トレーニング スクリプト内のパラメーターとしてデータストアへのパスを渡すだけで、トレーニングの実行中にアクセスすることができます (トレーニングや検証データなど)。

次の表では、実行中にデータストアの使用方法をコンピューティング先に指示する [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) メソッドの一覧を示します。

方法|Method|説明|
----|-----|--------
マウントする| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| コンピューティング先にデータストアをマウントするために使用します。
ダウンロード|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|データストアの内容を `path_on_compute` によって指定された場所にダウンロードするために使用します。 <br> トレーニング実行のコンテキストでは、このダウンロードは実行の前に行われます。
アップロード|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| `path_on_compute` によって指定された場所からデータストアにファイルをアップロードするために使用します。 <br> トレーニング実行のコンテキストでは、このアップロードは実行の後で行われます。

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

データストア内の特定のフォルダーまたはファイルを参照し、コンピューティング先で使用できるようにするには、データストアの [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) 関数を使用します。

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> すべての `ds` または `ds.path` オブジェクトは、コンピューティング先でのマウント/ダウンロード パスを表す値を持つ、`"$AZUREML_DATAREFERENCE_XXXX"` という形式の環境変数名に解決されます。 コンピューティング先でのデータストア パスは、トレーニング スクリプトの実行パスと同じであるとは限りません。

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>トレーニング コンピューティングとデータストアのマトリックス

次のマトリックスでは、トレーニング コンピューティング先とデータストアの異なる組み合わせのシナリオで使用可能なデータ アクセス機能を示します。 詳細については、[Azure Machine Learning のトレーニング コンピューティング先](how-to-set-up-training-targets.md#compute-targets-for-training)に関する記事を参照してください。

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

### <a name="examples"></a>例 

次のコード例は、トレーニング中に自分のデータストアにアクセスするための [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスに固有のものです。

このコードでは、`script_params` で定義したパラメーターを使用し、指定したソース ディレクトリのトレーニング スクリプト `train.py` を使用して、エスティメーターが作成されます。すべては、指定したトレーニング コンピューティング先で行われます。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

また、Estimator コンストラクターの `inputs` パラメーターにデータストアのリストを渡して、データストアとの間でマウントまたはコピーを行うこともできます。 コード例は次のとおりです。
* トレーニング スクリプト `train.py` が実行される前に、データストア `ds1` 内のコンテンツをすべて、コンピューティング先にダウンロードします
* `train.py` が実行される前に、データストア `ds2` 内のフォルダー `'./foo'` をコンピューティング先にダウンロードします
* スクリプトが実行された後、コンピューティング先からデータストア `ds3` にファイル `'./bar.pkl'` をアップロードします

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>次の手順

* [モデルをトレーニングする](how-to-train-ml-models.md)

* [モデルのデプロイ](how-to-deploy-and-where.md)
