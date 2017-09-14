---
title: "Azure ExpressRoute の Microsoft ピアリングにルート フィルターを構成する: PowerShell | Microsoft Docs"
description: "この記事では、PowerShell を使って Microsoft ピアリングにルート フィルターを構成する方法について説明します。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2017
ms.author: ganesr;cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: de3550c20439fa809869d98b8a57ea3be9c03e7c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="configure-route-filters-for-microsoft-peering"></a>Microsoft ピアリングにルート フィルターを構成する

ルート フィルターとは、Microsoft ピアリングでサポートされるサービスの一部だけを利用する手段です。 この記事の手順を通じて、ExpressRoute 回線にルート フィルターを構成し、管理することができます。

Dynamics 365 サービスと Office 365 サービス (Exchange Online、SharePoint Online、Skype for Business など) には、Microsoft ピアリングを介してアクセスすることができます。 ExpressRoute 回線に Microsoft ピアリングを構成すると、これらのサービスに関連したすべてのプレフィックスが、確立された BGP セッションを通じてアドバタイズされます。 提供されているサービスをプレフィックスで識別するために、すべてのプレフィックスには BGP コミュニティ値がアタッチされます。 BGP コミュニティ値とサービスのマッピング一覧については、[BGP コミュニティ](expressroute-routing.md#bgp)に関するページを参照してください。

すべてのサービスに接続する必要がある場合、多数のプレフィックスが BGP を通じてアドバタイズされます。 このことによって、ネットワーク内のルーターによって管理されるルート テーブルのサイズは著しく増加します。 Microsoft ピアリング経由で提供されるサービスの一部しか利用する予定がない場合は、ルート テーブルのサイズを 2 とおりの方法で減らすことができます。 次のようにすることができます。

- BGP コミュニティにルート フィルターを適用して不要なプレフィックスを除外する。 この方法はネットワーク運用の標準的技法であり、多くのネットワークで広く使用されています。

- ルート フィルターを定義して ExpressRoute 回線に適用する。 ルート フィルターは、Microsoft ピアリング経由で利用する予定の一連のサービスを選択できる新しいリソースです。 ExpressRoute ルーターからは、ルート フィルターで識別されたサービスに属しているプレフィックスのリストだけが送信されます。

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

- Microsoft ピアリングがプロビジョニングされたアクティブな ExpressRoute 回線を用意する。 その作業は、次の手順に従って実行できます。
  - あらかじめ [ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、接続プロバイダー経由で回線を有効にしておきます。 ExpressRoute 回線がプロビジョニングされて有効な状態になっている必要があります。
  - BGP セッションを直接管理する場合は、[Microsoft ピアリングを作成](expressroute-circuit-peerings.md)します。 または、ご利用の回線に対する Microsoft ピアリングのプロビジョニングを接続プロバイダーに依頼します。

-  ルート フィルターを作成して構成する。
    - Microsoft ピアリング経由で利用するサービスを特定します。
    - それらのサービスに関連付けられている一連の BGP コミュニティ値を特定します。
    - それらの BGP コミュニティ値と合致した一連のプレフィックスを許可するルールを作成します。

-  ExpressRoute 回線にルート フィルターをアタッチする。

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、次の条件を満たす必要があります。

 - Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 詳細については、「[Install and configure Azure PowerShell (Azure PowerShell のインストールと構成)](/powershell/azure/install-azurerm-ps)」を参照してください。

  > [!NOTE]
  > インストーラーは使用せずに、PowerShell ギャラリーから最新バージョンをダウンロードしてください。 現時点では、必要なコマンドレットがインストーラーではサポートされません。
  > 

 - 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。

 - アクティブな ExpressRoute 回線が必要です。 手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。 ExpressRoute 回線がプロビジョニングされて有効な状態になっている必要があります。

 - アクティブな Microsoft ピアリングが必要です。 [ピアリング構成の作成と変更](expressroute-circuit-peerings.md)に関するページの手順に従ってください。

### <a name="log-in-to-your-azure-account"></a>Azure アカウントへのログイン

この構成を開始する前に、Azure アカウントにログインする必要があります。 このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。

昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

```powershell
Login-AzureRmAccount
```

複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

```powershell
Get-AzureRmSubscription
```

使用するサブスクリプションを指定します。

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>手順 1.  一連のプレフィックスと BGP コミュニティ値を取得する

### <a name="1-get-a-list-of-bgp-community-values"></a>1.一連の BGP コミュニティ値を取得する

Microsoft ピアリング経由でアクセス可能なサービスに関連付けられている一連の BGP コミュニティ値とそれに対応する一連のプレフィックスを取得します。次のコマンドレットを使用してください。

```powershell
Get-AzureRmBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2.使用する値をリストアップする

ルート フィルターで使用する BGP コミュニティ値をリストアップします。 たとえば Dynamics 365 サービスの BGP コミュニティ値は 12076:5040 です。

## <a name="filter"></a>手順 2.  ルート フィルターとフィルター ルールを作成する

ルート フィルターに割り当てることができるルールは 1 つだけで、また "許可" タイプであることが必要です。 このルールに、一連の BGP コミュニティ値を関連付けることができます。

### <a name="1-create-a-route-filter"></a>1.ルート フィルターを作成する

まず、ルート フィルターを作成します。 "New-AzureRmRouteFilter" コマンドで作成されるのはルート フィルター リソースだけです。 このリソースを作成した後、自分でルールを作成し、ルート フィルター オブジェクトにアタッチする必要があります。 ルート フィルター リソースを作成するには、次のコマンドを実行します。

```powershell
New-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2.フィルター ルールを作成する

次の例に示したように、一連の BGP コミュニティ値は、コンマ区切りで指定することができます。 次のコマンドを実行して、新しいルールを作成してください。
 
```powershell
$rule = New-AzureRmRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3.ルート フィルターにルールを追加する

次のコマンドを実行して、ルート フィルターにフィルター ルールを追加します。
 
```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>手順 3. ルート フィルターを ExpressRoute 回線にアタッチする

Microsoft ピアリングのみの場合は、次のコマンドを実行して、ルート フィルターを ExpressRoute 回線にアタッチします。

```powershell
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="getproperties"></a>ルート フィルターのプロパティを取得するには

ルート フィルターのプロパティを取得するには、次の手順を使用します。

1. 次のコマンドを実行して、ルート フィルター リソースを取得します。

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. 次のコマンドを実行して、ルート フィルター リソースに使用されているルート フィルター ルールを取得します。

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

## <a name="updateproperties"></a>ルート フィルターのプロパティを更新するには

既にルート フィルターが回線にアタッチされている場合、一連の BGP コミュニティ値が更新されると、必要なプレフィックス アドバタイズの変更が、確立されている BGP セッションを通じて自動的に伝達されます。 ルート フィルターに対する一連の BGP コミュニティ値は、次のコマンドで更新できます。

```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="detach"></a>ExpressRoute 回線からルート フィルターをデタッチするには

ExpressRoute 回線からルート フィルターをデタッチした後は、いずれのプレフィックスも BGP セッションを通じてアドバタイズされません。 ExpressRoute 回線からルート フィルターをデタッチするには、次のコマンドを使用します。
  
```powershell
$ckt.Peerings[0].RouteFilter = $null
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="delete"></a>ルート フィルターを削除するには

削除できるのは、いずれの回線にもアタッチされていないルート フィルターだけです。 削除する前に、ルート フィルターが回線にアタッチされていないことを確認してください。 ルート フィルターを削除するには、次のコマンドを使用します。

```powershell
Remove-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>次のステップ

ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
