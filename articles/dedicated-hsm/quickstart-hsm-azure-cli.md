---
title: 'クイックスタート: Azure CLI を使用して Azure 専用 HSM を作成する'
description: Azure CLI を使用して、Azure 専用 HSM を作成、表示、一覧表示、更新、および削除します。
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020861"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>クイックスタート: Azure CLI を使用して Azure 専用 HSM を作成する

この記事では、[az dedicated-hsm](/cli/azure/ext/hardware-security-modules/dedicated-hsm) Azure CLI 拡張機能を使用して、Azure 専用 HSM を作成および管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 アカウントがない場合は、[無料アカウントを作成する](https://azure.microsoft.com/free/)ことができます。
  
  複数の Azure サブスクリプションがある場合は、Azure CLI [az account set](/cli/azure/account#az_account_set) コマンドを使用して、課金に使用するサブスクリプションを設定します。
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- 登録や承認、プロビジョニングに使用する仮想ネットワークと仮想マシンなど、専用 HSM の要件をすべて満たします。 専用 HSM の要件と前提条件の詳細については、「[チュートリアル: Azure CLI を使用して既存の仮想ネットワークに HSM をデプロイする](tutorial-deploy-hsm-cli.md)」を参照してください。
  

## <a name="create-a-resource-group"></a>リソース グループを作成する

[Azure リソース グループ](../azure-resource-manager/management/overview.md)とは、Azure リソースをグループとしてデプロイおよび管理するための論理コンテナーです。 専用 HSM 用のリソース グループがまだない場合は、最初に [az group create](/cli/azure/group#az_group_create) コマンドを使用して作成します。 次の例では、`myRG` という名前のリソース グループを `westus` Azure リージョンに作成します。

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>専用 HSM を作成する

専用 HSM を作成するには、[az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) コマンドを使用します。 次の例では、`westus` リージョン、`myRG` リソース グループ、指定のサブスクリプション、仮想ネットワーク、およびサブネットで、`hsm1` という名前の専用 HSM をプロビジョニングします。 必須のパラメーターは、`name`、`location`、および `resource group` です。

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

デプロイが完了するまで約 25 から 30 分かかります。

## <a name="get-a-dedicated-hsm"></a>専用 HSM を取得する

現在の専用 HSM を取得するには、[az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) コマンドを実行します。 次の例では、`myRG` リソース グループ内の `hsm1` 専用 HSM を取得します。

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>専用 HSM を更新する

専用 HSM を更新するには、[az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) コマンドを使用します。 次の例では、`myRG` リソース グループ内の `hsm1` 専用 HSM と、そのタグを更新します。

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>専用 HSM を一覧表示する

現在の専用 HSM に関する情報を取得するには、[az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) コマンドを実行します。 次の例では、`myRG` リソース グループ内の専用 HSM を一覧表示します。

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>専用 HSM を削除する

専用 HSM を削除するには、[az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) コマンドを使用します。 次の例では、`myRG` リソース グループから `hsm1` 専用 HSM を削除します。

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>リソース グループを削除します

専用 HSM 用に作成したリソース グループが不要になった場合は、[az group delete](/cli/azure/group#az_group_delete) コマンドを実行して削除できます。 このコマンドでは、グループとその中のすべてのリソース (専用 HSM に関連付けられていないものを含む) を削除します。 次の例では、`myRG` リソース グループとその中のすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>次のステップ

Azure Dedicated HSM の詳細については、[Azure Dedicated HSM](overview.md) に関する記事をご覧ください。
