---
title: Azure CLI を使用して Azure リソースにマネージド ID アクセスを割り当てる方法
description: Azure CLI を使用して、1 つのリソースにマネージド ID を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fb16b1762f1e5330cf058c37a6b7e0f008eb447
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443715"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Azure CLI を使用して、リソースにマネージド ID アクセスを割り当てる

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

マネージド ID で Azure リソースを構成した後、他のセキュリティ プリンシパルと同じように、別のリソースにマネージド ID アクセスを付与できます。 この例では、Azure CLI を使用して、Azure 仮想マシンまたは仮想マシン スケール セットのマネージド ID アクセスを Azure ストレージ アカウントに付与する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#how-does-it-work)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。
    - Azure ポータルから [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
    - 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
    - ローカル CLI コンソールを使用する場合は、[最新バージョンの Azure CLI をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC を使用して他のリソースにマネージド ID アクセスを割り当てる

[Azure 仮想マシン](qs-configure-cli-windows-vm.md)、[Azure 仮想マシン スケール セット](qs-configure-cli-windows-vmss.md)などの Azure リソースでマネージド ID を有効にした後、次の手順を実行します。 

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 VM または仮想マシン スケール セットをデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. この例では、ストレージ アカウントに Azure 仮想マシン アクセスを許可します。 まず、[az resource list](/cli/azure/resource/#az-resource-list) を使用して myVM という仮想マシンのサービス プリンシパルを取得します。

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Azure 仮想マシン スケール セットについてもコマンドはほぼ同じですが、ここでは、"DevTestVMSS" という名前の仮想マシン スケール セットのサービス プリンシパルを取得します。
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. サービス プリンシパル ID を作成したら、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用して、"myStorageAcct" というストレージ アカウントに、仮想マシンまたは仮想マシン スケール セットの "閲覧者" アクセスを付与します。

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>次の手順

- [Azure リソースのマネージド ID の概要](overview.md)
- Azure 仮想マシン上でマネージド ID を有効にするには、「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vm.md)」を参照してください。
- Azure 仮想マシン スケール セット上でマネージド ID を有効にするには、「[Azure CLI を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vmss.md)」を参照してください。
