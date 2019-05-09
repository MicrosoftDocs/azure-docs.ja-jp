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
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: 0ab01187b03b3d658b171029003667588382bd7f
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563536"
---
# <a name="access-data-from-your-datastores"></a>データストアからデータにアクセスする

 Azure Machine Learning service では、ソース コードを変更する必要なくストレージにアクセスできるよう、データストアはコンピューティングの場所に依存しないメカニズムになっています。 パラメーターとしてパスを受け取るようにトレーニング コードを記述するか、またはエスティメーターに直接データストアを提供するかに関係なく、Azure Machine Learning のワークフローでは、コンピューティング コンテキストからデータストアの場所にアクセスして使用できることが保証されます。

ここでは、次のタスクの例を示します。
* [データストアの選択](#access)
* [データの取得](#get)
* [データのデータストアへのアップロードとダウンロード](#up-and-down)
* [トレーニング中のデータストアへのアクセス](#train)

## <a name="prerequisites"></a>前提条件

データストアを使用するには、まず[ワークスペース](concept-azure-machine-learning-architecture.md#workspace)が必要です。

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

既存の Azure Storage がある場合は、ワークスペース上のデータストアとして登録できます。   すべての登録メソッドは [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) クラス上にあり、register_azure_* という形式があります。 

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

自分のデータストアをコンピューティング先で使用できるようにすると、トレーニング スクリプト内のパラメーターとしてデータストアへのパスを渡すだけで、トレーニングの実行中にアクセスすることができます (トレーニングや検証データなど)。

次の表では、実行中にデータストアの使用方法をコンピューティング先に指示する [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) メソッドの一覧を示します。

方法|方法|説明|
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

### <a name="compute-context-and-datastore-type-matrix"></a>コンピューティング コンテキストとデータストアの型の関係

次の表では、コンピューティング コンテキストとデータストアの異なる組み合わせのシナリオで使用可能なデータ アクセス関数を示します。 この表の "パイプライン" という用語は、[Azure Machine Learning パイプライン](https://docs.microsoft.com/azure/machine-learning/service/concept-ml-pipelines)の入力または出力としてデータストアを使用する機能を示します。

||ローカル コンピューティング|Azure Machine Learning コンピューティング|データ転送|Databricks|HDInsight|Azure Batch|Azure Data Lake Analytics|Virtual Machines|
-|--|-----------|----------|---------|-----|--------------|---------|---------|
|AzureBlobDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] <br> パイプライン|パイプライン|パイプライン|[`as_download()`] <br> [`as_upload()`]|パイプライン||[`as_download()`] <br> [`as_upload()`]|
|AzureFileDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] パイプライン |||[`as_download()`] [`as_upload()`]|||[`as_download()`] [`as_upload()`]|
|AzureDataLakeDatastore|||パイプライン|パイプライン|||パイプライン||
|AzureDataLakeGen2Datastore|||パイプライン||||||
|AzureDataPostgresSqlDatastore|||パイプライン||||||
|AzureSqlDatabaseDataDatastore|||パイプライン||||||


> [!NOTE]
> [`as_mount()`] ではなく [`as_download()`] を使用して、高度に反復的で大規模なデータ処理を高速で実行するシナリオがある場合があります。これは実験的に検証することができます。

### <a name="examples"></a>例 

次のコード例は、トレーニング中に自分のデータストアにアクセスするための [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスに固有のものです。

このコードでは、`script_params` で定義したパラメーターを使用し、指定したソース ディレクトリのトレーニング スクリプト `train.py` を使用して、エスティメーターが作成されます。すべては、指定したコンピューティング先で行われます。

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
