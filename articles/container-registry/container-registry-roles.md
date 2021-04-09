---
title: レジストリのロールとアクセス許可
description: Azure のロールベースのアクセス制御 (Azure RBAC) と ID およびアクセス管理 (IAM) を使用して、Azure コンテナー レジストリ内のリソースへのきめ細かいアクセス許可を提供します。
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 097ccf89caf63d2a504d072cf04c2b534a57a031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207956"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry のロールとアクセス許可

Azure Container Registry サービスは、Azure コンテナー レジストリにさまざまなレベルのアクセス許可を提供する [組み込みの Azure ロール](../role-based-access-control/built-in-roles.md) のセットをサポートします。 [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/index.yml) を使用して、たとえばコンテナー イメージをプッシュまたはプルするために、レジストリを操作する必要があるユーザー、サービス プリンシパル、またはその他の ID に特定のアクセス許可を割り当てます。 レジストリに対するさまざまな操作権限を細かく設定した[カスタム ロール](#custom-roles)を定義することもできます。

| ロール/アクセス許可       | [Resource Manager へのアクセス](#access-resource-manager) | [レジストリの作成/削除](#create-and-delete-registry) | [イメージのプッシュ](#push-image) | [イメージのプル](#pull-image) | [イメージ データを削除する](#delete-image-data) | [ポリシーの変更](#change-policies) |   [イメージの署名](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| 所有者 | X | X | X | X | X | X |  |  
| Contributor | X | X | X |  X | X | X |  |  
| Reader | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="assign-roles"></a>ロールを割り当てる

既存のユーザー、グループ、サービス プリンシパル、またはマネージド ID にロールの割り当てを追加するための手順の概要については、「[ロールの割り当てを追加する手順](../role-based-access-control/role-assignments-steps.md)」を参照してください。 Azure portal、Azure CLI、またはその他の Azure ツールを使用できます。

サービス プリンシパルを作成する場合は、コンテナー レジストリなどの Azure リソースへのアクセスとアクセス許可も構成します。 Azure CLI を使用したスクリプトの例については、「[ サービス プリンシパルによる Azure Container Registry 認証](container-registry-auth-service-principal.md#create-a-service-principal)」を参照してください。

## <a name="differentiate-users-and-services"></a>ユーザーとサービスを区別する

アクセス許可が適用されるときのベスト プラクティスは常に、タスクを実行できる範囲で最も限定的なアクセス許可のセットを人またはサービスに提供することです。 以下のアクセス許可セットでは、人およびヘッドレス サービスで使用できる機能のセットが示されています。

### <a name="cicd-solutions"></a>CI/CD ソリューション

CI/CD ソリューションから `docker build` コマンドを自動化するときは、`docker push` の機能が必要です。 これらのヘッドレス サービス シナリオでは、**AcrPush** ロールを割り当てることをお勧めします。 このロールでは、さらに範囲の広い **共同作成者** ロールとは異なり、アカウントによる他のレジストリ操作の実行または Azure Resource Manager へのアクセスは禁止されます。

### <a name="container-host-nodes"></a>コンテナー ホスト ノード

同様に、コンテナーを実行するノードでは、**AcrPull** ロールは必要ですが、**閲覧者** の機能は必要ないはずです。

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker 拡張機能

Visual Studio Code [Docker 拡張機能](https://code.visualstudio.com/docs/azure/docker)などのツールでは、使用可能な Azure コンテナー レジストリの一覧を取得するために、追加のリソース プロバイダー アクセスが必要です。 この場合は、**閲覧者** または **共同作成者** ロールへのアクセスをユーザーに提供します。 これらのロールでは、`docker pull`、`docker push`、`az acr list`、`az acr build`、およびその他の機能が許可されます。 

## <a name="access-resource-manager"></a>Resource Manager へのアクセス

Azure portal と [Azure CLI](/cli/azure/) によるレジストリ管理には、Azure Resource Manager へのアクセスが必要です。 たとえば、`az acr list` コマンドを使用してレジストリの一覧を取得するには、このアクセス許可セットが必要です。 

## <a name="create-and-delete-registry"></a>レジストリの作成と削除

Azure コンテナー レジストリを作成および削除する権限です。

## <a name="push-image"></a>イメージのプッシュ

レジストリに対して、イメージの `docker push` を行う、または Helm Chart などの別の[サポートされている成果物](container-registry-image-formats.md)をプッシュする権限です。 承認された ID を使用してレジストリで[認証](container-registry-authentication.md)を行う必要があります。 

## <a name="pull-image"></a>イメージのプル

レジストリから検疫されていないイメージの `docker pull` を行う、または Helm Chart などの別の[サポートされている成果物](container-registry-image-formats.md)をプルする権限です。 承認された ID を使用してレジストリで[認証](container-registry-authentication.md)を行う必要があります。

## <a name="delete-image-data"></a>イメージ データを削除する

レジストリから、[コンテナー イメージを削除する](container-registry-delete.md)機能、または Helm チャートなどの他の[サポートされている成果物](container-registry-image-formats.md)を削除する機能。

## <a name="change-policies"></a>ポリシーの変更

レジストリでポリシーを構成する権限です。 ポリシーには、イメージの削除、検疫の有効化、イメージの署名が含まれます。

## <a name="sign-images"></a>イメージの署名

イメージに署名する権限であり、通常は、サービス プリンシパルを使用する自動化されたプロセスに割り当てられます。 通常、このアクセス許可は、レジストリへの信頼できるイメージのプッシュを許可する[イメージのプッシュ](#push-image)と組み合わされます。 詳細については、「[Azure Container Registry におけるコンテンツの信頼](container-registry-content-trust.md)」をご覧ください。

## <a name="custom-roles"></a>カスタム ロール

他の Azure リソースと同様に、Azure Container Registry に対するきめ細かいアクセス許可を持つ[カスタムロール](../role-based-access-control/custom-roles.md)を作成できます。 次に、ユーザー、サービスプリンシパル、またはレジストリと対話する必要がある他の ID にカスタムロールを割り当てます。 

どのアクセス許可をカスタムロールに適用するか決めるには、Microsoft.containerregistry [アクション](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry) の一覧を参照し、[組み込みの ACR ロール](../role-based-access-control/built-in-roles.md) で許可されているアクションを確認するか、次のコマンドを実行します：

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

カスタムロールを定義するには、 [カスタムロールを作成する手順](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) を参照してください。

> [!IMPORTANT]
> カスタムロールでは、Azure Container Registry は、現在、一致するすべてのアクションへのアクセスを許可する `Microsoft.ContainerRegistry/*` や `Microsoft.ContainerRegistry/registries/*` などのワイルドカードを、サポートしていません。 ロールに、必要なアクションを個別に指定します。

### <a name="example-custom-role-to-import-images"></a>例:イメージをインポートするためのカスタム ロール

たとえば、次の JSON では、レジストリへの[イメージ インポート](container-registry-import-images.md)を許可するカスタム ロールの最小アクションが定義されます。

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

JSON 記述を使用してカスタム ロールを作成または更新するには、[Azure CLI](../role-based-access-control/custom-roles-cli.md)、[Azure Resource Manager テンプレート](../role-based-access-control/custom-roles-template.md)、[Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)、その他の Azure ツールを使用します。 組み込み Azure ロールのロール割り当て管理と同じ方法でカスタム ロールのロール割り当てを追加したり、削除したりします。

## <a name="next-steps"></a>次のステップ

* [Azure portal](../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../role-based-access-control/role-assignments-cli.md)、またはその他の Azure ツールを使用した Azure ID への Azure ロールの割り当てについてさらに理解します。

* Azure Container Registry の[認証オプション](container-registry-authentication.md)について理解します。

* コンテナーレジストリで [リポジトリスコープのアクセス許可](container-registry-repository-scoped-permissions.md) (プレビュー) を有効にする方法について学びます。
