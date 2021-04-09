---
title: チュートリアル - geo フィルタリング WAF ポリシーを構成する - Azure Front Door
description: このチュートリアルでは、geo フィルタリング WAF ポリシーを作成して、既存の Front Door フロントエンド ホストに関連付ける方法について学習します。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: f9b5869f7dd472c50ffb2c1c0dce765200f53882
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91324028"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>チュートリアル:Front Door に使用する geo フィルタリング WAF ポリシーを設定する方法
このチュートリアルでは、Azure PowerShell を使用して、サンプル geo フィルタリング ポリシーを作成し、それを既存の Front Door フロントエンド ホストに関連付ける方法を説明します。 このサンプル geo フィルタリング ポリシーでは、他のすべての国/地域 (米国を除く) からの要求がブロックされます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - geo フィルタリングの一致条件を定義する。
> - geo フィルタリングの一致条件をルールに追加する。
> - ポリシーにルールを追加する。
> - FrontDoor フロントエンド ホストに WAF ポリシーをリンクさせる。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件
* geo フィルター ポリシーの設定を開始する前に、PowerShell 環境を設定して Front Door プロファイルを作成します。
* Front Door を作成するには、[Front Door プロファイルの作成に関するクイック スタート](quickstart-create-front-door.md)で説明されている手順に従います。

## <a name="define-geo-filtering-match-condition"></a>geo フィルタリングの一致条件を定義する

一致条件を作成するときに、[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) のパラメーターを使用して、"US" から送信されていない要求を選択するサンプル一致条件を作成します。 2 文字の国および地域コードと国名および地域名の対応については、[こちら](front-door-geo-filtering.md)を参照してください。

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>アクションおよび優先度と共に geo フィルタリングの一致条件をルールに追加する

[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) を使用して、一致条件、アクション、および優先度に基づいて CustomRule オブジェクト `nonUSBlockRule` を作成します。  CustomRule には、複数の MatchCondition を含めることができます。  この例では、Action を Block に、Priority を 1 (最高優先度) に設定しています。

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```
## <a name="add-rules-to-a-policy"></a>ポリシーにルールを追加する
`Get-AzResourceGroup` を使用して、Front Door プロファイルが含まれているリソース グループの名前を見つけます。 次に、[New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用して、Front Door プロファイルが含まれている指定したリソース グループに、`nonUSBlockRule` を含む `geoPolicy` ポリシー オブジェクトを作成します。 geo フィルタリング ポリシーには、一意の名前を指定する必要があります。 

次の例では、*FrontDoorQS_rg0* という名前のリソース グループを使用します。また、Front Door プロファイルを作成したときに、[Front Door の作成に関するクイック スタート](quickstart-create-front-door.md)で説明されている手順に従ったと想定しています。 次の例のポリシー名 *geoPolicyAllowUSOnly* を一意のポリシー名に置き換えてください。

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Front Door フロントエンド ホストに WAF ポリシーをリンクさせる
WAF ポリシー オブジェクトを既存の Front Door フロントエンド ホストにリンクさせて、Front Door のプロパティを更新します。 

そうするには、まず、[Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor) を使用して Front Door オブジェクトを取得します。 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
次に、[Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor) を使用して、フロントエンド WebApplicationFirewallPolicyLink プロパティを `geoPolicy` の resourceId に設定します。

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Front Door フロントエンド ホストに WAF ポリシーをリンクさせるために必要な WebApplicationFirewallPolicyLink プロパティの設定は 1 回だけです。 それ以降のポリシーの更新は、自動的にフロントエンド ホストに適用されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

上記の手順では、WAF ポリシーに関連付けられている geo フィルタリング ルールを構成しました。 その後、Front Door のフロントエンド ホストにポリシーをリンクさせました。 geo フィルタリング ルールも WAF ポリシーも不要であれば、まず、フロントエンド ホストからポリシーの関連付けを解除したうえで、WAF ポリシーを削除する必要があります。

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="WAF ポリシーの関連付けを解除する":::

## <a name="next-steps"></a>次のステップ

Front Door の Web Application Firewall を構成する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Web アプリケーション ファイアウォールと Front Door](front-door-waf.md)
