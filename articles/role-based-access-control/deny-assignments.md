---
title: Azure 拒否割り当てについて - Azure RBAC
description: Azure ロールベースのアクセス制御 (Azure RBAC) の Azure 拒否割り当てについて説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: a5f17f009caa9306631debf511f2c890f8f2a450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82733774"
---
# <a name="understand-azure-deny-assignments"></a>Azure 拒否割り当てについて

ロールの割り当てと同様に、"*拒否割り当て*" ではアクセスの拒否を目的として、特定のスコープでユーザー、グループ、またはサービス プリンシパルに一連の拒否アクションがアタッチされます。 拒否割り当てを使用すると、ロールの割り当てでアクセスを許可されている場合であっても、指定した Azure リソース アクションをユーザーが実行できなくなります。

この記事では、拒否割り当てがどのように定義されるかについて説明します。

## <a name="how-deny-assignments-are-created"></a>拒否割り当てが作成されるしくみ

拒否割り当ては、リソースを保護するために Azure によって作成および管理されます。 Azure Blueprints および Azure マネージド アプリでは、システム管理対象リソースを保護するために拒否割り当てを使用します。 Azure Blueprints と Azure マネージド アプリは、拒否割り当てを作成できる唯一の方法です。 独自の拒否割り当てを直接作成することはできません。 Blueprints で拒否割り当てを使用してリソースをロックする方法の詳細については、「[Azure Blueprints でのリソース ロックについて](../governance/blueprints/concepts/resource-locking.md)」を参照してください。

> [!NOTE]
> 独自の拒否割り当てを直接作成することはできません。

## <a name="compare-role-assignments-and-deny-assignments"></a>ロール割り当てと拒否割り当ての比較

拒否割り当てはロール割り当てと同様のパターンに従いますが、いくつかの相違点もあります。

| 機能 | ロール割り当て | 拒否割り当て |
| --- | --- | --- |
| アクセス権の付与 | :heavy_check_mark: |  |
| アクセス拒否 |  | :heavy_check_mark: |
| 直接作成できる | :heavy_check_mark: |  |
| スコープで適用 | :heavy_check_mark: | :heavy_check_mark: |
| プリンシパルを除外 |  | :heavy_check_mark: |
| 子スコープへの継承を防止 |  | :heavy_check_mark: |
| [従来のサブスクリプション管理者](rbac-and-directory-admin-roles.md)の割り当てに適用 |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>拒否割り当てのプロパティ

 拒否割り当てには、以下のプロパティがあります。

> [!div class="mx-tableFixed"]
> | プロパティ | 必須 | Type | 説明 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | はい | String | 拒否割り当ての表示名。 名前は、指定のスコープで一意である必要があります。 |
> | `Description` | いいえ | String | 拒否割り当ての説明。 |
> | `Permissions.Actions` | 少なくとも 1 つの Actions または DataActions | String[] | 拒否割り当てによってアクセスがブロックされる管理操作を指定する文字列の配列。 |
> | `Permissions.NotActions` | いいえ | String[] | 拒否割り当てから除外される管理操作を指定する文字列の配列。 |
> | `Permissions.DataActions` | 少なくとも 1 つの Actions または DataActions | String[] | 拒否割り当てによってアクセスがブロックされるデータ操作を指定する文字列の配列。 |
> | `Permissions.NotDataActions` | いいえ | String[] | 拒否割り当てから除外されるデータ操作を指定する文字列の配列。 |
> | `Scope` | いいえ | String | 拒否割り当てが適用されるスコープを指定する文字列。 |
> | `DoNotApplyToChildScopes` | いいえ | Boolean | 拒否割り当てが子スコープに適用されるかどうかを指定します。 既定値は false です。 |
> | `Principals[i].Id` | はい | String[] | 拒否割り当てが適用される Azure AD プリンシパル オブジェクト ID (ユーザー、グループ、サービス プリンシパル、またはマネージド ID) の配列。 すべてのプリンシパルを表すために空の GUID `00000000-0000-0000-0000-000000000000` に設定されます。 |
> | `Principals[i].Type` | いいえ | String[] | Principals[i].Id によって表されるオブジェクトの種類の配列。すべてのプリンシパルを表すために `SystemDefined` に設定されます。 |
> | `ExcludePrincipals[i].Id` | いいえ | String[] | 拒否割り当てが適用されない Azure AD プリンシパル オブジェクト ID (ユーザー、グループ、サービス プリンシパル、またはマネージド ID) の配列。 |
> | `ExcludePrincipals[i].Type` | いいえ | String[] | ExcludePrincipals[i].Id によって表されるオブジェクトの種類の配列。 |
> | `IsSystemProtected` | いいえ | Boolean | この拒否割り当てが Azure によって作成されたものかどうか、およびこの拒否割り当てを編集または削除できるかどうかを指定します。 現在、すべての拒否割り当てはシステムによって保護されています。 |

## <a name="the-all-principals-principal"></a>All Principals プリンシパル

拒否割り当てをサポートするために、*All Principals* (すべてのプリンシパル) という名前のシステム定義プリンシパルが導入されました。 このプリンシパルは、Azure AD ディレクトリ内のすべてのユーザー、グループ、サービス プリンシパル、マネージド ID を表します。 プリンシパル ID がゼロ GUID `00000000-0000-0000-0000-000000000000` で、プリンシパルの種類が `SystemDefined` の場合、プリンシパルはすべてのプリンシパルを表します。 Azure PowerShell の出力では、All Principals は次のようになります。

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

All Principals は `ExcludePrincipals` と組み合わせて、一部のユーザー以外のすべてのプリンシパルを拒否することができます。 All Principals には次の制約があります。

- `Principals` でのみ使用することができ、`ExcludePrincipals` では使用できません。
- `Principals[i].Type` が `SystemDefined` に設定されていること。

## <a name="next-steps"></a>次のステップ

* [チュートリアル:Azure Blueprints のリソース ロックを使用して新しいリソースを保護する](../governance/blueprints/tutorials/protect-new-resources.md)
* [Azure portal を使用して Azure 拒否割り当てを一覧表示する](deny-assignments-portal.md)
