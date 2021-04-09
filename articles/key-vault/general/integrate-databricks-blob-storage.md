---
title: Azure Databricks と Azure Key Vault を使用して Azure Blob Storage にアクセスする
description: このチュートリアルでは、Azure Key Vault に格納されたシークレットを使用して Azure Databricks から Azure Blob Storage にアクセスする方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: fc33b24aac8964d0a8390b4b38c5e1862ed639a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198916"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>チュートリアル:Azure Databricks と Azure Key Vault を使用して Azure Blob Storage にアクセスする

このチュートリアルでは、Azure Key Vault に格納されたシークレットを使用して Azure Databricks から Azure Blob Storage にアクセスする方法について説明します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure CLI を使用してストレージ アカウントと BLOB コンテナーを作成する
> * キー コンテナーを作成し、シークレットを設定する
> * Azure Databricks ワークスペースを作成し、キー コンテナー シークレット スコープを追加する
> * Azure Databricks ワークスペースから自分の BLOB コンテナーにアクセスする

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このチュートリアルを開始する前に、[Azure CLI](/cli/azure/install-azure-cli-windows) をインストールします。

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Azure CLI を使用してストレージ アカウントと BLOB コンテナーを作成する

BLOB を使用するには、まず汎用ストレージ アカウントを作成する必要があります。 [リソース グループ](/cli/azure/group#az-group-create)がない場合は、コマンドを実行する前に作成します。 次のコマンドを実行すると、ストレージ コンテナーのメタデータが作成され、表示されます。 **ID** をメモします。

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![上記のコマンドのコンソール出力。 エンド ユーザーが確認できるように、ID が緑色で強調表示されています。](../media/databricks-command-output-1.png)

BLOB をアップロードするコンテナーを作成する前に、[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールを自分に割り当てる必要があります。 この例では、このロールは前に作成したストレージ アカウントに割り当てられます。

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

これで、ストレージ アカウントにロールを割り当てることができました。次は、BLOB のコンテナーを作成します。

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

コンテナーが作成されたら、そのコンテナーに BLOB (任意のファイル) をアップロードできます。 この例では、helloworld という名前の .txt ファイルがアップロードされます。

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

コンテナー内の BLOB を一覧表示して、コンテナーに対象の BLOB が含まれていることを確認します。

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![上記のコマンドのコンソール出力。 前の手順でコンテナーに格納されたファイルが表示されます。](../media/databricks-command-output-2.png)

次のコマンドを使用して、ストレージ コンテナーの **key1** 値を取得します。 値をメモします。

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![上記のコマンドのコンソール出力。 key1 の値が緑色のボックスで強調表示されています。](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>キー コンテナーを作成し、シークレットを設定する

次のコマンドを使用して、キー コンテナーを作成します。 このコマンドを実行すると、キー コンテナーのメタデータも表示されます。 **ID** と **vaultUri** をメモします。

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![画像](../media/databricks-command-output-4.png)
![上記のコマンドのコンソール出力。 ユーザーが確認できるように、ID と vaultUri の両方が緑色で強調表示されています。](../media/databricks-command-output-5.png)

シークレットを作成するには、次のコマンドを使用します。 シークレットの値を、対象のストレージ アカウントの **key1** 値に設定します。

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Azure Databricks ワークスペースを作成し、キー コンテナー シークレット スコープを追加する

このセクションは、コマンド ラインを使用して完了することはできません。 この[ガイド](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope)に従ってください。 次の操作を行うには、[Azure portal](https://ms.portal.azure.com/#home) にアクセスする必要があります。

1. Azure Databricks リソースを作成する
1. ワークスペースを起動する
1. キー コンテナーを利用するシークレットのスコープを作成する

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Azure Databricks ワークスペースから自分の BLOB コンテナーにアクセスする

このセクションは、コマンド ラインを使用して完了することはできません。 この[ガイド](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks)に従ってください。 次の操作を行うには、Azure Databricks ワークスペースを使用する必要があります。

1. **新しいクラスター** を作成する
1. **新しいノートブック** を作成する
1. Python スクリプトの対応するフィールドに入力する
1. Python スクリプトを実行する

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>次のステップ

キー コンテナーが回復可能であることを確認します。
> [!div class="nextstepaction"]
> [リソースをクリーンアップする](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)