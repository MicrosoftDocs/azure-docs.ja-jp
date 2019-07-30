---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 5e251402a89f84a0aa67eee676c1794207dd24fb
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869891"
---
| Resource | 既定の制限 | 上限 |
| --- | --- | --- |
| リソースの種類ごとの、[リソース グループ](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)あたりのリソース数 |800 |リソースの種類ごとに異なる |
| デプロイ履歴でのリソース グループあたりのデプロイ数 |800<sup>1</sup> |800 |
| デプロイあたりのリソース数 |800 |800 |
| 一意のスコープあたりの管理ロック数 |20 |20 |
| リソースまたはリソース グループあたりのタグ数 |15 |15 |
| タグ キーの長さ |512 |512 |
| タグ値の長さ |256 |256 |

<sup>1</sup> リソース グループあたり 800 のデプロイという上限に達した場合、不要になった履歴からデプロイを削除します。 デプロイ履歴からエントリを削除しても、デプロイ リソースには影響しません。 Azure CLI の場合は [az group deployment delete](/cli/azure/group/deployment) を、PowerShell では [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) を使用して、履歴からエントリを削除できます。  継続的インテグレーションと継続的デリバリー (CI/CD) シナリオでデプロイ削除を自動化する PowerShell スクリプトについては、「[remove-deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f)」を参照してください。

#### <a name="template-limits"></a>テンプレートの制限

| 値 | 既定の制限 | 上限 |
| --- | --- | --- |
| parameters |256 |256 |
| 変数 |256 |256 |
| リソース (コピー数を含む) |800 |800 |
| 出力 |64 |64 |
| テンプレート式 |24,576 文字 |24,576 文字 |
| エクスポートされたテンプレート内のリソース |200 |200 | 
| テンプレート サイズ |4 MB |4 MB |
| パラメーター ファイル サイズ |64 KB |64 KB |

入れ子になったテンプレートを使用すると、一部のテンプレートの制限を超過することができます。 詳細については、[Azure リソース デプロイ時のリンクされたテンプレートの使用](../articles/azure-resource-manager/resource-group-linked-templates.md)に関するページを参照してください。 パラメーター、変数、出力の数を減らすために、いくつかの値を 1 つのオブジェクトに結合することができます。 詳しくは、[パラメーターとしてのオブジェクト](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)に関する記事をご覧ください。
