---
title: Azure Red Hat OpenShift 4 のプライベート クラスターを作成する
description: OpenShift 4 を実行する Azure Red Hat OpenShift のプライベート クラスターを作成する方法について説明します
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 5a6eafa6b2380ee61c0de2e329a1751ec7d59c1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216073"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Azure Red Hat OpenShift 4 のプライベート クラスターを作成する

この記事では、OpenShift 4 を実行する Azure Red Hat OpenShift のプライベート クラスターを作成するための環境を準備します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件を設定し、必要な仮想ネットワークとサブネットを作成する
> * プライベート API サーバー エンドポイントとプライベート イングレス コントローラーを使用してクラスターをデプロイする

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

### <a name="register-the-resource-providers"></a>リソース プロバイダーを登録する

1. 複数の Azure サブスクリプションがある場合は、適切なサブスクリプション ID を指定します。

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. `Microsoft.RedHatOpenShift` リソース プロバイダーを登録します。

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```

1. `Microsoft.Compute` リソース プロバイダーを登録します。

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```

1. `Microsoft.Storage` リソース プロバイダーを登録します。

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat プル シークレットを取得する (省略可能)

Red Hat プル シークレットを使用すると、クラスターは追加のコンテンツと共に Red Hat コンテナー レジストリにアクセスできます。 この手順は省略可能ですが、実施することをお勧めします。

1. **[Red Hat OpenShift クラスター マネージャー ポータルに移動](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)し、ログインします。**

   Red Hat アカウントにログインするか、お使いのビジネス メール アドレスを使用して新しい Red Hat アカウントを作成し、使用条件に同意する必要があります。

2. **[Download pull secret]\(プル シークレットのダウンロード\) を選択します。**

保存されている `pull-secret.txt` ファイルは安全な場所に保管してください。このファイルは、クラスターを作成するたびに使用します。

`az aro create` コマンドを実行する場合は、`--pull-secret @pull-secret.txt` パラメーターを使用してプル シークレットを参照できます。 `pull-secret.txt` ファイルを格納したディレクトリから `az aro create` を実行します。 それ以外の場合は、`@pull-secret.txt` を `@<path-to-my-pull-secret-file` で置き換えます。

プル シークレットをコピーする場合や他のスクリプトで参照する場合は、プル シークレットを有効な JSON 文字列として書式設定する必要があります。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>2 つの空のサブネットを含む仮想ネットワークを作成する

次に、2 つの空のサブネットを含む仮想ネットワークを作成します。

1. **次の変数を設定します。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **リソース グループの作成**

    Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    次の出力例では、正常に作成されたリソース グループが示されています。

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

    前に作成したのと同じリソース グループ内に新しい仮想ネットワークを作成します。

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

5. **マスター サブネットの [サブネット プライベート エンドポイント ポリシーを無効にします](../private-link/disable-private-link-service-network-policy.md)。** これは、クラスターに接続して管理できるようにするために必要です。

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>クラスターを作成する

次のコマンドを実行して、クラスターを作成します。 必要に応じて、[Red Hat プル シークレットを渡して](#get-a-red-hat-pull-secret-optional)、クラスターが追加のコンテンツと共に Red Hat コンテナー レジストリにアクセスできるよう設定できます。

>[!NOTE]
> コマンドをコピーおよび貼り付けして、オプション パラメーターのいずれかを使用する場合は、先頭のハッシュ タグと末尾のコメント テキストを削除してください。 また、先行するコマンド行の引数の末尾に、円記号を付けて閉じます。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

`az aro create` コマンドを実行した後、クラスターが作成されるまでに通常約 35 分かかります。

>[!IMPORTANT]
> **foo.example.com** などのカスタム ドメインを指定すると、組み込みドメイン `https://console-openshift-console.apps.<random>.<location>.aroapp.io` の代わりに `https://console-openshift-console.apps.foo.example.com` などの URL で OpenShift コンソールを使用できるようになります。
>
> OpenShift では、既定で、`*.apps.<random>.<location>.aroapp.io` で作成されたすべてのルートに自己署名証明書が使用されます。  クラスターに接続した後でカスタム DNS を選択した場合は、OpenShift のドキュメントに従って、[イングレス コントローラー用のカスタム CA](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) と、[API サーバー用の カスタム CA を構成する](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)必要があります。

## <a name="connect-to-the-private-cluster"></a>プライベート クラスターに接続する

`kubeadmin` ユーザーを使用してクラスターにログインできます。  次のコマンドを実行して、`kubeadmin` ユーザーのパスワードを調べます。

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

次の出力例は、パスワードが `kubeadminPassword` に含まれることを示しています。

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

次のコマンドを実行すると、クラスター コンソールの URL を調べることができます。これは、`https://console-openshift-console.apps.<random>.<region>.aroapp.io/` のようになります。

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Azure Red Hat OpenShift のプライベート クラスターに接続するには、作成した仮想ネットワーク内、またはクラスターがデプロイされた仮想ネットワークで[ピアリング](../virtual-network/virtual-network-peering-overview.md)された仮想ネットワーク内のホストから、次の手順を実行する必要があります。

ブラウザーでコンソールの URL にアクセスし、`kubeadmin` 資格情報を使用してログインします。

![Azure Red Hat OpenShift ログイン画面を示すスクリーンショット。](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>OpenShift CLI をインストールする

OpenShift Web コンソールにログインしたら、右上の **[?]** をクリックし、 **[Command Line Tools]\(コマンド ライン ツール\)** をクリックします。 お使いのマシンに適したリリースをダウンロードします。

![Azure Red Hat OpenShift ログイン画面を示す画像](media/aro4-download-cli.png)

また、<https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> からお使いのマシンに適した CLI の最新リリースをダウンロードすることもできます。

## <a name="connect-using-the-openshift-cli"></a>OpenShift CLI を使用して接続する

API サーバーのアドレスを取得します。

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Azure Red Hat OpenShift のプライベート クラスターに接続するには、作成した仮想ネットワーク内、またはクラスターがデプロイされた仮想ネットワークで[ピアリング](../virtual-network/virtual-network-peering-overview.md)された仮想ネットワーク内のホストから、次の手順を実行する必要があります。

次のコマンドを使用して、OpenShift クラスターの API サーバーにログインします。 **\<kubeadmin password>** を今取得したパスワードに置き換えます。

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>次のステップ

この記事では、OpenShift 4 を実行する Azure Red Hat OpenShift クラスターをデプロイしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * 前提条件を設定し、必要な仮想ネットワークとサブネットを作成する
> * クラスターのデプロイ
> * `kubeadmin` ユーザーを使用してクラスターに接続する

次の記事に進み、Azure Active Directory を使用して認証のためにクラスターを構成する方法について学習します。


* [コマンドラインを使用した Azure Active Directory による認証の構成](configure-azure-ad-cli.md)


* [Azure portal と OpenShift Web コンソールを使用して Azure Active Directory による認証を構成する](configure-azure-ad-cli.md)
