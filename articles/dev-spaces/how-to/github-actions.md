---
title: GitHub のアクションと Azure Kubernetes Service (プレビュー)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: GitHub アクションと Azure Dev Spaces を使用して、Azure Kubernetes Service で直接 pull request からの変更を確認およびテストする
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, GitHub アクション, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 37ad621609f5a5631b498e55483e5d16e8ac4472
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202112"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub のアクションと Azure Kubernetes Service (プレビュー)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces は、pull request がリポジトリのメイン ブランチに結合される前に、pull request からの変更を AKS で直接テストできる GitHub Actions を使用してワークフローを提供します。 実行中のアプリケーションを用意して pull request の変更を確認することで、開発者とチーム メンバーの両方の信頼度を高めることができます。 この実行中のアプリケーションは、製品マネージャーやデザイナーなどのチーム メンバーが、開発の初期段階の間にレビュー プロセスの一部となることにも貢献できます。

このガイドでは、以下の方法について説明します。

* Azure のマネージド Kubernetes クラスターで Azure Dev Spaces をセットアップする。
* 複数のマイクロサービスを伴う大規模アプリケーションを開発空間にデプロイする。
* GitHub アクションを使用して CI/CD を設定する。
* 完全なアプリケーションのコンテキスト内の分離開発空間で単一のマイクロサービスをテストする。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること][azure-cli-installed]。
* [Helm 3 がインストールされていること][helm-installed]。
* [GitHub アクションが有効になっている][github-actions-beta-signup] GitHub アカウント。
* AKS クラスターで実行されている [Azure Dev Spaces 自転車共有サンプル アプリケーション](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md)。

## <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する

Azure Container Registry (ACR) を作成します。

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> ACR の名前は Azure 内で一意にする必要があり、英数字で 5 ～ 50 文字にする必要があります。 使用する文字はすべて小文字にする必要があります。

後の手順で使用されるため、出力の *loginServer* の値を保存します。

## <a name="create-a-service-principal-for-authentication"></a>認証用のサービス プリンシパルを作成する

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] を使用してサービス プリンシパルを作成します。 次に例を示します。

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

JSON 出力は、後の手順で使用されるため保存します。

[az aks show][az-aks-show] を使用して、AKS クラスターの *ID* を表示します。

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

[az acr show][az-acr-show] を使用して、ACR の *ID* を表示します。

```azurecli
az acr show --name <acrName> --query id
```

[az role assignment create][az-role-assignment-create] を使用して、AKS クラスターへの *共同作成者* アクセスと、ACR への *AcrPush* アクセスを付与します。

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> それらのリソースへのサービス プリンシパルのアクセスを付与するには、AKS クラスターと ACR の両方の所有者である必要があります。

## <a name="configure-your-github-action"></a>GitHub アクションを構成する

> [!IMPORTANT]
> リポジトリに対して GitHub アクションが有効になっている必要があります。 リポジトリに対して GitHub アクションを有効にするには、GitHub 上のリポジトリに移動し、[アクション] タブをクリックして、このリポジトリに対するアクションを有効にすることを選択します。

フォークされたリポジトリに移動し、 *[設定]* をクリックします。 左側のサイドバーの *[シークレット]* をクリックします。 *[Add a new secret] (新しいシークレットの追加)* をクリックして、下記の新しいシークレットをそれぞれ追加します。

1. *AZURE_CREDENTIALS*: サービス プリンシパルの作成からの出力全体。
1. *RESOURCE_GROUP*: AKS クラスターのリソース グループ。この例では、*MyResourceGroup* です。
1. *CLUSTER_NAME*: AKS クラスターの名前。この例では *MyAKS* です。
1. *CONTAINER_REGISTRY*: ACR 用の *loginServer*。
1. *HOST*: 開発空間用のホスト。 *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>* の形式となります。この例では、*dev.bikesharingweb.fedcab0987.eus.azds.io* です。
1. *IMAGE_PULL_SECRET*: 使用するシークレットの名前。*demo-secret* などです。
1. *MASTER_SPACE*: 親の開発空間の名前。この例では *dev* です。
1. *REGISTRY_USERNAME*: サービス プリンシパル作成の JSON 出力に含まれる *clientId* です。
1. *REGISTRY_PASSWORD*: サービス プリンシパル作成の JSON 出力に含まれる *clientSecret* です。

> [!NOTE]
> これらのシークレットはすべて GitHub アクションによって使用され、[.github/workflows/bikes.yml][github-action-yaml] 内で構成されます。

PR をマージした後にマスター スペースを更新する場合は、必要に応じて *GATEWAY_HOST* シークレットを追加します。その形式は *<MASTER_SPACE>.gateway.<HOST_SUFFIX>* で、この例では *dev.gateway.fedcab0987.eus.azds.io* となります。 変更をフォークのメイン ブランチにマージすると、別のアクションが実行され、マスター開発空間でアプリケーション全体がリビルドされ、実行されます。 この例では、マスター空間は *dev* です。 このアクションは [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml] 内で構成されています。

また、PR の変更が孫スペースで実行されるようにする場合は、*MASTER_SPACE* および *HOST* シークレットを更新します。 たとえば、アプリケーションが *dev* で実行されており、*dev/azureuser1* の子スペースがある場合、PR が *dev/azureuser1* の子空間で実行されるようにするには次の操作を行います。

* *MASTER_SPACE* を、親スペースとして必要な子スペースに更新します。この例では、*azureuser1* です。
* *HOST* を *<GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>* に更新します。この例では、*dev.bikesharingweb.fedcab0987.eus.azds.io* です。

## <a name="create-a-new-branch-for-code-changes"></a>コード変更用の新しいブランチを作成する

`BikeSharingApp/` に移動し、*bike-images* という新しいブランチを作成します。

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

[Bikes/server.js][bikes-server-js] を編集し、232 行と 233 行を削除します。

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

これでセクションは、次のようになります。

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

ファイルを保存してから、`git add` と `git commit` を使用して変更をステージングします。

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>変更をプッシュする

`git push` を使用して、フォークされたリポジトリに新しいブランチをプッシュします。

```cmd
git push origin bike-images
```

プッシュが完了したら、GitHub でフォークされたリポジトリに移動し、*bike-images* ブランチと比較される基本ブランチとして、フォークされたリポジトリの "*メイン*" ブランチを使用して pull request を作成します。

Pull request が開かれたら、 *[アクション]* タブに移動します。新しいアクションが開始され、*Bikes* サービスを構築中であることを確認します。

## <a name="view-the-child-space-with-your-changes"></a>変更を含む子空間を表示する

アクションが完了すると、pull request に含まれる変更に基づいて、新しい子空間への URL を含むコメントが表示されます。

> [!div class="mx-imgBorder"]
> ![GitHub アクションの Url](../media/github-actions/github-action-url.png)

コメントから URL を開いて *bikesharingweb* サービスに移動します。 ユーザーとして *Aurelia Briggs (顧客)* を選択してから、借りる自転車を選択します。 自転車のプレースホルダー画像が表示されなくなっていることを確認します。

変更をフォークの "*メイン*" ブランチにマージすると、別のアクションが実行され、親の開発空間でアプリケーション全体がリビルドされ、実行されます。 この例では、親空間は *dev* です。 このアクションは [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml] 内で構成されています。

## <a name="clean-up-your-azure-resources"></a>Azure リソースをクリーンアップする

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces のしくみの詳細について確認します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces のしくみ](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
