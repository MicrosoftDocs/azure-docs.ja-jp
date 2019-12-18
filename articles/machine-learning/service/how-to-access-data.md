---
title: Azure ストレージ サービスのデータにアクセスする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でトレーニング中にデータストアを使用して Azure ストレージ サービスにアクセスする方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 94cdf683bc8524786e1f32607ef18f976990ba07
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979123"
---
# <a name="access-data-in-azure-storage-services"></a>Azure ストレージ サービスのデータにアクセスする
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning データストアを使用して Azure ストレージ サービスのデータに簡単にアクセスする方法について説明します。 データストアは、サブスクリプション ID やトークン承認など接続情報を格納する目的で使用されます。 データストアを使用すれば、ストレージにアクセスするために接続情報をスクリプトにハードコーディングする必要がなくなります。 データストアは、これらの [Azure Storage ソリューション](#matrix)から作成できます。 サポートされていないストレージ ソリューションの場合は、また機械学習実験中のデータ エグレス コストを節約するために、サポートされている Azure Storage ソリューションにデータを移動することをお勧めします。 [データの移動方法に関する詳細情報](#move)。 

ここでは、次のタスクの例を示します。
* データストアを登録する
* ワークスペースからデータストアを取得する
* データストアを使用してデータをアップロードおよびダウンロードする
* トレーニング中にデータにアクセスする
* Azure Storage サービスにデータを移動する

## <a name="prerequisites"></a>前提条件
次のものが必要になります。
- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

- [Azure BLOB コンテナー](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)または [Azure ファイル共有](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)が含まれる Azure ストレージ アカウント。

- [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [Azure Machine Learning Studio](https://ml.azure.com/) へのアクセス。

- Azure Machine Learning ワークスペース。 
    - [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)するか、Python SDK を使用して既存のワークスペースを使用します。

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>データストアの作成と登録

Azure ストレージ ソリューションをデータストアとして登録すると、データストアが特定のワークスペースに自動的に作成されます。 Python SDK または Azure Machine Learning Studio を使用して、データストアを作成し、ワークスペースに登録することができます。

### <a name="using-the-python-sdk"></a>Python SDK の使用

すべての登録メソッドは [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) クラス上にあり、register_azure_* という形式があります。

register() メソッドにデータを入力するために必要な情報は、[Azure Machine Learning Studio](https://ml.azure.com) と次の手順を使用して見つけることができます。

1. 左側のウィンドウで **[ストレージ アカウント]** を選択し、登録するストレージ アカウントを選択します。 
2. **[概要]** ページには、アカウント名、コンテナー名、ファイル共有名などの情報が表示されます。 
3. アカウント キーまたは SAS トークンなどの認証情報については、左側の **[設定]** ウィンドウの **[アカウント キー]** に移動します。 

>[重要] ストレージ アカウントが VNET 内にある場合は、Azure BLOB データストアの作成のみがサポートされます。 パラメーター `grant_workspace_access` を `True` に設定して、ワークスペースにストレージ アカウントへのアクセス権を付与します。

次の例では、Azure BLOB コンテナーまたは Azure ファイル共有のデータストアとしての登録を示しています。

+ **Azure BLOB コンテナー データストア**の場合、[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) を使用します。

    次のコードでは、データストア (`my_datastore`) を作成し、ワークスペース (`ws`) に登録します。 このデータストアは、指定されたアカウントキーを使用して Azure ストレージ アカウント (`my_storage_account`) の Azure blob コンテナー (`my_blob_container`) にアクセスします。

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ **Azure ファイル共有データストア**の場合、[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) を使用します。 

    次のコードでは、データストア (`my_datastore`) を作成し、ワークスペース (`ws`) に登録します。 このデータストアは、指定されたアカウントキーを使用して Azure ストレージ アカウント (`my_storage_account`) の Azure ファイル共有 (`my_file_share`) にアクセスします。

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>ストレージのガイダンス

Azure BLOB コンテナーをお勧めします。 BLOB では Standard ストレージと Premium ストレージの両方を使用できます。 お勧めは Premium ストレージです。より高価になりますが、スループットの速度が上がるため、特に大規模なデータ セットに対するトレーニングでは、トレーニングの実行速度が向上する可能性があります。 ストレージ アカウントのコストの情報については、[Azure の料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)に関するページをご覧ください。

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning Studio の使用 

Azure Machine Learning Studio のいくつかの手順で、新しいデータストアを作成します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインします。
1. 左側のウィンドウの **[管理]** から、 **[データストア]** を選択します。
1. **[+ 新しいデータストア]** を選択します。
1. 新しいデータスト アフォームに入力します。 フォームは、Azure ストレージの種類と選択した認証の種類に基づいて更新されます。
  
このフォームにデータを入力するために必要な情報は、[Azure portal](https://portal.azure.com) を使用して見つけることができます。 左側のウィンドウで **[ストレージ アカウント]** を選択し、登録するストレージ アカウントを選択します。 **[概要]** ページには、アカウント名、コンテナー名、ファイル共有名などの情報が表示されます。 アカウント キー、SAS トークンなどの認証項目については、左側の **[設定]** ペインの **[アカウント キー]** に移動します。

次の例は、Azure BLOB データストアの作成のためのフォームがどのように表示されるかを示しています。 
    
 ![新しいデータストア](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>ワークスペースからデータストアを取得する

現在のワークスペースに登録されている特定のデータストアを取得するには、Datastore クラスで [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) 静的メソッドを使用します。

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
特定のワークスペースに登録されているデータストアの一覧を取得するには、ワークスペース オブジェクトに対して [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) プロパティを使用します。

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

ワークスペースを作成すると、Azure BLOB コンテナーと Azure ファイル共有が、それぞれ `workspaceblobstore` と `workspacefilestore` という名前のワークスペースに自動的に登録されます。 これらには、ワークスペースにアタッチされているストレージ アカウントでプロビジョニングされた BLOB コンテナーとファイル共有の接続情報が格納されます。 `workspaceblobstore` は既定のデータストアとして設定されます。

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
次の例に示されている [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) と [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) メソッドは、[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) クラスと [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) クラスに固有のものであり、これらのクラスに対して同様に動作します。

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

`target_path` パラメーターでは、アップロードするファイル共有 (または BLOB コンテナー) 内の場所が指定されます。 既定値は `None` で、その場合はデータがルートにアップロードされます。 そうでなく、`overwrite=True` の場合、`target_path` のすべての既存のデータが上書きされます。

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

> [!IMPORTANT]
> トレーニング中にデータにアクセスする手段として、今後は [Azure Machine Learning データセット](how-to-create-register-datasets.md)の使用が推奨されます。 データセットには、表形式データを pandas や spark DataFrame に読み込む機能のほか、Azure Blob、Azure Files、Azure Data Lake Gen 1、Azure Data Lake Gen 2、Azure SQL、Azure PostgreSQL から任意の形式のファイルをダウンロードしたりマウントしたりする機能が用意されています。 [データセットを使ってトレーニングする方法](how-to-train-with-datasets.md)の詳細をご覧ください。

次の表では、実行中にデータストアの使用方法をコンピューティング先に指示するメソッドの一覧を示します。 

方法|方法|説明|
----|-----|--------
マウントする| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| コンピューティング先にデータストアをマウントするために使用します。 マウントされると、データストアのすべてのファイルがコンピューティング先からアクセスできるようになります。
ダウンロード|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|データストアの内容を `path_on_compute` によって指定された場所にダウンロードするために使用します。 <br><br> このダウンロードは実行前に行われます。
アップロード|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| `path_on_compute` によって指定された場所からデータストアにファイルをアップロードするために使用します。 <br><br> このアップロードは実行後に行われます。

データストア内の特定のフォルダーまたはファイルを参照し、コンピューティング先で使用できるようにするには、データストアの [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) メソッドを使用します。

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> 指定した `datastore` または `datastore.path` オブジェクトは、コンピューティング先でのマウント パスまたはダウンロード パスを表す値を持つ、`"$AZUREML_DATAREFERENCE_XXXX"` という形式の環境変数名に解決されます。 コンピューティング先でのデータストア パスは、トレーニング スクリプトの実行パスと同じであるとは限りません。

### <a name="examples"></a>例 

トレーニング中にデータにアクセスするには [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) クラスを使用することをお勧めします。 

`script_params` 変数は、entry_script へのパラメーターを含むディクショナリです。 これを使用してデータストアを渡し、コンピューティング先でデータを使用できるようにする方法を記述します。 [チュートリアル](tutorial-train-models-with-aml.md)ではさらに詳しく説明しています。

```Python
from azureml.train.estimator import Estimator

# notice '/' is in front, this indicates the absolute path
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
トレーニングに RunConfig オブジェクトを使用する場合は、[DataReference](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) オブジェクトを設定する必要があります。 

次のコードは、予測パイプラインで DataReference オブジェクトを操作する方法を示しています。 完全な例については、この[ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb)を参照してください。

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

   output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

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

### <a name="accessing-source-code-during-training"></a>トレーニング中のソース コードへのアクセス

Azure BLOB ストレージは、Azure ファイル共有よりも高いスループット速度を実現し、並列で開始される多数のジョブに対応します。 このため、ソース コード ファイルを転送するために BLOB ストレージを使用するように実行を構成することをお勧めします。

次のコード例では、実行構成で、ソース コード転送に使用する BLOB データストアを指定します。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>スコアリング中にデータにアクセスする

Azure Machine Learning には、スコアリングにモデルを使用する方法が複数用意されています。 これらの方法の一部では、データストアへのアクセスは提供されていません。 次の表を使用して、スコアリング中にデータストアへのアクセスが許可されるのはどの方法かを理解します。

| 方法 | データストア アクセス | 説明 |
| ----- | :-----: | ----- |
| [バッチ予測](how-to-run-batch-predictions.md) | ✔ | 大量のデータの予測を非同期的に行います。 |
| [Web サービス](how-to-deploy-and-where.md) | &nbsp; | モデルを Web サービスとしてデプロイします。 |
| [IoT Edge モジュール](how-to-deploy-and-where.md) | &nbsp; | モデルを IoT Edge デバイスにデプロイします。 |

データストアへのアクセスが SDK から提供されない場合は、関連する Azure SDK を使用してデータにアクセスするカスタム コードを作成できる場合があります。 たとえば、BLOB またはファイルに格納されたデータには、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) というクライアント ライブラリを使用してアクセスすることができます。

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>サポートされている Azure Storage ソリューションにデータを移動する

Azure Machine Learning では、Azure BLOB、Azure File、Azure Data Lake Gen 1、Azure Data Lake Gen 2、Azure SQL、Azure PostgreSQL のデータのアクセスがサポートされています。 サポートされていないストレージの場合、機械学習実験中のデータ エグレス コストを節約するため、Azure Data Factory を使用するサポートされている Azure Storage ソリューションにデータを移行することをお勧めします。 Azure Data Factory では、事前構築済みの 80 を超えるコネクタ (Azure データ サービス、オンプレミスのデータ ソース、Amazon S3 と Redshift、Google BigQuery など) の拡大し続けるポートフォリオとの、効率的で回復力のあるデータ転送が、追加コストなしで提供されます。 [Azure Data Factory を使用してデータを移動するステップ バイ ステップ ガイドに従ってください](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)。

## <a name="next-steps"></a>次の手順

* [モデルをトレーニングします](how-to-train-ml-models.md)。

* [モデルをデプロイします](how-to-deploy-and-where.md)。
