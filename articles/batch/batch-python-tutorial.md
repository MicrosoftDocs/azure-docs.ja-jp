---
title: "チュートリアル - Python 向け Azure Batch SDK を使用する | Microsoft Docs"
description: "Azure Batch の基本的な概念について説明し、Python でシンプルなソリューションを作成します。"
services: batch
documentationcenter: python
author: tamram
manager: timlt
editor: 
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d1c327e90ca3343703784c22aba93280d4599723
ms.lasthandoff: 04/03/2017


---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Python 向け Batch SDK の概要

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
>
>

Python で作成された小さな Batch アプリケーションについて考察しながら [Azure Batch][azure_batch] と [Batch Python][py_azure_sdk] クライアントの基礎を取り上げます。 2 つのサンプル スクリプトが、Batch サービスを利用してクラウド上の Linux 仮想マシンで並列ワークロードを処理するようすや、それらのスクリプトから [Azure Storage](../storage/storage-introduction.md) とやり取りしてファイルを転送したり取得したりする方法について見ていきましょう。 また、一般的な Batch アプリケーション ワークフローと、ジョブ、タスク、プール、コンピューティング ノードなど、Batch の主なコンポーネントの基本も理解できます。

![Batch ソリューションのワークフロー (基本)][11]<br/>

## <a name="prerequisites"></a>前提条件
この記事は、Python の実務知識を持ち、Linux に精通している読者を想定しています。 また、以下で指定されている、Azure、Batch サービス、Storage サービスのアカウント作成要件を満たせることも前提としています。

### <a name="accounts"></a>アカウント
* **Azure アカウント**: まだ Azure サブスクリプションを持っていない場合は、[無料 Azure アカウントを作成][azure_free_account]します。
* **Batch アカウント**: Azure サブスクリプションの用意ができたら、 [Azure Batch アカウントを作成](batch-account-create-portal.md)します。
* **ストレージ アカウント**: 「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」の「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」を参照してください。

### <a name="code-sample"></a>サンプル コード
Python チュートリアルの[コード サンプル][github_article_samples]は、GitHub の [azure-batch-samples][github_samples] リポジトリに置かれている多数の Batch コード サンプルの 1 つです。 リポジトリのホーム ページから **[Clone or download (複製またはダウンロード)]、[Download ZIP (ZIP のダウンロード)]** の順にクリックするか、[azure-batch-samples-master.zip][github_samples_zip] というダウンロード リンクを直接クリックすると、すべてのサンプルをダウンロードできます。 ZIP ファイルの内容を抽出すると、このチュートリアルで使う 2 つのスクリプトが `article_samples` ディレクトリに展開されます。

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Python 環境
サンプル スクリプト *python_tutorial_client.py* をローカル ワークステーションで実行するには、バージョン **2.7** または **3.3+** と互換性のある **Python インタープリター**が必要です。 このスクリプトは、Linux と Windows の両方でテストされています。

### <a name="cryptography-dependencies"></a>暗号化の依存関係
`azure-batch` と `azure-storage` の Python パッケージに必要な、[暗号化][crypto]ライブラリの依存関係をインストールする必要があります。 プラットフォームに適した次のいずれかの操作を実行するか、[暗号化インストール][crypto_install]の詳細を参照してください。

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> Linux に Python 3.3+ をインストールする場合は、Python 依存関係には python3 に対応するものを使用します。 たとえば、Ubuntu では次のようになります: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Azure パッケージ
次に、**Azure Batch** と **Azure Storage** の Python パッケージをインストールします。 両方のパッケージをインストールするには、**pip** と、ここにある *requirements.txt* を使用します。

`/azure-batch-samples/Python/Batch/requirements.txt`

Batch パッケージと Storage パッケージをインストールするには、次の **pip** コマンドを実行します。

`pip install -r requirements.txt`

