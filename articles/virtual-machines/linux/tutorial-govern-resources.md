---
title: チュートリアル - Azure CLI を使用して Azure 仮想マシンを管理する | Microsoft Docs
description: このチュートリアルでは、Azure CLI を使用して、RBAC、ポリシー、ロック、およびタグを適用することによって Azure 仮想マシンを管理する方法について説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/12/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: d3182c51ca80a26159e962a6354a53b5283326a2
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343070"
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli"></a>チュートリアル:Azure CLI を使用した Azure 仮想マシンの管理方法の説明

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="understand-scope"></a>スコープを理解する

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

このチュートリアルでは、すべての管理設定をリソース グループに適用して、完了したらこれらの設定を容易に削除できるようにします。

そのリソース グループを作成しましょう。

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

現在、リソース グループは空です。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

組織のユーザーがこれらのリソースへの適切なアクセス レベルを持つようにします。 ユーザーに無制限のアクセス権を許可したくはありませんが、ユーザーが自分の作業を実行できるようにすることも必要です。 [ロールベースのアクセス制御](../../role-based-access-control/overview.md)を使うと、あるスコープで特定のアクションを実行するアクセス許可を持つユーザーを管理することができます。

ロールの割り当てを作成および削除するには、`Microsoft.Authorization/roleAssignments/*` アクセス権が必要です。 このアクセス権は、所有者ロールまたはユーザー アクセス管理者ロールを通じて許可されます。

仮想マシン ソリューションを管理するために、一般的に必要なアクセスを提供する、リソースに固有の次の 3 つのロールがあります。

* [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Network Contributor](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Storage Account Contributor](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

多くの場合は、個々のユーザーにロールを割り当てる代わりに、類似のアクションを実行する必要のあるユーザーを Azure Active Directory グループにまとめて使用する方が簡単です。 その後、そのグループを適切なロールに割り当てます。 この記事では、仮想マシンを管理するための既存のグループを使用するか、またはポータルを使用して [Azure Active Directory グループを作成](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)します。

新しいグループを作成するか、または既存のロールを見つけた後、[az role assignment create](/cli/azure/role/assignment) コマンドを使って、リソース グループの仮想マシン共同作成者ロールに、新しい Azure Active Directory グループを割り当てます。

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

"**プリンシパル <guid> がディレクトリ**  にありません" というエラーが発生した場合、まだ新しいグループが Azure Active Directory 全体に伝達されていません。 そのときは、コマンドをもう一度実行してみます。

通常は、デプロイされたリソースを管理するユーザーが確実に割り当てられるようにするために、このプロセスを*ネットワークの共同作業者*と*ストレージ アカウントの共同作業者*に対して繰り返します。 この記事では、これらの手順を省略できます。

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) は、サブスクリプション内のすべてのリソースが会社の基準を順守するために役立ちます。 サブスクリプションには、既にいくつかのポリシー定義が含まれています。 使用可能なポリシー定義を表示するには、[az policy definition list](/cli/azure/policy/definition) コマンドを使います。

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

既存のポリシー定義が表示されます。 ポリシーの種類は、**[BuiltIn] (ビルトイン)** または **[カスタム]** のどちらかです。 この中から、割り当てる条件を記述している定義を探します。 この記事では、次のようなポリシーを割り当てます。

* すべてのリソースの場所を制限する。
* 仮想マシンの SKU を制限する。
* マネージド ディスクを使用しない仮想マシンを監査する。

次の例では、表示名に基づいて 3 つのポリシー定義を取得します。 [az policy assignment create](/cli/azure/policy/assignment) コマンドを使って、それらの定義をリソース グループに割り当てます。 一部のポリシーについては、許可される値を指定するパラメーター値を提供します。

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

前の例では、ポリシーのパラメーターが既にわかっているものとしています。 パラメーターを表示する必要がある場合は、次のコマンドを使います。

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>仮想マシンをデプロイする

ロールとポリシーを割り当てたため、ソリューションをデプロイする準備ができました。 既定のサイズは Standard_DS1_v2 です。これは、許可される SKU の 1 つです。 既定の場所に SSH キーが存在しない場合は、コマンドで作成されます。

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

デプロイが完了したら、そのソリューションにより多くの管理設定を適用できます。

## <a name="lock-resources"></a>リソースのロック

[リソースのロック](../../azure-resource-manager/resource-group-lock-resources.md)は、組織内のユーザーが重要なリソースを誤って削除したり変更したりするのを防ぎます。 ロールベースのアクセス制御とは異なり、リソースのロックはすべてのユーザーとロールに制限を適用します。 ロック レベルは *CanNotDelete* または *ReadOnly* に設定できます。

管理ロックを作成または削除するには、`Microsoft.Authorization/locks/*` アクションにアクセスできる必要があります。 組み込みロールのうち、**所有者**と**ユーザー アクセス管理者**にのみこれらのアクションが許可されています。

仮想マシンとネットワーク セキュリティ グループをロックするには、[az lock create](/cli/azure/lock) コマンドを使います。

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

ロックをテストするには、次のコマンドを実行してみてください。

```azurecli-interactive 
az group delete --name myResourceGroup
```

ロックのために削除操作を完了できないことを示すエラーが表示されます。 リソース グループは、ロックを明確に削除した場合にのみ削除できます。 その手順は、「[リソースのクリーンアップ](#clean-up-resources)」に示されています。

## <a name="tag-resources"></a>リソースへのタグ付け

Azure リソースに[タグ](../../azure-resource-manager/resource-group-using-tags.md)を適用すると、カテゴリ別に論理的に整理できます。 各タグは名前と値で構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

仮想マシンにタグを適用するには、[az resource tag](/cli/azure/resource) コマンドを使います。 リソースの既存のタグは保持されません。

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>タグでリソースを見つける

タグの名前と値でリソースを検索するには、[az resource list](/cli/azure/resource) コマンドを使います。

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

返される値は、タグ値ですべての仮想マシンを停止するような管理タスクで使用できます。

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>タグ値でコストを表示する

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ロックされたネットワーク セキュリティ グループは、そのロックが削除されるまで削除できません。 ロックを解除するには、ロックの ID を取得して、[az lock delete](/cli/azure/lock) コマンドに ID を渡します。

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。 VM への SSH セッションを終了し、次の手順でリソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>次の手順

このチュートリアルでは、カスタム VM イメージを作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * ロールにユーザーを割り当てる
> * 基準を強制するポリシーを適用する
> * 重要なリソースをロックで保護する
> * 課金と管理のためにリソースにタグを付ける

次のチュートリアルに進み、仮想マシンの高可用性について学習してください。

> [!div class="nextstepaction"]
> [仮想マシンの監視](tutorial-monitoring.md)

