---
title: コンテナー イメージのインポート
description: Azure API を使用することで、Docker コマンドを実行することなく、Azure コンテナー レジストリにコンテナー イメージをインポートします。
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e6976f854b449f68faedd51878c2f3a7fe75cb0f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988251"
---
# <a name="import-container-images-to-a-container-registry"></a>コンテナー レジストリにコンテナー イメージをインポートする

Docker コマンドを使用せず、Azure コンテナー レジストリにコンテナー イメージを簡単にインポート (コピー) できます。 たとえば、開発レジストリのイメージを運用レジストリにインポートしたり、パブリック レジストリから基本イメージをコピーしたりすることができます。

Azure Container Registry では、複数の一般的なシナリオに対応して、既存のレジストリからイメージをコピーできます。

* パブリック レジストリからインポートする

* (同じまたは別の Azure サブスクリプションまたはテナント内にある) 別の Azure コンテナー レジストリからインポートする

* Azure 以外のプライベート コンテナー レジストリからインポートする

Azure コンテナー レジストリにイメージをインポートするやり方は、Docker CLI コマンドを使用する場合と比べて次のような利点があります。

* クライアント環境にローカルの Docker をインストールする必要がないので、サポートされている OS の種類に関係なく、任意のコンテナー イメージをインポートできます。

* マルチ アーキテクチャ イメージ (公式の Docker イメージなど) をインポートすると、マニフェストの一覧で指定されたすべてのアーキテクチャとプラットフォームのイメージがコピーされます。

* ターゲット レジストリへのアクセスに、レジストリのパブリック エンドポイントを使用する必要はありません。

コンテナー イメージをインポートするには、Azure CLI を Azure Cloud Shell またはローカルで実行する必要があります (バージョン 2.0.55 以降を推奨します)。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

> [!NOTE]
> 複数の Azure リージョンに同じコンテナー イメージを配布する必要がある場合、Azure Container Registry では [geo レプリケーション](container-registry-geo-replication.md)もサポートされます。 レジストリの geo レプリケーション (Premium サービス レベルが必要) を行うと、1 つのレジストリに含まれる同一のイメージとタグ名を、複数のリージョンで使用できます。
>

> [!IMPORTANT]
> 2 つの Azure コンテナー レジストリ間でのイメージのインポートに対する変更は、2021 年 1 月の時点で導入されました。
> * ネットワーク制限付きの Azure コンテナー レジストリとの間でインポートを行うには、ネットワークをバイパスできるように、制限付きレジストリで [**信頼されたサービスによるアクセスを許可**](allow-access-trusted-services.md)する必要があります。 既定では、この設定は有効で、インポートが可能になっています。 プライベート エンドポイントまたはレジストリ ファイアウォール規則を使用する新しく作成されたレジストリで設定が有効になっていない場合、インポートは失敗します。 
> * インポートのソースまたはターゲットとして使用される既存のネットワーク制限付き Azure コンテナー レジストリでは、このネットワーク セキュリティ機能を有効にすることは、任意ですが推奨されます。

## <a name="prerequisites"></a>前提条件

まだ Azure コンテナー レジストリがない場合は、レジストリを作成します。 手順については、「[クイック スタート: Azure CLI を使用したプライベート コンテナー レジストリの作成](container-registry-get-started-azure-cli.md)」を参照してください。

