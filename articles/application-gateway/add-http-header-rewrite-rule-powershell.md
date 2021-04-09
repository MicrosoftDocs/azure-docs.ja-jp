---
title: Azure Application Gateway で HTTP ヘッダーを書き換える
description: この記事では、Azure PowerShell を使用して、Azure Application Gateway で HTTP ヘッダーを書き換える方法に関する情報を提供します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 29ca3aff7d75c7a14bf7b325719924936762d191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711690"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Azure Application Gateway で HTTP の要求および応答ヘッダーを書き換える - Azure PowerShell

この記事では、Azure PowerShell を使用して、要求と応答の HTTP ヘッダーを書き換えるように [Application Gateway v2 SKU](./application-gateway-autoscaling-zone-redundant.md) インスタンスを構成する方法を説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

- この記事の手順を完了するには、ローカル環境で Azure PowerShell を実行する必要があります。 また、Az モジュール バージョン 1.0.0 以降がインストールされている必要があります。 `Import-Module Az` を実行してから `Get-Module Az` を実行し、インストールされているバージョンを確認します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell のバージョンを確認した後、`Login-AzAccount` を実行して Azure との接続を作成します。
- Application Gateway v2 SKU インスタンスが必要です。 ヘッダーの書き換えは v1 SKU ではサポートされていません。 v2 SKU を持っていない場合は、始める前に [Application Gateway v2 SKU](./tutorial-autoscale-ps.md) インスタンスを作成してください。

## <a name="create-required-objects"></a>必要なオブジェクトを作成する

HTTP ヘッダーの書き換えを構成するには、次の手順のようにする必要があります。

1. HTTP ヘッダーの書き換えに必要なオブジェクトを作成します。

   - **RequestHeaderConfiguration**: 書き換えようとしている要求ヘッダー フィールドとヘッダーの新しい値を指定するために使用されます。

   - **ResponseHeaderConfiguration**: 書き換えようとしている応答ヘッダー フィールドとヘッダーの新しい値を指定するために使用されます。

   - **ActionSet**: 上で指定した要求ヘッダーと応答ヘッダーの構成が格納されます。

   - **Condition**: オプション構成です。 書き換え条件では、HTTP(S) の要求と応答の内容が評価されます。 HTTP(S) の要求または応答が書き換え条件に一致する場合、書き換えアクションが発生します。

     複数の条件を 1 つのアクションと関連付けた場合は、すべての条件が満たされた場合にのみアクションが発生します。 つまり、操作は論理 AND 操作です。

   - **RewriteRule**: 複数の書き換えアクションまたは書き換え条件の組み合わせが含まれます。

   - **RuleSequence**: 書き換え規則の実行順序の決定に役立つオプションの構成です。 この構成は、書き換えセットに複数の書き換え規則がある場合に便利です。 規則のシーケンスの値が小さい書き換え規則から先に実行されます。 2 つの書き換え規則に同じ規則のシーケンスの値を割り当てた場合、実行順序は非決定論的となります。

     RuleSequence を明示的に指定しない場合、既定値の 100 が設定されます。

   - **RewriteRuleSet**: 要求ルーティング規則に関連付けられる複数の書き換え規則が含まれます。

2. RewriteRuleSet をルーティング規則にアタッチします。 書き換え構成が、ルーティング規則によってソース リスナーにアタッチされます。 基本ルーティング規則を使うと、ヘッダー書き換え構成はソース リスナーに関連付けられ、グローバルなヘッダーの書き換えになります。 パスベースのルーティング規則を使うと、ヘッダー書き換え構成は URL パス マップで定義されます。 その場合、サイトの特定のパス領域にのみ適用されます。

HTTP ヘッダーの書き換えセットを複数作成し、それぞれの書き換えセットを複数のリスナーに適用することができます。 ただし、特定のリスナーに対して適用できる書き換えセットは 1 つだけです。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>HTTP ヘッダーの書き換え規則の構成を指定する

この例では、場所ヘッダーに azurewebsites.net への参照が含まれている場合は常に、HTTP 応答の場所ヘッダーを書き換えて、リダイレクト URL を変更します。 これを行うには、応答の場所ヘッダーに azurewebsites.net が含まれているかどうかを評価する条件を追加します。 ここでは、パターン `(https?)://.*azurewebsites.net(.*)$` を使います。 そして、ヘッダー値として `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` を使います。 この値により、場所ヘッダーの *azurewebsites.net* が *contoso.com* に置き換えられます。

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>アプリケーション ゲートウェイの構成を取得する

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>要求ルーティング規則の構成を取得する

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>HTTP ヘッダーの書き換えのためにこの構成でアプリケーション ゲートウェイを更新する

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

## <a name="next-steps"></a>次のステップ

一般的なユース ケースの設定方法の詳細については、[共通ヘッダーの書き換えシナリオ](./rewrite-http-headers.md)に関する記事をご覧ください。