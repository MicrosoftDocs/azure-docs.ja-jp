---
title: Azure Application Gateway で HTTP ヘッダーを書き換える
description: この記事では、Azure PowerShell を使用して、Azure Application Gateway で HTTP ヘッダーを書き換える方法に関する情報を提供します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 405bc9aed4605e9728e112595f33c879bf55ec7f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005623"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Azure Application Gateway で HTTP の要求および応答ヘッダーを書き換える - Azure PowerShell

この記事では、Azure PowerShell を使用して、要求と応答の HTTP ヘッダーを書き換えるように [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) を構成する方法を示します。

> [!IMPORTANT]
> 自動スケールおよびゾーン冗長アプリケーション ゲートウェイの SKU は、現在、パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

- このチュートリアルでは、Azure PowerShell をローカルで実行する必要があります。 Az モジュール バージョン 1.0.0 以降がインストールされている必要があります。 バージョンを確認するには、`Import-Module Az`、`Get-Module Az` の順に実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell のバージョンを確認した後、`Login-AzAccount` を実行して Azure との接続を作成します。
- v1 SKU ではヘッダーの書き換え機能がサポートされていないため、Application Gateway v2 SKU が必要です。 v2 SKU をお持ちでない場合は、開始する前に [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) を作成してください。

## <a name="what-is-required-to-rewrite-a-header"></a>ヘッダーの書き換えに必要なもの

HTTP ヘッダーの書き換えを構成するには、次のようにする必要があります。

1. http ヘッダーの書き換えに必要な新しいオブジェクトを作成します。

   - **RequestHeaderConfiguration**: このオブジェクトは、書き換えようとしている要求ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。

   - **ResponseHeaderConfiguration**: このオブジェクトは、書き換えようとしている応答ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。

   - **ActionSet**: このオブジェクトには、上で指定した要求ヘッダーと応答ヘッダーの構成が格納されます。

   - **条件**: これはオプション構成です。 書き換え条件が追加された場合、HTTP(S) 要求と応答のコンテンツが評価されます。 書き換え条件に関連付けられた書き換えアクションの実行は、HTTP(S) 要求または応答が書き換え条件と一致するかどうかに基づいて決定されます。 

     1 つのアクションに複数の条件が関連付けられている場合、そのアクションが実行されるのは、すべての条件が満たされている場合のみです。つまり、論理 AND 演算が実行されます。

   - **RewriteRule**: 複数の書き換えアクション (書き換え条件の組み合わせ) が含まれます。

   - **RuleSequence**:これはオプション構成です。 別の書き換えルールの実行順序を決定するのに役立ちます。 これは、書き換えセットに複数の書き換えルールがある場合に便利です。 ルール順序の値が小さい書き換えルールから先に実行されます。 2 つの書き換えルールに同じルール順序を指定した場合、実行順序は非決定性となります。

     RuleSequence を明示的に指定しない場合、既定値の 100 が設定されます。

   - **RewriteRuleSet**: このオブジェクトには、要求ルーティング規則に関連付けられる複数の書き換えルールが含まれます。

2. rewriteRuleSet をルーティング規則にアタッチする必要があります。 これは、書き換え構成がルーティング規則によってソース リスナーにアタッチされるためです。 基本ルーティング規則の使用時には、書き換えの構成は、ソース リスナーに関連付けられている、グローバルなヘッダーの書き換えとなります。 パスベースのルーティング規則を使用する場合、ヘッダーの書き換えの構成は、URL パス マップで定義されています。 そのため、サイトの特定のパス領域にのみ適用されます。

http ヘッダーの書き換えセットを複数作成し、それぞれの書き換えセットを複数のリスナーに適用することができます。 しかし、特定のリスナーに対して適用できる書き換えセットは 1 つだけです。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**http ヘッダーの書き換えルールの構成を指定する**

この例では、場所ヘッダーに "azurewebsites.net" への参照が含まれている場合は常に、http 応答の場所ヘッダーを書き換えて、リダイレクト URL を変更します。 これを行うには、`(https?):\/\/.*azurewebsites\.net(.*)$` パターンを使用して、応答の場所ヘッダーに azurewebsites.net が含まれているかどうかを評価する条件を追加します。 ヘッダー値として `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` を使用します。 これにより、場所ヘッダーの *azurewebsites.net* が *contoso.com* に置き換えられます。

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>既存のアプリケーション ゲートウェイの構成を取得する

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>既存の要求ルーティング規則の構成を取得する

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>http ヘッダーの書き換えのためにこの構成でアプリケーション ゲートウェイを更新する

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>書き換えルールを削除する

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>次の手順

一般的ないくつかのユース ケースを実行するのに必要な構成についてさらに学習する場合は、[一般的なヘッダーの書き換えシナリオ](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)に関するページを参照してください。