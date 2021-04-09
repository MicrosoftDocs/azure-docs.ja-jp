---
title: クイック スタート:アプリケーションの HA のためのプロファイルを作成する - Azure CLI - Azure Traffic Manager
description: このクイックスタート記事では、高可用性 Web アプリケーションを構築するための Traffic Manager プロファイルを、Azure CLI を使用して作成する方法について説明します。
services: traffic-manager
author: duongau
mnager: kumud
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9a19e9c66967f36c3bdc4124fb9e60f7b7d2b36d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213438"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Web アプリケーションの高可用性を実現する Traffic Manager プロファイルを作成する

このクイック スタートでは、Web アプリケーションの高可用性を実現する Traffic Manager プロファイルの作成方法について説明します。

このクイック スタートでは、Web アプリケーションの 2 つのインスタンスを作成します。 これらは、それぞれ別の Azure リージョンで実行されています。 皆さんは、[エンドポイントの優先度](traffic-manager-routing-methods.md#priority-traffic-routing-method)に基づいて Traffic Manager プロファイルを作成します。 このプロファイルにより、Web アプリケーションを実行しているプライマリ サイトにユーザー トラフィックを誘導します。 Traffic Manager では、Web アプリケーションが継続的に監視されます。 プライマリ サイトが利用できなくなった場合には、バックアップ サイトへの自動フェールオーバーが実行されます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する
[az group create](/cli/azure/group) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

[az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-create) を使用して、エンドポイントの優先度に基づいてユーザー トラフィックを誘導する Traffic Manager プロファイルを作成します。

次の例の **<profile_name>** は、一意の Traffic Manager プロファイル名に置き換えます。

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Web アプリを作成する

このクイック スタートでは、2 つの異なる Azure リージョン ("*米国東部*" と "*西ヨーロッパ*") にデプロイされた、2 つの Web アプリケーション インスタンスが必要になります。 これらインスタンスは、それぞれ Traffic Manager のプライマリとフェールオーバーのエンドポイントとして機能します。

### <a name="create-web-app-service-plans"></a>Web App Service プランを作成する
2 つの異なる Azure リージョンにデプロイする Web アプリケーションの 2 つのインスタンスに対して、[az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) を使用して Web App Service プランを作成します。

次の例で、 **<appspname_eastus>** と **<appspname_westeurope>** は、一意の App Service プラン名に置き換えます

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>App Service プランで Web アプリを作成する
"*米国東部*" と "*西ヨーロッパ*" の Azure リージョンの App Service プランで、[az webapp create](/cli/azure/webapp#az-webapp-create) を使用して Web アプリケーションの 2 つのインスタンスを作成します。

次の例で、 **<app1name_eastus>** と **<app2name_westeurope>** を一意のアプリ名に置き換え、 **<appspname_eastus>** と **<appspname_westeurope>** を前のセクションでの App Service プランの作成に使用した名前に置き換えます。

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加
次のように、[az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create) を使用して、Traffic Manager プロファイルに 2 つの Web アプリを Traffic Manager エンドポイントとして追加します。

- Web アプリ ID を決定し、すべてのユーザー トラフィックをルーティングするプライマリ エンドポイントとして、"*米国東部*" Azure リージョンにある Web アプリを追加します。 
- Web アプリ ID を決定し、フェールオーバー エンドポイントとして、"*西ヨーロッパ*" Azure リージョンにある Web アプリを追加します。 

プライマリ エンドポイントが使用できない場合、トラフィックは自動的にフェールオーバー エンドポイントにルーティングされます。

次の例の **<app1name_eastus>** と **<app2name_westeurope>** は、前セクションでリージョンごとに作成したアプリ名に置き換えてください。 さらに、 **<profile_name>** は、前セクションで使用したプロファイル名に置き換えます。 

**米国東部エンドポイント**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

出力に表示された ID をメモし、次のコマンドでエンドポイントを追加するために使用します。

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**西ヨーロッパ エンドポイント**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

出力に表示された ID をメモし、次のコマンドでエンドポイントを追加するために使用します。

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Traffic Manager プロファイルをテストする

このセクションでは、Traffic Manager プロファイルのドメイン名を確認します。 また、プライマリ エンドポイントを使用できないように構成します。 最後に、Web アプリがまだ使用できることを確認します。 これは、Traffic Manager によってトラフィックがフェールオーバー エンドポイントへと送信されるためです。

次の例の **<app1name_eastus>** と **<app2name_westeurope>** は、前セクションでリージョンごとに作成したアプリ名に置き換えてください。 さらに、 **<profile_name>** は、前セクションで使用したプロファイル名に置き換えます。

### <a name="determine-the-dns-name"></a>DNS 名の決定

[az network traffic-manager profile show](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-show) を使用して Traffic Manager プロファイルの DNS 名を決定します。

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

**RelativeDnsName** 値をコピーします。 Traffic Manager プロファイルの DNS 名は *http://<* relativednsname *>.trafficmanager.net* です。 

### <a name="view-traffic-manager-in-action"></a>Traffic Manager の動作確認
1. Web ブラウザーで、Traffic Manager プロファイルの DNS 名 (*http://<* relativednsname *>.trafficmanager.net*) を入力して、Web アプリの既定の Web サイトを確認します。

    > [!NOTE]
    > このクイック スタート シナリオでは、すべての要求がプライマリ エンドポイントにルーティングされます。 これは **優先度 1** に設定されています。
2. 実際の Traffic Manager フェールオーバーを確認するには、[az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update) を使用してプライマリ サイトを無効にします。

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Traffic Manager プロファイルの DNS 名 (*http://<* relativednsname *>.trafficmanager.net*) をコピーして、新しい Web ブラウザー セッションで Web サイトを表示します。
4. Web アプリがまだ使用できることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

完了したら、[az group delete](/cli/azure/group#az-group-delete) を使用してリソース グループ、Web アプリケーション、およびすべての関連リソースを削除します。

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Web アプリケーションの高可用性を実現する Traffic Manager プロファイルを作成しました。 トラフィックのルーティングについて理解を深めるために、引き続き Traffic Manager のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [Traffic Manager のチュートリアル](tutorial-traffic-manager-improve-website-response.md)