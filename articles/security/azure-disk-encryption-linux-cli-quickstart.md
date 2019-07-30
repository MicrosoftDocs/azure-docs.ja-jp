---
title: Azure CLI を使用して Linux VM を作成、暗号化する
description: このクイック スタートでは、Azure CLI を使用して Linux 仮想マシンを作成して暗号化する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: c0ae2f6588ede0305e02edf1942ff196aeb807bb
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331573"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>クイック スタート:Azure CLI を使用して Linux VM を作成、暗号化する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure CLI を使用して、Linux 仮想マシン (VM) を作成、暗号化する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択する場合、このクイック スタートでは、Azure CLI バージョン 2.0.30 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成します。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --size Standard_D2S_V3
```

VM とサポートするリソースを作成するには数分かかります。 次の出力例では、成功した VM 作成操作を示します。

```azurecli-interactive
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>暗号化キー用に構成されたキー コンテナーを作成する

Azure Disk Encryption では、その暗号化キーは Azure キー コンテナーに格納されます。 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) を使ってキー コンテナーを作成します。 キー コンテナーで暗号化キーを格納できるようにするには、--enabled-for-disk-encryption パラメーターを使用します。

> [!Important]
> 各 Key Vault には一意の名前が必要です。 次の例では、*myKV* という名前のキー コンテナーを作成しますが、ご自身では別の名前を付けるようにしてください。

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>仮想マシンを暗号化する

[az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest) を使用して、一意のキー コンテナー名を --disk-encryption-keyvault パラメーターに指定して、VM を暗号化します。

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

しばらくすると、"暗号化の要求が受け入れられました。 'show' コマンドを使用して、進行状況を監視してください" というメッセージが返されます。 'show' コマンドは [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show) です。

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

暗号化が有効な場合、返された出力には以下が表示されます。

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループ、VM、および Key Vault を削除できます。 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、仮想マシンを作成し、暗号化キー用に有効化されたキー コンテナーを作成し、VM を暗号化しました。  次の記事に進み、IaaS VM に対する Azure Disk Encryption の前提条件について詳しく学習してください。

> [!div class="nextstepaction"]
> [Azure Disk Encryption の前提条件](azure-security-disk-encryption-prerequisites.md)
