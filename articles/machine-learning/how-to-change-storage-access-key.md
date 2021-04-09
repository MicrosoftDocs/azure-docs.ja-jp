---
title: ストレージ アカウントのアクセス キーを変更する
titleSuffix: Azure Machine Learning
description: ご利用のワークスペースで使用される Azure ストレージ アカウントのアクセス キーを変更する方法について説明します。 Azure Machine Learning では、Azure ストレージ アカウントを使用してデータとモデルが格納されます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 787f7727e18b7055055ed2b764ddce5be20f5afc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503438"
---
# <a name="regenerate-storage-account-access-keys"></a>ストレージ アカウント キーの再生成


Azure Machine Learning で使用される Azure ストレージ アカウントのアクセス キーを変更する方法について説明します。 Azure Machine Learning では、ストレージ アカウントを使用してデータまたはトレーニング済みモデルを保存できます。

セキュリティ上の理由から、Azure ストレージ アカウントのアクセス キーを変更することが必要になる場合があります。 アクセス キーを再生成した場合は、その新しいキーを使用するように Azure Machine Learning を更新する必要があります。 Azure Machine Learning では、ストレージ アカウントはモデル ストレージ用に使用される場合とデータストアとして使用される場合の両方があります。

> [!IMPORTANT]

> データストアに登録された資格情報は、ワークスペースに関連付けられている Azure キー コンテナーに保存されます。 キー コンテナーで[論理的な削除](../key-vault/general/soft-delete-overview.md)を有効にしている場合は、この記事に資格情報を更新する手順が記載されています。 データストアの登録を解除し、同じ名前で再登録しようとすると、この操作は失敗します。 このシナリオでの論理的な削除を有効にする方法については、「[既存のキー コンテナーの論理的な削除を有効にする]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、「[ワークスペースの作成](how-to-manage-workspace.md)を参照してください。

* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)。

* [Azure Machine Learning CLI 拡張機能](reference-azure-machine-learning-cli.md)。

> [!NOTE]
> このドキュメントのコード スニペットは、Python SDK のバージョン 1.0.83 を使用してテストされています。

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>更新する必要があるもの

ストレージ アカウントは、Azure Machine Learning のワークスペース (ログ、モデル、スナップショットなどを格納する) で使用することも、データストアとして使用することもできます。 ワークスペースを更新するプロセスは 1 つの Azure CLI コマンドであり、ストレージ キーを更新した後に実行できます。 データストアを更新するプロセスはより複雑で、どのデータストアが現在そのストレージ アカウントを使用しているのかを検出してから再登録する必要があります。

> [!IMPORTANT]
> Azure CLI を使用してワークスペースを更新し、同時に、Python を使用してデータストアを更新します。 どちらか一方を更新するだけでは不十分であり、両方が更新されるまでエラーが発生する可能性があります。

データストアで使用されているストレージ アカウントを検出するには、次のコードを使用します。

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

このコードでは、Azure Storage を使用する登録済みデータストアの有無を検索し、次の情報を一覧表示します。

* データストア名: ストレージ アカウントが登録されているデータストアの名前。
* ストレージ アカウント名: Azure ストレージ アカウントの名前。
* コンテナー: この登録によって使用されるストレージ アカウント内のコンテナー。

また、データストアが Azure Blob 用か Azure File 共有用かも示されています。それぞれの種類のデータストアを再登録するメソッドは異なるためです。

アクセス キーの再生成を計画している対象であるストレージ アカウントのエントリが存在する場合は、データストア名、ストレージ アカウント名、コンテナー名を保存します。

## <a name="update-the-access-key"></a>アクセス キーの更新

新しいキーを使用するように Azure Machine Learning を更新するには、次の手順のようにします。

> [!IMPORTANT]
> すべての手順を実行し、ワークスペース (CLI を使用して) とデータストア (Python を使用して) の両方を更新します。 どちらか一方だけを更新すると、両方が更新されるまでエラーが発生する可能性があります。

1. キーを再生成します。 アクセス キーを再生成する方法の詳細については、「[ストレージ アカウント アクセス キーを管理する](../storage/common/storage-account-keys-manage.md)」を参照してください。 新しいキーを保存します。

1. Azure Machine Learning ワークスペースによって新しいキーは自動的に同期され、1 時間後に使用が開始されます。 ワークスペースを強制的に新しいキーとすぐに同期するには、次の手順を実行します。

    1. ご利用のワークスペースが含まれる Azure サブスクリプションにサインインするには、次の Azure CLI コマンドを使用します。

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. 新しいキーを使用するようにワークスペースを更新するには、次のコマンドを使用します。 `myworkspace` をお使いの Azure Machine Learning ワークスペース名に置き換え、`myresourcegroup` を、そのワークスペースを含む Azure リソース グループの名前に置き換えます。

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        このコマンドにより、ワークスペースで使用する Azure ストレージ アカウントの新しいキーが自動的に同期されます。

1. SDK または [Azure Machine Learning Studio](https://ml.azure.com) を使用して、ストレージ アカウントを使用するデータストアを再登録することができます。
    1. **Python SDK を介してデータストアを再登録する** には、「[更新する必要があるもの](#whattoupdate)」セクションの値とステップ 1 でのキーを次のコードで使用します。 
    
        このコードでは `overwrite=True` が指定されているため、既存の登録が上書きされて、新しいキーを使用するように更新されます。
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. **Studio を使用してデータ ストアを再登録する** には、Studio の左側のペインから **[データストア]** を選択します。 
        1. 更新するデータストアを選択します。
        1. 左上にある **[資格情報の更新]** ボタンを選択します。 
        1. ステップ 1 の新しいアクセス キーを使用してフォームに入力し、 **[保存]** をクリックします。
        
            **既定のデータストア** の資格情報を更新する場合は、このステップを完了し、ステップ 2b を繰り返して、新しいキーをワークスペースの既定のデータストアと再同期します。 

## <a name="next-steps"></a>次のステップ

データストアの登録について詳しくは、[`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29) クラスのリファレンスを参照してください。