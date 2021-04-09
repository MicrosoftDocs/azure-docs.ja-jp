---
title: Apache Spark プールを使用したデータ ラングリング (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Synapse Analytics と Azure Machine Learning のデータ ラングリング用に Apache Spark プールをアタッチして起動する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: acd8df620e23ee4ebc103d8910c6443f47ffa141
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503829"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>データ ラングリング用に (Azure Synapse Analytics によって機能する) Apache Spark プールをアタッチする (プレビュー)

この記事では、[Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) によって機能する Apache Spark プールを、大規模なデータ ラングリング用にアタッチして起動する方法について説明します。 

この記事には、Jupyter Notebook の専用 Synapse セッション内で対話形式でデータ ラングリング タスクを実行するためのガイダンスが含まれています。 Azure Machine Learning パイプラインを使用する場合は、「[機械学習パイプライン内で (Azure Synapse Analytics で実行される) Apache Spark を使用する方法 (プレビュー)](how-to-use-synapsesparkstep.md)」を参照してください。

>[!IMPORTANT]
> Azure Machine Learning と Azure Synapse Analytics の統合はプレビュー段階です。 この記事で紹介している機能には、[試験的](/python/api/overview/azure/ml/#stable-vs-experimental)なプレビュー機能を含んだ `azureml-synapse` パッケージが採用されており、それらは随時変更される可能性があります。

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning と Azure Synapse Analytics の統合 (プレビュー)

Azure Synapse Analytics と Azure Machine Learning の統合 (プレビュー) によって、Azure Synapse によってサポートされる Apache Spark プールをアタッチし、インタラクティブなデータ探索とデータ準備を行うことができます。 この統合により、大規模なデータ ラングリングを行うための専用のコンピューティングが得られ、機械学習モデルのトレーニングにも使用する Python ノートブック内からそのすべてを利用することができます。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md?tabs=python)します。

* [Azure portal で Azure Synapse Analytics ワークスペースを作成する](../synapse-analytics/quickstart-create-workspace.md)。

* [Azure portal、Web ツール、Synapse Studio のいずれかを使用して Apache Spark プールを作成](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)します

* [開発環境を構成](how-to-configure-environment.md)して Azure Machine Learning SDK をインストールするか、SDK が既にインストールされている [Azure Machine Learning コンピューティング インスタンス](concept-compute-instance.md#create)を使用します。 

* 次のコードを使用して、`azureml-synapse` パッケージ (プレビュー) をインストールします。

  ```python
  pip install azureml-synapse
  ```

* [Azure Machine Learning ワークスペースと Azure Synapse Analytics ワークスペースをリンクします](how-to-link-synapse-ml-workspaces.md)。

## <a name="get-an-existing-linked-service"></a>既にあるリンクされたサービスを取得する
データ ラングリング専用のコンピューティングをアタッチする前に、Azure Synapse Analytics ワークスペースにリンクされている ML ワークスペースが必要です。これは、リンクされたサービスと呼ばれます。 

既にあるリンクされたサービスを取得して使用するためには、Azure Synapse Analytics ワークスペースに対する **ユーザーまたは共同作成者** のアクセス許可が必要です。

Machine Learning ワークスペースに関連付けられているリンクされたサービスをすべて表示します。 

```python
LinkedService.list(ws)
```

この例では、[`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) メソッドを使用して、既にあるリンクされたサービス (`synapselink1`) をワークスペース (`ws`) から取得します。
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Synapse Spark プールをコンピューティングとしてアタッチする

リンクされたサービスを取得したら、データ ラングリング タスク専用のコンピューティング リソースとして Synapse Apache Spark プールをアタッチします。 

Apache Spark プールは、次の手段でアタッチできます。
* Azure Machine Learning Studio
* [Azure Resource Manager (ARM) テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>スタジオを使用してプールをアタッチする
次の手順に従います。 

1. [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインします。
1. 左ペインの **[管理]** セクションにある **[リンクされたサービス]** を選択します。
1. Synapse ワークスペースを選択します。
1. 左上の **[Attached Spark pools]\(アタッチされた Spark プール\)** を選択します。 
1. **[接続]** を選択します。 
1. Apache Spark プールを一覧から選択し、名前を入力します。  
    1. コンピューティングにアタッチできる使用可能な Synapse Spark プールが、この一覧によって認識されます。 
    1. 新しい Synapse Spark プールを作成するには、[Synapse Studio を使用して Apache Spark プールを作成する](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)方法に関するページを参照してください。
1. **[Attach selected]\(選択されたアタッチ\)** を選択します。 

### <a name="attach-a-pool-with-the-python-sdk"></a>Python SDK を使用してプールをアタッチする

**Python SDK** を使用して Apache Spark プールをアタッチすることもできます。 

以下のコードは、次の処理を実行します。 
1. 次の情報を使用して SynapseCompute を構成します。

   1. 前の手順で作成または取得した LinkedService (`linked_service`)。 
   1. アタッチするコンピューティング先の種類 (`SynapseSpark`)
   1. Apache Spark プールの名前。 これは、Azure Synapse Analytics ワークスペースにある既存の Apache Spark プールと一致している必要があります。
   
1. 次の情報を渡して機械学習の ComputeTarget を作成します。 
   1. 使用する Machine Learning ワークスペース (`ws`)
   1. Azure Machine Learning ワークスペース内でコンピューティングを参照する場合の名前。 
   1. Synapse コンピューティングを構成するときに指定した attach_configuration。
       1. ComputeTarget.attach() の呼び出しは非同期であるため、このサンプルは、呼び出しが完了するまでブロック状態になります。

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Apache Spark プールがアタッチされていることを確認します。

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>データ準備タスク用に Synapse Spark プールを起動する

Apache Spark セッション中に使用する [Azure Machine Learning 環境](concept-environments.md)を指定できます。 この環境に指定された Conda 依存関係のみが有効になります。 Docker イメージはサポートされません。

>[!WARNING]
>  環境 Conda 依存関係に指定された Python 依存関係は、Apache Spark プールではサポートされません。 現在サポートされるのは、固定の Python バージョンのみです。 スクリプトに `sys.version_info` を追加して Python バージョンを確認してください。

次のコードでは、セッションの開始前に `azureml-core` バージョン 1.20.0 および `numpy` バージョン 1.17.0 をインストールする環境 (`myenv`) を作成します。 その後、Apache Spark セッションの `start` ステートメントにこの環境を含めることができます。

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Apache Spark プールを使用したデータ準備を開始するには、Apache Spark プールの名前を指定し、サブスクリプション ID、Machine Learning ワークスペースのリソース グループ、Machine Learning ワークスペースの名前、Apache Spark セッション中に使用する環境を入力します。 

> [!IMPORTANT]
> 引き続き Apache Spark プールを使用するためには、データ ラングリング タスクで使用するコンピューティング リソースを `%synapse` (1 行のコードの場合) および `%%synapse` (複数行の場合) で指定する必要があります。 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

セッションの開始後、セッションのメタデータを確認できます。

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>ストレージからデータを読み込む

Apache Spark セッションが開始されたら、準備するデータを読み取ります。 データの読み込みは、Azure Blob Storage および Azure Data Lake Storage Generation 1 および 2 でサポートされます。

これらのストレージ サービスからデータを読み込むには、2 とおりの方法があります。 

* 対応する Hadoop Distributed Files System (HDFS) パスを使用して、ストレージから直接データを読み込みます。

* 既存の [Azure Machine Learning データセット](how-to-create-register-datasets.md)からデータを読み取ります。

これらのストレージ サービスにアクセスするには、**ストレージ BLOB データ閲覧者** のアクセス許可が必要です。 これらのストレージ サービスにデータを書き戻す予定がある場合は、**ストレージ BLOB データ共同作成者** のアクセス許可が必要となります。 [詳細については、ストレージのアクセス許可とロールに関するページを参照](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)してください。

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Hadoop Distributed Files System (HDFS) パスを使用してデータを読み込む

対応する HDFS パスを使用して、ストレージからのデータの読み込みと読み取りを行うには、データ アクセスの認証資格情報を手元に用意しておく必要があります。 これらの資格情報は、ストレージの種類によって異なります。  

次のコードは、Shared Access Signature (SAS) トークンまたはアクセス キーを使用して、**Azure Blob Storage** から Spark データフレームにデータを読み取る方法を示しています。 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

次のコードは、サービス プリンシパルの資格情報を使用して、**Azure Data Lake Storage Generation 1 (ADLS Gen 1)** からデータを読み取る方法を示しています。 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

次のコードは、サービス プリンシパルの資格情報を使用して、**Azure Data Lake Storage Generation 2 (ADLS Gen 2)** からデータを読み取る方法を示しています。 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>登録済みデータセットからデータを読み取る

ワークスペースで既存の登録済みデータセットを取得し、それを Spark データフレームに変換することによってデータ準備を行うこともできます。  

次の例では、Blob Storage 内のファイルを参照する登録済みの TabularDataset (`blob_dset`) を取得し、それを Spark データフレームに変換します。 Spark データフレームにデータセットを変換する際は、`pyspark` のデータ探索ライブラリおよびデータ準備ライブラリを活用できます。  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>データ ラングリング タスクを実行する

データを取得して調査した後、データ ラングリング タスクを実行することができます。

次のコードは、前セクションで見た HDFS の例を応用し、**Survivor** 列に基づいて Spark データフレーム (`df`) のデータをフィルター選択し、そのリストを **Age** でグループ化します。

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>データをストレージに保存して Spark セッションを停止する

データ探索とデータ準備が完了したら、後で使用できるよう準備済みのデータを Azure 上のストレージ アカウントに保存します。

次の例では、準備済みのデータを Azure Blob Storage に書き戻し、`training_data` ディレクトリにある元の `Titanic.csv` ファイルを上書きします。 ストレージへの書き戻しを行うには、**ストレージ BLOB データ共同作成者** のアクセス許可が必要となります。 [詳細については、ストレージのアクセス許可とロールに関するページを参照](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)してください。

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

データ準備が完了し、準備済みのデータをストレージに保存したら、次のコマンドを使用して Apache Spark プールの使用を停止します。

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>準備済みのデータを表すデータセットを作成する

準備済みのデータをモデルのトレーニングに利用する際は、[Azure Machine Learning データストア](how-to-access-data.md)にストレージを接続し、[Azure Machine Learning データセット](how-to-create-register-datasets.md)で使用するファイルを指定します。

以下のコード例は、次の処理を行います。

* 準備済みのデータを保存したストレージ サービスに接続するデータストアが作成済みであることを前提としています。  
* その既存のデータストア (`mydatastore`) を、get() メソッドでワークスペース (`ws`) から取得します。
* `mydatastore` の `training_data` ディレクトリに格納されている準備済みのデータ ファイルを参照する [FileDataset](how-to-create-register-datasets.md#filedataset) (`train_ds`) を作成します。  
* 変数 `input1` を作成します。後でこの変数を使用して、コンピューティング先から利用できる `train_ds` データセットのデータ ファイルを作成できます。

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="next-steps"></a>次のステップ

* [モデルをトレーニングします](how-to-set-up-training-targets.md)。
* [Azure Machine Learning データセットをトレーニングする](how-to-train-with-datasets.md)
