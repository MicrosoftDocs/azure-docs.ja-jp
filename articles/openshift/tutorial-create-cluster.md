---
title: チュートリアル - Azure Red Hat OpenShift 4 クラスターを作成する
description: Azure CLI を使用して Microsoft Azure Red Hat OpenShift クラスターを作成する方法を学習します
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: a581678fdd05dade336f7ca9fcbcf5ad4c92d49a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300172"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>チュートリアル:Azure Red Hat OpenShift 4 クラスターを作成する

このチュートリアルは、3 部構成のパート 1 です。OpenShift 4 が実行される Azure Red Hat OpenShift クラスターを作成する環境を準備し、クラスターを作成します。 学習内容:
> [!div class="checklist"]
> * 前提条件を設定し、必要な仮想ネットワークとサブネットを作成する
> * クラスターのデプロイ

## <a name="before-you-begin"></a>開始する前に

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。

OpenShift クラスターを作成して実行するには、Azure Red Hat OpenShift に少なくとも 40 コアが必要です。 新しい Azure サブスクリプションの既定の Azure リソース クォータは、この要件を満たしていません。 リソースの制限の引き上げを依頼するには、[標準クォータ: VM シリーズでの制限の引き上げ](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)に関するページの説明に従って引き上げをリクエストしてください。

### <a name="verify-your-permissions"></a>アクセス許可を確認する

Azure Red Hat OpenShift クラスターを作成するには、次のアクセス許可が Azure サブスクリプション、Azure Active Directory ユーザー、またはサービス プリンシパルにあることを確認します。

|アクセス許可|VNet を含むリソース グループ|`az aro create` を実行するユーザー|`–client-id` として渡されるサービス プリンシパル|
|----|:----:|:----:|:----:|
|**User Access Administrator**|X|X| |
|**Contributor**|X|X|X|

### <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

次に、お使いのサブスクリプションに `Microsoft.RedHatOpenShift` リソースプロバイダーを登録する必要があります。

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat プル シークレットを取得する (省略可能)

Red Hat プル シークレットを使用すると、クラスターは追加のコンテンツと共に Red Hat コンテナー レジストリにアクセスできます。 この手順は省略可能ですが、実施することをお勧めします。

1. **[Red Hat OpenShift クラスター マネージャー ポータルに移動](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)し、ログインします。**

   Red Hat アカウントにログインするか、お使いのビジネス メール アドレスを使用して新しい Red Hat アカウントを作成し、使用条件に同意する必要があります。

2. クラスターを初めて作成する場合は、[**OpenShift 製品ページ**](https://developers.redhat.com/products/codeready-containers)を参照してください。 登録後に、[**Red Hat OpenShift Cluster Manager ページ**](https://cloud.redhat.com/openshift/)に移動し、 **[Download pull secret]\(プル シークレットのダウンロード\)** をクリックすると、ARO クラスターで使用するプル シークレットをダウンロードできます。

保存済みの `pull-secret.txt` ファイルは安全な場所に保管してください。 Red Hat または認定パートナーのサンプルやオペレーターを含むクラスターを作成する必要がある場合は、クラスターを作成するたびにこのファイルが使用されます。

`az aro create` コマンドを実行する場合は、`--pull-secret @pull-secret.txt` パラメーターを使用してプル シークレットを参照できます。 `pull-secret.txt` ファイルを格納したディレクトリから `az aro create` を実行します。 それ以外の場合は、`@pull-secret.txt` を `@<path-to-my-pull-secret-file>` で置き換えます。

プル シークレットをコピーする場合や他のスクリプトで参照する場合は、プル シークレットを有効な JSON 文字列として書式設定する必要があります。

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>クラスターのカスタム ドメインを準備する (省略可能)

`az aro create` コマンドを実行するときに、`--domain foo.example.com` パラメーターを使用してクラスターのカスタム ドメインを指定することができます。

クラスターのカスタム ドメインを指定する場合は、次の点に注意してください。

* クラスターを作成した後、指定した `--domain` に対して DNS サーバーに次の 2 つの DNS A レコードを作成する必要があります。
    * **api** - API サーバーをポイントします
    * **\*. apps** - イングレスをポイントします
    * 次のコマンドを実行して、これらの値を取得します: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'`。

* OpenShift コンソールは、組み込みのドメイン (`https://console-openshift-console.apps.<random>.<location>.aroapp.io`) ではなく、`https://console-openshift-console.apps.foo.example.com` のような URL で使用できるようになります。

* OpenShift では、既定で、`*.apps.<random>.<location>.aroapp.io` に作成されたすべてのルートに自己署名証明書が使用されます。  クラスターに接続した後でカスタム DNS を使用する場合は、OpenShift のドキュメントに従って、[イングレス コントローラー用のカスタム CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) と、[API サーバー用のカスタム CA を構成する](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)必要があります。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>2 つの空のサブネットを含む仮想ネットワークを作成する

次に、2 つの空のサブネットを含む仮想ネットワークを作成します。

1. **`az` コマンドを実行するシェル環境で、次の変数を設定します。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **リソース グループを作成します。**

    Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) コマンドを使用して、リソース グループを作成します。
    
> [!NOTE]
> Azure Red Hat OpenShift は、Azure リソース グループを作成できるすべてのリージョンで使用可能なわけではありません。 Azure Red Hat OpenShift がサポートされている場所については、「[使用可能なリージョン](https://docs.openshift.com/aro/4/welcome/index.html#available-regions)」を参照してください。

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    The following example output shows the resource group created successfully:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **仮想ネットワークを作成します。**

    OpenShift 4 を実行する Azure Red Hat OpenShift クラスターでは、マスター ノードとワーカー ノード用の 2 つの空のサブネットを持つ仮想ネットワークが必要です。

    先ほど作成したのと同じリソース グループ内に新しい仮想ネットワークを作成します。

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    次の出力例では、正常に作成された仮想ネットワークが示されています。

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **マスター ノード用の空のサブネットを追加します。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **ワーカー ノード用の空のサブネットを追加します。**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **マスター サブネットの[サブネット プライベート エンドポイント ポリシーを無効にします](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy)。** これは、クラスターに接続して管理できるようにするために必要です。

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>クラスターを作成する

次のコマンドを実行して、クラスターを作成します。 次のいずれかのオプションを使用する場合は、必要に応じてコマンドを変更してください。
* 必要に応じて、[Red Hat プル シークレットを渡して](#get-a-red-hat-pull-secret-optional)、クラスターが追加のコンテンツと共に Red Hat コンテナー レジストリにアクセスできるよう設定できます。 `--pull-secret @pull-secret.txt` 引数をコマンドに追加します。
* 必要に応じて、[カスタム ドメインを使用する](#prepare-a-custom-domain-for-your-cluster-optional)こともできます。 `--domain foo.example.com` 引数をコマンドに追加し、`foo.example.com` を独自のカスタム ドメインで置き換えます。

> [!NOTE]
> 省略可能な引数をコマンドに追加する場合は、先行するコマンド行の引数の末尾に、円記号を付けて閉じてください。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

`az aro create` コマンドを実行した後、クラスターが作成されるまでに通常約 35 分かかります。

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。
> [!div class="checklist"]
> * 前提条件を設定し、必要な仮想ネットワークとサブネットを作成する
> * クラスターのデプロイ

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターに接続する](tutorial-connect-cluster.md)
