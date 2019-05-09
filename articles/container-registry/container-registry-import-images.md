---
title: Azure Container Registry にコンテナー イメージをインポートする
description: Azure API を使用することで、Docker コマンドを実行することなく、Azure コンテナー レジストリにコンテナー イメージをインポートします。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: b8a2280fe82e0f4be8e2812f5494150927642692
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417745"
---
# <a name="import-container-images-to-a-container-registry"></a>コンテナー レジストリにコンテナー イメージをインポートする

Docker コマンドを使用せず、Azure コンテナー レジストリにコンテナー イメージを簡単にインポート (コピー) できます。 たとえば、開発レジストリのイメージを運用レジストリにインポートしたり、パブリック レジストリから基本イメージをコピーしたりすることができます。

Azure Container Registry では、複数の一般的なシナリオに対応して、既存のレジストリからイメージをコピーできます。

* パブリック レジストリからインポートする

* (同じまたは別の Azure サブスクリプション内にある) 別の Azure コンテナー レジストリからインポートする

* Azure 以外のプライベート コンテナー レジストリからインポートする

Azure コンテナー レジストリにイメージをインポートするやり方は、Docker CLI コマンドを使用する場合と比べて次のような利点があります。

* クライアント環境にローカルの Docker をインストールする必要がないので、サポートされている OS の種類に関係なく、任意のコンテナー イメージをインポートできます。

* マルチ アーキテクチャ イメージ (公式の Docker イメージなど) をインポートすると、マニフェストの一覧で指定されたすべてのアーキテクチャとプラットフォームのイメージがコピーされます。

コンテナー イメージをインポートするには、Azure CLI を Azure Cloud Shell またはローカルで実行する必要があります (バージョン 2.0.55 以降を推奨します)。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

> [!NOTE]
> 複数の Azure リージョンに同じコンテナー イメージを配布する必要がある場合、Azure Container Registry では [geo レプリケーション](container-registry-geo-replication.md)もサポートされます。 レジストリの geo レプリケーション (Premium SKU が必要) を行うと、1 つのレジストリに含まれる同一のイメージとタグ名を、複数のリージョンで使用できます。
>

## <a name="prerequisites"></a>前提条件

まだ Azure コンテナー レジストリがない場合は、レジストリを作成します。 手順については、「[クイック スタート: Azure CLI を使用したプライベート コンテナー レジストリの作成](container-registry-get-started-azure-cli.md)」を参照してください。

Azure コンテナー レジストリにイメージをインポートするには、使用する ID に、ターゲット レジストリへの書き込みアクセス許可 (少なくとも共同作成者ロール) が付与されている必要があります。 「[Azure Container Registry のロールとアクセス許可](container-registry-roles.md)」をご覧ください。 

## <a name="import-from-a-public-registry"></a>パブリック レジストリからインポートする

### <a name="import-from-docker-hub"></a>Docker Hub からインポートする

たとえば、[az acr import][az-acr-import] コマンドを使用して、Docker Hub から *myregistry* というレジストリに、マルチ アーキテクチャの `hello-world:latest` イメージをインポートすることができます。 `hello-world` は Docker Hub からの公式イメージなので、このイメージは既定の `library` リポジトリにあります。 `--source` イメージのパラメーターには、リポジトリ名と、必要に応じてタグの値を指定します (タグではなく、マニフェスト ダイジェストでイメージを識別することもできます。これにより、特定のバージョンのイメージが使用されることを保証できます)。
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

`az acr repository show-manifests` コマンドを実行することで、このイメージに複数のマニフェストが関連付けられていることを確認できます。

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

次の例では、Docker Hub の `tensorflow` リポジトリからパブリック イメージをインポートします。

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Microsoft Container Registry からインポートする

たとえば、Microsoft Container Registry 内の `windows` リポジトリから最新の Windows Server Core イメージをインポートすることができます。

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>別の Azure コンテナー レジストリからインポートする

統合された Azure Active Directory アクセス許可を使用して、別の Azure コンテナー レジストリからイメージをインポートできます。

* 使用する ID には、ソース レジストリからの読み取り (閲覧者ロール) と、ターゲット レジストリへの書き込み (共同作成者ロール) のための Azure Active Directory アクセス許可が付与されている必要があります。

* レジストリの場所は、同じ Active Directory テナント内であれば、同じ Azure サブスクリプション内でも、別の Azure サブスクリプション内でも構いません。

### <a name="import-from-a-registry-in-the-same-subscription"></a>同じサブスクリプション内のレジストリからインポートする

たとえば、ソース レジストリ *mysourceregistry* にある `aci-helloworld:latest` イメージを、同じ Azure サブスクリプション内の *myregistry* にインポートできます。

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

次の例では、タグではなくマニフェスト ダイジェスト (`sha256:...` として表される SHA-256 ハッシュ) によってイメージをインポートしています。

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>別のサブスクリプション内のレジストリからインポートする

次の例では、 *mysourceregistry* が、同じ Active Directory テナント内の *myregistry* とは別のサブスクリプション内にあります。 ソース レジストリのリソース ID は、`--registry` パラメーターを使用して指定しています。 なお、`--source` パラメーター名では、ソース リポジトリとイメージ名だけを指定しています (レジストリ ログイン サーバー名は指定していません)。
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>サービス プリンシパル資格情報を使用してレジストリからインポートする

Active Directory アクセス許可を使用してアクセスすることができないレジストリからインポートするには、サービス プリンシパル資格情報を使用できます (該当する場合)。 ソース レジストリへの ACRPull アクセス許可を持つ Active Directory [サービス プリンシパル](container-registry-auth-service-principal.md)の appID とパスワードを指定します。 サービス プリンシパルは、イメージをレジストリにインポートする必要のある無人のシステム (ビルド システムなど) で使用できます。

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Azure 以外のプライベート コンテナー レジストリからインポートする

プライベート レジストリからイメージをインポートするには、レジストリへのプル アクセスを有効にする資格情報を指定します。 たとえば、次のようにして、プライベート Docker レジストリからイメージをプルします。 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>次の手順

この記事では、パブリック レジストリや別のプライベート レジストリから Azure コンテナー レジストリにコンテナー イメージをインポートする方法について説明しました。 追加のイメージ インポート オプションについては、[az acr import][az-acr-import] コマンドのリファレンスをご覧ください。 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
