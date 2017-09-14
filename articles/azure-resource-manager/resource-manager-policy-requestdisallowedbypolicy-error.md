---
title: "Azure リソース ポリシーに関する RequestDisallowedByPolicy エラー | Microsoft Docs"
description: "RequestDisallowedByPolicy エラーの原因について説明します。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 182a27e444c2f5db66d518a1a0c608d3e319d553
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure リソース ポリシーに関する RequestDisallowedByPolicy エラー

この記事では、RequestDisallowedByPolicy エラーの原因について説明し、このエラーの解決策も示します。

## <a name="symptom"></a>症状

デプロイ中にアクションを実行しようとすると、**RequestDisallowedByPolicy** エラーを受け取り、このアクションを実行できない場合があります。 このエラーのサンプルを次に示します。

```
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>トラブルシューティング

次のいずれかの方法を使用して、デプロイをブロックしたポリシーに関する詳細を取得します。

### <a name="method-1"></a>方法 1

PowerShell では、そのポリシー識別子を **Id** パラメーターとして指定して、デプロイをブロックしたポリシーの詳細を取得します。

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>方法 2 

Azure CLI 2.0 では、ポリシー定義の名前を指定します。 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>解決策

IT 部門が、セキュリティとコンプライアンスに対応するために、Public IP Addresses、ネットワーク セキュリティ グループ、ユーザー定義ルート、ルート テーブルの作成を禁止するリソース ポリシーを適用している可能性があります。 「症状」セクションで説明されているエラー メッセージのサンプルではポリシーの名前は **regionPolicyDefinition** ですが、名前は違う可能性があります。
この問題を解決するには、IT 部門と連携して、リソース ポリシーを確認し、それらのポリシーに従いながら要求されたアクションを実行する方法を判別します。


詳細については、次の記事を参照してください。

- [リソース ポリシーの概要](resource-manager-policy.md)
- [一般的なデプロイのエラー - RequestDisallowedByPolicy](resource-manager-common-deployment-errors.md#requestdisallowedbypolicy)

 



