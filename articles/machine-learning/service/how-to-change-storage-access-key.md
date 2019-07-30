---
title: ストレージ アカウントのアクセス キーを変更する
titleSuffix: Azure Machine Learning service
description: ご利用のワークスペースで使用される Azure ストレージ アカウントのアクセス キーを変更する方法について説明します。 Azure Machine Learning service では、Azure ストレージ アカウントを使用してデータとモデルを格納します。 ストレージ アカウントのアクセス キーを再生成した場合は、その新しいキーを使用するように Azure Machine Learning service を更新する必要があります。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 0721542811709e9b938fea3f31bc2a0a28ecdc74
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358777"
---
# <a name="regenerate-storage-account-access-keys"></a>ストレージ アカウント キーの再生成

Azure Machine Learning service で使用される Azure ストレージ アカウントのアクセス キーを変更する方法について説明します。 Azure Machine Learning では、ストレージ アカウントを使用してデータまたはトレーニング済みモデルを保存できます。

セキュリティ上の理由から、Azure ストレージ アカウントのアクセス キーを変更することが必要になる場合があります。 アクセス キーを再生成した場合は、その新しいキーを使用するように Azure Machine Learning を更新する必要があります。 Azure Machine Learning では、ストレージ アカウントはモデル ストレージ用に使用される場合とデータストアとして使用される場合の両方があります。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、「[ワークスペースの作成](setup-create-workspace.md)を参照してください。

* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [Azure Machine Learning CLI 拡張機能](reference-azure-machine-learning-cli.md)。

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>更新する必要があるもの

ストレージ アカウントは、Azure Machine Learning service のワークスペース (ログ、モデル、スナップショットなどを格納する) で使用することも、データストアとして使用することもできます。 ワークスペースを更新するプロセスは 1 つの Azure CLI コマンドであり、ストレージ キーを更新した後に実行できます。 データストアを更新するプロセスはより複雑で、どのデータストアが現在そのストレージ アカウントを使用しているのかを検出してから再登録する必要があります。

> [!IMPORTANT]
> Azure CLI を使用してワークスペースを更新し、同時に、Python を使用してデータストアを更新します。 どちらか一方を更新するだけでは不十分であり、両方が更新されるまでエラーが発生する可能性があります。

データストアで使用されているストレージ アカウントを検出するには、次のコードを使用します。

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

このコードでは、Azure Storage を使用する登録済みデータストアの有無を検索し、次の情報を一覧表示します。

* データストア名: ストレージ アカウントが登録されているデータストアの名前。
* ストレージ アカウント名: Azure ストレージ アカウントの名前。
* コンテナー: この登録によって使用されるストレージ アカウント内のコンテナー。

アクセス キーの再生成を計画している対象であるストレージ アカウントのエントリが存在する場合は、データストア名、ストレージ アカウント名、コンテナー名を保存します。

## <a name="update-the-access-key"></a>アクセス キーの更新

新しいキーを使用するように Azure Machine Learning service を更新するには、次の手順に従います。

> [!IMPORTANT]
> すべての手順を実行し、ワークスペース (CLI を使用して) とデータストア (Python を使用して) の両方を更新します。 どちらか一方だけを更新すると、両方が更新されるまでエラーが発生する可能性があります。

1. キーを再生成します。 アクセス キーを再生成する方法の詳細については、「[ストレージ アカウントの管理](/azure/storage/common/storage-account-manage#access-keys)を参照してください。 新しいキーを保存します。

1. 新しいキーを使用するようにワークスペースを更新するには、次の手順に従います。

    1. ご利用のワークスペースが含まれる Azure サブスクリプションにサインインするには、次の Azure CLI コマンドを使用します。

        ```azurecli-interactive
        az login
        ```

    1. Azure Machine Learning 拡張機能をインストールするには、次のコマンドを使用します。

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. 新しいキーを使用するようにワークスペースを更新するには、次のコマンドを使用します。 `myworkspace` をお使いの Azure Machine Learning ワークスペース名に置き換え、`myresourcegroup` を、そのワークスペースを含む Azure リソース グループの名前に置き換えます。

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        このコマンドにより、ワークスペースで使用する Azure ストレージ アカウントの新しいキーが自動的に同期されます。

1. そのストレージ アカウントを使用するデータストアを再登録するには、「[更新する必要があるもの](#whattoupdate)」セクションでの値とステップ 1 でのキーを次のコードで使用します。

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    このコードでは `overwrite=True` が指定されているため、既存の登録が上書きされて、新しいキーを使用するように更新されます。

## <a name="next-steps"></a>次の手順

データストアの登録について詳しくは、[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) クラスのリファレンスを参照してください。
