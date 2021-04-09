---
title: OCI 成果物のプッシュおよびプル
description: Azure のプライベート コンテナー レジストリを使用して Open Container Initiative (OCI) 成果物をプッシュおよびプルする
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 8a73f295999888dab20531ffdd0fb042790a5357
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988229"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Azure コンテナー レジストリを使用して OCI 成果物をプッシュおよびプルする

Azure コンテナー レジストリを使用して、[Open Container Initiative (OCI) 成果物](container-registry-image-formats.md#oci-artifacts)のほか、Docker および Docker と互換性のあるコンテナー イメージを格納および管理することができます。

この機能を示すため、この記事では [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) ツールを使用して、サンプルの成果物 (テキスト ファイル) を Azure コンテナー レジストリにプッシュする方法について説明します。 次に、レジストリから成果物をプルします。 各成果物に適したさまざまなコマンドライン ツールを使用して、Azure コンテナー レジストリ内の多様な OCI 成果物を管理できます。

## <a name="prerequisites"></a>前提条件

* **Azure コンテナー レジストリ** - コンテナー レジストリは、Azure サブスクリプションに作成します。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用します。
* **ORAS ツール** - [GitHub リポジトリ](https://github.com/deislabs/oras/releases)から、ご使用のオペレーティング システムの現在の ORAS リリースをダウンロードしてインストールします。 このツールは、圧縮された tarball (`.tar.gz` ファイル) としてリリースされています。 ご使用のオペレーティング システムの標準的な手順を使用して、ファイルを抽出してインストールします。
* **Azure Active Directory サービス プリンシパル (オプション)** - ORAS を使用して直接認証するには、[サービス プリンシパル](container-registry-auth-service-principal.md)を作成してレジストリにアクセスします。 サービス プリンシパルに、成果物をプッシュおよびプルするアクセス許可が付与されるように、確実に AcrPush などのロールを割り当てます。
* **Azure CLI (オプション)** - 個々の ID を使用するには、Azure CLI のローカル インストールが必要です。 バージョン 2.0.71 以降をお勧めします。 バージョンを確認するには、`az --version ` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。
* **Docker (オプション)** - 個々の ID を使用するには、レジストリによる認証のために、Docker をローカルにインストールする必要もあります。 Docker では、[macOS][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。


## <a name="sign-in-to-a-registry"></a>レジストリにサインインする

このセクションでは、使用する ID に応じて、レジストリにサインインするための 2 つの推奨されるワークフローについて説明します。 ご使用の環境に適した方法を選択してください。

### <a name="sign-in-with-oras"></a>ORAS を使用してサインインする

プッシュ権限がある[サービス プリンシパル](container-registry-auth-service-principal.md)を使用して、`oras login` コマンドを実行し、サービス プリンシパルのアプリケーション ID とパスワードを使用してレジストリにサインインします。 レジストリの完全修飾名 (すべて小文字) を指定してください。この場合は "*myregistry.azurecr.io*" です。 サービス プリンシパルのアプリケーション ID が環境変数 `$SP_APP_ID` に、パスワードが変数 `$SP_PASSWD` に渡されます。

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Stdin からパスワードを読み取るには、`--password-stdin` を使用します。

### <a name="sign-in-with-azure-cli"></a>Azure CLI を使用してサインインする

コンテナー レジストリに対して成果物のプッシュとプルを行うために、ご自分の ID で Azure CLI に[サインイン](/cli/azure/authenticate-azure-cli)します。

次に、Acure CLI コマンド [az acr login](/cli/azure/acr#az-acr-login) を使用して、レジストリにアクセスします。 たとえば、"*myregistry*" という名前のレジストリに対して認証を行うには、次のように入力します。

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` では、Docker クライアントを使用して、`docker.config` ファイル内に Azure Active Directory トークンが設定されます。 個々の認証フローを完了するには、Docker クライアントをインストールして実行する必要があります。

## <a name="push-an-artifact"></a>成果物をプッシュする

サンプル テキストを使用して、ローカル作業用の作業ディレクトリにテキスト ファイルを作成します。 たとえば、Bash シェルで次のように入力します。

```bash
echo "Here is an artifact" > artifact.txt
```

`oras push` コマンドを使用して、このテキスト ファイルをレジストリにプッシュします。 次の例では、サンプルのテキスト ファイルを `samples/artifact` リポジトリにプッシュします。 レジストリは、レジストリの完全修飾名 "*myregistry.azurecr.io*" (すべて小文字) で識別されます。 成果物にはタグ `1.0` が付けられます。 成果物の型は既定では未定義で、ファイル名 `artifact.txt` に続く "*メディアの種類*" の文字列によって識別されます。 追加の型については、「[OCI Artifacts](https://github.com/opencontainers/artifacts)」 (OCI 成果物) を参照してください。 

**Linux または macOS**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

プッシュが成功した場合の出力は次のようになります。

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

レジストリ内の成果物を管理するには、Azure CLI を使用する場合は、イメージを管理するための標準の `az acr` コマンドを実行します。 たとえば、[az acr repository show][az-acr-repository-show] コマンドを使用して、成果物の属性を取得します。

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

次のような出力になります。

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>成果物をプルする

`oras pull` コマンドを実行して、レジストリから成果物をプルします。

まず、ローカルの作業ディレクトリからテキスト ファイルを削除します。

```bash
rm artifact.txt
```

`oras pull` を実行して成果物をプルし、成果物をプッシュするために使用したメディアの種類を指定します。

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

プルが成功したことを確認します。

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>成果物を削除する (省略可能)

Azure コンテナー レジストリから成果物を削除するには、[az acr repository delete][az-acr-repository-delete] コマンドを使用します。 次の例では、そこに格納した成果物を削除します。

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>例:OCI 成果物から Docker イメージを構築する

コンテナー イメージを構築するためのソース コードとバイナリを、OCI 成果物として Azure コンテナー レジストリに格納できます。 [ACR タスク](container-registry-tasks-overview.md)のビルド コンテキストとして、ソース成果物を参照できます。 この例では、Dockerfile を OCI 成果物として格納し、その成果物を参照してコンテナー イメージをビルドする方法を示します。

たとえば、1 行の Dockerfile を作成します。

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

ターゲットのコンテナー レジストリにログインします。

```azurecli
az login
az acr login --name myregistry
```

新しい OCI 成果物を作成し、`oras push` コマンドを使用してターゲット レジストリにプッシュします。 この例では、成果物の既定のメディアの種類を設定します。

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

[az acr build](/cli/azure/acr#az-acr-build) コマンドを実行して、新しい成果物をビルド コンテキストとして使用する hello-world イメージをビルドします。

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>次の手順

* 成果物のマニフェストを構成する方法など、[ORAS ライブラリ](https://github.com/deislabs/oras/tree/master/docs)の詳細について確認する
* [OCI 成果物](https://github.com/opencontainers/artifacts)リポジトリを参照して、新しい成果物の型に関するリファレンス情報を確認する



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
