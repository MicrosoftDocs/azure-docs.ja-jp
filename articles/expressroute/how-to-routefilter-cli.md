---
title: チュートリアル:Microsoft ピアリングにルート フィルターを構成する - Azure CLI
description: このチュートリアルでは、Azure CLI を使用して Microsoft ピアリングにルート フィルターを構成する方法について説明します
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: ac7fc5af21f11699331d41a074e88ae757170664
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975996"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-azure-cli"></a>チュートリアル:Microsoft ピアリングにルート フィルターを構成する:Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

ルート フィルターとは、Microsoft ピアリングでサポートされるサービスの一部だけを利用する手段です。 この記事の手順を通じて、ExpressRoute 回線にルート フィルターを構成し、管理することができます。

Microsoft 365 サービス (Exchange Online、SharePoint Online、Skype for Business など) には、Microsoft ピアリングを介してアクセスできます。 ExpressRoute 回線に Microsoft ピアリングを構成すると、これらのサービスに関連したすべてのプレフィックスが、確立された BGP セッションを通じてアドバタイズされます。 提供されているサービスをプレフィックスで識別するために、すべてのプレフィックスには BGP コミュニティ値がアタッチされます。 BGP コミュニティ値とサービスのマッピング一覧については、[BGP コミュニティ](expressroute-routing.md#bgp)に関するページを参照してください。

すべての Azure および Microsoft 365 サービスに接続すると、多数のプレフィックスが BGP を通じてアドバタイズされます。 このようにプレフィックスが多数になると、ネットワーク内のルーターによって管理されるルート テーブルのサイズが著しく増加します。 Microsoft ピアリング経由で提供されるサービスの一部しか利用する予定がない場合は、ルート テーブルのサイズを 2 とおりの方法で減らすことができます。 次のようにすることができます。

* BGP コミュニティにルート フィルターを適用して不要なプレフィックスを除外する。 ルート フィルター処理はネットワーク運用の標準的技法であり、多くのネットワークで広く使用されています。

* ルート フィルターを定義して ExpressRoute 回線に適用する。 ルート フィルターは、Microsoft ピアリング経由で利用する予定の一連のサービスを選択できる新しいリソースです。 ExpressRoute ルーターからは、ルート フィルターで識別されたサービスに属しているプレフィックスのリストだけが送信されます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - BGP コミュニティ値を取得する。
> - ルート フィルターとフィルターのルールを作成する。
> - ExpressRoute 回線にルート フィルターを関連付ける。

### <a name="about-route-filters"></a><a name="about"></a>ルート フィルターについて

ExpressRoute 回線に Microsoft ピアリングが構成されると、接続プロバイダーを介してご利用のエッジ ルーターとの間に一対の BGP セッションが Microsoft エッジ ルーターによって確立されます。 貴社のネットワークにはルートが一切アドバタイズされません。 ネットワークに対するルート アドバタイズを有効にするには、ルート フィルターを関連付ける必要があります。

ExpressRoute 回線の Microsoft ピアリング経由で利用するサービスがルート フィルターによって識別されます。 これは、実質的にはすべての BGP コミュニティ値から成る許可リストです。 ルート フィルター リソースを定義して ExpressRoute 回線にアタッチすると、BGP コミュニティ値にマッピングされたすべてのプレフィックスが貴社のネットワークにアドバタイズされます。

Microsoft 365 サービスにルート フィルターをアタッチするには、ExpressRoute 経由での Microsoft 365 サービスの利用に対する承認が必要となります。 ExpressRoute 経由で Microsoft 365 サービスを利用することが承認されていない場合、ルート フィルターをアタッチする操作は失敗します。 承認プロセスの詳細については、[Microsoft 365 向け Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)に関する記事を参照してください。

> [!IMPORTANT]
> 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。 2017 年 8 月 1 日以降に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが回線に接続されるまで、プレフィックスはアドバタイズされません。
> 

## <a name="prerequisites"></a>前提条件

Microsoft ピアリング経由でサービスに正しく接続するためには、次の構成手順を完了する必要があります。

* Microsoft ピアリングがプロビジョニングされたアクティブな ExpressRoute 回線を用意する。 その作業は、次の手順に従って実行できます。
  * あらかじめ [ExpressRoute 回線を作成](howto-circuit-cli.md)し、接続プロバイダー経由で回線を有効にしておきます。 ExpressRoute 回線がプロビジョニングされて有効な状態になっている必要があります。
  * BGP セッションを直接管理する場合は、[Microsoft ピアリングを作成](howto-routing-cli.md)します。 または、ご利用の回線に対する Microsoft ピアリングのプロビジョニングを接続プロバイダーに依頼します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI をローカルにインストールして使用する場合、このクイックスタートでは Azure CLI バージョン 2.0.28 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択する

構成を始めるには、Azure アカウントにサインインします。 "試してみる" を使用している場合は、自動的にサインインされ、ログイン手順をスキップできます。 接続については、次の例を参照してください。

```azurecli
az login
```

アカウントのサブスクリプションを確認します。

```azurecli-interactive
az account list
```

ExpressRoute 回線を作成するサブスクリプションを選択します。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>一連のプレフィックスと BGP コミュニティ値を取得する

1. 次のコマンドレットを使用して、BGP コミュニティ値と、Microsoft ピアリング経由でアクセス可能なサービスに関連付けられているプレフィックスとから成るリストを取得します。

    ```azurecli-interactive
    az network route-filter rule list-service-communities
    ```

1. ルート フィルターで使用する BGP コミュニティ値をリストアップします。

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>ルート フィルターとフィルター ルールを作成する

ルート フィルターに割り当てることができるルールは 1 つだけで、また "許可" タイプであることが必要です。 このルールに、一連の BGP コミュニティ値を関連付けることができます。 `az network route-filter create` コマンドで作成されるのはルート フィルター リソースだけです。 このリソースを作成した後、自分でルールを作成し、ルート フィルター オブジェクトにアタッチする必要があります。

1. ルート フィルター リソースを作成するには、次のコマンドを実行します。

    ```azurecli-interactive
    az network route-filter create -n MyRouteFilter -g MyResourceGroup
    ```

1. ルート フィルター規則を作成するには、次のコマンドを実行します。
 
    ```azurecli-interactive
    az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>ルート フィルターを ExpressRoute 回線にアタッチする

次のコマンドを実行して、ルート フィルターを ExpressRoute 回線にアタッチします。

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>一般的なタスク

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>ルート フィルターのプロパティを取得するには

ルート フィルターのプロパティを取得するには、次のコマンドを使用します。

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>ルート フィルターのプロパティを更新するには

既にルート フィルターが回線にアタッチされている場合、一連の BGP コミュニティ値が更新されると、プレフィックス アドバタイズの変更が、確立されている BGP セッションを通じて自動的に伝達されます。 ルート フィルターに対する一連の BGP コミュニティ値は、次のコマンドで更新できます。

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>ExpressRoute 回線からルート フィルターをデタッチするには

ExpressRoute 回線からルート フィルターをデタッチした後は、いずれのプレフィックスも BGP セッションを通じてアドバタイズされません。 ExpressRoute 回線からルート フィルターをデタッチするには、次のコマンドを使用します。

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

削除できるのは、いずれの回線にもアタッチされていないルート フィルターだけです。 ルート フィルターの削除を試みる前に、それが回線にアタッチされていないことを確認してください。 ルート フィルターを削除するには、次のコマンドを使用します。

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>次の手順

ルーター構成のサンプルについては、以下を参照してください。

> [!div class="nextstepaction"]
> [ルーティングをセットアップして管理するためのルーター構成のサンプル](expressroute-config-samples-routing.md)
