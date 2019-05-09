---
title: 'Microsoft ピアリングにルート フィルターを構成する - ExpressRoute: Azure CLI | Microsoft Docs'
description: この記事では、Azure CLI を使って Microsoft ピアリングにルート フィルターを構成する方法について説明します
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: cfd9f4c52d3ddddd944186a833cba48e6ca76182
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527967"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Microsoft ピアリングにルート フィルターを構成する: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

ルート フィルターとは、Microsoft ピアリングでサポートされるサービスの一部だけを利用する手段です。 この記事の手順を通じて、ExpressRoute 回線にルート フィルターを構成し、管理することができます。

Dynamics 365 サービスと Office 365 サービス (Exchange Online、SharePoint Online、Skype for Business など) には、Microsoft ピアリングを介してアクセスすることができます。 ExpressRoute 回線に Microsoft ピアリングを構成すると、これらのサービスに関連したすべてのプレフィックスが、確立された BGP セッションを通じてアドバタイズされます。 提供されているサービスをプレフィックスで識別するために、すべてのプレフィックスには BGP コミュニティ値がアタッチされます。 BGP コミュニティ値とサービスのマッピング一覧については、[BGP コミュニティ](expressroute-routing.md#bgp)に関するページを参照してください。

すべてのサービスに接続する必要がある場合、多数のプレフィックスが BGP を通じてアドバタイズされます。 このことによって、ネットワーク内のルーターによって管理されるルート テーブルのサイズは著しく増加します。 Microsoft ピアリング経由で提供されるサービスの一部しか利用する予定がない場合は、ルート テーブルのサイズを 2 とおりの方法で減らすことができます。 次のようにすることができます。

* BGP コミュニティにルート フィルターを適用して不要なプレフィックスを除外する。 この方法はネットワーク運用の標準的技法であり、多くのネットワークで広く使用されています。

* ルート フィルターを定義して ExpressRoute 回線に適用する。 ルート フィルターは、Microsoft ピアリング経由で利用する予定の一連のサービスを選択できる新しいリソースです。 ExpressRoute ルーターからは、ルート フィルターで識別されたサービスに属しているプレフィックスのリストだけが送信されます。

### <a name="about"></a>ルート フィルターについて

ExpressRoute 回線に Microsoft ピアリングが構成されると、Microsoft のエッジ ルーターは、(貴社または接続プロバイダーの) エッジ ルーターとの間に一対の BGP セッションを確立します。 貴社のネットワークにはルートが一切アドバタイズされません。 ネットワークに対するルート アドバタイズを有効にするには、ルート フィルターを関連付ける必要があります。

ExpressRoute 回線の Microsoft ピアリング経由で利用するサービスがルート フィルターによって識別されます。 これは、実質的には全 BGP コミュニティ値から成るホワイト リストです。 ルート フィルター リソースを定義して ExpressRoute 回線にアタッチすると、BGP コミュニティ値にマッピングされたすべてのプレフィックスが貴社のネットワークにアドバタイズされます。

その回線上の Office 365 サービスにルート フィルターをアタッチするには、ExpressRoute を経由して Office 365 サービスを利用することへの承認が必要となります。 ExpressRoute 経由で Office 365 サービスを利用することを承認されていない場合、ルート フィルターをアタッチすることはできません。 承認プロセスの詳細については、「[Office 365 向け Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)」を参照してください。 Dynamics 365 サービスへの接続に事前の承認は一切必要ありません。

> [!IMPORTANT]
> 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。 2017 年 8 月 1 日以降に構成された ExpressRoute 回路の Microsoft ピアリングでは、ルート フィルターが回線にアタッチされるまで、プレフィックスはアドバタイズされません。
> 
> 

### <a name="workflow"></a>ワークフロー

Microsoft ピアリング経由でサービスに正しく接続するためには、次の構成手順を完了する必要があります。

* Microsoft ピアリングがプロビジョニングされたアクティブな ExpressRoute 回線を用意する。 その作業は、次の手順に従って実行できます。
  * あらかじめ [ExpressRoute 回線を作成](howto-circuit-cli.md)し、接続プロバイダー経由で回線を有効にしておきます。 ExpressRoute 回線がプロビジョニングされて有効な状態になっている必要があります。
  * BGP セッションを直接管理する場合は、[Microsoft ピアリングを作成](howto-routing-cli.md)します。 または、ご利用の回線に対する Microsoft ピアリングのプロビジョニングを接続プロバイダーに依頼します。

* ルート フィルターを作成して構成する。
  * Microsoft ピアリング経由で利用するサービスを特定します。
  * それらのサービスに関連付けられている一連の BGP コミュニティ値を特定します。
  * それらの BGP コミュニティ値と合致した一連のプレフィックスを許可するルールを作成します。

* ExpressRoute 回線にルート フィルターをアタッチする。

## <a name="before-you-begin"></a>開始する前に

開始する前に、最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」および「[Azure CLI を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。

* アクティブな ExpressRoute 回線が必要です。 手順に従って、[ExpressRoute 回線を作成](howto-circuit-cli.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。 ExpressRoute 回線がプロビジョニングされて有効な状態になっている必要があります。

* アクティブな Microsoft ピアリングが必要です。 [ピアリング構成の作成と変更](howto-routing-cli.md)に関するページの手順に従ってください。

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

## <a name="prefixes"></a>手順 1: 一連のプレフィックスと BGP コミュニティ値を取得する

### <a name="1-get-a-list-of-bgp-community-values"></a>1.一連の BGP コミュニティ値を取得する

Microsoft ピアリング経由でアクセス可能なサービスに関連付けられている一連の BGP コミュニティ値とそれに対応する一連のプレフィックスを取得します。次のコマンドレットを使用してください。

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2.使用する値をリストアップする

ルート フィルターで使用する BGP コミュニティ値をリストアップします。 たとえば Dynamics 365 サービスの BGP コミュニティ値は 12076:5040 です。

## <a name="filter"></a>手順 2:ルート フィルターとフィルター ルールを作成する

ルート フィルターに割り当てることができるルールは 1 つだけで、また "許可" タイプであることが必要です。 このルールに、一連の BGP コミュニティ値を関連付けることができます。

### <a name="1-create-a-route-filter"></a>1.ルート フィルターを作成する

まず、ルート フィルターを作成します。 `az network route-filter create` コマンドで作成されるのはルート フィルター リソースだけです。 このリソースを作成した後、自分でルールを作成し、ルート フィルター オブジェクトにアタッチする必要があります。 ルート フィルター リソースを作成するには、次のコマンドを実行します。

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2.フィルター ルールを作成する

次のコマンドを実行して、新しいルールを作成してください。
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>手順 3: ルート フィルターを ExpressRoute 回線にアタッチする

次のコマンドを実行して、ルート フィルターを ExpressRoute 回線にアタッチします。

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>一般的なタスク

### <a name="getproperties"></a>ルート フィルターのプロパティを取得するには

ルート フィルターのプロパティを取得するには、次のコマンドを使用します。

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>ルート フィルターのプロパティを更新するには

既にルート フィルターが回線にアタッチされている場合、一連の BGP コミュニティ値が更新されると、必要なプレフィックス アドバタイズの変更が、確立されている BGP セッションを通じて自動的に伝達されます。 ルート フィルターに対する一連の BGP コミュニティ値は、次のコマンドで更新できます。

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>ExpressRoute 回線からルート フィルターをデタッチするには

ExpressRoute 回線からルート フィルターをデタッチした後は、いずれのプレフィックスも BGP セッションを通じてアドバタイズされません。 ExpressRoute 回線からルート フィルターをデタッチするには、次のコマンドを使用します。

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>ルート フィルターを削除するには

削除できるのは、いずれの回線にもアタッチされていないルート フィルターだけです。 削除する前に、ルート フィルターが回線にアタッチされていないことを確認してください。 ルート フィルターを削除するには、次のコマンドを使用します。

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>次の手順

ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
