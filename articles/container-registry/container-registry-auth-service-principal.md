---
title: サービス プリンシパルによる Azure Container Registry 認証
description: Azure Active Directory サービス プリンシパルを使用して、プライベート コンテナー レジストリ内のイメージへのアクセスを提供します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: bee8b801f46c0018e75d58f941470adcc271daf0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032377"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>サービス プリンシパルによる Azure Container Registry 認証

Azure Active Directory (Azure AD) サービス プリンシパルを使って、コンテナー レジストリへのコンテナー イメージの `docker push` アクセスと `pull` アクセスを提供できます。 サービス プリンシパルを使うことで、「ヘッドレス」のサービスとアプリケーションへのアクセスを提供できます。

## <a name="what-is-a-service-principal"></a>サービス プリンシパルとは

Azure AD の*サービス プリンシパル*は、サブスクリプション内の Azure リソースへのアクセスを提供します。 サービス プリンシパルはサービスのユーザー ID と考えることができます。ここで "サービス" は、リソースへのアクセスが必要なアプリケーション、サービス、またはプラットフォームです。 指定したそれらのリソースのみをスコープとするアクセス権を持つサービス プリンシパルを構成できます。 その後、サービス プリンシパルの資格情報を使用してそれらのリソースにアクセスするようにアプリケーションまたはサービスを構成します。

Azure Container Registry のコンテキストでは、Azure 内のプライベート レジストリに対するプル、プッシュとプル、またはその他のアクセス許可を持つ Azure AD サービス プリンシパルを作成できます。 完全な一覧については、[Azure Container Registry のロールとアクセス許可](container-registry-roles.md)に関するページを参照してください。

## <a name="why-use-a-service-principal"></a>サービス プリンシパルを使う理由

Azure AD サービス プリンシパルを使うことで、スコープを設定したプライベート コンテナー レジストリへのアクセスを提供できます。 各アプリケーションや各サービスに異なるサービス プリンシパルを作成し、それぞれにレジストリへの調整済みのアクセス権を持たせます。 また、サービスやアプリケーションの間での資格情報の共有を避けることができるため、資格情報を交換したり、選択したサービス プリンシパル (および対象とするアプリケーション) のみのアクセスを取り消したりできます。

たとえば、イメージの `pull` アクセスのみを提供するサービス プリンシパルを使用するように Web アプリケーションを構成し、一方でビルド システムでは `push` と `pull` 両方のアクセスを提供するサービス プリンシパルを使用します。 アプリケーション開発の所有者が変わった場合は、ビルド システムに影響を与えずにそのサービス プリンシパルの資格情報を交換することができます。

## <a name="when-to-use-a-service-principal"></a>サービス プリンシパルを使う場合

サービス プリンシパルは、**ヘッドレス シナリオ**でレジストリへのアクセスを提供する際に使う必要があります。 つまり、自動的にまたはそれ以外の無人の方法でコンテナー イメージをプッシュまたはプルする必要があるすべてのアプリケーション、サービス、またはスクリプトが対象です。 例:

  * *プル*:レジストリからオーケストレーション システム (Kubernetes、DC/OS、Docker Swarm など) にコンテナーをデプロイします。 また、コンテナー レジストリから、[Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)、[Azure Container Instances](container-registry-auth-aci.md)、[App Service](../app-service/index.yml)、[Batch](../batch/index.yml)、[Service Fabric](/azure/service-fabric/) などの関連する Azure サービスにプルすることもできます。

  * *プッシュ*:コンテナー イメージを作成し、継続的インテグレーションと Azure Pipelines や Jenkins などのデプロイ ソリューションを使用してレジストリにプッシュします。

コンテナー イメージを開発用ワークステーションに手動でプルするときなど、レジストリへの個別アクセスでは、代わりに各自の [Azure AD ID](container-registry-authentication.md#individual-login-with-azure-ad) を使ってレジストリにアクセスすることをお勧めします ([az acr login][az-acr-login] を使うなど)。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>サンプルのスクリプト

Azure CLI の以前のサンプル スクリプトを GitHub 上で検索できます。各バージョンの Azure PowerShell についても同様です。

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>サービス プリンシパルでの認証

コンテナー レジストリへのアクセスが許可されているサービス プリンシパルがある場合は、"ヘッドレス" サービスおよびアプリケーションにアクセスするための資格情報を構成するか、`docker login` コマンドを使用してそれらを入力することができます。 次の値を使用します。

* **ユーザー名** - サービス プリンシパルのアプリケーション ID (*クライアント ID* とも呼ばれます)
* **パスワード** - サービス プリンシパルのパスワード (*クライアント シークレット*とも呼ばれます)

各値は、`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` という形式の GUID です。 

> [!TIP]
> [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) コマンドを実行することで、サービス プリンシパルのパスワードを再生成できます。
>

### <a name="use-credentials-with-azure-services"></a>Azure サービスで資格情報を使用する

サービス プリンシパルの資格情報は、Azure コンテナー レジストリに対して認証を行うことができるあらゆる Azure サービスから使用できます。 たとえば、次のようになります。

* [Azure Kubernetes Service (AKS) から Azure Container Registry の認証を受ける](container-registry-auth-aks.md)
* [Azure Container Instances (ACI) から Azure Container Registry の認証を受ける](container-registry-auth-aci.md)

### <a name="use-with-docker-login"></a>Docker ログインで使用する

サービス プリンシパルを使用して `docker login` を実行することもできます。 次の例では、サービス プリンシパルのアプリケーション ID が環境変数 `$SP_APP_ID` に、パスワードが変数 `$SP_PASSWD` に渡されます。 Docker 資格情報の管理のベスト プラクティスについては、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドのリファレンスを参照してください。

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

ログインすると、Docker によって資格情報がキャッシュされます。

## <a name="next-steps"></a>次の手順

* Azure コンテナー レジストリによる認証を受けるその他のシナリオについては、[認証の概要](container-registry-authentication.md)に関する記事を参照してください。

* Azure キー コンテナーを使用してコンテナー レジストリのサービス プリンシパルの資格情報を格納および取得する例については、[ACR タスクを使用したコンテナー イメージの構築とデプロイ](container-registry-tutorial-quick-task.md)に関するチュートリアルを参照してください。

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
