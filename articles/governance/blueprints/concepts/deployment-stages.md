---
title: ブループリント デプロイのステージ
description: デプロイ中、Azure ブループリント サービスが通過する手順について説明します。
author: dcthegeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d7000813b51fb9c9aae9a21cbded3ae0028e83f4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60684694"
---
# <a name="stages-of-a-blueprint-deployment"></a>ブループリント デプロイのステージ

ブループリントがデプロイされるとき、Azure Blueprints サービスによって一連のアクションが行われ、ブループリントに定義されているリソースがデプロイされます。 この記事では、各手順の詳細を説明します。

ブループリント デプロイは、ブループリントをサブスクリプションに割り当てるか、[既存の割り当てを更新する](../how-to/update-existing-assignments.md)ことでトリガーされます。 大まかに言えば、デプロイ中、ブループリントは次の手順を行います。

> [!div class="checklist"]
> - Blueprints に所有者権限が与えられる
> - ブループリント割り当てオブジェクトが作成される
> - 省略可能 - Blueprints によって、**システム割り当て**のマネージド ID が作成される
> - マネージド ID によってブループリント アーティファクトがデプロイされる
> - ブループリント サービスと**システム割り当て**のマネージド ID の権限が取り消される

## <a name="blueprints-granted-owner-rights"></a>Blueprints に所有者権限が与えられる

Azure Blueprints サービス プリンシパルに、割り当てられたサブスクリプションの所有者権限が付与されます。 付与されたロールによって、Blueprints では[システム割り当て](../../../active-directory/managed-identities-azure-resources/overview.md)のマネージド ID を作成し、後で取り消すことができます。

割り当てがポータル経由で行われる場合、権限は自動的に付与されます。 ただし、割り当てが REST API 経由で行われる場合、権限付与は個別の API 呼び出しによって行う必要があります。 Azure Blueprint AppId は `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` ですが、サービス プリンシパルはテナントによって異なります。 [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) と REST エンドポイント [servicePrincipals](/graph/api/resources/serviceprincipal) を使用し、サービス プリンシパルを取得します。 次に、Azure Blueprints に "_所有者_" ロールを[ポータル](../../../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../../../role-based-access-control/role-assignments-cli.md)、[Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md)、[REST API](../../../role-based-access-control/role-assignments-rest.md)、または [Resource Manager テンプレート](../../../role-based-access-control/role-assignments-template.md)経由で付与します。

Blueprints サービスによって直接、リソースがデプロイされることはありません。

## <a name="the-blueprint-assignment-object-is-created"></a>ブループリント割り当てオブジェクトが作成される

ユーザー、グループ、サービス プリンシパルによってブループリントがサブスクリプションに割り当てられます。 割り当てオブジェクトは、ブループリントが割り当てられたサブスクリプション レベルで存在します。 デプロイによるリソースの作成は、エンティティのデプロイというコンテキストでは行われません。

ブループリント割り当てを作成するとき、[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) の種類が選択されます。 初期値は**システム割り当て**のマネージド ID です。 **ユーザー割り当て**のマネージド ID を選択できます。 **ユーザー割り当て**マネージド ID を使用するとき、ブループリント割り当ての作成前にアクセス許可を定義し、付与する必要があります。

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>省略可能 - Blueprints によって、システム割り当てのマネージド ID が作成されます

[システム割り当てのマネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) が割り当て中に選択されると、Blueprints によって ID が作成され、マネージド ID に[所有者](../../../role-based-access-control/built-in-roles.md#owner)ロールが付与されます。 [既存の割り当てがアップグレードされた](../how-to/update-existing-assignments.md)場合、Blueprints では、以前に作成されたマネージド ID が使用されます。

ブループリント割り当てに関連付けられているマネージド ID は、ブループリントに定義されているリソースのデプロイまたは再デプロイに使用されます。 この設計により、割り当てが誤って互いに干渉することを回避できます。
この設計ではまた、[リソース ロック](./resource-locking.md)機能がサポートされ、デプロイされた各リソースのセキュリティをブループリントから制御できます。

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>マネージド ID によってブループリント アーティファクトがデプロイされる

マネージド ID によってその後、定義されている[シーケンス順序](./sequencing-order.md)で、ブループリント内でアーティファクトの Resource Manager デプロイがトリガーされます。 他のアーティファクトに依存するアーティファクトが正しい順序でデプロイされるように順序を調整できます。

デプロイでアクセスできない原因は、多くの場合、マネージド ID に与えられたアクセス レベルにあります。 Blueprints サービスによって、**システム割り当て**マネージド ID のセキュリティ ライフサイクルが管理されます。 しかしながら、**ユーザー割り当て**マネージド ID の権限とライフサイクルを管理するのはユーザーの役目となります。

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>ブループリント サービスとシステム割り当てのマネージド ID の権限が取り消される

デプロイが完了すると、Blueprints によって、サブスクリプションから**システム割り当て**マネージド ID の権限が取り消されます。 次に、Blueprints サービスによって、その権限がサブスクリプションから取り消されます。 権限を取り消すことで、Blueprints がサブスクリプションの永久所有者になることを防ぎます。

## <a name="next-steps"></a>次の手順

- [静的および動的パラメーター](parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。