---
title: Azure リソースの拒否割り当ての概要 | Microsoft Docs
description: Azure リソースに対するロールベースのアクセス制御 (RBAC) の拒否割り当てについて説明します。
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
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 497571a65510f806d7d7994c9dc37f9a00b65a5f
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006727"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Azure リソースの拒否割り当ての概要

ロールの割り当てと同様に、"*拒否割り当て*" ではアクセスの拒否を目的として、特定のスコープでユーザー、グループ、またはサービス プリンシパルに一連の拒否アクションがアタッチされます。 拒否割り当てを使用すると、ロールの割り当てでアクセスを許可されている場合であっても、指定した Azure リソース アクションをユーザーが実行できなくなります。 Azure の一部のリソース プロバイダーに拒否割り当てが追加されました。

いくつかの点で、拒否割り当てはロールの割り当てとは異なります。 拒否割り当てはプリンシパルを除外できるほか、子スコープへの継承を避けることができます。 拒否割り当ては、[従来のサブスクリプション管理者](rbac-and-directory-admin-roles.md)の割り当てにも適用されます。

この記事では、拒否割り当てがどのように定義されるかについて説明します。

> [!NOTE]
> 現時点では、Azure Blueprints を使用することが、独自の拒否割り当てを追加する唯一の方法となります。 詳細については、「[Azure Blueprints のリソース ロックを使用して新しいリソースを保護する](../governance/blueprints/tutorials/protect-new-resources.md)」を参照してください。

## <a name="deny-assignment-properties"></a>拒否割り当てのプロパティ

 拒否割り当てには、以下のプロパティがあります。

> [!div class="mx-tableFixed"]
> | プロパティ | 必須 | Type | 説明 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | はい | String | 拒否割り当ての表示名。 名前は、指定のスコープで一意である必要があります。 |
> | `Description` | いいえ  | String | 拒否割り当ての説明。 |
> | `Permissions.Actions` | 少なくとも 1 つの Actions または DataActions | String[] | 拒否割り当てによってアクセスがブロックされる管理操作を指定する文字列の配列。 |
> | `Permissions.NotActions` | いいえ  | String[] | 拒否割り当てから除外される管理操作を指定する文字列の配列。 |
> | `Permissions.DataActions` | 少なくとも 1 つの Actions または DataActions | String[] | 拒否割り当てによってアクセスがブロックされるデータ操作を指定する文字列の配列。 |
> | `Permissions.NotDataActions` | いいえ  | String[] | 拒否割り当てから除外されるデータ操作を指定する文字列の配列。 |
> | `Scope` | いいえ  | String | 拒否割り当てが適用されるスコープを指定する文字列。 |
> | `DoNotApplyToChildScopes` | いいえ  | Boolean | 拒否割り当てが子スコープに適用されるかどうかを指定します。 既定値は false です。 |
> | `Principals[i].Id` | はい | String[] | 拒否割り当てが適用される Azure AD プリンシパル オブジェクト ID (ユーザー、グループ、サービス プリンシパル、またはマネージド ID) の配列。 すべてのプリンシパルを表すために空の GUID `00000000-0000-0000-0000-000000000000` に設定されます。 |
> | `Principals[i].Type` | いいえ  | String[] | Principals[i].Id によって表されるオブジェクトの種類の配列。すべてのプリンシパルを表すために `SystemDefined` に設定されます。 |
> | `ExcludePrincipals[i].Id` | いいえ  | String[] | 拒否割り当てが適用されない Azure AD プリンシパル オブジェクト ID (ユーザー、グループ、サービス プリンシパル、またはマネージド ID) の配列。 |
> | `ExcludePrincipals[i].Type` | いいえ  | String[] | ExcludePrincipals[i].Id によって表されるオブジェクトの種類の配列。 |
> | `IsSystemProtected` | いいえ  | Boolean | この拒否割り当てが Azure によって作成されたものかどうか、およびこの拒否割り当てを編集または削除できるかどうかを指定します。 現在、すべての拒否割り当てはシステムによって保護されています。 |

## <a name="system-defined-principal"></a>システム定義のプリンシパル

拒否割り当てをサポートするために、**システム定義のプリンシパル**が導入されました。 このプリンシパルは、Azure AD ディレクトリ内のすべてのユーザー、グループ、サービス プリンシパル、マネージド ID を表します。 プリンシパル ID がゼロ GUID `00000000-0000-0000-0000-000000000000` で、プリンシパルの種類が `SystemDefined` の場合、プリンシパルはすべてのプリンシパルを表します。 `SystemDefined` は `ExcludePrincipals` と組み合わせて、一部のユーザー以外のすべてのプリンシパルを拒否することができます。 `SystemDefined` には次の制約があります。

- `Principals` でのみ使用することができ、`ExcludePrincipals` では使用できません。
- `Principals[i].Type` `SystemDefined` に設定する必要があります。

## <a name="next-steps"></a>次の手順

* [Azure portal を使用して Azure リソースの拒否割り当てを表示する](deny-assignments-portal.md)
* [Azure リソースのロール定義の概要](role-definitions.md)
