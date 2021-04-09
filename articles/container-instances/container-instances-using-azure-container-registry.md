---
title: Azure Container Registry からコンテナー イメージをデプロイする
description: Azure コンテナー レジストリからコンテナー イメージをプルして、Azure Container Instances にコンテナーをデプロイする方法について説明します。
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cca1001f0f84f4e4fc87df233f872fc1efdb3267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92736729"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure Container Registry から Azure Container Instances へのデプロイ

[Azure Container Registry](../container-registry/container-registry-intro.md) は、プライベート Docker コンテナー イメージを格納するために使用される、Azure ベースの管理されたコンテナー レジストリ サービスです。 この記事では、Azure Container Instances にデプロイするときに、Azure コンテナー レジストリに格納されているコンテナー イメージをプルする方法について説明します。 レジストリ アクセスを構成するには、Azure Active Directory サービス プリンシパルとパスワードを作成し、ログイン資格情報を Azure キー コンテナーに格納することをお勧めします。

## <a name="prerequisites"></a>前提条件

**Azure コンテナー レジストリ**: この記事の手順を完了するには、Azure コンテナー レジストリと、そのレジストリ内の少なくとも 1 つのコンテナー イメージが必要です。 レジストリが必要な場合は、「[Azure CLI を使用したコンテナー レジストリの作成](../container-registry/container-registry-get-started-azure-cli.md)」を参照してください。

**Azure CLI**:この記事のコマンド ラインの例は [Azure CLI](/cli/azure/) を使用し、Bash シェル用にフォーマットされています。 ローカルに [Azure CLI をインストール](/cli/azure/install-azure-cli)するほかに、[Azure Cloud Shell][cloud-shell-bash] を使用することもできます。

## <a name="limitations"></a>制限事項

* コンテナー グループのデプロイ時に、イメージをプルするために、同じコンテナー グループに構成されている[マネージド ID](container-instances-managed-identity.md) を使用して Azure Container Registry に対して認証を行うことはできません。
* 現時点では、Azure Virtual Network にデプロイされた [Azure Container Registry](../container-registry/container-registry-vnet.md) からイメージをプルすることはできません。

## <a name="configure-registry-authentication"></a>レジストリの認証を構成する

"ヘッドレス" サービスとアプリケーションへのアクセスを提供する運用シナリオでは、[サービス プリンシパル](../container-registry/container-registry-auth-service-principal.md)を使用して、レジストリ アクセスを構成することをお勧めします。 サービス プリンシパルを使用すると、コンテナー イメージに [Azure ロールベースのアクセス制御 (Azure RBAC)](../container-registry/container-registry-roles.md) を提供できます。 たとえば、レジストリに対するプルのみのアクセス権を持つサービス プリンシパルを設定できます。

Azure Container Registry は、追加の[認証オプション](../container-registry/container-registry-authentication.md)を提供します。

次のセクションでは、Azure キー コンテナーとサービス プリンシパルを作成し、サービス プリンシパルの資格情報をコンテナーに格納します。

### <a name="create-key-vault"></a>キー コンテナーの作成

[Azure キー コンテナー](../key-vault/general/overview.md)にコンテナーがない場合、次のコマンドを使用して Azure CLI で 1 つ作成します。

`RES_GROUP` 変数をキー コンテナーが作成される既存のリソース グループの名前で、また `ACR_NAME` をコンテナー レジストリの名前で更新します。 簡潔にするために、この記事のコマンドでは、レジストリ、キー コンテナー、コンテナー インスタンスがすべて同じリソース グループ内に作成されることを前提としています。

 `AKV_NAME` で新しいキー コンテナーの名前を指定します。 コンテナー名は、3 ～ 24 文字の英数字で、Azure 内で一意である必要があります。名前の先頭には英字、末尾には英字または数字を使用する必要があります。また、ハイフンを連続させることはできません。

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>サービス プリンシパルを作成し、資格情報を格納する

今度は、サービス プリンシパルを作成し、その資格情報をキー コンテナーに格納します。

次のコマンドでは、[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] を使用してサービス プリンシパルを作成し、[az keyvault secret set][az-keyvault-secret-set] を使用してコンテナーにサービス プリンシパルの **パスワード** を格納します。