または、[azure-batch][pypi_batch] と [azure-storage][pypi_storage] の Python パッケージを手動でインストールしてもかまいません。

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> 特権のないアカウントを使用する場合、コマンドの前に「`sudo`」を入力する必要があります。 たとえば、「 `sudo pip install -r requirements.txt`」のように入力します。 Python パッケージのインストールの詳細については、python.org の「[Installing Packages (パッケージのインストール)][pypi_install]」を参照してください。
>
>

## <a name="batch-python-tutorial-code-sample"></a>Batch Python チュートリアルのコード サンプル
Batch Python チュートリアルのコード サンプルは、2 つの Python スクリプトといくつかのデータ ファイルで構成されています。

* **python_tutorial_client.py**: Batch サービスおよび Storage サービスとやり取りして、コンピューティング ノード (仮想マシン) で並列ワークロードを実行します。 *python_tutorial_client.py* スクリプトは、ローカル ワークステーションで実行します。
* **python_tutorial_task.py**: Azure のコンピューティング ノードで実際の作業を行うために実行されるスクリプトです。 このサンプルの *python_tutorial_task.py* では、Azure Storage からダウンロードされたファイル (入力ファイル) のテキストを解析します。 次に、入力ファイル内で出現回数が多い上位 3 つの単語の一覧を含むテキスト ファイル (出力ファイル) を生成します。 *python_tutorial_task.py* は出力ファイルの作成後、そのファイルを Azure Storage にアップロードします。 ローカル ワークステーションで実行されているクライアント スクリプトは、ここから出力ファイルをダウンロードすることができます。 *python_tutorial_task.py* スクリプトは、Batch サービス内の複数のコンピューティング ノードで並列に実行されます。
* **./data/taskdata\*.txt**: コンピューティング ノード上で実行されるタスクの入力として、この 3 つのテキスト ファイルを使用します。

次の図は、クライアント スクリプトとタスク スクリプトによって実行される主な処理を示しています。 この基本ワークフローは、Batch で作成する多くのコンピューティング ソリューションの中でも一般的なものです。 Batch サービスで使用できるすべての機能を網羅しているわけではありませんが、同様のワークフローは、ほぼすべての Batch シナリオに含まれます。

![Batch のワークフロー例][8]<br/>

