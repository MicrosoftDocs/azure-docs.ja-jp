---
title: Azure Policy を使用して VM 拡張機能のインストールを制限する | Microsoft Docs
description: Azure Policy を使用して VM 拡張機能の展開を制限できます。
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz
ms.reviewer: cynthn
ms.openlocfilehash: 79a65dc0e7aa85b2f8cef5f884fab7bc6cd076fe
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876720"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Azure Policy を使用して Linux VM への拡張機能のインストールを制限する

自分の Linux VM で特定の拡張機能が使用またはインストールされないようにする必要がある場合は、CLI を使用して Azure ポリシーを作成すると、リソース グループ内で VM の拡張機能を制限できます。 

このチュートリアルでは、Azure Cloud Shell で CLI を使用します。このバージョンは常に更新され最新になっています。 Azure CLI をローカルで実行する場合は、バージョン 2.0.26 以降をインストールする必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="create-a-rules-file"></a>規則ファイルを作成する

どの拡張機能をインストールできるようにするかを制限するには、拡張機能を特定するロジックを提供する[規則](../../governance/policy/concepts/definition-structure.md#policy-rule)が必要です。

この例では、Azure Cloud Shell で規則ファイルを作成することで、"Microsoft.OSTCExtensions" によって発行された拡張機能のインストールを拒否する方法を示していますが、ローカルの CLI で作業している場合は、ローカル ファイルを作成して、パス (~/clouddrive) を、ご利用のマシンのそのローカル ファイルへのパスに置き換えることもできます。

[Bash Cloud Shell](https://shell.azure.com/bash) で、次のように入力します。

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

以下の .json をコピーして、ファイルに貼り付けます。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

完了したら、**Esc** キーを押して、「 **:wq**」と入力し、ファイルを保存して閉じます。


## <a name="create-a-parameters-file"></a>パラメーター ファイルを作成する

また、ブロックする拡張機能の一覧を渡すための構造体が自動的に作成されるように、[パラメーター](../../governance/policy/concepts/definition-structure.md#parameters) ファイルを作成する必要もあります。 

この例では、Cloud Shell で Linux VM 用のパラメーター ファイルを作成する方法を示していますが、ローカルの CLI で作業している場合は、ローカル ファイルを作成して、パス (~/clouddrive) を、ご利用のマシンのそのローカル ファイルへのパスに置き換えることもできます。

[Bash Cloud Shell](https://shell.azure.com/bash) で、次のように入力します。

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

以下の .json をコピーして、ファイルに貼り付けます。

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

完了したら、**Esc** キーを押して、「 **:wq**」と入力し、ファイルを保存して閉じます。

## <a name="create-the-policy"></a>ポリシーの作成

ポリシー定義は、使用したい構成を格納するためのオブジェクトです。 ポリシー定義では、規則とパラメーター ファイルを使用してポリシーを定義します。 ポリシー定義は、[az policy definition create](/cli/azure/role/assignment?view=azure-cli-latest) を使用して作成します。

この例において、規則とパラメーターは、使用しているクラウド シェル内で、.json ファイルとして作成、格納したファイルです。

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>ポリシーを割り当てる

この例では、[az policy assignment create](/cli/azure/policy/assignment) を使用して、ポリシーをリソース グループに割り当てます。 **myResourceGroup** リソース グループに作成された VM はどれも、Linux VM Access や Linux 用のカスタム スクリプト拡張機能をインストールできません。 ポリシーを割り当てるには、必ずリソース グループが存在している必要があります。

[az account list](/cli/azure/account?view=azure-cli-latest) を使用して、この例の ID の代わりに使用するご自身のサブスクリプション ID を取得します。


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>ポリシーをテストする

ポリシーをテストするために、新しい VM を作成し、新しいユーザーの追加してみます。


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

VM Access 拡張機能を使用して、**myNewUser** という名前の新しいユーザーを作成してみます。

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>割り当てを解除する

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>ポリシーを削除する

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>次の手順

詳細については、[Azure Policy](../../governance/policy/overview.md) に関するページをご覧ください。