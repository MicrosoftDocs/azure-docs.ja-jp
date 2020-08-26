---
title: ワークスペースでロールを管理する
titleSuffix: Azure Machine Learning
description: ロールベースのアクセス制御 (RBAC) を使用して、Azure Machine Learning ワークスペースにアクセスする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: afffdd0267cde8ffc841587748e51dd27e021369
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079588"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペースへのアクセスの管理
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning ワークスペースへのアクセスを管理する方法について説明します。 Azure リソースへのアクセスの管理には、[Azure ロールベースのアクセス制御 (Azure RBAC)](/azure/role-based-access-control/overview) が使用されます。 Azure Active Directory のユーザーには、リソースへのアクセス権を付与する特定のロールが割り当てられます。 Azure には、組み込みロールと、カスタム ロールを作成する機能の両方が用意されています。

## <a name="default-roles"></a>既定のロール

Azure Machine Learning ワークスペースは Azure リソースの 1 つです。 他の Azure リソースと同様、新しい Azure Machine Learning ワークスペースの作成時には次の 3 つの既定のロールが提供されます。 ワークスペースにユーザーを追加し、これらの組み込みロールのいずれかに割り当てることができます。

| Role | アクセス レベル |
| --- | --- |
| **Reader** | ワークスペースでの読み取り専用のアクション。 閲覧者はワークスペースで資産 ([データストア](how-to-access-data.md)の資格情報を含む) を一覧および表示できます。 閲覧者がこれらの資産を作成または更新することはできません。 |
| **Contributor** | ワークスペース内の資産を表示、作成、編集、削除 (該当する場合) します。 たとえば、共同作成者は実験を作成したり、コンピューティング クラスターを作成またはアタッチしたり、実行を送信したり、Web サービスをデプロイしたりできます。 |
| **所有者** | ワークスペース内の資産を表示、作成、編集、削除 (該当する場合) する機能など、ワークスペースへのフル アクセス。 また、ロールの割り当てを変更することができます。 |
| **カスタム ロール** | ワークスペース内の特定のコントロールまたはデータ プレーン操作へのアクセスをカスタマイズできます。 たとえば、実行の送信、コンピューティングの作成、モデルの配置、データセットの登録などです。 |

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

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) をインストール済みの場合は、CLI コマンドを使用してユーザーにロールを割り当てることができます。

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` フィールドは、ワークスペースの親サブスクリプションが存在する Azure Active Directory のインスタンスにおける既存のユーザーのメール アドレスです。 このコマンドレットを使用する方法の例を次に示します。

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> "az ml workspace share" コマンドは、Azure Active Directory B2B のフェデレーション アカウントでは機能しません。 コマンドの代わりに Azure UI ポータルを使用してください。


## <a name="azure-machine-learning-operations"></a>Azure Machine Learning の操作

多くの操作とタスクのための Azure Machine Learning の組み込みアクションがあります。 完全な一覧については、[Azure リソース プロバイダーの操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)に関するページを参照してください。

## <a name="create-custom-role"></a>カスタム ロールの作成

組み込みロールが十分ではない場合は、カスタム ロールを作成できます。 カスタム ロールには、そのワークスペース内のリソースの読み取り、書き込み、削除、コンピューティングのアクセス許可を与えることができます。 ロールは、特定のワークスペース レベル、特定のリソース グループ レベル、または特定のサブスクリプション レベルで使用できるようにすることができます。

> [!NOTE]
> そのリソース内のカスタム ロールを作成するには、そのレベルでのリソースの所有者でなければなりません。

カスタム ロールを作成するには、最初に、ロールのアクセス許可とスコープを指定するロールの定義 JSON ファイルを構築します。 次の例では、特定のワークスペース レベルにスコープ指定された "Data Scientist Custom" という名前のカスタム ロールを定義しています。

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> `AssignableScopes` フィールドを変更し、サブスクリプション レベル、リソース グループ レベル、特定のワークスペース レベルでこのカスタム ロールのスコープを設定することができます。
> 上記のカスタム ロールは一例にすぎません。推奨されるいくつかの [Azure Machine Learning service のカスタム ロール](#customroles)を参照してください。

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

カスタム ロールの詳細については、「[Azure カスタム ロール](/azure/role-based-access-control/custom-roles)」を参照してください。 カスタム ロールで使用できる操作 (アクションとアクション以外) の詳細については、「[リソース プロバイダー操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)」を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>Q. Azure Machine Learning service でいくつかの一般的なシナリオを実行するために必要なアクセス許可はどのようなものですか?

次の表は、Azure Machine Learning アクティビティの概要と、それらを最小のスコープで実行するために必要なアクセス許可をまとめたものです。 たとえば、ワークスペース スコープ (列 4) を使用してアクティビティを実行できる場合は、そのアクセス許可を持つそれより高いすべてのスコープも自動的に機能します。

> [!IMPORTANT]
> この表内の `/` で始まるすべてのパスは、`Microsoft.MachineLearningServices/` に対する**相対パス**です。

| アクティビティ | サブスクリプション レベルのスコープ | リソース グループレベルのスコープ | ワークスペースレベルのスコープ |
| ----- | ----- | ----- | ----- |
| 新しいワークスペースの作成 | 必要なし | 所有者または共同作成者 | 該当なし (所有者になるか、作成後に上位のスコープ ロールを継承します) |
| ワークスペースのエディションを更新する | 必要なし | 必要なし | 所有者、共同作成者、または `/workspaces/write` が可能なカスタム ロール |
| サブスクリプション レベル Amlcompute クォータを要求するか、ワークスペース レベルのクォータを設定する | 所有者、共同作成者、またはサブスクリプション スコープで  が可能なカスタム ロール </br>allowing <ph id="ph1">`/locations/updateQuotas/action`</ph></br> at subscription scope | 権限なし | 権限なし |
| 新しいコンピューティング クラスターの作成 | 必要なし | 必要なし | 所有者、共同作成者、または `/workspaces/computes/write` が可能なカスタム ロール |
| 新しいコンピューティング インスタンスの作成 | 必要なし | 必要なし | 所有者、共同作成者、または `/workspaces/computes/write` が可能なカスタム ロール |
| 任意の種類の実行を送信する | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` が可能なカスタム ロール |
| パイプライン エンドポイントを公開する | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` が可能なカスタム ロール |
| AKS/ACI リソースに登録済みモデルを配置する | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` が可能なカスタム ロール |
| 配置された AKS エンドポイントに対するスコアリング | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (Azure Active Directory 認証を使用していない場合) または `"/workspaces/read"` (トークン認証を使用している場合) が可能なカスタム ロール |
| 対話型ノートブックを使用してストレージにアクセスする | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*"` が可能なカスタム ロール |
| 新しいカスタム ロールを作成する | 所有者、共同作成者、または `Microsoft.Authorization/roleDefinitions/write` が可能なカスタム ロール | 必要なし | 所有者、共同作成者、または `/workspaces/computes/write` が可能なカスタム ロール |

