---
title: チュートリアル - Azure Red Hat OpenShift 4 クラスターを作成する
description: Azure CLI を使用して Microsoft Azure Red Hat OpenShift クラスターを作成する方法を学習します
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 10/26/2020
ms.openlocfilehash: 55c1b6f6c6690f0c8f00a8a2469834781f35fb3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449800"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>チュートリアル:Azure Red Hat OpenShift 4 クラスターを作成する

このチュートリアルは、3 部構成のパート 1 です。OpenShift 4 が実行される Azure Red Hat OpenShift クラスターを作成する環境を準備し、クラスターを作成します。 学習内容は次のとおりです。
> [!div class="checklist"]
> * 前提条件を設定する 
> * 必要な仮想ネットワークとサブネットを作成する
> * クラスターのデプロイ

## <a name="before-you-begin"></a>開始する前に

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

OpenShift クラスターを作成して実行するには、Azure Red Hat OpenShift に少なくとも 40 コアが必要です。 新しい Azure サブスクリプションの既定の Azure リソース クォータは、この要件を満たしていません。 リソースの制限の引き上げを依頼するには、「[標準クォータ:VM シリーズでの制限の引き上げ](../azure-portal/supportability/per-vm-quota-requests.md)」を参照してください。

ARO プル シークレットを使用することで、ARO の RH OpenShift ライセンスのコストが変わることはありません。

### <a name="verify-your-permissions"></a>アクセス許可を確認する

このチュートリアルでは、クラスターの仮想ネットワークを含むリソース グループを作成します。 共同作成者とユーザー アクセス管理者のアクセス許可、または仮想ネットワークに対する直接の所有者アクセス許可、またはそれを含むリソース グループまたはサブスクリプションに対する所有者アクセス許可を取得している必要があります。

また、クラスターのアプリケーションとサービス プリンシパルを作成するために、ツールに対する十分な Azure Active Directory アクセス許可を取得している必要があります。

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
    
1. `Microsoft.Authorization` リソース プロバイダーを登録します。

    ```azurecli-interactive
    az provider register -n Microsoft.Authorization --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat プル シークレットを取得する (省略可能)

Red Hat プル シークレットを使用すると、クラスターは追加のコンテンツと共に Red Hat コンテナー レジストリにアクセスできます。 この手順は省略可能ですが、実施することをお勧めします。

1. [Red Hat OpenShift クラスター マネージャー ポータルに移動](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)し、ログインします。

   Red Hat アカウントにログインするか、お使いのビジネス メール アドレスを使用して新しい Red Hat アカウントを作成し、使用条件に同意する必要があります。

1. **[Download pull secret]\(プル シークレットのダウンロード\)** をクリックし、ARO クラスターで使用するプル シークレットをダウンロードします。

    保存済みの `pull-secret.txt` ファイルは安全な場所に保管してください。 Red Hat または認定パートナーのサンプルやオペレーターを含むクラスターを作成する必要がある場合は、クラスターを作成するたびにこのファイルが使用されます。

    `az aro create` コマンドを実行する場合は、`--pull-secret @pull-secret.txt` パラメーターを使用してプル シークレットを参照できます。 `pull-secret.txt` ファイルを格納したディレクトリから `az aro create` を実行します。 それ以外の場合は、`@pull-secret.txt` を `@/path/to/my/pull-secret.txt` で置き換えます。

    プル シークレットをコピーする場合や他のスクリプトで参照する場合は、プル シークレットを有効な JSON 文字列として書式設定する必要があります。

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>クラスターのカスタム ドメインを準備する (省略可能)

`az aro create` コマンドを実行するときに、`--domain foo.example.com` パラメーターを使用してクラスターのカスタム ドメインを指定することができます。

クラスターのカスタム ドメインを指定する場合は、次の点に注意してください。

* クラスターを作成した後、指定した `--domain` に対して DNS サーバーに次の 2 つの DNS A レコードを作成する必要があります。
    * **api** - API サーバーの IP アドレスを指します
    * **\*.apps** - イングレス IP アドレスを指します
    * クラスターの作成後に次のコマンドを実行して、これらの値を取得します: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'`。

* OpenShift コンソールは、組み込みのドメイン (`https://console-openshift-console.apps.<random>.<location>.aroapp.io`) ではなく、`https://console-openshift-console.apps.example.com` のような URL で使用できるようになります。

* OpenShift では、既定で、`*.apps.example.com` カスタム ドメインに作成されたすべてのルートに自己署名証明書が使用されます。  クラスターに接続した後でカスタム DNS を使用する場合は、OpenShift のドキュメントに従って、[イングレス コントローラー用のカスタム CA](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html) と、[API サーバー用のカスタム CA を構成する](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html)必要があります。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>2 つの空のサブネットを含む仮想ネットワークを作成する

次に、2 つの空のサブネットを含む仮想ネットワークを作成します。 要件を満たす仮想ネットワークが既にある場合は、この手順を省略できます。

1. **`az` コマンドを実行するシェル環境で、次の変数を設定します。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **リソース グループを作成します。**

   Azure リソース グループは、Azure リソースが展開され管理される論理グループです。 リソース グループを作成する際は、場所を指定するよう求められます。 この場所は、リソース グループのメタデータが格納される場所です。また、リソースの作成時に別のリージョンを指定しない場合は、Azure でリソースが実行される場所でもあります。 [az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。
    
   > [!NOTE] 
   > Azure Red Hat OpenShift は、Azure リソース グループを作成できるすべてのリージョンで使用可能なわけではありません。 Azure Red Hat OpenShift がサポートされている場所については、「[使用可能なリージョン](https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=openshift)」を参照してください。

   ```azurecli-interactive
   az group create \
     --name $RESOURCEGROUP \
     --location $LOCATION
   ```

   次の出力例では、正常に作成されたリソース グループが示されています。

   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
     "location": "eastus",
     "name": "aro-rg",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "type": "Microsoft.Resources/resourceGroups"
   }
   ```

2. **仮想ネットワークを作成します。**

   OpenShift 4 を実行する Azure Red Hat OpenShift クラスターでは、マスター ノードとワーカー ノード用の 2 つの空のサブネットを持つ仮想ネットワークが必要です。 これのために新しい仮想ネットワークを作成することも、既存の仮想ネットワークを使用することもできます。

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
       "dhcpOptions": {
         "dnsServers": []
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

5. **マスター サブネットの [サブネット プライベート エンドポイント ポリシーを無効にします](../private-link/disable-private-link-service-network-policy.md)。** これは、サービスがクラスターに接続して管理できるようにするために必要です。

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