```azurecli
# Create service principal, store its password in vault (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

上記のコマンドの `--role` 引数により、*acrpull* ロールを持つサービス プリンシパルが構成されます。これにより、レジストリに対するプルのみのアクセス権が付与されます。 プッシュ アクセス権とプル アクセス権の両方を付与するには、`--role` 引数を *acrpush* に変更します。

次にサービス プリンシパルの *appId* をコンテナーに格納します。appId は、認証のために Azure コンテナー レジストリに渡す **ユーザー名** です。

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Azure キー コンテナーを作成し、そこに 2 つのシークレットを格納します。

* `$ACR_NAME-pull-usr`:サービス プリンシパル ID。コンテナー レジストリの **ユーザー名** として使用します。
* `$ACR_NAME-pull-pwd`:サービス プリンシパルのパスワード。コンテナー レジストリの **パスワード** として使用します。

これらのシークレットは、アプリケーションおよびサービスがレジストリからイメージをプルしたときの名前で参照できます。

## <a name="deploy-container-with-azure-cli"></a>Azure CLI でコンテナーをデプロイする

これでサービス プリンシパルの資格情報を Azure キー コンテナー シークレットに格納し、格納した資格情報をアプリケーションとサービスで使用してプライベート レジストリにアクセスできます。

まず、[az acr show][az-acr-show] コマンドを使用して、レジストリのログイン サーバー名を取得します。 ログイン サーバー名はすべて小文字であり、`myregistry.azurecr.io` のようになります。

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

次の [az container create][az-container-create] コマンドを実行して、コンテナー インスタンスをデプロイします。 このコマンドは、Azure キー コンテナーに格納されたサービス プリンシパルの資格情報を使用して、コンテナー レジストリに対する認証を行います。事前に [aci-helloworld](container-instances-quickstart.md) イメージをレジストリにプッシュしていることが前提です。 レジストリにある別のイメージを使用する場合、`--image` の値を更新します。

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

前のコマンドが、コンテナーの DNS 名のラベルに乱数を追加するため、`--dns-name-label` の値は Azure 内で一意でなければなりません。 コマンドの出力には、次のようにコンテナーの完全修飾ドメイン名 (FQDN) が表示されます。

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

コンテナーが正常に開始されると、ブラウザーでその FQDN に移動して、アプリケーションが正常に実行されていることを確認できます。

## <a name="deploy-with-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用したデプロイ

`imageRegistryCredentials` プロパティをコンテナー グループに含めることで、Azure Resource Manager テンプレート内に Azure Container Registry のプロパティを指定できます。 たとえば、レジストリの資格情報を直接指定できます。

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

コンテナー グループ設定の詳細については、[Resource Manager テンプレート リファレンス](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups)を参照してください。    

Resource Manager テンプレートでの Azure キー コンテナーシークレットの参照については、「[デプロイ時に Azure キー コンテナーを使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)」を参照してください。

## <a name="deploy-with-azure-portal"></a>Azure Portal でのデプロイ

Azure Container Registry にコンテナー イメージを保持している場合は、Azure portal を使用して Azure Container Instances 内にコンテナーを簡単に作成できます。 Azure portal を使用してコンテナー レジストリからコンテナー インスタンスをデプロイするときは、レジストリの[管理者アカウント](../container-registry/container-registry-authentication.md#admin-account)を有効にする必要があります。 管理者アカウントは、主にテストのために、1 人のユーザーがレジストリにアクセスすることを目的としています。 

1. Azure ポータルで、自分のコンテナー レジストリに移動します。

1. 管理者アカウントが有効であることを確認するには、 **[アクセス キー]** を選択し、 **[管理者ユーザー]** の下で **[有効化]** を選択します。

1. **[リポジトリ]** を選択し、次にデプロイ元のリポジトリを選択して、デプロイするコンテナー イメージのタグを右クリックし、 **[実行インスタンス]** を選択します。

    ![Azure Portal の Azure Container Registry の "実行インスタンス"][acr-runinstance-contextmenu]

1. コンテナーの名前とリソース グループの名前を入力します。 必要に応じて既定の値を変更することもできます。

    ![Azure Container Instances の [作成] メニュー][acr-create-deeplink]

1. デプロイが完了したら、通知ウィンドウからコンテナー グループに移動して、その IP アドレスとその他のプロパティを確認できます。

    ![Azure Container Instances のコンテナー グループの詳細ビュー][aci-detailsview]

## <a name="next-steps"></a>次のステップ

Azure コンテナー レジストリの認証について詳しくは、「[Azure コンテナー レジストリによる認証](../container-registry/container-registry-authentication.md)」をご覧ください。

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