> [!TIP]
> 最初にワークスペースを作成しようとしたときにエラーが発生した場合は、ご自分のロールで `Microsoft.MachineLearningServices/register/action` が許可されていることを確認してください。 このアクションにより、Azure Machine Learning リソース プロバイダーを Azure サブスクリプションに登録できます。

### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>Q. Machine Learning service に対して Azure 組み込みロールを公開していますか?

現在、Machine Learning service 用の [Azure 組み込みロール](/azure/role-based-access-control/built-in-roles)は公開していません。 一度公開された組み込みロールは更新できません。お客様のシナリオとフィードバックに基づいてロールの定義を確定しています。 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>Q. Machine Learning service には、最も一般的なシナリオのためのカスタムロール テンプレートがありますか?

はい、独自のカスタム ロールを定義するためのベースとして使用できる、提案されたカスタム ロール定義を使用した一般的なシナリオをいくつか紹介します。

* __Data Scientist Custom__:データ科学者がワークスペース内で以下を**除く**すべての操作を実行することができます。

    * コンピューティングの作成
    * 実稼働 AKS クラスターへのモデルの配置
    * 運用環境でのパイプライン エンドポイントの配置

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Data Scientist Restricted Custom__:許可されたアクションにワイルドカードを含まない、より制限されたロール定義。 ワークスペース内で以下を**除く**すべての操作が実行できます。

    * コンピューティングの作成
    * 実稼働 AKS クラスターへのモデルの配置
    * 運用環境でのパイプライン エンドポイントの配置

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __MLOps Custom__:サービス プリンシパルにロールを割り当て、それを使用して MLOps パイプラインを自動化することができます。 たとえば、既に公開されているパイプラインに対して実行を送信するには、次のようにします。

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Workspace Admin__:ワークスペースのスコープ内で以下を**除く**すべての操作を実行することができます。

    * 新しいワークスペースの作成。
    * サブスクリプションまたはワークスペース レベルのクォータの割り当て
    * ワークスペースのエディションの更新

    ワークスペース管理者は、新しいロールを作成することもできません。 ワークスペースのスコープ内で既存の組み込みロールまたはカスタム ロールのみを割り当てることができます。

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Labeler Custom__:データのラベル付けのみを対象とするロールを定義することができます。

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. サブスクリプション内のすべてのカスタム ロールを一覧表示するにはどうすればよいですか。

Azure CLI で、次のコマンドを実行します。

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>Q. Machine Learning service でサポートされている操作を見つけるにはどうすればよいですか?

Azure CLI で、次のコマンドを実行します。

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

