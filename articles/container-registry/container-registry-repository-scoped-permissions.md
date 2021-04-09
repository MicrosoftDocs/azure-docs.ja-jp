---
title: Azure Container Registry でのリポジトリに対するアクセス許可
description: イメージのプルやプッシュまたは他のアクションを実行するための、Premium レジストリ内の特定のリポジトリをスコープとするアクセス許可を持つトークンを作成します。
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: ceec69d746f77ea7a23bc70d029c8b3736e7f292
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988259"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>リポジトリ スコープのアクセス許可を持つトークンを作成する

この記事では、コンテナー レジストリで特定のリポジトリへのアクセスを管理するためのトークンとスコープ マップを作成する方法について説明します。 レジストリの所有者は、トークンを作成することにより、イメージのプルやプッシュまたは他のアクションを実行するための、リポジトリをスコープとする期間限定のアクセス権を、ユーザーまたはサービスに提供することができます。 トークンを使うと、レジストリ全体がアクセス許可のスコープである他のレジストリ[認証オプション](container-registry-authentication.md)より、きめ細かいアクセス許可を提供できます。 

トークンの作成には次のようなシナリオがあります。

* リポジトリからイメージをプルできるように IoT デバイスに個別のトークンを許可する
* 特定のリポジトリへのアクセス許可を外部組織に付与する 
* リポジトリ アクセスを組織内の異なるユーザー グループに制限する。 たとえば、特定のリポジトリをターゲットとしたイメージを構築する開発者に書き込みおよび読み取りアクセス権を付与し、それらのリポジトリからデプロイするチームに読み取りアクセス権を付与します。

この機能は、**Premium** コンテナー レジストリ サービス レベルで使用できます。 レジストリ サービスのレベルと制限については、[Azure Container Registry のサービス レベル](container-registry-skus.md)に関する記事を参照してください。

