---
title: ACR タスクの Azure マネージド ID
description: Azure Container Registry (ACR) タスクで Azure リソースのマネージド ID を有効にして、そのタスクで他のプライベート コンテナー レジストリを含む他の Azure リソースにアクセスできるようにします。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 8f2749a18a5ac6aed0822553d59beaacc9060228
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915949"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>ACR タスクで Azure マネージド ID を使用する 

[ACR タスク](container-registry-tasks-overview.md)で [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にします。これにより、タスクで他の Azure リソースにアクセスできます。このとき、資格情報を指定または管理する必要はありません。 たとえば、マネージド ID を使用して、コンテナー イメージを別のレジストリからプルまたは別のレジストリにプッシュするためのタスク ステップを有効にします。

この記事では、Azure CLI を使用して、ACR タスクでユーザー割り当てまたはシステム割り当てのマネージド ID を有効にする方法について説明します。 Azure Cloud Shell または Azure CLI のローカル インストールを使用できます。 それをローカルで使う場合は、バージョン 2.0.68 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

説明のために、この記事のコマンドの例では [az acr task create][az-acr-task-create] を使用して、マネージド ID を有効にする基本的なイメージ ビルド タスクを作成します。 マネージド ID を使用して ACR タスクからセキュリティで保護されたリソースにアクセスするサンプル シナリオについては、以下を参照してください。

* [クロスレジストリ認証](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault に格納されているシークレットを使用して外部リソースにアクセスする](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>マネージド ID を使用する理由

Azure リソースのマネージド ID により、選択した Azure サービスに、Azure Active Directory 内の自動マネージド ID が提供されます。 マネージド ID を所有する ACR タスクを構成できます。これにより、そのタスクで、タスク ステップで資格情報を渡すことなく、セキュリティで保護された Azure リソースにアクセスできます。

マネージド ID には、次の 2 種類があります。

* *ユーザー割り当て ID*: この ID は複数のリソースに割り当てることができ、任意の有効期間を設定できます。 ユーザー割り当て ID は、現在プレビューの段階です。

* *システム割り当て ID*: この ID は ACR タスクなど特定のリソースに固有であり、有効期間はそのリソースの有効期間と同じです。

ACR タスクでは、一方または両方の種類の ID を有効にできます。 セキュリティ プリンシパルと同様に、他のリソースへのアクセス許可を ID に付与します。 タスクが実行されると、アクセスが必要なタスク ステップで、ID を使用してリソースへのアクセスが行われます。

## <a name="steps-to-use-a-managed-identity"></a>マネージド ID を使用する手順

次の大まかな手順に従って、ACR タスクでマネージド ID を使用します。

### <a name="1-optional-create-a-user-assigned-identity"></a>1.(省略可能) ユーザー割り当て ID を作成する

ユーザー割り当て ID の使用を予定している場合は、既存の ID を使用するか、Azure CLI またはその他の Azure ツールを使用して ID を作成します。 たとえば、[az identity create][az-identity-create] コマンドを使用します。 

システム割り当て ID のみ使用する場合は、この手順はスキップしてください。 システム割り当て ID は ACR タスクを作成するときに作成します。

### <a name="2-enable-identity-on-an-acr-task"></a>2.ACR タスクで ID を有効にする

ACR タスクを作成するときに、ユーザー割り当て ID、システム割り当て ID、またはこれらの両方を必要に応じて有効にします。 たとえば、Azure CLI で [az acr task create][az-acr-task-create] コマンドを実行するときに `--assign-identity` パラメーターを渡します。

システム割り当て ID を有効にするには、値なしで `--assign-identity` を渡すか、`assign-identity [system]` を渡します。 次のコマンドの例は、`hello-world` イメージをビルドしてからシステム割り当てマネージド ID を有効にする Linux タスクをパブリック GitHub リポジトリから作成します。

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

ユーザー割り当て ID を有効にするには、ID の *リソース ID* の値を指定して `--assign-identity` を渡します。 次のコマンドの例は、`hello-world` イメージをビルドしてからユーザー割り当てマネージド ID を有効にする Linux タスクをパブリック GitHub リポジトリから作成します。

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

[az identity show][az-identity-show] コマンドを実行することで ID のリソース ID を取得できます。 リソース グループ *myResourceGroup* の ID *myUserAssignedIdentity* のリソース ID は次の形式です。 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3.他の Azure リソースにアクセスするためのアクセス許可を ID に付与する

タスクの要件に応じて、他の Azure リソースにアクセスするためのアクセス許可を ID に付与します。 たとえば、次のようになります。

* マネージド ID に、Azure 内のターゲット コンテナー レジストリに対するプル、プッシュとプル、またはその他のアクセス許可を持つロールを割り当てます。 レジストリ ロールの全一覧については、「[Azure Container Registry のロールとアクセス許可](container-registry-roles.md)」を参照してください。 
* Azure Key Vault でシークレットを読み取るためのロールをマネージド ID に割り当てます。

[Azure CLI](../role-based-access-control/role-assignments-cli.md) またはその他の Azure ツールを使用して、リソースへのロールベースのアクセスを管理します。 たとえば、[az role assignment create][az-role-assignment-create] コマンドを実行して、リソースへのロールを ID に割り当てます。 

次の例では、コンテナー レジストリからプルできるアクセス許可をマネージド ID に割り当てています。 このコマンドでは、タスク ID の *プリンシパル ID* とターゲット レジストリの *リソース ID* を指定しています。


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4.(省略可能) タスクに資格情報を追加する

別のカスタム レジストリにイメージをプルまたはプッシュしたり、他のリソースにアクセスしたりするためにタスクで資格情報が必要な場合は、そのタスクに資格情報を追加します。 [az acr task credential add][az-acr-task-credential-add] コマンドを実行して資格情報を追加し、ID が資格情報にアクセスできることを示すために `--use-identity` パラメーターを渡します。 

たとえば、システム割り当て ID の資格情報を追加して、Azure コンテナー レジストリ *targetregistry* で認証を行うには、`use-identity [system]` を渡します。

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

ユーザー割り当て ID の資格情報を追加して、レジストリ *targetregistry* で認証を行うには、ID の *クライアント ID* の値を指定して `use-identity` を渡します。 次に例を示します。

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

[az identity show][az-identity-show] コマンドを実行することで ID のクライアント ID を取得できます。 クライアント ID は、形式 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` の GUID です。

### <a name="5-run-the-task"></a>5.タスクを実行する

マネージド ID でタスクを構成したら、タスクを実行します。 たとえば、この記事で作成したタスクのいずれかをテストするには、[az acr task run][az-acr-task-run] コマンドを使用して手動でそれをトリガーします。 追加の自動タスク トリガーを構成した場合は、自動的にトリガーされたときにタスクは実行されます。

## <a name="next-steps"></a>次のステップ

この記事では、ACR タスクでユーザー割り当てまたはシステム割り当てのマネージド ID を有効にし、使用する方法について説明しました。 マネージド ID を使用して ACR タスクからセキュリティで保護されたリソースにアクセスするシナリオについては、以下を参照してください。

* [クロスレジストリ認証](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault に格納されているシークレットを使用して外部リソースにアクセスする](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