[**手順 1.**](#step-1-create-storage-containers) Azure Blob Storage で**コンテナー**を作成します。<br/>
[**手順 2.**](#step-2-upload-task-script-and-data-files) タスク スクリプトと入力ファイルをコンテナーにアップロードします。<br/>
[**手順 3.**](#step-3-create-batch-pool) Batch **プール**を作成します。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** プール **StartTask** は、そこに参加したノードにタスク スクリプト (python_tutorial_task.py) をダウンロードします。<br/>
[**手順 4.**](#step-4-create-batch-job) Batch **ジョブ**を作成します。<br/>
[**手順 5.**](#step-5-add-tasks-to-job) ジョブに **タスク** を追加します。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** タスクはノード上で実行されるようにスケジュールされています。<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** 各タスクは Azure Storage から入力データをダウンロードし、実行を開始します。<br/>
[**手順 6.**](#step-6-monitor-tasks) タスクを監視するします。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** タスクの完了後に、出力データを Azure Storage にアップロードします。<br/>
[**手順 7.**](#step-7-download-task-output) Storage からタスク出力をダウンロードします。

既に述べたように、このとおりの実行手順ではない Batch ソリューションも存在します。他の手順が含まれる場合もありますが、このサンプルでは、Batch ソリューションの一般的なプロセスを示します。

## <a name="prepare-client-script"></a>クライアント スクリプトの準備
サンプルを実行する前に、Batch および Storage アカウントの資格情報を *python_tutorial_client.py* に追加します。 まだこの処理が完了していない場合は、任意のエディターでファイルを開き、次の行に実際の資格情報を指定してください。

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

[Azure Portal][azure_portal] の各サービスのアカウント ブレード内に Batch および Storage アカウント資格情報があります。

![ポータルの Batch の資格情報][9]
![ポータルの Storage の資格情報][10]<br/>

スクリプトから Batch サービスでワークロードを処理する際の手順については、以降の各セクションで詳しく見ていきます。 以降の記事は、ご使用のエディターでスクリプトを参照しながら読み進めることをお勧めします。

**python_tutorial_client.py** 内の次の行に移動し、手順 1. から始めてください。

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>手順 1: ストレージ コンテナーを作成する
![Azure Storage でコンテナーを作成する][1]
<br/>

Batch には、Azure Storage とやり取りするための組み込みのサポートが含まれています。 Storage アカウントのコンテナーは、Batch アカウントで実行するタスクで必要なファイルを提供します。 また、タスクによって生成される出力データを格納する場所も提供します。 *python_tutorial_client.py* スクリプトではまず、[Azure Blob Storage](../storage/storage-introduction.md#blob-storage) に 3 つのコンテナーを作成します。

* **application**: このコンテナーには、タスクによって実行される Python スクリプト (*python_tutorial_task.py*) が格納されます。
* **input**: タスクで、 *input* コンテナーから処理対象のデータ ファイルをダウンロードします。
* **output**: タスクで入力ファイルの処理を完了した後に、結果を *output* コンテナーにアップロードします。

ストレージ アカウントを使用してコンテナーを作成するために、[azure-storage][pypi_storage] パッケージを使用して [BlockBlobService][py_blockblobservice] オブジェクト ("BLOB クライアント") を作成します。 そのうえで BLOB クライアントを使用してストレージ アカウントに 3 つのコンテナーを作成します。

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

コンテナーを作成すると、アプリケーションから、タスクで使用するファイルをアップロードできるようになります。

> [!TIP]
> 「[Python から Azure BLOB ストレージを使用する方法](../storage/storage-python-how-to-use-blob-storage.md)」に、Azure Storage コンテナーと BLOB の操作がわかりやすく示されています。 Batch を扱う場合は、この記事を早い段階で読むことをお勧めします。
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>手順 2: タスク スクリプトとデータ ファイルをアップロードする
![タスク アプリケーションと入力 (データ) ファイルをコンテナーにアップロードする][2]
<br/>

ファイルのアップロード操作で、*python_tutorial_client.py* にローカル コンピューターの **application** と **input** のファイル パスのコレクションをまず定義します。 次に、それらのファイルを前の手順で作成したコンテナーにアップロードします。

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

リストの内包表記を使用し、コレクション内の各ファイルについて `upload_file_to_container` 関数を呼び出し、2 つの [ResourceFile][py_resource_file] コレクションにデータを投入します。 `upload_file_to_container` 関数は次のとおりです。

```python
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
[ResourceFile][py_resource_file] は、Batch のタスクに対して、タスクの実行前にコンピューティング ノードにダウンロードする Azure Storage のファイルの URL を提供します。 [ResourceFile][py_resource_file].**blob_source** プロパティには、Azure Storage にあるファイルの完全な URL を指定します。 URL には、ファイルに対する安全なアクセスを提供する Shared Access Signature (SAS) も含めることができます。 Batch 内のほとんどの種類のタスクには、次のように *ResourceFiles* プロパティが含まれます。

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

このサンプルには JobPreparationTask または JobReleaseTask という種類のタスクは使用しませんが、詳細については、「 [Azure Batch コンピューティング ノードでのジョブ準備タスクとジョブ完了タスクの実行](batch-job-prep-release.md)」を参照してください。

### <a name="shared-access-signature-sas"></a>Shared Access Signature (SAS)
Shared Access Signature (SAS) は、Azure Storage のコンテナーと BLOB への安全なアクセスを提供することができる文字列です。 *python_tutorial_client.py* スクリプトは、BLOB とコンテナー両方の Shared Access Signature を使用し、Storage サービスからこれらの Shared Access Signature 文字列を取得する方法を示します。

* **BLOB Shared Access Signature**: プールの StartTask は、Storage からタスク スクリプトと入力データ ファイルをダウンロードするときに BLOB の Shared Access Signature を使用します (以下の [手順 3.](#step-3-create-batch-pool) を参照してください)。 *python_tutorial_client.py* 内の `upload_file_to_container` 関数には、各 BLOB の Shared Access Signature を取得するコードが含まれます。 これは、Storage モジュールの [BlockBlobService.make_blob_url][py_make_blob_url] を呼び出すことによって行います。
* **コンテナー Shared Access Signature**: 各タスクでコンピューティング ノードでの処理が完了すると、その出力ファイルが Azure Storage の *output* コンテナーにアップロードされます。 その際、コンテナーへの書き込みアクセスを可能にする Shared Access Signature が *python_tutorial_task.py* によって使用されます。 *python_tutorial_client.py* の `get_container_sas_token` 関数でコンテナーの Shared Access Signature を取得し、それをコマンド ライン引数としてタスクに渡します。 コンテナーの SAS の使用方法については、「 [手順 5: ジョブにタスクを追加する](#step-5-add-tasks-to-job)」で説明します。

> [!TIP]
> Storage アカウントのデータに安全なアクセスを提供する方法については、Shared Access Signature に関する 2 つの記事、[第 1 部: SAS モデルの概要](../storage/storage-dotnet-shared-access-signature-part-1.md)と[第 2 部: BLOB service での SAS の作成と使用](../storage/storage-dotnet-shared-access-signature-part-2.md)に関するページをご覧ください。
>
>

## <a name="step-3-create-batch-pool"></a>手順 3: Batch プールを作成する
![Batch プールを作成する][3]
<br/>

Batch **プール**は複数のコンピューティング ノード (仮想マシン) をまとめたものです。Batch は、このプールでジョブのタスクを実行することになります。

*python_tutorial_client.py* は、タスク スクリプトとデータ ファイルをストレージ アカウントにアップロードした後、Batch Python モジュールを使用して Batch サービスとのやり取りを開始します。 そのための [BatchServiceClient][py_batchserviceclient] を作成します。

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

次に、 `create_pool`を呼び出してコンピューティング ノードのプールが Batch アカウントに作成されます。

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

プールを作成するときに [PoolAddParameter][py_pooladdparam] を定義し、プールのプロパティをいくつか指定します。

* プールの **ID** (*id* - 必須)<p/>Batch のほとんどのエンティティと同様、新しいプールには、Batch アカウント内で一意となる ID が必要です。 このプールをコードから参照するときには、対応する ID を使用します。[Azure Portal][azure_portal] でも、このようにしてプールを識別することになります。
* **コンピューティング ノード数** (*target_dedicated* - 必須)<p/>プールにデプロイする VM の数を指定するプロパティです。 すべての Batch アカウントには、1 つの Batch アカウントで使用できる**コア**数 (つまり、コンピューティング ノード数) に上限を設ける既定の**クォータ**が割り当てられています。 既定のクォータと、[クォータを増やす](batch-quota-limit.md#increase-a-quota)手順 (Batch アカウントの最大コア数を増やす方法など) については、「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。 なぜかプール内のノードが一定数を超えない、と疑問を感じている場合、 このコア クォータが原因である可能性があります。
* ノードの**オペレーティング システム** (*virtual_machine_configuration* **または** *cloud_service_configuration* - 必須)<p/>*python_tutorial_client.py* では、[VirtualMachineConfiguration][py_vm_config] を使用して、Linux ノードのプールを作成します。 `common.helpers` の `select_latest_verified_vm_image_with_node_agent_sku` 関数を使用すると、[Azure Virtual Machines Marketplace][vm_marketplace] イメージの操作を簡素化できます。 Marketplace イメージの使用の詳細については、「 [Azure Batch プールの Linux コンピューティング ノードのプロビジョニング](batch-linux-nodes.md) 」を参照してください。
* **コンピューティング ノードのサイズ** (*vm_size* - 必須)<p/>ここでは [VirtualMachineConfiguration][py_vm_config] に Linux ノードを指定するため、[Azure の仮想マシンのサイズ](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に基づいて VM サイズ (このサンプルでは `STANDARD_A1`) を指定します。 詳細については、「 [Azure Batch プールの Linux コンピューティング ノードのプロビジョニング](batch-linux-nodes.md) 」を参照してください。
* **開始タスク** (*start_task* - 任意)<p/>前に示した物理ノードのプロパティに加え、必要に応じてプールの [StartTask][py_starttask] も指定できます。 各ノードがプールに参加するときと、ノードの再起動のたびに、各ノードで StartTask が実行されます。 StartTask は、タスクの実行に使用するコンピューティング ノードを準備する (たとえばタスクで実行するアプリケーションをインストールする) 場合に特に有効です。<p/>このサンプル アプリケーションでは、StartTask が、StartTask "*作業ディレクトリ*" の Storage からダウンロードするファイル (StartTask の **resource_files** プロパティを使用して指定します) を、ノードで実行されるすべてのタスクからアクセスできる "*共有*" ディレクトリにコピーします。 基本的に、これはノードがプールに参加するときに各ノードの共有ディレクトリに `python_tutorial_task.py` をコピーし、ノードで実行するすべてのタスクがアクセスできるようにします。

`wrap_commands_in_shell` ヘルパー関数の呼び出しに注目してください。 これは、独立した複数のコマンドのコレクションを引数として受け取り、タスクのコマンド ライン プロパティに適した単一のコマンド ラインを作成する関数です。

また、上記のコード スニペットでは、StartTask の **command_line** プロパティで `AZ_BATCH_TASK_WORKING_DIR` と `AZ_BATCH_NODE_SHARED_DIR` という 2 つの環境変数を使用している点についても注目してください。 Batch プールの各コンピューティング ノードには、Batch に固有の環境変数がいくつか自動的に構成されます。 また、タスクによって実行されるプロセスは、これらの環境変数に対するアクセス権を持ちます。

> [!TIP]
> Batch プールのコンピューティング ノードで使用できる環境変数と、タスクの作業ディレクトリの詳細については、[Azure Batch 機能の概要](batch-api-basics.md)に関するページの「**タスクの環境設定**」と「**ファイルとディレクトリ**」を参照してください。
>
>

## <a name="step-4-create-batch-job"></a>手順 4: Batch ジョブを作成する
![Batch ジョブを作成する][4]<br/>

基本的に、Batch **ジョブ** はタスクのコレクションであり、コンピューティング ノードのプールに関連付けられます。 ジョブに含まれる一連のタスクは、関連付けられているプールのコンピューティング ノードで実行されます。

ジョブを使うと、関連するワークロードのタスクを整理し、追跡するだけでなく、ジョブ (さらにはタスク) の最長実行時間をはじめとする一定の制限や、Batch アカウントの他のジョブと関連するジョブの優先度を設けることができます。 ただし、この例では、ジョブは手順 3 で作成したプールにのみ関連付けられています。 他のプロパティは構成されていません。

すべての Batch ジョブは、特定のプールに関連付けられています。 この関連付けはジョブのタスクがどのノードで実行されるかを示します。 プールを指定するには、以下のコード スニペットに示すとおり、[PoolInformation][py_poolinfo] プロパティを使用します。

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

ジョブを作成したので、次は作業を実行するタスクを実行します。

## <a name="step-5-add-tasks-to-job"></a>手順 5: ジョブにタスクを追加する
![ジョブにタスクを追加する][5]<br/>
"*(1) タスクをジョブに追加します。(2) ノードで実行されるようにタスクをスケジュールします。(3) タスクで処理対象のデータ ファイルをダウンロードします*"

Batch の **タスク** は、コンピューティング ノードで実行される独立した作業単位です。 タスクはコマンド ラインを持ち、スクリプト (またはそのコマンド ラインに指定された実行可能ファイル) を実行します。

実際に作業を実行するには、タスクをジョブに追加する必要があります。 コマンド ラインが自動的に実行される前に、タスクによってノードにダウンロードされる [ResourceFiles][py_resource_file] (プールの StartTask と同様) とコマンド ライン プロパティを使用して、各 [CloudTask][py_task] を構成します。 このサンプルでは、各タスクで処理するファイルは 1 つだけです。 したがって、その ResourceFiles コレクションには、1 つの要素が含まれています。

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> `$AZ_BATCH_NODE_SHARED_DIR` などの環境変数にアクセスする場合や、ノードの `PATH` にないアプリケーションを実行する場合は、`/bin/sh -c MyTaskApplication $MY_ENV_VAR` を使用するなど、タスク コマンド ラインからシェルを明示的に呼び出す必要があります。 タスクがノードの `PATH` 内にあるアプリケーションを実行し、環境変数を参照しない場合、この要件は不要です。
>
>

上記のコード スニペットの `for` ループ内では、5 つのコマンド ライン引数を *python_tutorial_task.py* が受け取るようにタスクのコマンド ラインが構成されています。

1. **filepath**: これは、ノードに存在するファイルのローカル パスです。 先ほど手順 2. で `upload_file_to_container` の ResourceFile オブジェクトを作成したときに、ファイル名がこのプロパティに (ResourceFile コンストラクターの `file_path` パラメーターとして) 使用されています。 そのため、ノード上で、*python_tutorial_task.py* と同じディレクトリにファイルがあることがわかります。
2. **numwords**: 上位 *N* 個の単語を出力ファイルに書き出すように指定するものです。
3. **storageaccount**: タスクからの出力のアップロード先となるコンテナーを所有するストレージ アカウントの名前。
4. **storagecontainer**: 出力ファイルのアップロード先となるストレージ コンテナーの名前。
5. **sastoken**: Azure Storage の **output** コンテナーに対する書き込みアクセス権を提供する Shared Access Signature (SAS)。 この Shared Access Signature は、*python_tutorial_task.py* スクリプトがその BlockBlobService 参照を作成するときに使用します。 これでストレージ アカウントのアクセス キーがなくてもコンテナーへの書き込みアクセスが可能となります。

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>手順 6: タスクを監視する
![タスクを監視する][6]<br/>
"*スクリプトで (1) タスクの完了の状態を監視し、(2) タスクから結果データを Azure Storage にアップロードします*"

タスクをジョブに追加すると、そのジョブに関連付けられたプール内のコンピューティング ノードに実行待ちとして自動的にキューに追加され、スケジュールされます。 指定した設定に基づき、Batch は、すべてのタスクのキュー、スケジュール、再試行など、タスク管理作業を処理します。

タスクの実行を監視する方法は多数ありますが、 *python_tutorial_client.py* の `wait_for_tasks_to_complete` 関数は、タスクの特定の状態を監視する単純な例です。このケースでは [completed][py_taskstate] 状態が該当します。

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>手順 7: タスクの出力をダウンロードする
![Storage からタスク出力をダウンロードする][7]<br/>

これでジョブが完了したので、タスクの出力を Azure Storage からダウンロードできます。 ダウンロードするには、*python_tutorial_client.py* 内の `download_blobs_from_container` を呼び出します。

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> *python_tutorial_client.py* では、ファイルのダウンロード先がホーム ディレクトリとなるように `download_blobs_from_container` を呼び出しています。 この出力場所は自由に変更できます。
>
>

## <a name="step-8-delete-containers"></a>手順 8: コンテナーを削除する
Azure Storage にあるデータは課金対象なので、Batch ジョブに使用しなくなった BLOB がある場合は削除することをお勧めします。 *python_tutorial_client.py* では、[BlockBlobService.delete_container][py_delete_container] を 3 回呼び出すことによってこの処理を行います。

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>手順 9: ジョブとプールを削除する
最後の手順では、*python_tutorial_client.py* スクリプトで作成されたジョブとプールを削除するかどうかを確認するメッセージが表示されます。 ジョブとタスク自体は課金対象ではありませんが、コンピューティング ノードは " *課金対象* " です。 そのため、必要な場合にのみノードを割り当てることをお勧めします。 使用されていないプールは、メンテナンス プロセスの一環として削除できます。

BatchServiceClient の [JobOperations][py_job] と [PoolOperations][py_pool] には、いずれも対応する削除メソッドがあります。このメソッドは、ユーザーが削除を確定すると呼び出されます。

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> コンピューティング リソースは課金対象であるため、使用していないプールを削除することでコストを最小限に抑えられることを覚えておいてください。 また、プールを削除すると、そのプール内のすべてのコンピューティング ノードが削除され、プールの削除後はノード上のデータを復元できなくなる点にも注意してください。
>
>

## <a name="run-the-sample-script"></a>サンプル スクリプトの実行
チュートリアルの[コード サンプル][github_article_samples]にある *python_tutorial_client.py* スクリプトを実行すると、コンソールの出力は次のようになります。 プールのコンピューティング ノードを作成するときや起動するとき、またはプールの起動タスクのコマンドを実行しているときに、画面に `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` と表示されて待機状態になります。 実行中と実行後のプール、コンピューティング ノード、ジョブ、タスクを監視するには、[Azure Portal][azure_portal] を使用します。 アプリケーションで作成された Storage リソース (コンテナーと BLOB) を表示するには、[Azure Portal][azure_portal] または [Microsoft Azure ストレージ エクスプローラー][storage_explorer]を使用します。

> [!TIP]
> `azure-batch-samples/Python/Batch/article_samples` ディレクトリ内から *python_tutorial_client.py* スクリプトを実行してください。 `common.helpers` モジュール インポートの相対パスが使用されるため、このディレクトリ内からスクリプトを実行しなかった場合に `ImportError: No module named 'common'` が表示されることがあります。
>
>

既定の構成でサンプルを実行する場合、通常の実行時間は **約 5 ～ 7 分間** です。

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>次のステップ
コンピューティングに関するさまざまなシナリオを試すために、*python_tutorial_client.py* と *python_tutorial_task.py* は自由に変更を加えてください。 たとえば、*python_tutorial_task.py* に実行遅延を追加して、実行時間が長いタスクをシミュレートし、ポータルで監視することができます。 タスクを追加したり、コンピューティング ノード数を調整したりすることもできます。 実行時間を短縮するためには、既存のプールの使用をチェックしたり許可したりするためのロジックを追加します。

Batch ソリューションの基本的なワークフローを理解したところで、次は Batch サービスのその他の機能を掘り下げてみましょう。

* このサービスを初めて扱う場合は、 [Azure Batch 機能の概要](batch-api-basics.md) に関する記事を確認することをお勧めします。
* [Batch ラーニング パス][batch_learning_path]の「**開発の詳細**」にある他の Batch 開発記事をお読みください。
* [TopNWords][github_topnwords] サンプルで、Batch を使用した "上位 N 個の単語" ワークロードの処理について、さまざまな実装を確認してください。

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Azure Storage でコンテナーを作成する"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "タスク アプリケーションと入力 (データ) ファイルをコンテナーにアップロードする"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Batch プールを作成する"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Batch ジョブを作成する"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "ジョブにタスクを追加する"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "タスクを監視する"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Storage からタスク出力をダウンロードする"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Batch ソリューション ワークフロー (完全な図)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "ポータルの Batch の資格情報"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "ポータルの Storage の資格情報"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Batch ソリューション ワークフロー (最小限の図)"

