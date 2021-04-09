---
title: Azure のストレージ サービスに接続する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でトレーニング中にデータストアを使用して Azure ストレージ サービスにアクセスする方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/03/2020
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 78b7bab204a08b474ea3c5cf5c2f7735c019a9c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519930"
---
# <a name="connect-to-storage-services-on-azure"></a>Azure のストレージ サービスに接続する

この記事では、Azure Machine Learning データストアと [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) を使用して Azure のデータ ストレージ サービスに接続する方法について説明します。

データストアは、ユーザーの認証資格情報、および元のデータ ソースの整合性を危険にさらすことなく、Azure のストレージ サービスに安全に接続します。 これらには、ワークスペースに関連付けられている[キー コンテナー](https://azure.microsoft.com/services/key-vault/)内のサブスクリプション ID やトークン承認のような接続情報が格納されるため、それらをスクリプトにハードコーディングすることなく、ストレージに安全にアクセスできます。 [これらの Azure Storage ソリューション](#matrix)に接続するデータストアを作成できます。

Azure Machine Learning のデータ アクセス ワークフロー全体におけるデータストアの位置付けの詳細については、[データへの安全なアクセス](concept-data.md#data-workflow)に関するページを参照してください。

ローコード エクスペリエンスについては、[Azure Machine Learning スタジオを使ってデータストアを作成して登録](how-to-connect-data-ui.md#create-datastores)する方法に関する記事を参照してください。

>[!TIP]
> この記事では、サービス プリンシパルや Shared Access Signature (SAS) トークンなど、資格情報ベースの認証資格情報を使用してストレージ サービスに接続することを前提としています。 資格情報がデータストアに登録されている場合は、ワークスペースの "*閲覧者*" ロールを持つすべてのユーザーがこれらの資格情報を取得できることに注意してください。 [ワークスペースの "*閲覧者*" ロールの詳細については、こちらをご覧ください。](how-to-assign-roles.md#default-roles) <br><br>これが懸念される場合は、[ID ベースのアクセスを使用してストレージ サービスに接続](how-to-identity-based-data-access.md)する方法に関するページを参照してください。 <br><br>この機能は[試験段階](/python/api/overview/azure/ml/#stable-vs-experimental)のプレビュー機能であり、随時変更される可能性があります。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

- [サポートされている種類のストレージ](#matrix)を持つ Azure ストレージ アカウント。

- [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro)。

- Azure Machine Learning ワークスペース。
  
  [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)するか、Python SDK を介して既存のワークスペースを使用します。 

    `Workspace` および `Datastore` クラスをインポートし、関数 `from_config()` を使用してファイル `config.json` からサブスクリプション情報を読み込みます。 これにより、既定で現在のディレクトリ内の JSON ファイルが検索されますが、`from_config(path="your/file/path")` を使用してパス パラメーターを指定してファイルを指すこともできます。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    ワークスペースを作成すると、ワークスペースに Azure BLOB コンテナーと Azure ファイル共有がデータストアとして自動的に登録されます。 これらの名前は、それぞれ `workspaceblobstore` および `workspacefilestore` となります。 `workspaceblobstore` は、ワークスペースの成果物と機械学習実験ログを格納するために使用されます。 また、これは **既定のデータストア** として設定され、ワークスペースから削除することはできません。 `workspacefilestore` は、[コンピューティング インスタンス](./concept-compute-instance.md#accessing-files)によって承認されたノートブックと R スクリプトを格納するために使用されます。
    
    > [!NOTE]
    > Azure Machine Learning デザイナーでは、デザイナーのホームページでサンプルを開いたときに、**azureml_globaldatasets** という名前のデータストアが自動的に作成されます。 このデータストアには、サンプル データセットのみが含まれます。 機密データへのアクセスには、このデータストアを使用 **しないでください**。

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>サポートされているデータ ストレージ サービスの種類

現在、データストアは、次のマトリックスに示すストレージ サービスに対する接続情報の格納をサポートしています。 

> [!TIP]
> **サポートされていないストレージ ソリューションの場合**、また ML 実験中のデータ エグレス コストを節約するために、サポートされている Azure Storage ソリューションに [データを移行](#move)します。 

| Storage&nbsp;type | Authentication&nbsp;type | [Azure&nbsp;Machine&nbsp;Learning studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](/python/api/overview/azure/ml/intro) |  [Azure&nbsp;Machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](/rest/api/azureml/) | VS Code
---|---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;Storage](../storage/blobs/storage-blobs-overview.md)| アカウント キー <br> SAS トークン | ✓ | ✓ | ✓ |✓ |✓
[Azure&nbsp;File&nbsp;Share](../storage/files/storage-files-introduction.md)| アカウント キー <br> SAS トークン | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](../data-lake-store/index.yml)| サービス プリンシパル| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](../storage/blobs/data-lake-storage-introduction.md)| サービス プリンシパル| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;SQL&nbsp;Database](../azure-sql/database/sql-database-paas-overview.md)| SQL 認証 <br>サービス プリンシパル| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](../postgresql/overview.md) | SQL 認証| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Database&nbsp;for&nbsp;MySQL](../mysql/overview.md) | SQL 認証|  | ✓* | ✓* |✓*|
[Databricks&nbsp;ファイル&nbsp;システム](/azure/databricks/data/databricks-file-system)| 認証なし | | ✓** | ✓ ** |✓** |

\* MySQL は、パイプラインの [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) でのみサポートされています<br />
\*\* Databricks は、パイプラインの [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep) でのみサポートされています


### <a name="storage-guidance"></a>ストレージのガイダンス

[Azure BLOB コンテナー](../storage/blobs/storage-blobs-introduction.md)のデータストアを作成することをお勧めします。 BLOB では Standard ストレージと Premium ストレージの両方を使用できます。 Premium ストレージはより高価ですが、スループットの速度が上がるため、特に大規模なデータセットに対するトレーニングでは、トレーニングの実行速度が向上する可能性があります。 ストレージ アカウントの費用については、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)に関するページを参照してください。

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) は、Azure Blob Storage を基にして構築され、エンタープライズ ビッグ データ分析用に設計されています。 Data Lake Storage Gen2 の基礎部分は、BLOB ストレージに[階層型名前空間](../storage/blobs/data-lake-storage-namespace.md)を追加したものです。 階層型名前空間には、効率的なデータ アクセスのためにオブジェクトやファイルがディレクトリ階層に編成されています。

