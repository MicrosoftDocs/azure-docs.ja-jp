---
title: Azure Front Door Service 用の Web アプリケーション ファイアウォール規則で IP 制限規則を構成する
description: 既存の Azure Front Door Service エンドポイントの IP アドレスを制限する Web アプリケーション ファイアウォール規則を構成する方法について説明します。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 611a52f43b5b0e076ae37df9df86479ec894c6f4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849165"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Azure Front Door Service 用の Web アプリケーション ファイアウォールで IP 制限規則を構成する
この記事では、Azure CLI、Azure PowerShell、または Azure Resource Manager テンプレートを使って、Azure Front Door Service 用の Web アプリケーション ファイアウォール (WAF) で IP 制限規則を構成する方法を示します。

IP アドレス ベースのアクセス制御規則は、Web アプリケーションへのアクセスの制御を可能にするカスタム WAF 規則です。 これは、Classless Inter-Domain Routing (CIDR) 形式で IP アドレスまたは IP アドレス範囲の一覧を指定することによって行われます。

既定では、Web アプリケーションにはインターネットからアクセスできます。 既知の IP アドレスまたは IP アドレス範囲の一覧のクライアントにアクセスを制限する場合は、2 つの IP 一致規則を作成する必要があります。 1 番目の IP 一致規則には、一致する値として IP アドレスの一覧が含まれており、アクションは "**許可**" に設定されます。 優先度が低い 2 番目のものは、"**ALL**" 演算子を使用し、アクションを "**ブロック**" に設定して、他のすべての IP アドレスをブロックします。 IP 制限規則が適用されると、この許可リストに含まれていないアドレスからの要求は、403 (禁止) 応答を受け取ります。  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Azure CLI で WAF ポリシーを構成する

### <a name="prerequisites"></a>前提条件
IP 制限ポリシーの構成を始める前に、ご自分の CLI 環境をセットアップして、Azure Front Door Service プロファイルを作成します。

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLI 環境をセットアップする
1. [Azure CLI](/cli/azure/install-azure-cli) をインストールするか、Azure Cloud Shell を使用します。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 続く CLI コマンドで **[試してみる]** ボタンを選択し、次に、開いた Cloud Shell セッションでご自分の Azure アカウントにサインインします。 セッションが開始されたら、`az extension add --name front-door` を入力して Azure Front Door Service 拡張機能を追加します。
 2. Bash でローカルに CLI を使用している場合は、`az login` を使って Azure にサインインします。

#### <a name="create-an-azure-front-door-service-profile"></a>Azure Front Door Service プロファイルを作成する
Azure Front Door Service プロファイルを作成するには、「[クイック スタート: 高可用性グローバル Web アプリケーション向けのフロント ドアを作成する](quickstart-create-front-door.md)」で説明されている手順に従ってください。

### <a name="create-a-waf-policy"></a>WAF ポリシーを作成する

[az network waf-policy create](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) コマンドを使用して WAF ポリシーを作成します。 次の例で、ポリシー名 *IPAllowPolicyExampleCLI* を一意のポリシー名に置き換えます。

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>カスタム IP アクセス制御規則を追加する

[az network waf-policy custom-rule create](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) コマンドを使用して、たった今作成した WAF ポリシーのカスタム IP アクセス制御規則を追加します。

次の例で以下を実行します。
-  *IPAllowPolicyExampleCLI* を、先ほど作成した一意のポリシーに置き換えます。
-  *ip-address-range-1*、*ip-address-range-2* を独自の範囲に置き換えます。

最初に、指定したアドレスに対する IP 許可規則を作成します。

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ("<ip-address-range-1>","<ip-address-range-2>") \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
次に、前の**許可**規則よりも優先度の低い、**すべてブロック**規則を作成します。 ここでも、次の例の *IPAllowPolicyExampleCLI* を、先ほど作成した一意のポリシーに置き換えます。

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
    
### <a name="find-the-id-of-a-waf-policy"></a>WAF ポリシーの ID を見つける 
[az network waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) コマンドを使用して、WAF ポリシーの ID を見つけます。 次の例の *IPAllowPolicyExampleCLI* を、先ほど作成した一意のポリシーに置き換えます。

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>WAF ポリシーを Azure Front Door Service のフロントエンド ホストにリンクする

