---
title: チュートリアル - Azure Front Door サービスの geo フィルタリング Web アプリケーション ファイアウォール ポリシーを構成する
description: このチュートリアルでは、単純な geo フィルタリング ポリシーを作成して、既存の Front Door フロントエンド ホストに関連付ける方法を説明します。
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 97a3d63f94ca7e60f57177703a98b60e3f6c42ec
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849185"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Front Door に使用する geo フィルタリング WAF ポリシーを設定する方法
このチュートリアルでは、Azure PowerShell を使用して、サンプル geo フィルタリング ポリシーを作成し、それを既存の Front Door フロントエンド ホストに関連付ける方法を説明します。 このサンプル geo フィルタリング ポリシーでは、他のすべての国/地域 (米国を除く) からの要求がブロックされます。

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件
geo フィルター ポリシーの設定を開始する前に、PowerShell 環境を設定して Front Door プロファイルを作成します。
### <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースの管理に [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) モデルを使う一連のコマンドレットが用意されています。 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 リンク先のページの手順に従って Azure の資格情報でサインインし、Az PowerShell モジュールをインストールします。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>サインインのための対話型ダイアログを使用して Azure に接続する
```
Connect-AzAccount
Install-Module -Name Az
```
最新バージョンの PowerShellGet がインストールされていることを確認します。 次のコマンドを実行して、PowerShell を再度開きます。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor モジュールをインストールする 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Front Door プロファイルを作成する
Front Door プロファイルを作成するには、[Front Door プロファイルの作成に関するクイック スタート](quickstart-create-front-door.md)で説明されている手順に従います。

## <a name="define-geo-filtering-match-condition"></a>geo フィルタリングの一致条件を定義する

一致条件を作成するときに、[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) のパラメーターを使用して、"US" から送信されていない要求を選択するサンプル一致条件を作成します。 2 文字の国番号と国名との対応については、[こちら](front-door-geo-filtering.md)を参照してください。

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
`Get-AzResourceGroup` を使用して、Front Door プロファイルが含まれているリソース グループの名前を見つけます。 次に、[New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用して、Front Door プロファイルが含まれている指定したリソース グループに、`nonUSBlockRule` を含む `geoPolicy` ポリシー オブジェクトを作成します。 geo ポリシーには、一意の名前を指定する必要があります。 

次の例では、*myResourceGroupFD1* という名前のリソース グループを使用します。また、Front Door プロファイルを作成したときに、[Front Door の作成に関するクイック スタート](quickstart-create-front-door.md)で説明されている手順に従ったと想定しています。 次の例のポリシー名 *geoPolicyAllowUSOnly* を一意のポリシー名に置き換えてください。

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Front Door フロントエンド ホストに WAF ポリシーをリンクさせる
WAF ポリシー オブジェクトを既存の Front Door フロントエンド ホストにリンクさせて、Front Door のプロパティを更新します。 

そうするには、まず、[Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor) を使用して Front Door オブジェクトを取得します。 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

次に、[Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor) を使用して、フロントエンド WebApplicationFirewallPolicyLink プロパティを `geoPolicy` の resourceId に設定します。

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Front Door フロントエンド ホストに WAF ポリシーをリンクさせるために必要な WebApplicationFirewallPolicyLink プロパティの設定は 1 回だけです。 それ以降のポリシーの更新は、自動的にフロントエンド ホストに適用されます。

## <a name="next-steps"></a>次の手順
- [Azure Web アプリケーション ファイアウォール](waf-overview.md)について学習します。
- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
