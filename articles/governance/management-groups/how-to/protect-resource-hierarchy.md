---
title: リソース階層を保護する方法 - Azure のガバナンス
description: 既定の管理グループの設定を含む階層設定を使用して、リソース階層を保護する方法について説明します。
ms.date: 08/10/2020
ms.topic: conceptual
ms.openlocfilehash: 2dd6791e152ba3ef02f6e6f710589cbe7d3442bc
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056620"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>リソース階層を保護する方法

リソース、リソース グループ、サブスクリプション、管理グループ、およびテナントは、全体でリソース階層を成しています。 ルート管理グループの設定 (Azure カスタム ロールや Azure Policy ポリシーの割り当てなど) は、リソース階層内のすべてのリソースに影響を与える可能性があります。 すべてのリソースに悪影響を及ぼす可能性のある変更からリソース階層を保護することが重要です。

管理グループには、テナント管理者がこれらの動作を制御できるようにする階層設定が用意されました。 この記事では、使用可能な各階層設定と、その設定方法について説明します。

## <a name="rbac-permissions-for-hierarchy-settings"></a>階層設定の RBAC アクセス許可

いずれかの階層設定を構成するには、ルート管理グループに対して次の 2 つの RBAC 操作を行う必要があります。

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

これらの操作では、ユーザーに階層設定の読み取りと更新のみを許可します。 管理グループ階層または階層内のリソースへのその他のアクセスは提供しません。 これらの操作はいずれも、Azure 組み込みロール **Hierarchy Settings Administrator** で使用できます。

## <a name="setting---default-management-group"></a>設定 - 既定の管理グループ

既定では、テナント内に追加された新しいサブスクリプションは、ルート管理グループのメンバーとして追加されます。 ポリシー割り当て、ロールベースのアクセス制御 (RBAC)、およびその他のガバナンス構成要素がルート管理グループに割り当てられている場合、これらは新しいサブスクリプションに直ちに影響します。 このため、多くの組織では、これらの構成要素の割り当てに適した場所であるにもかかわらず、ルート管理グループにこれらの構成要素を適用しません。 また、新しいサブスクリプションにはより制限の厳しい制御のセットが必要であっても、それをすべてのサブスクリプションに割り当てるべきではない場合もあります。 この設定では、両方のユース ケースがサポートされています。

新しいサブスクリプションに対して既定の管理グループを定義できるようにすることで、組織全体のガバナンス構成要素をルート管理グループに適用し、それとは別に、新しいサブスクリプションに適したポリシー割り当てまたは Azure ロールの割り当てを持つ管理グループを、定義することができます。

この設定を構成するには、[階層設定](/rest/api/resources/hierarchysettings) REST API エンドポイントが呼び出されます。 これを行うには、次の REST API URI と本文の形式を使用します。 `{rootMgID}` をルート管理グループの ID に置き換え、`{defaultGroupID}` を既定の管理グループにする管理グループの ID に置き換えます。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- 要求本文

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

既定の管理グループをルート管理グループに設定し直すには、同じエンドポイントを使用して **defaultManagementGroup** に `/providers/Microsoft.Management/managementGroups/{rootMgID}` の値を設定します。

## <a name="setting---require-authorization"></a>設定 - 承認を要求する

既定では、すべてのユーザーがテナント内に新しい管理グループを作成できます。 テナントの管理者は、管理グループ階層内の整合性と適合性を維持するために、これらのアクセス許可を特定のユーザーのみに提供したい場合があります。 有効にした場合、ユーザーは新しい子管理グループを作成するために、ルート管理グループに対する `Microsoft.Management/managementGroups/write` 操作が必要になります。

この設定を構成するには、[階層設定](/rest/api/resources/hierarchysettings) REST API エンドポイントが呼び出されます。 これを行うには、次の REST API URI と本文の形式を使用します。 この値は _boolean_ であるため、値として **true** または **false** を指定します。 **true** の値を指定すると、管理グループ階層を保護するこの方法が有効になります。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- 要求本文

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

この設定をオフに戻すには、同じエンドポイントを使用し、**requireAuthorizationForGroupCreation** の値を **false** に設定します。

## <a name="next-steps"></a>次のステップ

管理グループについて詳しくは、以下をご覧ください。

- [管理グループを作成して Azure リソースを整理する](../create.md)
- [管理グループを変更、削除、または管理する方法](../manage.md)
