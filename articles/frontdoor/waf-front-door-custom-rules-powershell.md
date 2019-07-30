---
title: Front Door 用のカスタム規則と既定の規則セットで Web アプリケーション ファイアウォール (WAF) ポリシーを構成する - Azure PowerShell
description: 既存の Front Door エンドポイントに対するカスタム規則とマネージド規則の両方で構成される WAF ポリシーを構成する方法を説明します。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: e9509172ac96a601235cc16e0d6d83c9b2f51902
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849115"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Azure PowerShell を使用して Web アプリケーション ファイアウォール ポリシーを構成する
Azure の Web アプリケーション ファイアウォール (WAF) ポリシーでは、要求が Front Door に到着したときに必要な検査が定義されています。
この記事では、いくつかのカスタム規則と有効にされた Azure マネージド既定の規則セットで構成される WAF ポリシーを構成する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件
レート制限ポリシーの設定を開始する前に、PowerShell 環境を設定して Front Door プロファイルを作成します。
### <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースの管理に [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) モデルを使う一連のコマンドレットが用意されています。 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 リンク先のページの手順に従って Azure の資格情報でサインインし、Az PowerShell モジュールをインストールします。

#### <a name="sign-in-to-azure"></a>Azure へのサインイン
```
Connect-AzAccount

```
Front Door モジュールをインストールする前に、現在のバージョンの PowerShellGet がインストールされていることを確認します。 次のコマンドを実行して、PowerShell を再度開きます。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor モジュールをインストールする 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Front Door プロファイルを作成する
Front Door プロファイルを作成するには、[Front Door プロファイルの作成に関するクイック スタート](quickstart-create-front-door.md)で説明されている手順に従います

## <a name="custom-rule-based-on-http-parameters"></a>http パラメーターに基づくカスタム規則

次の例では、[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) を使用して 2 つの一致条件を含むカスタム規則を構成する方法を示します。 要求は参照元によって定義されている指定サイトからのもので、クエリ文字列に "パスワード" は含まれません。 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>http 要求メソッドに基づくカスタム規則
次のように、[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) を使用して、"PUT" メソッドをブロックする規則を作成します。

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>サイズ制約に基づくカスタム規則を作成する

次の例では、Azure PowerShell を使用して、100 文字より長い URL が含まれる要求をブロックする規則を作成します。
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>マネージド既定の規則セットを追加する

次の例では、Azure PowerShell を使用して、マネージド既定の規則セットを作成します。
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>セキュリティ ポリシーを構成する

`Get-AzResourceGroup` を使用して、Front Door プロファイルが含まれているリソース グループの名前を見つけます。 次に、[New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用して、Front Door プロファイルが含まれる指定のリソース グループに、前の手順で作成した規則によってセキュリティ ポリシーを構成します。

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Front Door フロントエンド ホストにポリシーをリンクする
セキュリティ ポリシー オブジェクトを既存の Front Door フロントエンド ホストにリンクして、Front Door のプロパティを更新します。 最初に、[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) を使用して Front Door オブジェクトを取得します。
次に、[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) を使用して、フロントエンド *WebApplicationFirewallPolicyLink* プロパティを、前のステップで作成した "$myWAFPolicy$" の *resourceId* に設定します。 

次の例では、*myResourceGroupFD1* という名前のリソース グループを使用します。また、Front Door プロファイルを作成したときに、[Front Door の作成に関するクイック スタート](quickstart-create-front-door.md)で説明されている手順に従ったと想定しています。 また、次の例では、$frontDoorName を Front Door プロファイルの名前に置き換えます。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Front Door フロントエンドにセキュリティ ポリシーをリンクさせるために必要な *WebApplicationFirewallPolicyLink* プロパティの設定は 1 回だけです。 それ以降のポリシーの更新は、自動的にフロントエンドに適用されます。

## <a name="next-steps"></a>次の手順

- [Front Door](front-door-overview.md) の詳細を確認する 
- [Front Door 用 WAF](waf-overview.md) の詳細を確認する
