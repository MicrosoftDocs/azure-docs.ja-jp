---
title: Azure Machine Learning ワークスペースでのロールの管理
titleSuffix: Azure Machine Learning service
description: ロールベースのアクセス制御 (RBAC) を使用して、Azure Machine Learning service ワークスペースにアクセスする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0f28397717d5c89e5a5bcd5e7bdc17b4feb49577
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467975"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペースへのアクセスの管理

この記事では、Azure Machine Learning ワークスペースへのアクセスを管理する方法について説明します。 Azure リソースへのアクセスの管理には、[ロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/overview) が使用されます。 Azure Active Directory のユーザーには、リソースへのアクセス権を付与する特定のロールが割り当てられます。 Azure には、組み込みロールと、カスタム ロールを作成する機能の両方が用意されています。

## <a name="default-roles"></a>既定のロール

Azure Machine Learning ワークスペースは Azure リソースの 1 つです。 他の Azure リソースと同様、新しい Azure Machine Learning ワークスペースの作成時には次の 3 つの既定のロールが提供されます。 ワークスペースにユーザーを追加し、これらの組み込みロールのいずれかに割り当てることができます。

| Role | アクセス レベル |
| --- | --- |
| **Reader** | ワークスペースでの読み取り専用のアクション。 閲覧者はワークスペースで資産を一覧および表示できますが、これらの資産を作成または更新することはできません。 |
| **Contributor** | ワークスペース内の資産を表示、作成、編集、削除 (該当する場合) します。 たとえば、共同作成者は実験を作成したり、コンピューティング クラスターを作成またはアタッチしたり、実行を送信したり、Web サービスをデプロイしたりできます。 |
| **Owner** | ワークスペース内の資産を表示、作成、編集、削除 (該当する場合) する機能など、ワークスペースへのフル アクセス。 また、ロールの割り当てを変更することができます。 |

> [!IMPORTANT]
> ロール アクセス権は、Azure の複数のレベルにスコープ指定できます。 たとえば、ワークスペースへの所有者アクセス権を持つユーザーであっても、そのワークスペースが含まれるリソース グループへの所有者アクセス権を持っていないことがあります。 詳細については、「[RBAC のしくみ](/azure/role-based-access-control/overview#how-rbac-works)」を参照してください。

特定の組み込みロールの詳細については、「[Azure リソースの組み込みロール](/azure/role-based-access-control/built-in-roles)」を参照してください。

## <a name="manage-workspace-access"></a>ワークスペース アクセスの管理

ワークスペースの所有者は、ワークスペースのロールを追加および削除できます。 ユーザーにロールを割り当てることもできます。 アクセスを管理する方法を確認するには、次のリンクを使用します。
- [Azure portal UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure リソース マネージャーのテンプレート](/azure/role-based-access-control/role-assignments-template)

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) をインストール済みの場合は、CLI コマンドを使用してユーザーにロールを割り当てることもできます。

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` フィールドは、ワークスペースの親サブスクリプションが存在する Azure Active Directory のインスタンスにおける既存のユーザーのメール アドレスです。 このコマンドレットを使用する方法の例を次に示します。

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>カスタム ロールの作成

組み込みロールが十分ではない場合は、カスタム ロールを作成できます。 カスタム ロールには、そのワークスペース内のリソースの読み取り、書き込み、削除、コンピューティングのアクセス許可を与えることができます。 ロールは、特定のワークスペース レベル、特定のリソース グループ レベル、または特定のサブスクリプション レベルで使用できるようにすることができます。

> [!NOTE]
> そのリソース内のカスタム ロールを作成するには、そのレベルでのリソースの所有者でなければなりません。

カスタム ロールを作成するには、最初に、ロールのアクセス許可とスコープを指定するロールの定義 JSON ファイルを構築します。 次の例では、特定のワークスペース レベルにスコープ指定された "データ サイエンティスト" という名前のカスタム ロールを定義しています。

`data_scientist_role.json`:
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

`AssignableScopes` フィールドを変更し、サブスクリプション レベル、リソース グループ レベル、特定のワークスペース レベルでこのカスタム ロールのスコープを設定することができます。

このカスタム ロールは、次のアクションを除くすべてのアクションをワークスペース内で実行できます。

- コンピューティング リソースを作成または更新できません。
- コンピューティング リソースを削除できません。
- ロールの割り当てを追加、削除、変更できません。
- ワークスペースを削除できません。

このカスタム ロールをデプロイするには、次の Azure CLI コマンドを使用します。

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

デプロイ後、このロールは、指定したワークスペース内で利用可能になります。 これで、このロールを Azure portal で追加し、割り当てることができるようになりました。 または、`az ml workspace share` CLI コマンドを使用して、このロールをユーザーに割り当てることができます。

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


詳細については、「[Azure リソースのカスタム ロール](/azure/role-based-access-control/custom-roles)」を参照してください。

## <a name="next-steps"></a>次の手順

- [エンタープライズ セキュリティの概要](concept-enterprise-security.md)
- [仮想ネットワーク内での実験と推論/スコアの安全な実行](how-to-enable-virtual-network.md)
- [チュートリアル:モデルをトレーニングする](tutorial-train-models-with-aml.md)
