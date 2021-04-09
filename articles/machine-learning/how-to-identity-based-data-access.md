---
title: Azure 上のストレージ サービスへの ID ベースのデータ アクセス
titleSuffix: Azure Machine Learning
description: ID ベースのデータ アクセスを使用して Azure 上のストレージ サービスに接続する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 68d07481e228b1d1b2f4571a783f925add261cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520015"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>ID ベースのデータ アクセスを使用したストレージへの接続 (プレビュー)

>[!IMPORTANT]
> この記事に記載されている機能はプレビュー段階です。随時変更される可能性がある[試験段階](/python/api/overview/azure/ml/#stable-vs-experimental)のプレビュー機能と見なす必要があります。

この記事では、ID ベースのデータ アクセスと Azure Machine Learning データストアを使用して、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) を介して Azure 上のストレージ サービスに接続する方法について説明します。  

通常、データストアでは、資格情報ベースのデータ アクセスを使用して、ストレージ サービスにアクセスするためのアクセス許可があることが確認されます。 サブスクリプション ID やトークン認可などの接続情報は、ワークスペースに関連付けられた [Key Vault](https://azure.microsoft.com/services/key-vault/) に格納されます。 ID ベースのデータ アクセスを使用するデータストアを作成する場合は、Azure ログイン ([Azure Active Directory トークン](../active-directory/fundamentals/active-directory-whatis.md)) を使用して、ストレージ サービスにアクセスするためのアクセス許可があることが確認されます。 このシナリオでは、認証資格情報は保存されず、ストレージ アカウント情報のみがデータストアに格納されます。 

アクセス キーやサービス プリンシパルなどを使用した、資格情報ベースの認証を使用するデータストアを作成するには、「[Azure のストレージ サービスに接続する](how-to-access-data.md)」を参照してください。

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Azure Machine Learning での ID ベースの データ アクセス

Azure Machine Learning には、ID ベースのデータ アクセスが適用される領域が 2 つあります。 特に、機密データを扱う場合は、より詳細なデータ アクセス管理が必要です。 

1. ストレージ サービスへのアクセス。
1. プライベート データを使用した機械学習モデルのトレーニング。

### <a name="accessing-storage-services"></a>ストレージ サービスへのアクセス

Azure Machine Learning データストアまたは [Azure Machine Learning データセット](how-to-create-register-datasets.md)を使用した ID ベースのデータ アクセスを使用してストレージ サービスに接続できます。 

通常、認証資格情報は、ストレージ サービスにアクセスするためのアクセス許可があることを確認するために使用されるデータストアに保持されます。 これらの資格情報がデータストアに登録されると、ワークスペースの "*閲覧者*" ロールを持つすべてのユーザーはこれらを取得できます。これは、一部の組織ではセキュリティ上の問題になる可能性があります。 [ワークスペースの "*閲覧者*" ロールの詳細について参照してください](how-to-assign-roles.md#default-roles)。 

ID ベースのデータ アクセスを使用すると、データストアに資格情報を保持する代わりに、Azure Machine Learning によって、データ アクセス認証用の Azure Active Directory トークンの入力が求められます。 これにより、ストレージ レベルでのデータ アクセス管理が可能になり、資格情報が機密として保持されます。 

同じ動作が、次の場合にも当てはまります。

* [ストレージ URL から直接データセットを作成する場合](#use-data-in-storage)。 
* ローカル コンピューターまたは[コンピューティング インスタンス](concept-compute-instance.md)で Jupyter Notebook を使用して対話形式でデータを操作する場合。

> [!NOTE]
> 資格情報ベースの認証を使用して格納される資格情報には、サブスクリプション ID、Shared Access Signature (SAS) トークン、ストレージ アクセス キー、サービス プリンシパル情報 (クライアント ID やテナント ID など) が含まれます。

### <a name="model-training-on-private-data"></a>プライベート データでのモデル トレーニング

特定の機械学習のシナリオには、プライベート データを使用したトレーニング モデルが含まれます。 このような場合、データ サイエンティストは、機密入力データを公開せずにトレーニング ワークフローを実行する必要があります。 このシナリオでは、トレーニング コンピューティングのマネージド ID がデータ アクセス認証に使用されます。 これにより、ストレージ管理者は、個々のデータ サイエンティストではなく、トレーニング コンピューティングでトレーニング ジョブを実行するために使用されるマネージド ID に **ストレージ BLOB データ閲覧者** アクセス許可を付与できます。 [コンピューティングでマネージド ID を設定](how-to-create-attach-compute-cluster.md#managed-identity)する方法を参照してください。


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

- サポートされている種類のストレージを持つ Azure ストレージ アカウント。 プレビューでは、次のストレージの種類がサポートされています。 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Gen 1](../data-lake-store/index.yml)
    - [Azure Data Lake Gen 2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL データベース](../azure-sql/database/sql-database-paas-overview.md)

- [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/install)。

- Azure Machine Learning ワークスペース。
  
  [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)するか、[Python SDK を介して既存のもの](how-to-manage-workspace.md#connect-to-a-workspace)を使用します。 

## <a name="storage-access-permissions"></a>ストレージ アクセスのためのアクセス許可

Azure 上のストレージ サービスに安全に接続できるように、Azure Machine Learning では、対応するデータ ストレージにアクセスするためのアクセス許可が必要です。

ID ベースのデータ アクセスでは、次のストレージ サービスへの接続のみがサポートされます。

* Azure Blob Storage
* Azure Data Lake Generation 1
* Azure Data Lake Generation 2
* Azure SQL データベース

これらのストレージ サービスにアクセスするには、少なくとも **ストレージ BLOB データ閲覧者** のアクセス権が必要です。 [ストレージ BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)の詳細については、こちらを参照してください。 [Azure portal を使用してアクセス レベルを変更](../storage/common/storage-auth-aad-rbac-portal.md)できるのは、ストレージ アカウントの所有者だけです。

リモート コンピューティング先でモデルをトレーニングする場合は、コンピューティング ID にストレージ サービスから **ストレージ BLOB データ閲覧者** 以上のロールが付与されている必要があります。 [コンピューティングでマネージド ID を設定](how-to-create-attach-compute-cluster.md#managed-identity)する方法を参照してください。

## <a name="work-with-virtual-networks"></a>仮想ネットワークの使用

既定では、Azure Machine Learning は、ファイアウォールの内側または仮想ネットワーク内にあるストレージ アカウントとは通信できません。

ストレージ アカウントは、特定の仮想ネットワーク内からのみアクセスを許可するように構成できます。その場合、データがネットワークの外部に漏れることがないように、追加の構成が必要になります。 この動作は、資格情報ベースのデータ アクセスの場合と同じです。[必要な構成とそれらを仮想ネットワークのシナリオに適用する方法](how-to-access-data.md#virtual-network)を参照してください。 

## <a name="create-and-register-datastores"></a>データストアの作成と登録

Azure 上のストレージ サービスをデータストアとして登録すると、特定のワークスペースにそのデータストアが自動的に作成および登録されます。 必要なアクセス許可の種類のガイダンスについては「[ストレージ アクセスのためのアクセス許可](#storage-access-permissions)」、仮想ネットワークの内側にあるデータ ストレージに接続する方法の詳細については「[仮想ネットワークの使用](#work-with-virtual-networks)」のセクションをそれぞれ確認してください。

次のコードでは、認証パラメーター (`sas_token`、`account_key`、`subscription_id`、またはサービス プリンシパル `client_id` など) がないことに注意してください。 この省略は、Azure Machine Learning での認証に ID ベースのデータ アクセスが使用されていることを示します。 データストアの作成は通常、ノートブックまたはスタジオを使用して対話的に行われるため、データ アクセス認証には Azure Active Directory トークンが使用されます。

> [!NOTE]
> データストア名は、小文字、数字、およびアンダースコアのみで構成する必要があります。 

### <a name="azure-blob-container"></a>Azure BLOB コンテナー

Azure BLOB コンテナーをデータストアとして登録するには、[`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) を使用します。

次のコードでは、`credentialless_blob` データストアを作成して `ws` ワークスペースに登録し、それが変数 `blob_datastore` に割り当てられます。 このデータストアは、ストレージ アカウント `my-account-name` の BLOB コンテナー `my_container_name` にアクセスします。

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage Generation 1

Azure Data Lake Storage Generation 1 (ADLS Gen 1) データストアの場合、[register_azure_data_lake()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) を使用して、Azure DataLake Gen 1 ストレージに接続するデータストアを登録します。

次のコードでは、`credentialless_adls1` データストアを作成して `workspace` ワークスペースに登録し、それが変数 `adls_dstore` に割り当てられます。 このデータストアは、`adls_storage` Azure Data Lake Store ストレージ アカウントにアクセスします。

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Azure Data Lake Storage Generation 2 (ADLS Gen 2) データストアの場合、[register_azure_data_lake_gen2()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) を使用して、Azure DataLake Gen 2 ストレージに接続するデータストアを登録します。

次のコードでは、`credentialless_adls2` データストアを作成して `ws` ワークスペースに登録し、それが変数 `adls2_dstore` に割り当てられます。 このデータストアは、ストレージ アカウント `myadls2` の ファイル システム `tabular` にアクセスします。  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>ストレージ内のデータを使用する

[Azure Machine Learning データセット](how-to-create-register-datasets.md)は、Azure Machine Learning でストレージ内のデータを操作するために推奨される方法です。 

データセットを使用すると、データを機械学習タスク (トレーニングなど) 用に遅延評価された使用可能なオブジェクトにパッケージ化できます。 また、データセットを使用すると、Azure BLOB ストレージや Azure Data Lake などの Azure ストレージ サービスからコンピューティング先に任意の形式のファイルを[ダウンロードまたはマウント](how-to-train-with-datasets.md#mount-vs-download)できます。


**ID ベースのデータ アクセスを使用してデータセットを作成する** には、次のオプションがあります。 この種類のデータセットの作成では、データアクセス認証用の Azure Active Directory トークンが採用されています。 

*  ID ベースのデータ アクセスも使用するデータストアからのパスを参照する。 
<br>次の例では、`blob_datastore` が以前に ID ベースのデータ アクセスを使用して作成されています。   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* データストアの作成をスキップし、ストレージ URL から直接データセットを作成する。 現在、この機能では、Azure BLOB と Azure Data Lake Storage Generation 1 および 2 のみがサポートされています。

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**ただし、ID ベースのデータ アクセスを使用して作成されたデータセットを使用するトレーニング ジョブを送信する場合**、Azure Active Directory トークンではなく、トレーニング コンピューティングのマネージド ID がデータ アクセス認証に使用されます。 このシナリオでは、コンピューティングのマネージド IDに、ストレージ サービスから **ストレージ BLOB データ閲覧者** 以上のロールが付与されていることを確認します。 [コンピューティングでマネージド ID を設定](how-to-create-attach-compute-cluster.md#managed-identity)する方法を参照してください。 

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)。
* [データセットを使ってトレーニングする](how-to-train-with-datasets.md)。
* [キー ベースのデータ アクセスを使用してデータストアを作成する](how-to-access-data.md)。
