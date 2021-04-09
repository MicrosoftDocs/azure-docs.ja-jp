---
title: Azure Attestation の要求規則の文法
description: Azure Attestation ポリシーの要求と要求規則に関する詳細情報。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91285420"
---
# <a name="claim-and-claim-rules"></a>要求と要求規則

要求規則の文法を理解するためには、まず構成証明ポリシーの要求について理解する必要があります。

## <a name="claim"></a>要求

要求は、適切な情報を提供するためにまとめられた一連のプロパティです。 Azure Attestation における要求には、次のプロパティが含まれています。

- **type**: 要求の種類を表す文字列値。
- **value**:要求の値を表すブール値、整数、または文字列値。
- **valueType**: value プロパティに格納された情報のデータ型。 サポートされる型は、String、Integer、Boolean です。 定義しなかった場合は、"String" が既定値となります。
- **issuer**: 要求の発行者に関する情報。 発行者は、次のいずれかの種類に該当します。
  - **AttestationService**: ある特定の要求は、Azure Attestation によってポリシーの作成者に提供されます。構成証明ポリシーの作成者は、それを使用して、適切なポリシーを作成することができます。
  - **AttestationPolicy**: 入力された証拠には、処理の過程で、(管理者によって定義された) ポリシー自体が要求を追加できます。 このケースでは、issuer が "AttestationPolicy" に設定されます。
  - **CustomClaim**: 証明者 (クライアント) は、構成証明の証拠に対して別の要求を追加することもできます。 このケースでは、issuer が "CustomClaim" に設定されます。

定義されなかった場合は、"CustomClaim" が既定値となります。 "CustomClaim" が既定値となります。

## <a name="claim-rule"></a>要求規則

入力要求セットは、構成証明の結果を計算するためにポリシー エンジンによって使用されます。 要求規則とは、入力要求を検証して、定義されたアクションを実行するために使用される一連の条件です。

```
Conditions list => Action (Claim)
```

Azure Attestation による要求規則の評価には、次のステップが伴います。

- 条件リストが存在しない場合、指定された要求でアクションを実行します。 
- それ以外の場合は、条件リストにある条件を評価します。
- 条件リストが false として評価された場合は、停止します。 それ以外の場合は、続行します。

アクションを実行する必要があるかどうかは、要求規則に含まれている条件を使用して決定されます。 条件リストは、"&&" 演算子で区切られた一連の条件です。

条件リストは、次のような構造になっています。

```
Condition && Condition && ...
```

条件は、次のような構造になっています。

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

条件リストは、要求のさまざまなプロパティに対する個別の条件から成ります。 条件には、任意で識別子を割り当てることができます。これを使用して、条件を満たした要求を参照することができます。 この参照を他の条件で使用したり、同じ規則のアクションで使用したりすることができます。

次に例を示します。

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

次に示すのは、条件を調べる際に使用できる演算子です。

| Valuetype | サポートされる演算 |
|--|--|
| Integer | == (等しい)、\!= (等しくない)、<= (以下)、< (より小さい)、>= (以上)、> (より大きい) |
| String | == (等しい)、\!= (等しくない) |
| Boolean | == (等しい)、\!= (等しくない) |

条件リストの評価:

- "&&" 演算子が存在するとき、条件リストが true として評価されるのは、リストに含まれるすべての条件が true として評価された場合のみです。
- 条件は、一連の要求に対するフィルター条件を表します。 条件自体は、その条件を満たした要求が少なくとも 1 つ見つかった場合に、true に評価されたと見なされます。
- 要求が、条件によって表されたフィルター条件を満たしていると見なされるためには、その各プロパティが、条件に存在する対応する要求のプロパティ条件を満たしている必要があります。  

以下、ポリシーで使用できる一連のアクションについて説明します。

| アクション動詞 | 説明 | 適用先となるポリシー セクション |
|--|--|--|
| permit() | 入力要求セットを使用して **issuancerules** を計算できます。 パラメーターとして要求は受け取りません。 | **authorizationrules** |
| deny() | 入力要求セットを使用して **issuancerules** を計算することはできません。パラメーターとして要求は受け取りません。 | **authorizationrules** |
| add(claim) | 入力要求セットに要求を追加します。 入力要求セットに追加された要求は、後続の要求規則で使用できるようになります。 |**authorizationrules**、**issuancerules** |
| issue(claim) | 入力および出力要求セットに要求を追加します。 | **issuancerules** |
| issueproperty(claim) | 入力およびプロパティ要求セットに要求を追加します。 | **issuancerules**

## <a name="next-steps"></a>次のステップ

- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [PowerShell を使用して Azure Attestation を設定する](quickstart-powershell.md)

