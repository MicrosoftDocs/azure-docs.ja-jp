---
title: "ストレージ アカウントに対する Azure リソース ポリシー | Microsoft Docs"
description: "ストレージ アカウントのデプロイを管理するための Azure Resource Manager のポリシーについて説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5ea75843bf671ad4d879c01cdd20d5bbc5e889c2
ms.openlocfilehash: 08c991e9f217c49828889d0b806888e193b245a8


---
# <a name="apply-resource-policies-to-storage-accounts"></a>ストレージ アカウントにリソース ポリシーを適用する
このトピックでは、Azure ストレージ アカウントに適用できる複数の[リソース ポリシー](resource-manager-policy.md)を示します。 これらのポリシーは、組織にデプロイされているストレージ アカウントの一貫性を保証します。 

## <a name="define-permitted-storage-account-types"></a>許可されているストレージ アカウントの種類の定義

次のポリシーは、デプロイ可能な[ストレージ アカウントの種類](../storage/storage-redundancy.md)を制限します。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

許可された SKU を受け入れるためのパラメーターを持つ似たポリシー規則を、組み込みのポリシー定義として利用できます。 組み込みポリシーのリソース ID は `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1` です。 

## <a name="define-permitted-access-tier"></a>許可されているアクセス層の定義

次のポリシーは、ストレージ アカウントに対して指定可能な[アクセス層](../storage/storage-blob-storage-tiers.md)の種類を指定します。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>暗号化が有効になっていることの確認

次のポリシーは、[ストレージ サービスの暗号化](../storage/storage-service-encryption.md)にすべてのストレージ アカウントを必要とします。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

このポリシー規則は、リソース ID が `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f` の組み込みポリシー定義としても使用できます。

## <a name="next-steps"></a>次のステップ
* (上記の例で示すように) ポリシー規則を定義した後、ポリシー定義を作成してスコープに割り当てる必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポリシーの作成と割り当ての例については、「[リソース ポリシーの割り当てと管理](resource-manager-policy-create-assign.md)」をご覧ください。 
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。




<!--HONumber=Feb17_HO3-->


