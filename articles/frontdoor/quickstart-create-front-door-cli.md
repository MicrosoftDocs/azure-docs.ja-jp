---
title: クイックスタート:Azure Front Door を使用して高可用性を設定する - Azure CLI
description: このクイックスタートでは、Azure Front Door を使用して、Azure CLI で高可用性を実現するハイ パフォーマンスのグローバル Web アプリケーションを作成する方法を説明します。
services: front-door
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 0b82c11dcd615dfbdcfd70f5f90edd7ba41b4562
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201619"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>クイックスタート:Azure CLI を使用して高可用性を実現するグローバル Web アプリケーションのための Front Door を作成する

Azure CLI によって Azure Front Door の使用を開始し、高可用性を実現するハイ パフォーマンスなグローバル Web アプリケーションを作成しましょう。

Front Door により、バックエンド プール内の特定のリソースに Web トラフィックが送信されます。 フロントエンド ドメインを定義し、バックエンド プールにリソースを追加して、ルーティング規則を作成します。 この記事では、1 つのバックエンド プールに 2 つの Web アプリ リソースがある単純な構成と、"/*" に一致する既定のパスを使用する 1 つのルーティング規則を使用します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure CLI または Azure Cloud Shell
- Azure CLI にフロントドア拡張機能が追加されていることを確認してください

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイックスタートでは Azure CLI バージョン 2.0.28 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure で、関連するリソースをリソース グループに割り当てます。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。

このクイックスタートでは、2 つのリソース グループが必要です。 1 つは *米国中部*、もう 1 つは *米国中南部* に必要です。

[az group create](/cli/azure/group#az-group-create) を使用して、次のようにリソース グループを作成します。

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>Web アプリの 2 つのインスタンスを作成する

このクイックスタートには、別々の Azure リージョンで実行される、2 つの Web アプリケーション インスタンスが必要です。 どちらの Web アプリケーション インスタンスも "アクティブ/アクティブ " モードで実行されるため、どちらでもトラフィックに対応することができます。

まだ Web アプリがない場合は、次のスクリプトを使用して 2 つのサンプル Web アプリを設定します。

### <a name="create-app-service-plans"></a>App Service プランを作成する

Web アプリを作成する前に、2 つの App Service プランを、1 つは *米国中部* に、もう 1 つは *米国中南部* に作成する必要があります。

[az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true) を使用して App Service プランを作成します。

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
--resource-group myRGFDSouthCentral
```

### <a name="create-web-apps"></a>Web アプリを作成する

次のコマンドを実行すると、前の手順の各 App Service プランに Web アプリが作成されます。 Web アプリ名は、グローバルに一意である必要があります。

[az webapp create](/cli/azure/webapp#az_webapp_create&preserve-view=true) を使用して Web アプリを作成します。

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

次のステップで Front Door をデプロイするときにバックエンド アドレスを定義できるように、各 Web アプリの既定のホスト名をメモしておきます。

## <a name="create-the-front-door"></a>Front Door を作成する

次のように実行して、既定の負荷分散設定、正常性プローブ、およびルーティング規則を持つ基本的な Front Door を作成します。

[az network front-door create](/cli/azure/ext/front-door/network/front-door#ext_front_door_az_network_front_door_create&preserve-view=true) を使用して Front Door を作成します。

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
```

**--resource-group:** Front Door をデプロイするリソース グループを指定します。

**--name:** Azure Front Door のグローバルに一意の名前を指定します。 

**--accepted-protocols:** 指定できる値は、**http** と **https** です。 両方を使用する場合は、両方をスペースで区切って指定します。

**--backend-address:** ここでは、両方の Web アプリのホスト名をスペースで区切って規定します。

デプロイが正常に完了したら、 *[frontEndpoints]* セクションのホスト名をメモしておきます。

## <a name="test-the-front-door"></a>Front Door をテストする

Web ブラウザーを開き、コマンドで取得したホスト名を入力します。 Front Door によって、要求がバックエンド リソースのいずれかに送信されます。

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Front Door のテスト ページ":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Front Door で作成したリソースが不要になった場合は、両方のリソース グループを削除します。 リソース グループを削除すると、Front Door とそのすべての関連リソースも削除されます。 

リソース グループを削除するには、[az group delete](/cli/azure/group#az_group_delete&preserve-view=true) を使用します。

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、次のものを作成しました。
* Front Door
* 2 つの Web アプリ

Front Door にカスタム ドメインを追加する方法を学習するには、Front Door のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](front-door-custom-domain.md)
