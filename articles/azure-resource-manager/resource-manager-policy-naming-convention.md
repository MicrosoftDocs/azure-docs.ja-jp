---
title: "名前付け規則用の Azure リソース ポリシー | Microsoft Docs"
description: "リソースに名前を付けるための Azure Resource Manager のポリシーについて説明します。"
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
ms.date: 06/27/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 51b3519bbba8cb4c768bfdd7dadf92fced434f22
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017


---
# <a name="apply-resource-policies-for-names-and-text"></a>名前とテキスト用のリソース ポリシーを適用する
このトピックでは、名前とテキストに関する規則を確立するために適用できるさまざまな[リソース ポリシー](resource-manager-policy.md)を示します。 これらのポリシーによって、リソース名とタグ値の一貫性を確保することができます。 

## <a name="set-naming-convention-with-wildcard"></a>名前付け規則をワイルドカードを使用して設定する
次の例では、**like** の条件でサポートされているワイルドカードが使用されています。 この条件は、名前が指定のパターン (namePrefix\*nameSuffix) と一致する場合、要求を拒否するように指定しています。

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>名前付け規則をパターンを使用して設定する

特定のパターンと一致するリソース名を指定するには、match 条件を使います。 次の例は、`contoso` で始まりその後に 6 つの文字が続く名前と一致します。

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>タグ値の日付パターンを設定する

2 桁の数字 + ダッシュ + 3 つの文字 + ダッシュ + 4 桁の数字から成る日付パターンは、次のように入力します。

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>次のステップ
* (上記の例で示すように) ポリシー規則を定義した後、ポリシー定義を作成してスコープに割り当てる必要があります。 スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 ポータルでポリシーを割り当てる方法については、「[Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md)」(Azure Portal によるリソース ポリシーの割り当てと管理) を参照してください。 REST API、PowerShell、Azure CLI でポリシーを割り当てる方法については、「[Assign and manage policies through script](resource-manager-policy-create-assign.md)」(スクリプトによるポリシーの割り当てと管理) を参照してください。 
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。