これらは、[リソース プロバイダー操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)の一覧にもあります。


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>Q. Azure RBAC を使用する場合の一般的な注意事項は何ですか?

Azure ロールベースのアクセス制御 (Azure RBAC) を使用している間は、次の点に注意する必要があります。

- ワークスペースなど、Azure でリソースを作成する場合、ワークスペースの直接の所有者ではありません。 ロールは、そのサブスクリプションで承認されている最も高いスコープのロールから継承されます。 例として、Machine Learning ワークスペースを作成するアクセス許可を持っているネットワーク管理者の場合、そのワークスペースに対して、所有者ロールではなく、ネットワーク管理者ロールが割り当てられます。
- 同じ Azure Active Directory ユーザーに 2 つのロールの割り当てがあり、Actions/NotActions のセクションが競合している場合、あるロールの NotActions に一覧表示されている操作は、他のロールで Actions としても一覧表示されている場合、有効にならない可能性があります。 Azure がロールの割り当てを解析する方法の詳細については、「[ユーザーがリソースへのアクセス権を持っているどうかを Azure RBAC が特定する方法](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)」を参照してください。
- VNet 内にコンピューティング リソースをデプロイするには、次のアクションのアクセス許可を明示的に付与する必要があります。
    - VNet リソース上の "Microsoft.Network/virtualNetworks/join/action"。
    - サブネット リソース上の "Microsoft.Network/virtualNetworks/subnet/join/action"。
    
    ネットワークでの RBAC の詳細については、[ネットワークの組み込みロール](/azure/role-based-access-control/built-in-roles#networking)に関するページを参照してください。

- 新しいロールの割り当てがスタック全体のキャッシュされたアクセス許可に対して有効になるには、最大で 1 時間かかることがあります。

### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>Q. ユーザー割り当てのマネージド ID を Amlcompute クラスターで使用するには、どのようなアクセス許可が必要ですか?

Amlcompute クラスターでユーザー割り当て ID を割り当てるには、コンピューティングを作成するための書き込みアクセス許可と、[マネージド ID オペレーター ロール](/azure/role-based-access-control/built-in-roles#managed-identity-operator)を持っている必要があります。 マネージド ID を使用した RBAC の詳細については、[ユーザー割り当て ID の管理方法](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)に関するページを参照してください。


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>Q. Studio ポータルでロールベースのアクセス制御はサポートされていますか?

Azure Machine Learning Studio では、Azure ロールベースのアクセス制御 (Azure RBAC) がサポートされています。 

> [!IMPORTANT]
> ワークスペース内のデータ科学者に特定のアクセス許可を持つカスタム ロールを割り当てると、対応するアクション (コンピューティング ボタンの追加など) が自動的にユーザーに表示されなくなります。 これらの項目を非表示にすると、使用時にサービスから未承認のアクセス通知を返すコントロールが表示されることによる混乱を防ぐことができます。

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. サブスクリプション内のロールについてのロールの定義を見つけるにはどうすればよいですか。

Azure CLI で、次のコマンドを実行します。 `<role-name>` は、上記のコマンドで返されるのと同じ形式である必要があります。

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. ロールの定義を更新するにはどうすればよいですか。

Azure CLI で、次のコマンドを実行します。

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

新しいロールの定義のスコープ全体に対するアクセス許可が必要となります。 たとえば、この新しいロールが 3 つのサブスクリプションにわたってスコープを持つ場合は、3 つすべてのサブスクリプションに対するアクセス許可が必要です。 

> [!NOTE]
> ロールの更新は、そのスコープ内のすべてのロールの割り当てに適用されるまでに 15 分 ~ 1 時間かかることがあります。
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. ワークスペースのエディションを更新できないようにするロールを定義できますか。 

はい。ワークスペースのエディションの更新を禁止するロールを定義できます。 ワークスペースの更新は、ワークスペース オブジェクトのパッチ呼び出しであるため、JSON 定義の `"NotActions"` 配列に次のアクションを配置して、これを行います。 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. ワークスペースでクォータ操作を実行するには、どのようなアクセス許可が必要ですか。 

ワークスペースでクォータ関連の操作を実行するには、サブスクリプション レベルのアクセス許可が必要です。 これは、マネージド コンピューティング リソースに対するサブスクリプション レベルのクォータまたはワークスペース レベルのクォータの設定は、サブスクリプション スコープで書き込みアクセス許可がある場合にのみ可能であることを意味します。 


## <a name="next-steps"></a>次のステップ

- [エンタープライズ セキュリティの概要](concept-enterprise-security.md)
- [仮想ネットワーク内での実験と推論/スコアの安全な実行](how-to-enable-virtual-network.md)
- [チュートリアル:モデルをトレーニングする](tutorial-train-models-with-aml.md)
- [リソース プロバイダー操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
