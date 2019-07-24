---
title: Azure Storage を Terraform バックエンドとして使用する
description: Terraform 状態を Azure Storage に格納する方法の紹介。
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: 7145a50bc53fd28afafd3de9c724b5e5f71624fa
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762308"
---
# <a name="store-terraform-state-in-azure-storage"></a>Terraform 状態を Azure Storage に格納する

Terraform 状態は、Terraform 構成を使用してデプロイされたリソースを調整するために使用されます。 Terraform では、状態を使用して、追加、更新、または削除する Azure リソースが認識されます。 既定では、*Terraform apply* を実行すると、Terraform 状態がローカルに格納されます。 この構成は、次のようないくつかの理由で理想的ではありません。

- ローカルの状態は、チーム環境または共同作業環境ではあまり実用的でない
- Terraform 状態に機密情報が含まれる可能性がある
- 状態をローカルに格納すると、不注意で削除される可能性が高くなる

Terraform には、状態バックエンドの概念が含まれています。状態バックエンドは、Terraform 状態のリモート ストレージです。 状態バックエンドを使用すると、状態ファイルは Azure Storage などのデータ ストアに格納されます。 このドキュメントでは、Azure Storage を Terraform の状態バックエンドとして構成して使用する方法について詳しく説明します。

## <a name="configure-storage-account"></a>ストレージ アカウントの構成

Azure Storage をバックエンドとして使用する前に、ストレージ アカウントを作成する必要があります。 ストレージ アカウントは、Azure portal、PowerShell、Azure CLI、または Terraform 自体を使用して作成できます。 Azure CLI を使用してストレージ アカウントを構成するには、次のサンプルを使用します。

```azurecli-interactive
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

ストレージ アカウント名、コンテナー名、ストレージ アクセス キーをメモしておきます。 これらの値は、リモート状態を構成するときに必要です。

## <a name="configure-state-backend"></a>状態バックエンドの構成

Terraform の状態バックエンドは、*Terraform init* の実行時に構成されます。 状態バックエンドを構成するには、次のデータが必要です。

- storage_account_name - Azure ストレージ アカウントの名前。
- container_name - BLOB コンテナーの名前。
- key - 作成される状態ストア ファイルの名前。
- access_key - ストレージ アクセス キー。

これらの値は Terraform 構成ファイルまたはコマンド ラインでそれぞれ指定できますが、`access_key` には環境変数を使用することをお勧めします。 環境変数を使用することで、キーがディスクに書き込まれるのを防ぐことができます。

`ARM_ACCESS_KEY` という名前の環境変数を作成し、その値に Azure ストレージ アクセス キーを指定します。

```console
export ARM_ACCESS_KEY=<storage access key>
```

Azure ストレージ アカウントのアクセス キーをさらに保護するには、これを Azure Key Vault に格納します。 環境変数は、次のようなコマンドを使用して設定できます。 Azure Key Vault の詳細については、[Azure Key Vault のドキュメント][azure-key-vault]を参照してください。

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

バックエンドが使用されるよう Terraform を構成するには、Terraform 構成に *backend* 構成を含め、その種類を *azurerm* に設定します。 *storage_account_name*、*container_name*、*key* の値を構成ブロックに追加します。

次の例では、Terraform バックエンドを構成し、Azure リソース グループを作成します。 値は、実際の環境の値に置き換えてください。

```json
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

次に、*Terraform init* を使用して構成を初期化したうえで、*Terraform apply* を使用して構成を実行します。 完了すると、Azure Storage Blob で状態ファイルを見つけることができます。

## <a name="state-locking"></a>状態のロック

Azure Storage Blob を状態ストレージに使用すると、BLOB は、状態の書き込みを伴うすべての操作の前に自動的にロックされます。 この構成によって、破損を引き起こす可能性がある、複数による同時実行の状態操作が防止されます。 詳細については、Terraform ドキュメントの「[State Locking (状態のロック)][terraform-state-lock]」を参照してください。

Azure portal または他の Azure 管理ツールを使用して BLOB を調べると、ロックを確認できます。

![ロックされた Azure BLOB](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>保存時の暗号化

既定では、Azure BLOB の格納データは、ストレージ インフラストラクチャに永続化される前に暗号化されます。 Terraform で必要になると、状態はバックエンドから取得され、開発システム上のメモリに格納されます。 この構成では、状態は Azure Storage 内で保護され、お客様のローカル ディスクには書き込まれません。

Azure Storage 暗号化の詳細については、「[Azure Storage Service Encryption for data at rest (保存データ向け Azure Storage Service Encryption)][azure-storage-encryption]」を参照してください。

## <a name="next-steps"></a>次の手順

Terraform バックエンドの構成の詳細については、[Terraform バックエンドのドキュメント][terraform-backend]を参照してください。

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
