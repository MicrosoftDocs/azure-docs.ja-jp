---
title: RequestDisallowedByPolicy エラー
description: Azure Resource Manager でのリソースのデプロイ時の RequestDisallowedByPolicy エラーの原因について説明します。
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75474273"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure リソース ポリシーに関する RequestDisallowedByPolicy エラー

この記事では、RequestDisallowedByPolicy エラーの原因について説明し、このエラーの解決策も示します。

## <a name="symptom"></a>症状

デプロイ中に、**RequestDisallowedByPolicy** エラーを受け取り、リソースを作成できないことがあります。 次の例は、そのエラーを示します。

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>トラブルシューティング

次のいずれかの方法を使用して、デプロイをブロックしたポリシーに関する詳細を取得します。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell では、そのポリシー識別子を `Id` パラメーターとして指定して、デプロイをブロックしたポリシーの詳細を取得します。

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI では、ポリシー定義の名前を指定します。

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>解決策

セキュリティやコンプライアンスについて、サブスクリプション管理者はリソースのデプロイ方法を制限するポリシーを割り当てることができます。 たとえば、サブスクリプションには、パブリック IP アドレス、ネットワーク セキュリティ グループ、ユーザー定義ルート、またはルート テーブルの作成を禁止するポリシーがある場合があります。 **[現象]** セクションのエラー メッセージは、ポリシーの名前を示します。
この問題を解決するには、リソース ポリシーを確認し、それらのポリシーに準拠しているリソースのデプロイ方法を決定します。

詳細については、次の記事を参照してください。

- [Azure Policy とは](../../governance/policy/overview.md)
- [コンプライアンスを強制するポリシーの作成と管理](../../governance/policy/tutorials/create-and-manage.md)