[az network front-door update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) コマンドを使用して、Azure Front Door Service の *WebApplicationFirewallPolicyLink* ID をポリシー ID に設定します。 *IPAllowPolicyExampleCLI* を、前に作成した一意のポリシーに置き換えます。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
この例では、WAF ポリシーは **FrontendEndpoints[0]** に適用されます。 WAF ポリシーは、お使いのどのフロントエンドにもリンクできます。
> [!Note]
> WAF ポリシーを Azure Front Door Service フロント エンドにリンクするには、**WebApplicationFirewallPolicyLink** プロパティを 1 回だけ設定する必要があります。 それ以降のポリシーの更新は、自動的にフロントエンドに適用されます。

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Azure PowerShell を使用して WAF ポリシーを構成する

### <a name="prerequisites"></a>前提条件
IP 制限ポリシーの構成を始める前に、PowerShell 環境をセットアップして、Azure Front Door Service プロファイルを作成します。

#### <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースを管理するために [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) モデルを使用する一連のコマンドレットが用意されています。

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 ページの手順に従い、Azure の資格情報を使用して PowerShell にサインインし、次に Az モジュールをインストールします。

1. 次のコマンドを使用して Azure に接続し、次に対話型ダイアログを使用してサインインします。
    ```
    Connect-AzAccount
    ```
 2. Azure Front Door Service モジュールをインストールする前に、PowerShellGet モジュールの最新バージョンがインストールされていることを確認します。 次のコマンドを実行し、それから PowerShell を再度開きます。

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 次のコマンドを使用して Az.FrontDoor モジュールをインストールします。 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Azure Front Door Service プロファイルを作成する
Azure Front Door Service プロファイルを作成するには、「[クイック スタート: 高可用性グローバル Web アプリケーション向けのフロント ドアを作成する](quickstart-create-front-door.md)」で説明されている手順に従ってください。

### <a name="define-an-ip-match-condition"></a>IP の一致条件を定義する
IP の一致条件を定義するには、[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) コマンドを使います。
次の例で、*ip-address-range-1* と *ip-address-range-2* を独自の範囲に置き換えます。    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
```
次のコマンドを使用して、IP の*すべての条件に一致*規則を作成します。
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>カスタム IP 許可規則を作成する

アクションを定義して優先順位を設定するには、[New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) コマンドを使用します。 次の例では、リストに一致するクライアント IP からの要求が許可されます。

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
前の IP **許可**規則より優先度の低い、**すべてブロック**規則を作成します。
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>WAF ポリシーを構成する
`Get-AzResourceGroup` を使用して、Azure Front Door Service プロファイルが含まれているリソース グループの名前を見つけます。 次に、[New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用して、IP **すべてブロック**規則で WAF ポリシーを構成します。

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>WAF ポリシーを Azure Front Door Service のフロントエンド ホストにリンクする

WAF ポリシー オブジェクトを既存のフロントエンド ホストにリンクして、Azure Front Door Service のプロパティを更新します。 最初に、[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) を使用して Azure Front Door Service オブジェクトを取得します。 次に、[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) コマンドを使用して、**WebApplicationFirewallPolicyLink** プロパティを、前のステップで作成した *$IPAllowPolicyExamplePS* のリソース ID に設定します。

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> この例では、WAF ポリシーは **FrontendEndpoints[0]** に適用されます。 WAF ポリシーは、お使いのどのフロントエンドにもリンクできます。 WAF ポリシーを Azure Front Door Service フロント エンドにリンクするには、**WebApplicationFirewallPolicyLink** プロパティを 1 回だけ設定する必要があります。 それ以降のポリシーの更新は、自動的にフロントエンドに適用されます。


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用して WAF ポリシーを構成する
カスタム IP 制限規則を使用して Azure Front Door Service ポリシーと WAF ポリシーを作成するテンプレートを表示するには、[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip) にアクセスしてください。


## <a name="next-steps"></a>次の手順

- [Azure Front Door Service プロファイルを作成する](quickstart-create-front-door.md)方法について学習します。
