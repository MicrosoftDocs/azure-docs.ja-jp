---
title: "プライベート Docker コンテナー レジストリの作成 - Azure CLI | Microsoft Docs"
description: "Azure CLI 2.0 を使用したプライベート Docker コンテナー レジストリの作成と管理についての概要"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2875f4089231ed12a0312b2c2e077938440365c6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用したプライベート Docker コンテナー レジストリの作成
Linux、Mac、または Windows コンピューターから [Azure CLI 2.0](https://github.com/Azure/azure-cli) のコマンドを使用して、コンテナー レジストリを作成し、その設定を管理します。 コンテナー レジストリの作成と管理は、[Azure Portal](container-registry-get-started-portal.md) を使用するか、Container Registry [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) を使用してプログラムで行うこともできます。


* 背景と概念については、[概要](container-registry-intro.md)に関するページを参照してください。
* Container Registry CLI コマンド (`az acr` コマンド) に関するヘルプについては、任意のコマンドに `-h` のパラメーターを渡します。


## <a name="prerequisites"></a>前提条件
* **Azure CLI 2.0**: CLI 2.0 をインストールし、利用を開始するには、[インストール手順](/cli/azure/install-azure-cli)を参照してください。 `az login` を実行して Azure サブスクリプションにログインします。 詳細については、[CLI 2.0 の概要](/cli/azure/get-started-with-azure-cli)に関する記事を参照してください。
* **リソース グループ**: コンテナー レジストリを作成する前に[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)を作成するか、既存のリソース グループを使用します。 Container Registry サービスが[利用可能](https://azure.microsoft.com/regions/services/)な場所にリソース グループがあることを確認します。 CLI 2.0 を使用してリソース グループを作成するには、[CLI 2.0 のリファレンス](/cli/azure/group)を参照してください。
* **ストレージ アカウント** (省略可能): 同じ場所にコンテナー レジストリをバックアップするには、Azure Standard [ストレージ アカウント](../storage/common/storage-introduction.md)を作成します。 `az acr create` を使用してレジストリを作成するときにストレージ アカウントを指定しないと、ストレージ アカウントが自動的に作成されます。 CLI 2.0 を使用してストレージ アカウントを作成するには、[CLI 2.0 のリファレンス](/cli/azure/storage/account)を参照してください。 現在、Premium Storage はサポートされていません。
* **サービス プリンシパル** (省略可能): CLI を使用してレジストリを作成する場合、既定ではアクセス権が設定されません。 必要に応じて、既存の Azure Active Directory サービス プリンシパルをレジストリに割り当てる (または新しく作成して割り当てる) か、レジストリの管理者ユーザー アカウントを有効にします。 この記事の後半のセクションを参照してください。 レジストリ アクセスの詳細については、「[Authenticate with a container registry (コンテナー レジストリによる認証)](container-registry-authentication.md)」を参照してください。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
コンテナー レジストリを作成するには、`az acr create` コマンドを実行します。

> [!TIP]
> レジストリを作成する場合は、アルファベットと数字のみを含む、グローバルに一意のトップレベル ドメイン名を指定します。 この例のレジストリの名前は `myRegistry1` ですが、独自の一意の名前に置き換えてください。
>
>

次のコマンドでは、最小限のパラメーターを使用して、リソース グループ `myResourceGroup`にコンテナー レジストリ `myRegistry1` を作成します。また、*Basic* SKU を使用します。

```azurecli
az acr create --name myRegistry1 --resource-group myResourceGroup --sku Basic
```

* `--storage-account-name` はオプションです。 指定しない場合、レジストリ名とタイムスタンプから成る名前のストレージ アカウントが指定したリソース グループに作成されます。

レジストリが作成されると、出力は次のようになります。

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T18:36:29.124842+00:00",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry
/registries/myRegistry1",
  "location": "southcentralus",
  "loginServer": "myregistry1.azurecr.io",
  "name": "myRegistry1",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myregistry123456789"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```


次の点に特に注意してください。

* `id` - サブスクリプション内のレジストリの識別子。サービス プリンシパルを割り当てる場合に必要です。
* `loginServer` - [レジストリにログイン](container-registry-authentication.md)するために指定する完全修飾名。 この例では、`myregistry1.exp.azurecr.io` です (すべて小文字)。

## <a name="assign-a-service-principal"></a>サービス プリンシパルの割り当て
CLI 2.0 コマンドを使用して、Azure Active Directory サービス プリンシパルをレジストリに割り当てます。 これらの例のサービス プリンシパルでは所有者ロールが割り当てられますが、必要に応じて[他のロール](../active-directory/role-based-access-control-configure.md)を割り当てることができます。

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>サービス プリンシパルを作成し、レジストリにアクセス権を割り当てる
次のコマンドでは、`--scopes` パラメーターで渡したレジストリ ID に新しいサービス プリンシパルで所有者ロールのアクセス権を割り当てます。 `--password` パラメーターで強力なパスワードを指定します。

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>既存のサービス プリンシパルを割り当てる
既存のサービス プリンシパルでレジストリに所有者ロールのアクセス権を割り当てる場合は、次の例のようなコマンドを実行します。 `--assignee` パラメーターを使用して、サービス プリンシパルのアプリ ID を渡します。

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>管理者の資格情報の管理
管理者アカウントは、コンテナー レジストリごとに自動的に作成され、既定で無効になります。 次の例では、コンテナー レジストリの管理者の資格情報を管理する `az acr` CLI コマンドを示しています。

### <a name="obtain-admin-user-credentials"></a>管理者ユーザーの資格情報を取得する
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>既存のレジストリの管理者ユーザーを有効にする
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>既存のレジストリの管理者ユーザーを無効にする
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>イメージとタグの一覧表示
`az acr` CLI コマンドを使用して、リポジトリ内のイメージとタグを照会します。

> [!NOTE]
> 現在、Container Registry では、`docker search` コマンドによるイメージとタグの照会をサポートしていません。


### <a name="list-repositories"></a>リポジトリの一覧を表示する
次の例では、JSON (JavaScript Object Notation) 形式でレジストリ内のリポジトリの一覧を表示します。

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>タグの一覧を表示する
次の例では、JSON 形式で **samples/nginx** リポジトリのタグの一覧を表示します。

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>次のステップ
* [Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)