> [!IMPORTANT]
> この機能は現在プレビュー段階であり、一定の[制限事項が適用されます](#preview-limitations)。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="preview-limitations"></a>プレビューの制限事項

* 現在、リポジトリ スコープのアクセス許可を、サービス プリンシパルやマネージド ID などの Azure Active Directory ID に割り当てることはできません。
* [匿名プル アクセス](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)に対して有効になっているレジストリにスコープ マップを作成することはできません。

## <a name="concepts"></a>概念

リポジトリ スコープのアクセス許可を構成するには、"*スコープ マップ*" が関連付けられた "*トークン*" を作成します。 

* **トークン** と生成されたパスワードを使用して、ユーザーはレジストリでの認証を行うことができます。 トークンのパスワードに有効期限を設定したり、トークンをいつでも無効にしたりできます。  

  トークンを使用して認証を行った後、ユーザーまたはサービスは、1 つ以上のリポジトリをスコープとする 1 つ以上の "*アクション*" を実行できます。

  |アクション  |説明  | 例 |
  |---------|---------|--------|
  |`content/delete`    | リポジトリからデータを削除します  | リポジトリまたはマニフェストを削除する |
  |`content/read`     |  リポジトリからデータを読み取ります |  成果物をプルする |
  |`content/write`     |  リポジトリにデータを書き込みます     | `content/read` と共に使用して、アーティファクトをプッシュする |
  |`metadata/read`    | リポジトリからメタデータを読み取ります   | タグまたはマニフェストの一覧を表示する |
  |`metadata/write`     |  メタデータをリポジトリに書き込みます  | 読み取り、書き込み、削除の操作を有効または無効にする |

* **スコープ マップ** を使って、トークンに適用し、他のトークンに再適用できるリポジトリ アクセス許可をグループ化します。 すべてのトークンは、1 つのスコープ マップに関連付けられています。 

   スコープ マップでは次のことを行います。

    * リポジトリのセットに対して、同一のアクセス許可を持つ複数のトークンを構成します
    * スコープ マップでリポジトリ アクションを追加または削除するときにトークンのアクセス許可を更新するか、または別のスコープ マップを適用します 

  Azure Container Registry には、トークンの作成時に適用できる複数のシステム定義のスコープ マップも用意されています。 システム定義のスコープ マップのアクセス許可は、レジストリ内のすべてのリポジトリに適用されます。

次の図では、トークンとスコープ マップの関係を示します。 

![レジストリ トークンとスコープ マップ](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>前提条件

* **Azure CLI** - この記事の Azure CLI コマンドのコマンド例には、Azure CLI バージョン 2.17.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。
* **Docker** - イメージをプルまたはプッシュするためにレジストリで認証を行うには、ローカル環境に Docker をインストールする必要もあります。 Docker では、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、および [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システム向けのインストールの指示を提供しています。
* **コンテナー レジストリ** - Premium コンテナー レジストリがない場合は、Azure サブスクリプションで作成するか、または既存のレジストリをアップグレードします。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用します。 

## <a name="create-token---cli"></a>トークンを作成する - CLI

### <a name="create-token-and-specify-repositories"></a>トークンを作成してリポジトリを指定する

[az acr token create][az-acr-token-create] コマンドを使用してトークンを作成します。 トークンを作成するときは、1 つ以上のリポジトリと、各リポジトリに関連付けるアクションを指定できます。 リポジトリは、レジストリに既に存在している必要はありません。 既存のスコープ マップを指定することによってトークンを作成するには、[次のセクション](#create-token-and-specify-scope-map)をご覧ください。

次の例では、`samples/hello-world` リポジトリに対する `content/write` と `content/read` のアクセス許可を持つトークンを、レジストリ *myregistry* に作成します。 既定では、このコマンドによって既定のトークンの状態は `enabled` に設定されますが、いつでも状態を `disabled` に更新できます。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

出力には、トークンの詳細が表示されます。 既定では、有効期限がない 2 つのパスワードが生成されますが、必要に応じて有効期限を設定することもできます。 パスワードは、後で認証に使用するので、安全な場所に保存することをお勧めします。 そのパスワードを再度取得することはできませんが、新しいパスワードを生成することはできます。

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

> [!NOTE]
> トークンのパスワードと有効期限を再生成するには、この記事の後半の「[トークンのパスワードを再生成する](#regenerate-token-passwords)」を参照してください。

出力には、コマンドによって作成されたスコープ マップに関する詳細が含まれます。 スコープ マップ (ここでは `MyToken-scope-map` という名前) を使用することで、同じリポジトリ アクションを他のトークンにも適用できます。 または、後でスコープ マップを更新して、関連付けられているトークンのアクセス許可を変更します。

### <a name="create-token-and-specify-scope-map"></a>トークンを作成してスコープ マップを指定する

トークンを作成するもう 1 つの方法は、既存のスコープ マップを指定することです。 スコープ マップがまだない場合は、最初に、リポジトリとそれに関連付けるアクションを指定することによって作成します。 次に、トークンを作成するときにスコープ マップを指定します。 

スコープ マップを作成するには、[az acr scope-map create][az-acr-scope-map-create] コマンドを使用します。 次のコマンドでは、前に使用した `samples/hello-world` リポジトリに対するものと同じアクセス許可を持つスコープ マップが作成されます。 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

[az acr token create][az-acr-token-create] を実行してトークンを作成し、*MyScopeMap* スコープ マップを指定します。 前の例と同様に、このコマンドではトークンの既定の状態が `enabled` に設定されます。

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

出力には、トークンの詳細が表示されます。 既定では、2 つのパスワードが生成されます。 パスワードは、後で認証に使用するので、安全な場所に保存することをお勧めします。 そのパスワードを再度取得することはできませんが、新しいパスワードを生成することはできます。

> [!NOTE]
> トークンのパスワードと有効期限を再生成するには、この記事の後半の「[トークンのパスワードを再生成する](#regenerate-token-passwords)」を参照してください。

## <a name="create-token---portal"></a>トークンを作成する - ポータル

Azure portal を使用して、トークンとスコープ マップを作成できます。 `az acr token create` CLI コマンドの場合と同様に、既存のスコープ マップを適用することも、トークンを作成するときに、1 つ以上のリポジトリとそれに関連付けるアクションを指定して、スコープ マップを作成することもできます。 リポジトリは、レジストリに既に存在している必要はありません。 

次の例では、トークンを作成し、`samples/hello-world` リポジトリに対して `content/write` と `content/read` のアクセス許可を持つスコープ マップを作成します。

1. ポータルで、自分のコンテナー レジストリに移動します。
1. **[リポジトリのアクセス許可]** で、 **[トークン (プレビュー)] > [+ 追加]** を選択します。

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="ポータルでトークンを作成する":::
1. トークンの名前を入力します。
1. **[Scope map]\(スコープ マップ\)** で、 **[新規作成]** を選択します。
1. スコープ マップを構成します。
    1. スコープ マップの名前と説明を入力します。 
    1. **[リポジトリ]** に「`samples/hello-world`」と入力し、 **[アクセス許可]** で [`content/read`] と [`content/write`] を選択します。 次に、 **[+追加]** を選択します。  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="ポータルでスコープ マップを作成する":::

    1. リポジトリとアクセス許可を追加した後、 **[追加]** を選択してスコープ マップを追加します。
1. トークンの **[状態]** では既定値の **[有効]** をそのまま使用し、 **[作成]** を選択します。

トークンが検証されて作成された後、トークンの詳細が **[トークン]** 画面に表示されます。

### <a name="add-token-password"></a>トークンのパスワードを追加する

ポータルで作成されたトークンを使用するには、パスワードを生成する必要があります。 1 つまたは 2 つのパスワードを生成し、それぞれに有効期限を設定することができます。 

1. ポータルで、自分のコンテナー レジストリに移動します。
1. **[リポジトリのアクセス許可]** で **[トークン (プレビュー)]** を選択し、トークンを選択します。
1. トークンの詳細で、**password1** または **password2** を選択し、[生成] アイコンを選択します。
1. パスワード画面で、必要に応じてパスワードの有効期限を設定し、 **[生成]** を選択します。 有効期限を設定することをお勧めします。
1. パスワードが生成されたら、それをコピーして安全な場所に保存します。 画面を閉じてしまうと生成されたパスワードを取得できなくなりますが、新しいパスワードを生成することはできます。

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="ポータルでトークンのパスワードを作成する":::

## <a name="authenticate-with-token"></a>トークンで認証を行う

ユーザーまたはサービスがトークンを使用してターゲット レジストリで認証を行うときは、ユーザー名としてトークン名を指定し、生成したパスワードの 1 つを提供します。 

認証方法は、構成されているアクションまたはトークンに関連付けられているアクションによって異なります。

|アクション  |認証方法  |
  |---------|---------|
  |`content/delete`    | Azure CLI の `az acr repository delete`<br/><br/>例: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>Azure CLI の `az acr login`<br/><br/>例: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>Azure CLI の `az acr login`     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>Azure CLI の `az acr repository show-manifests`   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>Azure CLI の `az acr repository update` |

## <a name="examples-use-token"></a>例 :トークンを使用する

次の例では、この記事で前に作成したトークンを使って、リポジトリに対する一般的なアクションを実行します。イメージのプッシュとプル、イメージの削除、リポジトリ タグの一覧表示などを行います。 トークンは、最初に `samples/hello-world` リポジトリでのプッシュ アクセス許可 (`content/write` および `content/read` アクション) で設定されています。

### <a name="pull-and-tag-test-images"></a>テスト イメージをプルしてタグを付ける

次の例では、パブリックの `hello-world` イメージと `nginx` イメージを Microsoft Container Registry からプルし、レジストリとリポジトリのタグを付けます。

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>トークンを使用して認証を行う

`docker login` または `az acr login` を実行して、イメージをプッシュまたはプルするためにレジストリで認証します。 ユーザー名としてトークン名を入力してから、そのパスワードのいずれかを指定します。 トークンは、`Enabled` 状態になっている必要があります。

次の例については、Bash シェル用にフォーマットされており、環境変数を使用して値が指定されています。

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

出力には、認証に成功したことが表示されます。

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>イメージをレジストリにプッシュ

正常にログインした後、タグが付けられたイメージのレジストリへのプッシュを試みます。 トークンには `samples/hello-world` リポジトリにイメージをプッシュするアクセス許可があるため、次のプッシュは成功します。

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

トークンには `samples/nginx` リポジトリに対するアクセス許可がないため、次のプッシュの試行は失敗し、`requested access to the resource is denied` のようなエラーが発生します。

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>トークンのアクセス許可を更新する

トークンのアクセス許可を更新するには、関連付けられているスコープ マップでアクセス許可を更新します。 更新したスコープ マップは、関連付けられているすべてのトークンに直ちに適用されます。 

たとえば、`samples/ngnx` リポジトリに対する `content/write` アクションと `content/read` アクションで `MyToken-scope-map` を更新し、`samples/hello-world` リポジトリに対する `content/write` アクションを削除します。  

Azure CLI を使って行うには、[az acr scope-map update][az-acr-scope-map-update] を実行して、スコープ マップを更新します。

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

Azure Portal で次の操作を行います。

1. お使いのコンテナー レジストリに移動します。
1. **[リポジトリのアクセス許可]** で **[スコープ マップ (プレビュー)]** を選択し、更新するスコープ マップを選択します。
1. **[リポジトリ]** に「`samples/nginx`」と入力し、 **[アクセス許可]** で [`content/read`] と [`content/write`] を選択します。 次に、 **[+追加]** を選択します。
1. **[リポジトリ]** で `samples/hello-world` を選択し、 **[アクセス許可]** で [`content/write`] を削除します。 次に、 **[保存]** を選択します。

スコープ マップを更新した後は、次のプッシュが成功するようになります。

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

スコープ マップには `samples/hello-world` リポジトリに対する `content/read` アクセス許可のみがあるため、`samples/hello-world` リポジトリへのプッシュの試行は失敗します。
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

このスコープ マップでは両方のリポジトリに対して `content/read` アクセス許可が提供されるため、両方のリポジトリからのイメージのプルは成功します。

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>イメージを削除する

`nginx` リポジトリに対する `content/delete` アクションを追加することにより、スコープ マップを更新します。 このアクションにより、リポジトリ内のイメージまたはリポジトリ全体を削除できます。

簡潔にするため、[az acr scope-map update][az-acr-scope-map-update] コマンドによるスコープ マップの更新のみを示しします。

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

ポータルを使用したスコープ マップの更新については、[前のセクション](#update-token-permissions)を参照してください。

`samples/nginx` リポジトリを削除するには、次の [az acr repository delete][az-acr-repository-delete] コマンドを使用します。 イメージまたはリポジトリを削除するには、トークンの名前とパスワードをコマンドに渡します。 次の例では、前の記事で作成した環境変数を使用します。

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>リポジトリ タグを表示する 

`hello-world` リポジトリに対する `metadata/read` アクションを追加することにより、スコープ マップを更新します。 このアクションにより、リポジトリ内のマニフェストとタグ データを読み取ることができます。

簡潔にするため、[az acr scope-map update][az-acr-scope-map-update] コマンドによるスコープ マップの更新のみを示しします。

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

ポータルを使用したスコープ マップの更新については、[前のセクション](#update-token-permissions)を参照してください。

`samples/hello-world` リポジトリ内のメタデータを読み取るには、[az acr repository show-manifests][az-acr-repository-show-manifests] コマンドまたは [az acr repository show-tags][az-acr-repository-show-tags] コマンドを実行します。 

メタデータを読み込むには、どちらのコマンドにもトークンの名前とパスワードを渡します。 次の例では、前の記事で作成した環境変数を使用します。

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

サンプル出力:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>トークンとスコープ マップを管理する

### <a name="list-scope-maps"></a>スコープ マップの一覧を表示する

レジストリに構成されているすべてのスコープ マップの一覧を表示するには、[az acr scope-map list][az-acr-scope-map-list] コマンドまたはポータルの **[スコープ マップ (プレビュー)]** 画面を使用します。 次に例を示します。

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

出力は、3 つのシステム定義のスコープ マップと、自分で生成したその他のスコープマップで構成されます。 トークンは、これらのスコープ マップのいずれかを使用して構成できます。

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>トークンの詳細を表示する

状態やパスワードの有効期限など、トークンの詳細を表示するには、[az acr token show][az-acr-token-show] コマンドを実行するか、ポータルの **[トークン (プレビュー)]** 画面でトークンを選択します。 次に例を示します。

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

レジストリに構成されているすべてのトークンの一覧を表示するには、[az acr token list][az-acr-token-list] コマンドまたはポータルの **[トークン (プレビュー)]** 画面を使用します。 次に例を示します。

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>トークンのパスワードを再生成する

トークンのパスワードを生成しなかった場合、または新しいパスワードを生成する場合は、[az acr token credential generate][az-acr-token-credential-generate] コマンドを実行します。 

次の例では、*MyToken* トークンに対する password1 の新しい値が、30 日の有効期間で生成されます。 パスワードは、環境変数 `TOKEN_PWD` に格納されます。 この例は Bash シェル用にフォーマットされています。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Azure portal を使用してトークンのパスワードを生成する方法については、前の「[トークンを作成する - ポータル](#create-token---portal)」の手順をご覧ください。

### <a name="update-token-with-new-scope-map"></a>新しいスコープ マップでトークンを更新する

別のスコープ マップを使用してトークンを更新する場合は、[az acr token update][az-acr-token-update] を実行し、新しいスコープ マップを指定します。 次に例を示します。

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

ポータルの **[トークン (プレビュー)]** 画面でトークンを選択し、 **[スコープ マップ]** で別のスコープ マップを選択します。

> [!TIP]
> 新しいスコープ マップでトークンを更新した後は、新しいトークン パスワードを生成することをお勧めします。 [az acr token credential generate][az-acr-token-credential-generate] コマンドを使用するか、Azure portal でトークン パスワードを再生成します。

## <a name="disable-or-delete-token"></a>トークンを無効にするか削除する

ユーザーまたはサービスに対するトークン資格情報の使用を、一時的に無効にすることが必要になる場合があります。 

Azure CLI を使用し、[az acr token update][az-acr-token-update] コマンドを実行して、`status` を `disabled` に設定します。

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

ポータルでは、 **[トークン (プレビュー)]** 画面でトークンを選択し、 **[状態]** で **[無効]** を選択します。

トークンを削除し、永続的に誰もその資格情報を使用してアクセスできないようにするには、[az acr token delete][az-acr-token-delete] コマンドを実行します。 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

ポータルでは、 **[トークン (プレビュー)]** 画面でトークンを選択し、 **[破棄]** を選択します。

## <a name="next-steps"></a>次のステップ

* スコープ マップとトークンを管理するには、[az acr scope-map][az-acr-scope-map] および [az acr token][az-acr-token] コマンド グループの追加コマンドを使用します。
* Azure Active Directory ID、サービス プリンシパル、管理者アカウントの使用など、Azure コンテナー レジストリで認証を行うための他のオプションについては、[認証の概要](container-registry-authentication.md)に関するページを参照してください。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