Azure コンテナー レジストリにイメージをインポートするには、使用する ID に、ターゲット レジストリへの書き込みアクセス許可 (少なくとも共同作成者ロールか、importImage アクションを許可するカスタム ロール) が付与されている必要があります。 「[Azure Container Registry のロールとアクセス許可](container-registry-roles.md#custom-roles)」をご覧ください。 

## <a name="import-from-a-public-registry"></a>パブリック レジストリからインポートする

### <a name="import-from-docker-hub"></a>Docker Hub からインポートする

たとえば、[az acr import][az-acr-import] コマンドを使用して、Docker Hub から *myregistry* というレジストリに、マルチ アーキテクチャの `hello-world:latest` イメージをインポートすることができます。 `hello-world` は Docker Hub からの公式イメージなので、このイメージは既定の `library` リポジトリにあります。 `--source` イメージのパラメーターには、リポジトリ名と、必要に応じてタグの値を指定します (タグではなく、マニフェスト ダイジェストでイメージを識別することもできます。これにより、特定のバージョンのイメージが使用されることを保証できます)。
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

`az acr repository show-manifests` コマンドを実行することで、このイメージに複数のマニフェストが関連付けられていることを確認できます。

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

[Docker Hub アカウント](https://www.docker.com/pricing)がある場合は、Docker Hub からイメージをインポートするときに資格情報を使用することをお勧めします。 Docker Hub のユーザー名とパスワード、または[個人用アクセストークン](https://docs.docker.com/docker-hub/access-tokens/)をパラメーターとして `az acr import` に渡します。 次の例では、Docker Hub 資格情報を使用して、Docker Hub の `tensorflow` リポジトリからパブリック イメージをインポートします。

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Microsoft Container Registry からインポートする

たとえば、Microsoft Container Registry 内の `windows` リポジトリから `ltsc2019` Windows Server Core イメージをインポートすることができます。

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>同じ AD テナント内の Azure コンテナー レジストリからインポートする

統合された Azure Active Directory アクセス許可を使用して、同じ AD テナント内の Azure コンテナー レジストリからイメージをインポートできます。

* 使用する ID には、ソース レジストリからの読み取り (閲覧者ロール) と、ターゲット レジストリへのインポート (共同作成者ロールか、importImage アクションを許可する[カスタム ロール](container-registry-roles.md#custom-roles)) のための Azure Active Directory アクセス許可が付与されている必要があります。

* レジストリの場所は、同じ Active Directory テナント内であれば、同じ Azure サブスクリプション内でも、別の Azure サブスクリプション内でも構いません。

* ソース レジストリへの[パブリック アクセス](container-registry-access-selected-networks.md#disable-public-network-access)は無効になっている場合があります。 パブリック アクセスが無効になっている場合、レジストリ ログイン サーバー名ではなく、リソース ID でソース レジストリを指定します。

* ソース レジストリまたはターゲット レジストリにプライベート エンドポイントがある場合、またはレジストリ ファイアウォール規則が適用される場合は、制限付きレジストリでネットワークにアクセスできるよう[信頼されたサービスが許可されている](allow-access-trusted-services.md)ことを確認します。

### <a name="import-from-a-registry-in-the-same-subscription"></a>同じサブスクリプション内のレジストリからインポートする

たとえば、ソース レジストリ *mysourceregistry* にある `aci-helloworld:latest` イメージを、同じ Azure サブスクリプション内の *myregistry* にインポートできます。

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

次の例では、レジストリのパブリック エンドポイントが無効になっているソース レジストリ *mysourceregistry* から *myregistry* に `aci-helloworld:latest` イメージがインポートされます。 ソース レジストリのリソース ID は、`--registry` パラメーターを使用して指定しています。 `--source` パラメーターには、レジストリ ログイン サーバー名ではなく、ソース リポジトリとタグのみを指定することに注意してください。

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

次の例では、タグではなくマニフェスト ダイジェスト (`sha256:...` として表される SHA-256 ハッシュ) によってイメージをインポートしています。

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>別のサブスクリプション内のレジストリからインポートする

次の例では、 *mysourceregistry* が、同じ Active Directory テナント内の *myregistry* とは別のサブスクリプション内にあります。 ソース レジストリのリソース ID は、`--registry` パラメーターを使用して指定しています。 `--source` パラメーターには、レジストリ ログイン サーバー名ではなく、ソース リポジトリとタグのみを指定することに注意してください。

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>サービス プリンシパル資格情報を使用してレジストリからインポートする

統合された Active Directory アクセス許可を使用したアクセスができないレジストリからインポートするには、ソース レジストリに対してサービス プリンシパル資格情報を使用できます (該当する場合)。 ソース レジストリへの ACRPull アクセス許可を持つ Active Directory [サービス プリンシパル](container-registry-auth-service-principal.md)の appID とパスワードを指定します。 サービス プリンシパルは、イメージをレジストリにインポートする必要のある無人のシステム (ビルド システムなど) で使用できます。

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>別の AD テナント内の Azure コンテナー レジストリからインポートする

別の Azure Active Directory テナント内の Azure コンテナー レジストリからインポートするには、ログイン サーバー名でソース レジストリを指定し、レジストリへのプル アクセスを有効にするユーザー名とパスワードの資格情報を指定します。 たとえば、[リポジトリ スコープのトークン](container-registry-repository-scoped-permissions.md)とパスワード、またはソース レジストリへの ACRPull アクセス許可を持つ Active Directory [サービス プリンシパル](container-registry-auth-service-principal.md)の appID とパスワードを指定します。 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Azure 以外のプライベート コンテナー レジストリからインポートする

Azure 以外のプライベート レジストリからイメージをインポートするには、レジストリへのプル アクセスを有効にする資格情報を指定します。 たとえば、次のようにして、プライベート Docker レジストリからイメージをプルします。 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>次のステップ

この記事では、パブリック レジストリや別のプライベート レジストリから Azure コンテナー レジストリにコンテナー イメージをインポートする方法について説明しました。 追加のイメージ インポート オプションについては、[az acr import][az-acr-import] コマンドのリファレンスをご覧ください。 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
