---
title: 個人データ
description: Azure Resource Manager の操作に関連する個人データを管理する方法について説明します。
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 1e531f7cd9992536bcc191637111761c5bbdefa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693703"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Azure Resource Manager に関連する個人データを管理する

機密情報の漏洩を回避するために、展開、リソース グループ、またはタグに含まれている個人情報を削除します。 Azure Resource Manager では、展開、リソース グループ、またはタグに含まれている個人データを管理する操作が提供されています。

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>展開履歴内の個人データを削除する

展開の場合、Azure Resource Manager は、パラメーター値とステータス メッセージを展開履歴内に保持しています。 これらの値は、履歴から展開を削除するまで保持されます。 これらの値の中に個人データが含まれているかどうかを確認するには、展開を一覧表示します。 個人データが見つかった場合は、履歴から展開を削除します。

履歴内の **展開** を一覧表示するには、以下のコマンドを使用します。

* [List By Resource Group](/rest/api/resources/deployments/listbyresourcegroup)
* [Get AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list)

履歴から **展開** を削除するには、以下のコマンドを使用します。

* [削除](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az deployment group delete](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>リソース グループ名内の個人データを削除する

リソース グループ名は、リソース グループを削除するまで保持されます。 リソース グループ名の中に個人データが含まれているかどうかを確認するには、リソース グループを一覧表示します。 個人データを見つけた場合は、[リソースを新しいリソース グループに移動](move-resource-group-and-subscription.md)し、名前の中に個人データが含まれているリソース グループを削除します。

**リソース グループ** を一覧表示するには、以下のコマンドを使用します。

* [一覧](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

**リソース グループ** を削除するには、以下のコマンドを使用します。

* [削除](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>タグ内の個人データを削除する

タグの名前と値は、タグを削除するか、変更するまで保持されます。 タグの中に個人データが含まれているかどうかを確認するには、タグを一覧表示します。 個人データが見つかった場合は、タグを削除します。

**タグ** を一覧表示するには、以下のコマンドを使用します。

* [一覧](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az-tag-list)

**タグ** を削除するには、以下のコマンドを使用します。

* [削除](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>次のステップ
* Azure Resource Manager の概要については、「[Azure Resource Manager の概要](overview.md)」を参照してください。