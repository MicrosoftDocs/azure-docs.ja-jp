---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 97d80e999ac61a2c2f8f561dc19213419014beb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471011"
---
| リソース | 制限 |
| --- | --- |
| [Azure Active Directory テナントに関連付けられている](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)サブスクリプション | 無制限 |
| サブスクリプションあたりの[共同管理者](../articles/cost-management-billing/manage/add-change-subscription-administrator.md)数 |無制限 |
| サブスクリプションあたりの[リソース グループ数](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure Resource Manager API 要求サイズ |4,194,304 バイト |
| サブスクリプションあたりのタグ数<sup>1</sup> |50 |
| サブスクリプションあたりの一意のタグの計算<sup>1</sup> | 80,000 |
| 場所あたりの[サブスクリプション レベルのデプロイ](../articles/azure-resource-manager/templates/deploy-to-subscription.md)数 | 800<sup>2</sup> |

<sup>1</sup>サブスクリプションにはタグを最大で 50 個直接適用することができます。 ただし、サブスクリプション内のリソース グループとリソースに適用する場合には、サブスクリプションに無制限にタグを含めることができます。 リソースまたはリソース グループあたりのタグの数は、50 に制限されています。 サブスクリプションに存在する[一意のタグ名と値のリスト](/rest/api/resources/tags)が Resource Manager から返されるのは、タグの数が 80,000 以下の場合に限られます。 タグの数が 80,000 を超える場合でも、タグでリソースを検出することはできます。

<sup>2</sup> デプロイ数の上限 800 に達した場合、不要になったデプロイを履歴から削除します。 サブスクリプション レベルのデプロイを削除するには、[Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) または [az deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete) を使用します。
