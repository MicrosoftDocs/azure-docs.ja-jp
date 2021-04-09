---
title: Azure CLI を使用して、リソースにマネージド ID アクセスを割り当てる - Azure AD
description: Azure CLI を使用して、1 つのリソースにマネージド ID を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3b06ce76ae77aa62b20b707a736e8e20e5f6c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99090046"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Azure CLI を使用して、リソースにマネージド ID アクセスを割り当てる

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

マネージド ID で Azure リソースを構成した後、他のセキュリティ プリンシパルと同じように、別のリソースにマネージド ID アクセスを付与できます。 この例では、Azure CLI を使用して、Azure 仮想マシンまたは仮想マシン スケール セットのマネージド ID アクセスを Azure ストレージ アカウントに付与する方法について説明します。

まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID について不明な場合は、「[Azure リソースのマネージド ID とは](overview.md)」を参照してください。 システム割り当てとユーザー割り当ての両方の種類のマネージド ID の詳細については、「[マネージド ID の種類](overview.md#managed-identity-types)」をご覧ください。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC を使用して他のリソースにマネージド ID アクセスを割り当てる

[Azure 仮想マシン](qs-configure-cli-windows-vm.md)、[Azure 仮想マシン スケール セット](qs-configure-cli-windows-vmss.md)などの Azure リソースでマネージド ID を有効にした後、次の手順を実行します。 

1. この例では、ストレージ アカウントに Azure 仮想マシン アクセスを許可します。 まず、[az resource list](/cli/azure/resource/#az-resource-list) を使用して myVM という仮想マシンのサービス プリンシパルを取得します。

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Azure 仮想マシン スケール セットについてもコマンドはほぼ同じですが、ここでは、"DevTestVMSS" という名前の仮想マシン スケール セットのサービス プリンシパルを取得します。
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. サービス プリンシパル ID を作成したら、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用して、"myStorageAcct" というストレージ アカウントに、仮想マシンまたは仮想マシン スケール セットの "閲覧者" アクセスを付与します。

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>次のステップ

- [Azure リソースのマネージド ID の概要](overview.md)
- Azure 仮想マシン上でマネージド ID を有効にするには、「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vm.md)」を参照してください。
- Azure 仮想マシン スケール セット上でマネージド ID を有効にするには、「[Azure CLI を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する](qs-configure-cli-windows-vmss.md)」を参照してください。
