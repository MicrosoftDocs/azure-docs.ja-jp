---
title: アクセスの制御にマネージド ID を使用する (プレビュー)
titleSuffix: Azure Machine Learning
description: マネージド ID を使用して、Azure Machine Learning ワークスペースから Azure リソースへのアクセスを制御する方法について説明します。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: a7efd57100ad89fa9824b7a635e11698515e13ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521018"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Azure Machine Learning でマネージド ID を使用する (プレビュー)

[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用すると、"*リソースへのアクセスに必要な最低限のアクセス許可*" でワークスペースを構成できます。 

Azure Machine Learning ワークスペースを信頼できる方法で構成する場合、ワークスペースに関連付けられているさまざまなサービスのアクセス レベルが適切であることを確認することが重要です。 たとえば機械学習ワークフロー時に、ワークスペースでは、Docker イメージ用に Azure Container Registry (ACR) と、トレーニング データ用にストレージ アカウントへのアクセスが必要です。 

さらにマネージド ID を使用すると、アクセス許可をきめ細かく制御できます。たとえば、特定のコンピューティング リソースからの特定の ACR へのアクセスを許可する、または取り消すことができます。

この記事では、マネージド ID を使用して次のことを行う方法について説明します。

 * ACR への管理者ユーザーのアクセスを有効にせずに、Azure Machine Learning ワークスペース用に ACR を構成して使用します。
 * ワークスペースの外部のプライベート ACR にアクセスして、トレーニングまたは推論用のベース イメージをプルします。
 * 関連付けられているリソースにアクセスするために、ユーザー割り当てのマネージド ID を使用してワークスペースを作成します。

> [!IMPORTANT]
> マネージド ID を使用した Azure Machine Learning でのリソースへのアクセス制御は、現在プレビュー段階です。 プレビュー機能では、サポートやサービス レベル アグリーメントは保証されず、"現状有姿" で提供されます。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
 
## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- [Machine Learning service 用 Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)
- [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)。
- ロールを割り当てるには、Azure サブスクリプションのログインに [マネージド ID オペレーター](../role-based-access-control/built-in-roles.md#managed-identity-operator) ロール、または必要なアクション (__所有者__ など) を付与するその他のロールが含まれている必要があります。
- [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の作成と操作に慣れている必要があります。

## <a name="configure-managed-identities"></a>マネージド ID の構成

場合によっては、Azure Container Registry への管理者ユーザーのアクセスを禁止する必要があります。 たとえば、ACR が共有可能な場合に、他のユーザーによる管理者のアクセスを禁止する必要がある場合などです。 または、管理者ユーザーが有効になっている ACR の作成が、サブスクリプション レベルのポリシーで許可されていない場合です。

> [!IMPORTANT]
> Azure Container Instance (ACI) での推論に Azure Machine Learning を使用する場合、ACR への管理者ユーザー アクセスが __必要__ です。 推論のために ACI にモデルをデプロイする予定の場合は、無効にしないでください。

管理者ユーザーのアクセスを有効にせずに ACR を作成した場合、マネージド ID を使用して ACR にアクセスし、Docker イメージをビルドおよびプルします。

ワークスペースの作成時に、管理者ユーザーが無効になっている独自の ACR を使用できます。 または、Azure Machine Learning でワークスペース ACR を作成し、後で管理者ユーザーを無効にすることもできます。

### <a name="bring-your-own-acr"></a>独自の ACR を使用する

ACR 管理者ユーザーがサブスクリプション ポリシーによって禁止されている場合は、まず管理者ユーザーなしで ACR を作成し、それをワークスペースに関連付けます。 また、管理者ユーザーが無効になっている既存の ACR がある場合は、それをワークスペースにアタッチすることもできます。

```--admin-enabled``` 引数を設定せずに [Azure CLI から ACR を作成する](../container-registry/container-registry-get-started-azure-cli.md)か、管理者ユーザーを有効にせずに Azure portal から ACR を作成します。 次に、Azure Machine Learning ワークスペースを作成するときに、ACR の Azure リソース ID を指定します。 次の例は、既存の ACR を使用する新しい Azure ML ワークスペースを作成する方法を示しています。

> [!TIP]
> `--container-registry` パラメーターの値を取得するには、[az acr show](/cli/azure/acr#az_acr_show) コマンドを使用して ACR の情報を表示します。 `id` フィールドに、ACR のリソース ID が表示されます。

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Azure Machine Learning サービスでワークスペース ACR を作成する

独自の ACR を使用しない場合、それを必要とする操作を実行したときに Azure Machine Learning サービスによってそれが作成されます。 たとえば、トレーニングの実行を Machine Learning コンピューティングに送信したり、環境をビルドしたり、Web サービス エンドポイントをデプロイしたときです。 ワークスペースによって作成された ACR では管理者ユーザーが有効になるため、管理者ユーザーを手動で無効にする必要があります。

1. 新しいワークスペースを作成する

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. ACR を必要とするアクションを実行します。 たとえば、[モデルのトレーニングに関するチュートリアル](tutorial-train-models-with-aml.md)があります。

1. クラスターによって作成された ACR 名を取得します。

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    このコマンドにより、次のテキストのような値が返されます。 テキストの最後の部分 (ACR インスタンス名) のみが必要です。

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. ACR を更新して、管理者ユーザーを無効にします。

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>マネージド ID 付きのコンピューティングを作成し、トレーニング用の Docker イメージにアクセスする

ワークスペース ACR にアクセスするには、システム割り当てマネージド ID が有効になっている機械学習コンピューティング クラスターを作成します。 コンピューティングの作成時に Azure portal または Studio から、または以下を使用して Azure CLI から、ID を有効にすることができます。 詳細については、[コンピューティング クラスターでのマネージド ID の使用](how-to-create-attach-compute-cluster.md#managed-identity)に関するページを参照してください。

# <a name="python"></a>[Python](#tab/python)

[AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration) でコンピューティング クラスターを作成する場合は、`identity_type` パラメーターを使用してマネージド ID の種類を設定します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

スタジオでコンピューティング クラスターを作成する際のマネージド ID の構成の詳細については、「[マネージド ID を設定する](how-to-create-attach-compute-studio.md#managed-identity)」を参照してください。

---

マネージド ID には、トレーニング用の Docker イメージのプルを有効にするために、ワークスペース ACR の ACRPull ロールが自動的に付与されます。

> [!NOTE]
> ワークスペース ACR を作成する前に、最初にコンピューティングを作成する場合は、ACRPull ロールを手動で割り当てる必要があります。

## <a name="access-base-images-from-private-acr"></a>プライベート ACR からベース イメージにアクセスする

既定では、Azure Machine Learning では、Microsoft によって管理されるパブリック リポジトリから取得した Docker ベース イメージが使用されます。 次に、そのイメージ上にトレーニング環境または推論環境が構築されます。 詳細については、[ML 環境とは](concept-environments.md)に関するページを参照してください。

ACR 自社内のカスタム ベース イメージを使用するために、マネージド ID を使用してプライベート ACR にアクセスできます。 次の 2 つのユース ケースがあります。

 * トレーニングにベース イメージをそのまま使用します。
 * カスタム イメージをベースとして使用して、Azure Machine Learning マネージド イメージをビルドします。

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>機械学習コンピューティング クラスターに Docker ベース イメージをプルして、そのままトレーニングを行う

上記で説明したように、システム割り当てマネージド ID が有効になっている機械学習コンピューティング クラスターを作成します。 次に、マネージド ID のプリンシパル ID を決定します。

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

必要に応じて、コンピューティング クラスターを更新して、ユーザー割り当てマネージド ID を割り当てることができます。

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

コンピューティング クラスターでベース イメージをプルできるようにするには、マネージド サービス ID にプライベート ACR の ACRPull ロールを付与します。

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

最後に、トレーニングの実行を送信するときに、[環境定義](how-to-use-environments.md#use-existing-environments)でベース イメージの場所を指定します。

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> ベース イメージがコンピューティング リソースに直接プルされるようにするには、`user_managed_dependencies = True` を設定し、Dockerfile を指定しません。 そうしなければ、Azure Machine Learning サービスによって新しい Docker イメージの作成が試みられ、失敗します。これは、コンピューティング クラスターだけが ACR からベース イメージをプルするためのアクセス権を持っているためです。

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>トレーニングまたは推論用に、プライベート ACR からベース イメージに Azure Machine Learning マネージド環境をビルドする

このシナリオでは、Azure Machine Learning サービスによって、プライベート ACR から提供するベース イメージ上にトレーニングまたは推論環境がビルドされます。 イメージのビルド タスクは、ワークスペース ACR 上で ACR タスクを使用して実行されるため、アクセスを許可するためにその他の手順を実行する必要があります。

1. __ユーザー割り当てマネージド ID__ を作成し、ID に __プライベート ACR__ への ACRPull アクセス権を付与します。  
1. ワークスペースの __システム割り当てマネージド ID__ に、前の手順の __ユーザー割り当てマネージド ID__ のマネージド ID オペレーター ロールを付与します。 このロールによってワークスペースでは、マネージド環境をビルドするために、ユーザー割り当てマネージド ID を ACR タスクに割り当てることができるようになります。 

    1. ワークスペースのシステム割り当て マネージド ID のプリンシパル ID を取得します。

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. マネージド ID オペレーター ロールを付与します。

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        UAI リソース ID は、ユーザー割り当て ID の Azure リソース ID で、`/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` の形式です。

1. [Workspace.set_connection メソッド](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-)を使用して、ワークスペース接続の外部 ACR と __ユーザー割り当てマネージド ID__ のクライアント ID を指定します。

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

構成が完了したら、トレーニングまたは推論用の環境のビルド時に、プライベート ACR のベース イメージを使用できるようになります。 次のコード スニペットは、環境定義内でベース イメージ ACR とイメージ名を指定する方法を示しています。

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

必要に応じて [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity) を使用することで、環境定義自体でマネージド ID リソース URL とクライアント ID を指定できます。 レジストリ ID を明示的に使用すると、それ以前に指定したワークスペースの接続がオーバーライドされます。

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>推論に Docker イメージを使用する

前述のように管理者ユーザーなしで ACR を構成した後は、Azure Kubernetes Service (AKS) から管理者キーを使用せずに、推論用の Docker イメージにアクセスできます。 AKS をワークスペースで作成、またはワークスペースにアタッチすると、クラスターのサービス プリンシパルに、ワークスペース ACR への ACRPull アクセスが自動的に割り当てられます。

> [!NOTE]
> 独自の AKS クラスターを使用する場合は、マネージド ID の代わりにサービス プリンシパルがクラスターで有効になっている必要があります。

## <a name="create-workspace-with-user-assigned-managed-identity"></a>ユーザー割り当てのマネージド ID を使用してワークスペースを作成する

ワークスペースを作成する際は、関連付けられているリソースにアクセスするために使用される、ユーザー割り当てのマネージド ID を指定できます。ACR、KeyVault、Storage、および App Insights。

最初に[ユーザー割り当てのマネージド ID を作成](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli])し、マネージド ID の ARM リソース ID を書き留めます。

その後、Azure CLI または Python SDK を使用してワークスペースを作成します。 CLI を使用する場合は、`--primary-user-assigned-identity` パラメーターを使用して ID を指定します。 SDK を使用する場合は、`primary_user_assigned_identity` を使用します。 次に、これらのパラメーターを使用して新しいワークスペースを作成するための Azure CLI と Python の使用例を示します。

__Azure CLI__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

また、[ARM テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)を使用して、ユーザー割り当てのマネージド ID を持つワークスペースを作成することもできます。

> [!IMPORTANT]
> Azure Machine Learning service で作成するのではなく、関連付けられている独自のリソースを使用する場合は、それらのリソースにマネージド ID ロールを付与する必要があります。 割り当てを行うには、[ロール割り当て ARM テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment)を使用します。

(暗号化用のカスタマー マネージド キー) [https://docs.microsoft.com/azure/machine-learning/concept-data-encryption ] があるワークスペースでは、ユーザー割り当てマネージド ID を渡して、ストレージから Key Vault に認証できます。 引数 __user-assigned-identity-for-cmk-encryption__ (CLI) または __user_assigned_identity_for_cmk_encryption__ (SDK) を使用して、マネージド ID を渡します。 このマネージド ID は、ワークスペースのプライマリ ユーザー割り当てのマネージド ID と同じであることも異なることもあります。

既存のワークスペースがある場合は、```az ml workspace update``` CLI コマンドまたは ```Workspace.update``` Python SDK メソッドを使用して、システム割り当てからユーザー割り当てマネージド ID に更新できます。


## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning のエンタープライズ セキュリティ](concept-enterprise-security.md)の詳細を確認します。
