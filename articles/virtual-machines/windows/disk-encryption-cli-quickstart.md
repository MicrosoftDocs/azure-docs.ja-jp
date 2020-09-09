---
title: Azure CLI を使用して Windows VM を作成、暗号化する
description: このクイックスタートでは、Azure CLI を使用して Windows 仮想マシンを作成して暗号化する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e501cd74d652e6415b6e8f750ddfb3d182a8387
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496136"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>クイック スタート:Azure CLI を使用して Windows VM を作成、暗号化する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイックスタートでは、Azure CLI を使用して、Windows Server 2016 仮想マシン (VM) を作成、暗号化する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルにインストールして使用することを選択した場合、このクイックスタートでは Azure CLI バージョン 2.0.30 以降を実行していることが必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成します。 この例では、管理ユーザーの名前に *azureuser*、パスワードに *myPassword12* を使用します。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

VM とサポートするリソースを作成するには数分かかります。 次の出力例では、成功した VM 作成操作を示します。

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>暗号化キー用に構成されたキー コンテナーの作成

Azure Disk Encryption では、その暗号化キーは Azure キー コンテナーに格納されます。 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) を使用して、キー コンテナーを作成します。 キー コンテナーで暗号化キーを格納できるようにするには、--enabled-for-disk-encryption パラメーターを使用します。
> [!Important]
> 各キー コンテナーには一意の名前が必要です。 次の例では、*myKV* という名前のキー コンテナーを作成しますが、ご自身では別の名前を付けるようにしてください。

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>仮想マシンを暗号化する

[az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest) を使用して、一意のキー コンテナー名を --disk-encryption-keyvault パラメーターに指定して、VM を暗号化します。

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

VM で暗号化が有効になっていることは、[az vm show](/cli/azure/vm/encryption#az-vm-encryption-show) を使用して検証できます。

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

返される出力に、次の内容が含まれます。

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループ、VM、およびキー コンテナーを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、仮想マシンを作成し、暗号化キー用に有効にされたキー コンテナーを作成し、VM を暗号化しました。  次の記事に進み、IaaS VM に対する Azure Disk Encryption の前提条件について詳しく学習してください。

> [!div class="nextstepaction"]
> [Azure Disk Encryption の概要](disk-encryption-overview.md)
