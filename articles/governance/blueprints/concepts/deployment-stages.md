---
title: ブループリント デプロイのステージ
description: ブループリント割り当ての作成時に Azure Blueprints サービスで行われるセキュリティとアーティファクト関連の手順について説明します。
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: e686dec1c9d79e42dafee17d8a937284aac4fdd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918553"
---
# <a name="stages-of-a-blueprint-deployment"></a>ブループリント デプロイのステージ

ブループリントがデプロイされるとき、Azure Blueprints サービスによって一連のアクションが行われ、ブループリントに定義されているリソースがデプロイされます。 この記事では、各手順の詳細を説明します。

ブループリント デプロイは、ブループリントをサブスクリプションに割り当てるか、[既存の割り当てを更新する](../how-to/update-existing-assignments.md)ことでトリガーされます。 デプロイ中、Azure Blueprints では次の大まかな手順が行われます。

> [!div class="checklist"]
> - Azure Blueprints に所有者権限が与えられる
> - ブループリント割り当てオブジェクトが作成される
> - 省略可能 - Azure Blueprints によって、**システム割り当て** マネージド ID が作成される
> - マネージド ID によってブループリント アーティファクトがデプロイされる
> - Azure Blueprints サービスと **システム割り当て** マネージド ID の権限が取り消される

## <a name="azure-blueprints-granted-owner-rights"></a>Azure Blueprints に所有者権限が与えられる

[システム割り当てマネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) マネージド ID が使用されるとき、Azure Blueprints サービス プリンシパルには、割り当てられた 1 つまたは複数のサブスクリプションに対する所有者権限が付与されます。 付与されたロールにより、Azure Blueprints では **システム割り当て** マネージド ID を作成し、その後、取り消すことができます。 **ユーザー割り当て** マネージド ID を使用している場合、Azure Blueprints サービス プリンシパルはサブスクリプションに対する所有者権限を取得せず、またその必要もありません。

割り当てがポータル経由で行われる場合、権限は自動的に付与されます。 ただし、割り当てが REST API 経由で行われる場合、権限付与は個別の API 呼び出しによって行う必要があります。 Azure Blueprints AppId は `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` ですが、サービス プリンシパルはテナントによって異なります。 [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) と REST エンドポイント [servicePrincipals](/graph/api/resources/serviceprincipal) を使用し、サービス プリンシパルを取得します。 次に、Azure Blueprints に "_所有者_" ロールを [ポータル](../../../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../../../role-based-access-control/role-assignments-cli.md)、[Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md)、[REST API](../../../role-based-access-control/role-assignments-rest.md)、または [Azure Resource Manager テンプレート](../../../role-based-access-control/role-assignments-template.md)経由で付与します。

Azure Blueprints サービスによって、直接リソースがデプロイされることはありません。

## <a name="the-blueprint-assignment-object-is-created"></a>ブループリント割り当てオブジェクトが作成される

ユーザー、グループ、サービス プリンシパルによってブループリントがサブスクリプションに割り当てられます。 割り当てオブジェクトは、ブループリントが割り当てられたサブスクリプション レベルで存在します。 デプロイによるリソースの作成は、エンティティのデプロイというコンテキストでは行われません。

ブループリント割り当てを作成するとき、[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) の種類が選択されます。 初期値は **システム割り当て** のマネージド ID です。 **ユーザー割り当て** のマネージド ID を選択できます。 **ユーザー割り当て** マネージド ID を使用するとき、ブループリント割り当ての作成前にアクセス許可を定義し、付与する必要があります。 [所有者](../../../role-based-access-control/built-in-roles.md#owner)および [ブループリント オペレーター](../../../role-based-access-control/built-in-roles.md#blueprint-operator)組み込みロールはどちらも、**ユーザー割り当て** マネージド ID を使用する、割り当てを作成するために必要な `blueprintAssignment/write` アクセス許可を持っています。

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>省略可能 - Azure Blueprints によって、システム割り当てマネージド ID が作成される

[システム割り当てマネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) が割り当て中に選択されると、Azure Blueprints によって ID が作成され、マネージド ID に[所有者](../../../role-based-access-control/built-in-roles.md#owner)ロールが付与されます。 [既存の割り当てがアップグレードされた](../how-to/update-existing-assignments.md)場合、Azure Blueprints では、以前に作成されたマネージド ID が使用されます。

ブループリント割り当てに関連付けられているマネージド ID は、ブループリントに定義されているリソースのデプロイまたは再デプロイに使用されます。 この設計により、割り当てが誤って互いに干渉することを回避できます。
この設計ではまた、[リソース ロック](./resource-locking.md)機能がサポートされ、デプロイされた各リソースのセキュリティをブループリントから制御できます。

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>マネージド ID によってブループリント アーティファクトがデプロイされる

マネージド ID によってその後、定義されている[シーケンス順序](./sequencing-order.md)で、ブループリント内でアーティファクトの Resource Manager デプロイがトリガーされます。 他のアーティファクトに依存するアーティファクトが正しい順序でデプロイされるように順序を調整できます。

デプロイでアクセスできない原因は、多くの場合、マネージド ID に与えられたアクセス レベルにあります。 Azure Blueprints サービスによって、**システム割り当て** マネージド ID のセキュリティ ライフサイクルが管理されます。 しかしながら、**ユーザー割り当て** マネージド ID の権限とライフサイクルを管理するのはユーザーの役目となります。

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>ブループリント サービスとシステム割り当てのマネージド ID の権限が取り消される

デプロイが完了すると、Azure Blueprints によって、サブスクリプションから **システム割り当て** マネージド ID の権限が取り消されます。 その後、Azure Blueprints サービスによって、その権限がサブスクリプションから取り消されます。 権限を取り消すことで、Azure Blueprints がサブスクリプションの永久所有者になることを防ぎます。

## <a name="next-steps"></a>次のステップ

- [静的および動的パラメーター](./parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](./sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](./resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。