## <a name="storage-access-and-permissions"></a>ストレージへのアクセスとアクセス許可

Azure ストレージ サービスに安全に接続できるように、Azure Machine Learning では、対応するデータ ストレージ コンテナーにアクセスするためのアクセス許可が必要です。 このアクセスは、データストアの登録に使用される認証資格情報に依存します。 

### <a name="virtual-network"></a>仮想ネットワーク 

既定では、Azure Machine Learning は、ファイアウォールの内側または仮想ネットワーク内にあるストレージ アカウントとは通信できません。 データ ストレージ アカウントが **仮想ネットワーク** 内にある場合、Azure Machine Learning がデータにアクセスできるようにするためには、追加の構成手順が必要になります。 

> [!NOTE]
> このガイダンスは、[ID ベースのデータ アクセスを使用して作成されたデータストア (プレビュー)](how-to-identity-based-data-access.md) にも適用されます。 

**Python SDK ユーザーの場合**、コンピューティング ターゲットでトレーニング スクリプトを使用してデータにアクセスするには、コンピューティング ターゲットをストレージと同じ仮想ネットワークとサブネット内に配置する必要があります。  

**Azure Machine Learning スタジオ ユーザーの場合**、データセットのプレビュー、プロファイル、自動機械学習など、データセットからのデータの読み取りが可能であることに依存する機能がいくつかあります。 これらの機能で仮想ネットワークの内側にあるストレージを操作するには、[スタジオでワークスペースのマネージド ID](how-to-enable-studio-virtual-network.md) を使用して、Azure Machine Learning が仮想ネットワークの外部からストレージ アカウントにアクセスできるようにします。 

