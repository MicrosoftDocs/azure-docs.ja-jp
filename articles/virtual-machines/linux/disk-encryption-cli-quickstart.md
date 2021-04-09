---
title: Azure CLI を使用して Linux VM を作成、暗号化する
description: このクイック スタートでは、Azure CLI を使用して Linux 仮想マシンを作成して暗号化する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b600c895f98c46dbaafce7deef86bbbee8f57c99
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561043"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>クイック スタート:Azure CLI を使用して Linux VM を作成、暗号化する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure CLI を使用して、Linux 仮想マシン (VM) を作成、暗号化する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

Azure CLI をローカルにインストールして使用することを選択した場合、このクイックスタートでは Azure CLI バージョン 2.0.30 以降を実行していることが必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成します。

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
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

Azure Disk Encryption では、その暗号化キーは Azure キー コンテナーに格納されます。 [az keyvault create](/cli/azure/keyvault#az_keyvault_create) を使用して、キー コンテナーを作成します。 キー コンテナーで暗号化キーを格納できるようにするには、--enabled-for-disk-encryption パラメーターを使用します。

> [!Important]
> すべてのキー コンテナーに、Azure 全体で一意の名前を付ける必要があります。 次の例の <your-unique-keyvault-name> を任意の名前に置き換えてください。

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>仮想マシンを暗号化する

[az vm encryption](/cli/azure/vm/encryption) を使用して、一意のキー コンテナー名を --disk-encryption-keyvault パラメーターに指定して、VM を暗号化します。

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

しばらくすると、"暗号化の要求が受け入れられました。 'show' コマンドを使用して、進行状況を監視してください" というメッセージが返されます。 'show' コマンドは [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show) です。

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

暗号化が有効な場合、返された出力には以下が表示されます。

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループ、VM、およびキー コンテナーを削除できます。 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、仮想マシンを作成し、暗号化キー用に有効にされたキー コンテナーを作成し、VM を暗号化しました。  次の記事に進み、Linux VM に対する Azure Disk Encryption について詳しく学習してください。

> [!div class="nextstepaction"]
> [Azure Disk Encryption の概要](disk-encryption-overview.md)
