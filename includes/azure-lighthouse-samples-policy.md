---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 837e82131b17fa02fca87da067eac6d77903b31d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111664"
---
これらのサンプルでは、Azure Lighthouse にオンボードされたサブスクリプションで Azure Policy を使用する方法を示します。

| **テンプレート** | **説明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | 委任されたサブスクリプションに (modify 効果を使用して) タグを追加または削除するポリシーを割り当てます。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../articles/lighthouse/how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | 委任の割り当てを監査するポリシーを割り当てます。 |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | (deployIfNotExists 効果を使用して) 委任されたサブスクリプションの Azure Key Vault リソースの診断を有効にするポリシーを割り当てます。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../articles/lighthouse/how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | 委任されたサブスクリプションに対する診断を有効にするいくつかのポリシーを割り当て、Windows と Linux のすべての VM を、このポリシーで作成された Log Analytics のワークスペースに接続します。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../articles/lighthouse/how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | 1 つのイニシアチブ (複数の関連するポリシー定義) を委任されたサブスクリプションに適用します。 |

