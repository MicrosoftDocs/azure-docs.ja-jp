---
title: Azure Container Instances に gitRepo ボリュームをマウントする
description: gitRepo ボリュームをマウントし、Git リポジトリの複製をコンテナー インスタンスに作成する方法について説明します。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: e8afa9e14941920cdcfb984e6660bdc666240716
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325437"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Azure Container Instances に gitRepo ボリュームをマウントする

*gitRepo ボリューム*をマウントし、Git リポジトリの複製をコンテナー インスタンスに作成する方法について説明します。

> [!NOTE]
> *gitRepo* ボリュームのマウントは現在、Linux コンテナーに限定されています。 Microsoft ではすべての機能を Windows コンテナーにも取り入れるように取り組んでいますが、現在のプラットフォームの違いは、[概要](container-instances-overview.md#linux-and-windows-containers)に関するページで確認できます。

## <a name="gitrepo-volume"></a>gitRepo ボリューム

*gitRepo* ボリュームはディレクトリをマウントし、コンテナーの起動時、指定の Git リポジトリの複製をそのディレクトリに作成します。 コンテナー インスタンスで *gitRepo* ボリュームを使用することで、そのためのコードを自分のアプリケーションに追加する必要がなくなります。

*gitRepo* ボリュームをマウントするとき、次の 3 つのプロパティを設定し、ボリュームを構成できます。

| プロパティ | 必須 | 説明 |
| -------- | -------- | ----------- |
| `repository` | はい | 完全 URL。複製を作成する Git リポジトリの `http://` または `https://` も含まれます。|
| `directory` | いいえ | リポジトリの複製を作成するディレクトリ。 パスには "`..`" を含めることができません。  "`.`" を指定すると、リポジトリの複製がボリュームのディレクトリに作成されます。 指定しない場合、ボリューム ディレクトリ内の指定の下位ディレクトリに Git リポジトリの複製が作成されます。 |
| `revision` | いいえ | 複製を作成するリビジョンのコミット ハッシュ。 指定しない場合、`HEAD` リビジョンが複製されます。 |

## <a name="mount-gitrepo-volume-azure-cli"></a>gitRepo ボリュームのマウント:Azure CLI

[Azure CLI](/cli/azure) を使用してコンテナー インスタンスをデプロイするときに gitRepo ボリュームをマウントするには、[az container create][az-container-create] コマンドに `--gitrepo-url` および `--gitrepo-mount-path` パラメーターを指定します。 必要に応じて、複製先となるボリューム内のディレクトリ (`--gitrepo-dir`) と複製されるリビジョンのコミット ハッシュ (`--gitrepo-revision`) を指定することもできます。

この例のコマンドでは、Microsoft [aci-helloworld][aci-helloworld] サンプル アプリケーションがコンテナー インスタンス内の `/mnt/aci-helloworld` に複製されます。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

gitRepo ボリュームがマウントされたことを確認するには、[az container exec][az-container-exec] を使ってコンテナー内のシェルを起動し、ディレクトリを一覧表示します。

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>gitRepo ボリュームのマウント:リソース マネージャー

[Azure Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)を使ってコンテナー インスタンスをデプロイするときに gitRepo ボリュームをマウントするには、最初にテンプレートのコンテナー グループの `properties` セクションにある `volumes` 配列を設定します。 次に、*gitRepo* ボリュームをマウントするコンテナー グループ内の各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。

たとえば、次の Resource Manager テンプレートでは、1 つのコンテナーから構成されるコンテナー グループが作成されます。 このコンテナーによって、*gitRepo* ボリューム ブロックにより指定される 2 つの GitHub リポジトリが複製されます。 2 つ目のボリュームには、複製先のディレクトリを指定する追加プロパティと複製する特定のリビジョンのコミット ハッシュが含まれています。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

先のテンプレートに定義されていた 2 つの複製リポジトリのディレクトリ構造は結果的に次のようになります。

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Azure Resource Manager テンプレートによるコンテナー インスタンスのデプロイ例を見るには、[Azure Container Instances での複数コンテナーのデプロイ](container-instances-multi-container-group.md)に関するページを参照してください。

## <a name="private-git-repo-authentication"></a>プライベート Git リポジトリの認証

プライベート Git リポジトリ用の gitRepo ボリュームをマウントするには、リポジトリの URL に資格情報を指定します。 通常、資格情報は、範囲が指定されたアクセスをリポジトリに付与するユーザー名と個人用アクセス トークン (PAT) の形式です。

たとえば、プライベート GitHub リポジトリの Azure CLI `--gitrepo-url` パラメーターは、次のようになります ("gituser" は GitHub のユーザー名であり、"abcdef1234fdsa4321abcdef" はユーザーの個人用アクセス トークンです)。

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Azure Repos Git リポジトリの場合、有効な PAT と組み合わせて任意のユーザー名を指定します (次の例のように "azurereposuser" を使用できます)。

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

GitHub と Azure Repos の個人用アクセス トークンの詳細については、以下を参照してください。

GitHub:[コマンド ラインに使用する個人用アクセス トークンを作成する][pat-github]

Azure Repos:[アクセスの認証に使用する個人用アクセス トークンを作成する][pat-repos]

## <a name="next-steps"></a>次の手順

Azure Container Instances にその他の種類のボリュームをマウントする方法について学習してください。

* [Azure Container Instances に Azure ファイル共有をマウントする](container-instances-volume-azure-files.md)
* [Azure Container Instances に emptyDir ボリュームをマウントする](container-instances-volume-emptydir.md)
* [Azure Container Instances にシークレット ボリュームをマウントする](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