Azure Machine Learning では、仮想ネットワークの外側にあるクライアントからの要求を受信できます。 サービスからのデータを要求しているエンティティが安全であることを確認するには、[ワークスペース用に Azure Private Link を設定](how-to-configure-private-link.md)します。

### <a name="access-validation"></a>アクセス検証

**最初のデータストアの作成と登録のプロセスの一部として**、Azure Machine Learning により、基になるストレージ サービスが存在すること、およびユーザーが指定したプリンシパル (ユーザー名、サービス プリンシパル、または SAS トークン) で指定したストレージにアクセスできることが自動的に検証されます。

**データストアの作成後**、この検証は、データストア オブジェクトが取得されるたび **ではなく**、基になるストレージ コンテナーにアクセスする必要があるメソッドに対してのみ実行されます。 たとえば、データストアからファイルをダウンロードする場合は検証が行われますが、既定のデータストアを変更するだけの場合は、検証は行われません。

基になるストレージ サービスへのアクセスを認証するには、作成するデータストアの種類に対応する `register_azure_*()` メソッドで、アカウント キー、Shared Access Signature (SAS) トークン、またはサービス プリンシパルを指定します。 [ストレージの種類のマトリックス](#matrix)には、各データストアの種類に対応する、サポートされている認証の種類が一覧表示されています。

アカウント キー、SAS トークン、およびサービス プリンシパルの情報は、[Azure portal](https://portal.azure.com) で確認できます。

* 認証にアカウント キーまたは SAS トークンを使用する予定の場合は、左側のウィンドウで **[ストレージ アカウント]** を選択し、登録するストレージ アカウントを選択します。 
  * **[概要]** ページには、アカウント名、コンテナー、ファイル共有名などの情報が表示されます。 
      1. アカウント キーの場合、 **[設定]** ペインの **[アクセス キー]** に移動します。 
      1. SAS トークンの場合は、 **[設定]** ペインの **[共有アクセス署名]** に移動します。

* 認証にサービス プリンシパルを使用する予定の場合は、 **[アプリの登録]** に移動して、使用するアプリを選択します。 
    * 対応する **[概要]** ページに、テナント ID やクライアント ID などの必要な情報が記載されています。

> [!IMPORTANT]
> * Azure Storage アカウントのアクセス キー (アカウント キーまたは SAS トークン) を変更する必要がある場合は、新しい資格情報をワークスペースおよびそれに接続されているデータストアと同期してください。 [更新された資格情報を同期する](how-to-change-storage-access-key.md)方法を参照してください。 
### <a name="permissions"></a>アクセス許可

Azure BLOB コンテナーと Azure Data Lake Gen 2 ストレージの場合は、認証資格情報に **ストレージ BLOB データ閲覧者** アクセスがあることを確認します。 [ストレージ BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)の詳細については、こちらを参照してください。 アカウントの SAS トークンは、既定ではアクセス許可なしに設定されます。 
* データ **読み取りアクセス** の場合、認証資格情報には、コンテナーとオブジェクトに対するリストと読み取りのアクセス許可が少なくとも必要となります。 

* データ **書き込みアクセス** の場合は、書き込みと追加のアクセス許可も必要です。

<a name="python"></a>

## <a name="create-and-register-datastores"></a>データストアの作成と登録

Azure Storage ソリューションをデータストアとして登録すると、特定のワークスペースにそのデータストアが自動的に作成および登録されます。 仮想ネットワークのシナリオに関するガイダンスや、必要な認証資格情報を検索する場所については、「[ストレージへのアクセスとアクセス許可](#storage-access-and-permissions)」のセクションを参照してください。 

このセクションでは、次のストレージの種類に対して Python SDK を使用してデータストアを作成して登録する方法の例を示します。 これらの例で提供されるパラメーターは、データストアを作成および登録するための **必須パラメーター** です。

* [Azure BLOB コンテナー](#azure-blob-container)
* [Azure ファイル共有](#azure-file-share)
* [Azure Data Lake Storage Generation 2](#azure-data-lake-storage-generation-2)

 サポートされている他のストレージ サービスのデータストアを作成するには、[該当する `register_azure_*` メソッドに関するリファレンス ドキュメント](/python/api/azureml-core/azureml.core.datastore.datastore#methods)を参照してください。

コードの少ないエクスペリエンスの方がよい場合は、[Azure Machine Learning Studio でのデータへの接続](how-to-connect-data-ui.md)に関する記事を参照してください。
>[!IMPORTANT]
> データストアの登録を解除し、同じ名前を使用して再登録しようとして失敗した場合は、ワークスペースの Azure Key Vault で、論理的な削除が有効になっていない可能性があります。 既定では、ワークスペースによって作成されたキー コンテナー インスタンスでは論理的な削除が有効になっていますが、既存のキー コンテナーを使用した場合、または 2020 年 10 月より前にワークスペースを作成した場合は、論理的な削除が有効になっていないことがあります。 論理的な削除を有効にする方法の詳細については、「[既存のキー コンテナーの論理的な削除を有効にする]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault)」を参照してください。

> [!NOTE]
> データストア名は、小文字、数字、およびアンダースコアのみで構成する必要があります。 

### <a name="azure-blob-container"></a>Azure BLOB コンテナー

Azure BLOB コンテナーをデータストアとして登録するには、[`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) を使用します。

次のコードでは、データストア `blob_datastore_name` を作成し、ワークスペース `ws` に登録しています。 このデータストアを使うと、指定したアカウント アクセス キーを使用して、`my-account-name` ストレージ アカウントの BLOB コンテナー `my-container-name` にアクセスできます。 仮想ネットワークのシナリオに関するガイダンスや、必要な認証資格情報を検索する場所については、「[ストレージへのアクセスとアクセス許可](#storage-access-and-permissions)」のセクションを参照してください。 

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

### <a name="azure-file-share"></a>Azure ファイル共有

Azure ファイル共有をデータストアとして登録するには、[`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) を使用します。 

次のコードでは、データストア `file_datastore_name` を作成し、ワークスペース `ws` に登録しています。 このデータストアを使うと、指定したアカウント アクセス キーを使用して、`my-account-name` ストレージ アカウントのファイル共有 `my-fileshare-name` にアクセスできます。 仮想ネットワークのシナリオに関するガイダンスや、必要な認証資格情報を検索する場所については、「[ストレージへのアクセスとアクセス許可](#storage-access-and-permissions)」のセクションを参照してください。 

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Azure Data Lake Storage Generation 2 (ADLS Gen 2) データストアの場合、[register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) を使用して、[サービス プリンシパルのアクセス許可](../active-directory/develop/howto-create-service-principal-portal.md)を持つ Azure Data Lake Gen 2 ストレージに接続されている資格情報データストアを登録します。  

サービス プリンシパルを利用するには、[アプリケーションを登録](../active-directory/develop/app-objects-and-service-principals.md)し、Azure ロールベースのアクセス制御 (Azure RBAC) またはアクセス制御リスト (ACL) を使用してデータ アクセスをサービス プリンシパルに付与する必要があります。 詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](../storage/blobs/data-lake-storage-access-control-model.md)」をご覧ください。 

次のコードでは、データストア `adlsgen2_datastore_name` を作成し、ワークスペース `ws` に登録しています。 このデータストアは、指定されたサービス プリンシパルの資格情報を使用して、`account_name` ストレージ アカウントのファイル システム `test` にアクセスします。 仮想ネットワークのシナリオに関するガイダンスや、必要な認証資格情報を検索する場所については、「[ストレージへのアクセスとアクセス許可](#storage-access-and-permissions)」のセクションを参照してください。 

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```



## <a name="create-datastores-with-other-azure-tools"></a>他の Azure ツールを使用してデータストアを作成する
Python SDK とスタジオを使用してデータストアを作成することに加えて、Azure Resource Manager テンプレートや Azure Machine Learning VS Code 拡張機能を使用することもできます。 

<a name="arm"></a>
### <a name="azure-resource-manager"></a>Azure Resource Manager

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) には、データストアの作成に使用できる多数のテンプレートがあります。

これらのテンプレートの使用に関する詳細については、「[Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成します](how-to-create-workspace-template.md)」を参照してください。

### <a name="vs-code-extension"></a>VS Code 拡張機能

Azure Machine Learning VS Code 拡張機能を使用してデータストアを作成して管理する場合、詳細については、[VS Code リソース管理の攻略ガイド](how-to-manage-resources-vscode.md#datastores)に関するページを参照してください。
<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>データストアでデータを使用する

データストアを作成したら、データを操作するための [Azure Machine Learning データセットを作成します](how-to-create-register-datasets.md)。 データセットを使用すると、データを機械学習タスク (トレーニングなど) 用に遅延評価された使用可能なオブジェクトにパッケージ化できます。 

データセットを使用すると、コンピューティング ターゲットでモデルのトレーニングを行うために、Azure Storage サービスから任意の形式のファイルを[ダウンロードまたはマウント](how-to-train-with-datasets.md#mount-vs-download)できます。 [データセットを使って ML モデルをトレーニングする方法の詳細をご覧ください](how-to-train-with-datasets.md)。

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>ワークスペースからデータストアを取得する

現在のワークスペースに登録されている特定のデータストアを取得するには、`Datastore` クラスの静的メソッド [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29#get-workspace--datastore-name-) を使用します。

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
特定のワークスペースに登録されているデータストアの一覧を取得するには、ワークスペース オブジェクトに対して [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29#datastores) プロパティを使用します。

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

ワークスペースの既定のデータストアを取得するには、次の行を使用します。

```Python
datastore = ws.get_default_datastore()
```
次のコードを使用して、既定のデータストアを変更することもできます。 この機能は、SDK でのみサポートされています。 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>スコアリング中にデータにアクセスする

Azure Machine Learning には、スコアリングにモデルを使用する方法が複数用意されています。 これらの方法の一部では、データストアへのアクセスが提供されていません。 次の表を使用して、スコアリング中にデータストアへのアクセスが許可されるのはどの方法かを理解します。

| Method | データストア アクセス | 説明 |
| ----- | :-----: | ----- |
| [バッチ予測](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | 大量のデータの予測を非同期的に行います。 |
| [Web サービス](how-to-deploy-and-where.md) | &nbsp; | モデルを Web サービスとしてデプロイします。 |
| [Azure IoT Edge モジュール](how-to-deploy-and-where.md) | &nbsp; | モデルを IoT Edge デバイスにデプロイします。 |

SDK でデータストアへのアクセスが提供されない場合は、関連する Azure SDK を使用してデータにアクセスするカスタム コードを作成できる場合があります。 たとえば、BLOB またはファイルに格納されたデータには、[Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) というクライアント ライブラリを使用してアクセスすることができます。

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>サポートされている Azure Storage ソリューションにデータを移動する

Azure Machine Learning では、Azure BLOB ストレージ、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、および Azure Database for PostgreSQL からのデータ アクセスがサポートされています。 サポートされていないストレージを使用する場合は、[Azure Data Factory およびこれらの手順](../data-factory/quickstart-create-data-factory-copy-data-tool.md)を使用して、サポートされている Azure Storage ソリューションにデータを移動することをお勧めします。 サポートされているストレージにデータを移動すると、機械学習実験中のデータ エグレス コストを節約する助けとなります。 

Azure Data Factory では、80 を超える構築済みのコネクタによって、追加コストなしで効率的かつ回復性があるデータ転送が提供されます。 これには、Azure のデータ サービス、オンプレミスのデータ ソース、Amazon S3 および Redshift、Google BigQuery などのコネクタが含まれます。

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)
* [モデルをトレーニングする](how-to-set-up-training-targets.md)
* [モデルのデプロイ](how-to-deploy-and-where.md)