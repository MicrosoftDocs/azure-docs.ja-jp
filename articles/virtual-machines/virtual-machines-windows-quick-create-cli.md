---
title: "Azure クイック スタート - CLI で Windows VM を作成する | Microsoft Docs"
description: "Azure CLI で Windows 仮想マシンを作成する方法を簡単に説明します。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/20/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 0f9ff487e289eadb857508134b7e08b00360fdd3
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>Azure CLI で Windows 仮想マシンを作成する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して、Windows Server 2016 を実行する仮想マシンをデプロイする方法について詳しく説明します。

開始する前に、Azure CLI がインストールされていることを確認してください。 詳細については、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)を参照してください。 

## <a name="log-in-to-azure"></a>Azure へのログイン 

[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#create) を使用して VM を作成します。 

次の例では、`myVM` という名前の VM を作成します。 この例では、管理ユーザーの名前に `azureuser`、パスワードに ` myPassword12` を使用します。 これらの値を、環境に適した内容に更新します。 これらの値は、仮想マシンとの接続を作成する際に必要です。

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 パブリック IP アドレスを書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスを仮想マシンのパブリック IP アドレスに置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```bash 
mstsc /v:<Public IP Address>
```

## <a name="delete-virtual-machine"></a>仮想マシンの削除

必要がなくなったら、次のコマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[ロールのインストールとファイアウォールの構成のチュートリアル](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[VM デプロイ CLI サンプルを探索する](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
