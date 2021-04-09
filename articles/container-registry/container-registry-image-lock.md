---
title: イメージのロック
description: コンテナー イメージまたはリポジトリの属性を設定して、Azure コンテナー レジストリで削除や上書きができないようにします。
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77659698"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Azure コンテナー レジストリのコンテナー イメージをロックする

Azure コンテナー レジストリでは、イメージ バージョンまたはリポジトリをロックして、削除や更新ができないようにすることができます。 イメージまたはリポジトリをロックするには、Azure CLI コマンド [az acr repository update][az-acr-repository-update] を使用して、その属性を更新します。 

この記事では、Azure CLI を Azure Cloud Shell またはローカルで実行する必要があります (バージョン 2.0.55 以降を推奨)。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

> [!IMPORTANT]
> この記事は、Azure portal の **[設定] > [ロック]** 、または Azure CLI で `az lock` コマンドを使用するレジストリ全体のロックには適用されません。 レジストリ リソースをロックしても、レポジトリ内のデータの作成、更新、または削除は実行できます。 レジストリのロックは、レプリケーションの追加や削除、あるいはレジストリ自体の削除などの管理操作にのみ影響します。 詳細については、「[リソースのロックによる予期せぬ変更の防止](../azure-resource-manager/management/lock-resources.md)」を参照してください。

## <a name="scenarios"></a>シナリオ

既定では、Azure Container Registry のタグ付けされたイメージは *変更可能* であるため、適切なアクセス許可で、同じタグのイメージを定期的に更新し、レジストリにプッシュすることができます。 必要に応じて、コンテナー イメージを[削除する](container-registry-delete.md)こともできます。 この動作は、イメージを開発し、レジストリのサイズを維持する必要がある場合に役立ちます。

しかし、コンテナー イメージを運用環境にデプロイするときに、*不変の* コンテナー イメージが必要になる場合があります。 不変のイメージとは、誤って削除や上書きできないものです。

レジストリでのタグ付けとイメージ作成の方法については、「[コンテナー イメージのタグ付けとバージョン管理に関する推奨事項](container-registry-image-tag-version.md)」を参照してください。

[az acr repository update][az-acr-repository-update] コマンドを使用してリポジトリ属性を設定することで、以下のことが可能になります。

* イメージ バージョン、またはリポジトリ全体をロックする

* イメージ バージョンまたはリポジトリを削除されないようにするが、更新は許可する

* イメージ バージョン、またはリポジトリ全体で読み取り (プル) 操作ができないようにする

例については、次のセクションを参照してください。 

## <a name="lock-an-image-or-repository"></a>イメージまたはリポジトリをロックする 

### <a name="show-the-current-repository-attributes"></a>リポジトリの現在の属性を表示する
リポジトリの現在の属性を表示するには、次の [az acr repository show][az-acr-repository-show] コマンドを実行します。

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>現在のイメージの属性を表示する
タグの現在の属性を表示するには、次の [az acr repository show][az-acr-repository-show] コマンドを実行します。

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>タグでイメージをロックする

*myregistry* の *myrepo/myimage:tag* イメージをロックするには、次の [az acr repository update][az-acr-repository-update] コマンドを実行します。

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>マニフェスト ダイジェストでイメージをロックする

マニフェスト ダイジェスト (`sha256:...` として表される、SHA-256 ハッシュ) で識別された *myrepo/myimage* イメージをロックするには、次のコマンドを実行します (1 つまたは複数のイメージ タグに関連付けられているマニフェスト ダイジェストを見つけるには、[az acr repository show-manifests][az-acr-repository-show-manifests] コマンドを実行します)。

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>リポジトリをロックする

*myrepo/myimage* リポジトリと、それに含まれるすべてのイメージをロックするには、次のコマンドを実行します。

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>イメージやリポジトリが削除されないようにする

### <a name="protect-an-image-from-deletion"></a>イメージが削除されないようにする

*myrepo/myimage:tag* イメージの更新は許可するが、削除は許可しない場合は、次のコマンドを実行します。

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>リポジトリが削除されないようにする

次のコマンドで *myrepo/myimage* リポジトリを設定し、削除できないようにします。 個々のイメージは引き続き、更新したり、削除したりすることができます。

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>イメージまたはリポジトリに対する読み取り操作ができないようにする

*myrepo/myimage:tag* イメージに対する読み取り (プル) 操作ができないようにするには、次のコマンドを実行します。

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

*myrepo/myimage:tag* リポジトリ内のすべてのイメージに対する読み取り操作ができないようにするには、次のコマンドを実行します。

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>イメージまたはリポジトリのロックを解除する

*myrepo/myimage:tag* イメージの既定の動作を復元し、削除と更新ができるようにするには、次のコマンドを実行します。

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

*myrepo/myimage:tag* リポジトリとすべてのイメージの既定の動作を復元し、削除と更新ができるようにするには、次のコマンドを実行します。

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>次のステップ

この記事では、[az acr repository update][az-acr-repository-update] コマンドを使用して、リポジトリ内のイメージ バージョンの削除や更新ができないようにする方法を学習しました。 追加の属性を設定する場合は、[az acr repository update][az-acr-repository-update] コマンドのリファレンスを参照してください。

イメージ バージョンまたはリポジトリに対して設定された属性を確認するには、[az acr repository show][az-acr-repository-show] コマンドを使用します。

削除操作の詳細については、「[Azure Container Registry のコンテナー イメージを削除する][container-registry-delete]」を参照してください。

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

