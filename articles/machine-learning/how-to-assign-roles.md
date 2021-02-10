---
title: ワークスペースでロールを管理する
titleSuffix: Azure Machine Learning
description: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Machine Learning ワークスペースにアクセスする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 01/20/2020
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 8420aecbc160fa6df2640d2ba0ae8a8b77702b67
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624542"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペースへのアクセスの管理

この記事では、Azure Machine Learning ワークスペースへのアクセス (認可) を管理する方法について説明します。 [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) は、Azure リソースへのアクセスを管理するために使用されます (新しいリソースの作成や既存のリソースの使用など)。 Azure Active Directory (Azure AD) のユーザーには、リソースへのアクセス権を付与する特定のロールが割り当てられます。 Azure には、組み込みロールと、カスタム ロールを作成する機能の両方が用意されています。

> [!TIP]
> この記事では Azure Machine Learning に焦点を当てていますが、Azure ML が依存する個々のサービスには独自の RBAC 設定があります。 たとえば、この記事の情報を使用して、Azure Kubernetes Service で Web サービスとしてデプロイされたモデルにスコアリング要求を送信できるユーザーを構成できます。 ただし、Azure Kubernetes Service には、独自の Azure ロールのセットがあります。 Azure Machine Learning で役立つ可能性があるサービス固有の RBAC 情報については、次のリンクを参照してください。
>
> * [Azure Kubernetes クラスター リソースへのアクセスを制御する](../aks/azure-ad-rbac.md)
> * [Kubernetes 認可に Azure RBAC を使用する](../aks/manage-azure-rbac.md)
> * [Azure RBAC を使用して BLOB データにアクセスする](../storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> 一部のロールを適用すると、他のユーザーが Azure Machine Learning スタジオを使用するときに UI 機能が制限されることがあります。 たとえば、ユーザーのロールにコンピューティング インスタンスを作成する機能がない場合、コンピューティング インスタンスを作成するオプションをスタジオで使用できません。 この動作は想定されており、アクセス拒否エラーが返される操作をユーザーが実行できないようにしています。

## <a name="default-roles"></a>既定のロール

Azure Machine Learning ワークスペースは Azure リソースの 1 つです。 他の Azure リソースと同様、新しい Azure Machine Learning ワークスペースの作成時には次の 3 つの既定のロールが提供されます。 ワークスペースにユーザーを追加し、これらの組み込みロールのいずれかに割り当てることができます。

| Role | アクセス レベル |
| --- | --- |
| **Reader** | ワークスペースでの読み取り専用のアクション。 閲覧者はワークスペースで資産 ([データストア](how-to-access-data.md)の資格情報を含む) を一覧および表示できます。 閲覧者がこれらの資産を作成または更新することはできません。 |
| **Contributor** | ワークスペース内の資産を表示、作成、編集、削除 (該当する場合) します。 たとえば、共同作成者は実験を作成したり、コンピューティング クラスターを作成またはアタッチしたり、実行を送信したり、Web サービスをデプロイしたりできます。 |
| **所有者** | ワークスペース内の資産を表示、作成、編集、削除 (該当する場合) する機能など、ワークスペースへのフル アクセス。 また、ロールの割り当てを変更することができます。 |
| **カスタム ロール** | ワークスペース内の特定のコントロールまたはデータ プレーン操作へのアクセスをカスタマイズできます。 たとえば、実行の送信、コンピューティングの作成、モデルの配置、データセットの登録などです。 |

> [!IMPORTANT]
> ロール アクセス権は、Azure の複数のレベルにスコープ指定できます。 たとえば、ワークスペースへの所有者アクセス権を持つユーザーであっても、そのワークスペースが含まれるリソース グループへの所有者アクセス権を持っていないことがあります。 詳細については、「[Azure RBAC のしくみ](../role-based-access-control/overview.md#how-azure-rbac-works)」を参照してください。

現時点では、Azure Machine Learning に固有の追加の組み込みロールはありません。 組み込みロールの詳細については、「[Azure 組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。

## <a name="manage-workspace-access"></a>ワークスペース アクセスの管理

ワークスペースの所有者は、ワークスペースのロールを追加および削除できます。 ユーザーにロールを割り当てることもできます。 アクセスを管理する方法を確認するには、次のリンクを使用します。
- [Azure portal UI](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Azure リソース マネージャーのテンプレート](../role-based-access-control/role-assignments-template.md)

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

カスタム ロールの詳細については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。 

### <a name="azure-machine-learning-operations"></a>Azure Machine Learning の操作

カスタム ロールで使用できる操作 (アクションとアクション以外) の詳細については、「[リソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)」を参照してください。 次の Azure CLI コマンドを使用して、操作を一覧表示することもできます。

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示

Azure CLI で、次のコマンドを実行します。

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

特定のカスタム ロールのロール定義を表示するには、次の Azure CLI コマンドを使用します。 `<role-name>` は、上記のコマンドで返されるのと同じ形式である必要があります。

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>カスタム ロールの更新

Azure CLI で、次のコマンドを実行します。

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

新しいロールの定義のスコープ全体に対するアクセス許可が必要となります。 たとえば、この新しいロールが 3 つのサブスクリプションにわたってスコープを持つ場合は、3 つすべてのサブスクリプションに対するアクセス許可が必要です。 

> [!NOTE]
> ロールの更新は、そのスコープ内のすべてのロールの割り当てに適用されるまでに 15 分 ~ 1 時間かかることがあります。

## <a name="use-azure-resource-manager-templates-for-repeatability"></a>Azure Resource Manager テンプレートを繰り返し使用する

複雑なロール割り当てを何度も作成することになりそうであれば、Azure Resource Manager テンプレートが非常に便利です。 [201-machine-learning-dependencies-role-assignment template](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) では、ソース コードでロール割り当てを再使用できるよう指定する方法を示しています。 

## <a name="common-scenarios"></a>一般的なシナリオ

次の表は、Azure Machine Learning アクティビティの概要と、それらを最小のスコープで実行するために必要なアクセス許可をまとめたものです。 たとえば、ワークスペース スコープ (列 4) を使用してアクティビティを実行できる場合は、そのアクセス許可を持つそれより高いすべてのスコープも自動的に機能します。

> [!IMPORTANT]
> この表内の `/` で始まるすべてのパスは、`Microsoft.MachineLearningServices/` に対する **相対パス** です。

| アクティビティ | サブスクリプション レベルのスコープ | リソース グループレベルのスコープ | ワークスペースレベルのスコープ |
| ----- | ----- | ----- | ----- |
| 新しいワークスペースの作成 | 必要なし | 所有者または共同作成者 | 該当なし (所有者になるか、作成後に上位のスコープ ロールを継承します) |
| サブスクリプション レベル Amlcompute クォータを要求するか、ワークスペース レベルのクォータを設定する | 所有者、共同作成者、またはサブスクリプション スコープで  が可能なカスタム ロール </br>`/locations/updateQuotas/action` が可能な</br> カスタム ロール | 権限なし | 権限なし |
| 新しいコンピューティング クラスターの作成 | 必要なし | 必要なし | 所有者、共同作成者、または `/workspaces/computes/write` が可能なカスタム ロール |
| 新しいコンピューティング インスタンスの作成 | 必要なし | 必要なし | 所有者、共同作成者、または `/workspaces/computes/write` が可能なカスタム ロール |
| 任意の種類の実行を送信する | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` が可能なカスタム ロール |
| パイプラインとエンドポイントの公開 | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` が可能なカスタム ロール |
| AKS/ACI リソースに登録済みモデルを配置する | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` が可能なカスタム ロール |
| 配置された AKS エンドポイントに対するスコアリング | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (Azure Active Directory 認証を使用していない場合) または `"/workspaces/read"` (トークン認証を使用している場合) が可能なカスタム ロール |
| 対話型ノートブックを使用してストレージにアクセスする | 必要なし | 必要なし | 所有者、共同作成者、または `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` が可能なカスタム ロール |
| 新しいカスタム ロールを作成する | 所有者、共同作成者、または `Microsoft.Authorization/roleDefinitions/write` が可能なカスタム ロール | 必要なし | 所有者、共同作成者、または `/workspaces/computes/write` が可能なカスタム ロール |

> [!TIP]
> 最初にワークスペースを作成しようとしたときにエラーが発生した場合は、ご自分のロールで `Microsoft.MachineLearningServices/register/action` が許可されていることを確認してください。 このアクションにより、Azure Machine Learning リソース プロバイダーを Azure サブスクリプションに登録できます。

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Azure ML コンピューティング クラスターのユーザー割り当てマネージド ID

Azure Machine Learning コンピューティング クラスターにユーザー割り当て ID を割り当てるには、コンピューティングと[マネージド ID オペレーター ロール](../role-based-access-control/built-in-roles.md#managed-identity-operator)を作成するための書き込みアクセス許可を持っている必要があります。 マネージド ID を使用した Azure RBAC の詳細については、[ユーザー割り当て ID の管理方法](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)に関するページを参照してください。

### <a name="mlflow-operations"></a>MLflow 操作

Azure Machine Learning ワークスペースで MLflow 操作を実行するには、カスタム ロールに次のスコープを使用します。

| MLflow 操作 | Scope |
| --- | --- |
| ワークスペース追跡ストア内のすべての実験の一覧表示、ID による実験の取得、名前による実験の取得 | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| 名前を指定した実験の作成、実験のタグの設定、削除対象としてマークされた実験の復元| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| 実験の削除 | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| 実行および関連するデータとメタデータの取得、特定の実行について指定したメトリックのすべての値の一覧の取得、実行の成果物の一覧表示 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| 実験内での新しい実行の作成、実行の削除、削除された実行の復元、現在の実行におけるメトリックのログ記録、実行のタグの設定、実行のタグの削除、実行に使用されるパラメーター (キーと値のペア) のログ記録、実行のためのメトリック、パラメーター、タグのバッチのログ記録、実行の状態の更新 | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| 名前による登録済みモデルの取得、レジストリ内のすべての登録済みモデルの一覧の取得、登録済みモデルの検索、各要求ステージの最新バージョンのモデル、登録済みモデルのバージョンの取得、モデルのバージョンの検索、モデルのバージョンの成果物が格納されている URI の取得、実験 ID による実行の検索 | `Microsoft.MachineLearningServices/workspaces/models/read` |
| 新しい登録済みモデルの作成、登録済みモデルの名前または説明の更新、既存の登録済みモデルの名前の変更、モデルの新しいバージョンの作成、モデルのバージョンの説明の更新、いずれかのステージへの登録済みモデルの移行 | `Microsoft.MachineLearningServices/workspaces/models/write` |
| 登録済みモデルとそのすべてのバージョンの削除、登録済みモデルの特定のバージョンの削除 | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>カスタム ロールの例

### <a name="data-scientist"></a>データ サイエンティスト

データ科学者がワークスペース内で以下を **除く** すべての操作を実行することができます。

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

### <a name="data-scientist-restricted"></a>制限されたデータ サイエンティスト

許可されたアクションにワイルドカードを含まない、より制限されたロール定義。 ワークスペース内で以下を **除く** すべての操作が実行できます。

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
     
### <a name="mlflow-data-scientist"></a>MLflow データ サイエンティスト

データ科学者が、以下を **除く**、MLflow AzureML でサポートされているすべての操作を実行できます。

* コンピューティングの作成
* 実稼働 AKS クラスターへのモデルの配置
* 運用環境でのパイプライン エンドポイントの配置

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
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

### <a name="mlops"></a>MLOps

サービス プリンシパルにロールを割り当て、それを使用して MLOps パイプラインを自動化することができます。 たとえば、既に公開されているパイプラインに対して実行を送信するには、次のようにします。

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

### <a name="workspace-admin"></a>ワークスペース管理者

ワークスペースのスコープ内で以下を **除く** すべての操作を実行することができます。

* 新しいワークスペースの作成。
* サブスクリプションまたはワークスペース レベルのクォータの割り当て

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
### <a name="data-labeler"></a>データ ラベラー

データのラベル付けのみを対象とするロールを定義することができます。

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

## <a name="troubleshooting"></a>トラブルシューティング

Azure ロールベースのアクセス制御 (Azure RBAC) を使用している間は、次の点に注意する必要があります。

- Azure でリソース (ワークスペースなど) を作成しても、リソースの直接の所有者にはなりません。 ロールは、そのサブスクリプションで認可されている最も高いスコープのロールから継承されます。 たとえば、Machine Learning ワークスペースを作成するアクセス許可を持っているネットワーク管理者の場合、所有者ロールではなく、そのワークスペースに対するネットワーク管理者ロールが割り当てられます。

- ワークスペースでクォータ操作を実行するには、サブスクリプション レベルのアクセス許可が必要です。 これは、マネージド コンピューティング リソースに対するサブスクリプション レベルのクォータまたはワークスペース レベルのクォータの設定は、サブスクリプション スコープで書き込みアクセス許可がある場合にのみ可能であることを意味します。

- 同じ Azure Active Directory ユーザーに 2 つのロールの割り当てがあり、Actions/NotActions のセクションが競合している場合、あるロールの NotActions に一覧表示されている操作は、他のロールで Actions としても一覧表示されている場合、有効にならない可能性があります。 Azure がロールの割り当てを解析する方法の詳細については、「[ユーザーがリソースへのアクセス権を持っているどうかを Azure RBAC が特定する方法](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)」を参照してください。

- VNet 内にコンピューティング リソースをデプロイするには、次のアクションのアクセス許可を明示的に付与する必要があります。
    - VNet リソースに対する `Microsoft.Network/virtualNetworks/join/action`。
    - サブネット リソースに対する `Microsoft.Network/virtualNetworks/subnet/join/action`。
    
    ネットワークでの Azure RBAC の詳細については、[ネットワークの組み込みロール](../role-based-access-control/built-in-roles.md#networking)に関するページを参照してください。

- 新しいロールの割り当てがスタック全体のキャッシュされたアクセス許可に対して有効になるには、最大で 1 時間かかることがあります。

## <a name="next-steps"></a>次のステップ

- [エンタープライズ セキュリティの概要](concept-enterprise-security.md)
- [仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)
- [チュートリアル:モデルをトレーニングする](tutorial-train-models-with-aml.md)
- [リソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
