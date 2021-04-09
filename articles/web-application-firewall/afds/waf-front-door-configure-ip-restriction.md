---
title: Azure Front Door 用の IP 制限 WAF 規則を構成する
description: 既存の Azure Front Door エンドポイントの IP アドレスを制限する Web アプリケーション ファイアウォール規則を構成する方法について学習します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 65e378c0380804c13e4b42d855aede7781b93592
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211670"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Azure Front Door 用の Web アプリケーション ファイアウォールで IP 制限規則を構成する

この記事では、Azure portal、Azure CLI、Azure PowerShell、または Azure Resource Manager テンプレートを使って、Azure Front Door 用の Web アプリケーション ファイアウォール (WAF) で IP 制限規則を構成する方法を示します。

IP アドレス ベースのアクセス制御規則は、Web アプリケーションへのアクセスの制御を可能にするカスタム WAF 規則です。 これは、Classless Inter-Domain Routing (CIDR) 形式で IP アドレスまたは IP アドレス範囲の一覧を指定することによって行われます。 IP アドレスの一致には、**RemoteAddr** と **SocketAddr** という 2 種類の一致変数があります。 RemoteAddr は、通常 X-Forwarded-For 要求ヘッダーを介して送信される元のクライアント IP です。 SocketAddr は、WAF が認識する発信元 IP アドレスです。 ユーザーがプロキシの内側にある場合、SocketAddr は通常、プロキシ サーバーのアドレスです。

既定では、Web アプリケーションにはインターネットからアクセスできます。 既知の IP アドレスまたは IP アドレス範囲の一覧からクライアントへのアクセスを制限する場合、照合値として IP アドレスの一覧が含まれ、演算子が "Not" (否定が true) に、アクションが **[ブロック]** に設定する IP 照合ルールを作成する必要があります。 IP 制限規則が適用されると、この許可リストに含まれていないアドレスからの要求は、403 (禁止) 応答を受け取ります。

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Azure portal で WAF ポリシーを構成する

### <a name="prerequisites"></a>[前提条件]

Azure Front Door プロファイルを作成するには、「[クイック スタート:高可用性グローバル Web アプリケーション向けのフロント ドアを作成する](../../frontdoor/quickstart-create-front-door.md)」で説明されている手順に従ってください。

### <a name="create-a-waf-policy"></a>WAF ポリシーを作成する

1. Azure portal で、 **[Create a resource]/(リソースの作成/)** を選択し、検索ボックスに「**Web application firewall**」と入力して、 **[Web Application Firewall (WAF)]/(Web アプリケーション ファイアウォール (WAF)/)** を選択します。
2. **［作成］** を選択します
3. **[Create a WAF policy]/(WAF ポリシーの作成/)** ページで、次の値を使用して **[基本]** タブを完成させます。
   
   |設定  |値  |
   |---------|---------|
   |次に対するポリシー     |グローバル WAF (フロント ドア)|
   |サブスクリプション     |サブスクリプションを選択します。|
   |Resource group     |フロント ドアがあるリソース グループを選択します。|
   |ポリシー名     |ポリシーの名前を入力します|
   |[ポリシーの状態]     |Enabled|

   **Next:[Next: Policy settings]/(次へ: ポリシー設定/)** を選択します

1. **[ポリシー設定]** タブで、 **[防止]** を選択します。 **ブロックの応答本文** には、「*You've been blocked!* 」と入力します そうすると、カスタム ルールが有効になっていることを確認できます。
2. **Next:[Next: Managed rules]/(次へ: マネージド ルール/)** を選択します。
3. **Next:[Next: Custom rules]/(次へ: カスタム ルール/)** を選択します。
4. **[カスタム ルールの追加]** を選択します。
5. **[カスタム ルールの追加]** ページで、次のテスト値を使用してカスタム ルールを作成します。

   |設定  |値  |
   |---------|---------|
   |カスタム ルール名     |FdWafCustRule|
   |Status     |Enabled|
   |規則の種類     |一致したもの|
   |Priority    |100|
   |一致の種類     |IP アドレス|
   |一致変数|RemoteAddr|
   |操作|[次の値を含まない]|
   |IP アドレスまたは範囲|10.10.10.0/24|
   |THEN|トラフィックを拒否する|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="カスタム規則":::

   **[追加]** を選択します。
6. **Next:[Next: Association]/(次へ: 関連付け/)** を選択します。
7. **[Add frontend host]/(フロントエンド ホストの追加/)** を選択します。
8. **[フロントエンド ホスト]** では、フロントエンド ホストを選択し、 **[追加]** を選択します。
9. **[Review + create]\(レビュー + 作成\)** を選択します。
10. ポリシー検証に合格した後、 **[作成]** を選択します。

### <a name="test-your-waf-policy"></a>WAF ポリシーをテストする

1. WAF ポリシーの展開が完了したら、Front Door フロントエンド ホスト名を参照します。
2. カスタム ブロック メッセージが表示されています。

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF 規則のテスト":::

   > [!NOTE]
   > プライベート IP アドレスは、必ずルールがトリガーされるように、カスタム ルールで意図的に使用されていました。 実際の展開では、特定の状況に対して IP アドレスを使用して、*許可* および *拒否* ルールを作成します。

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Azure CLI で WAF ポリシーを構成する

### <a name="prerequisites"></a>[前提条件]
IP 制限ポリシーの構成を始める前に、ご自分の CLI 環境をセットアップして、Azure Front Door プロファイルを作成します。

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLI 環境をセットアップする
1. [Azure CLI](/cli/azure/install-azure-cli) をインストールするか、Azure Cloud Shell を使用します。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 続く CLI コマンドで **[使ってみる]** ボタンを選択し、次に、開いた Cloud Shell セッションでご自分の Azure アカウントにサインインします。 セッションが開始されたら、`az extension add --name front-door` を入力して Azure Front Door 拡張機能を追加します。
 2. Bash でローカルに CLI を使用している場合は、`az login` を使って Azure にサインインします。

#### <a name="create-an-azure-front-door-profile"></a>Azure Front Door プロファイルを作成する
Azure Front Door プロファイルを作成するには、「[クイック スタート:高可用性グローバル Web アプリケーション向けのフロント ドアを作成する](../../frontdoor/quickstart-create-front-door.md)」で説明されている手順に従ってください。

### <a name="create-a-waf-policy"></a>WAF ポリシーを作成する

[az network front-door waf-policy create](/cli/azure/ext/front-door/network/front-door/waf-policy#ext-front-door-az-network-front-door-waf-policy-create) コマンドを使用して WAF ポリシーを作成します。 次の例で、ポリシー名 *IPAllowPolicyExampleCLI* を一意のポリシー名に置き換えます。

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>カスタム IP アクセス制御規則を追加する

[az network front-door waf-policy custom-rule create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule#ext-front-door-az-network-front-door-waf-policy-rule-create) コマンドを使用して、たった今作成した WAF ポリシーのカスタム IP アクセス制御規則を追加します。

次の例で以下を実行します。
-  *IPAllowPolicyExampleCLI* を、先ほど作成した一意のポリシーに置き換えます。
-  *ip-address-range-1*、*ip-address-range-2* を独自の範囲に置き換えます。

最初に、前の手順で作成したポリシーの IP 許可規則を作成します。 
> [!NOTE]
> 規則には次の手順で追加される一致条件が必要なため、 **--defer** が必要です。

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
次に、一致条件を規則に追加します。

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>WAF ポリシーの ID を見つける 
[az network front-door waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy#ext-front-door-az-network-front-door-waf-policy-show) コマンドを使用して、WAF ポリシーの ID を見つけます。 次の例の *IPAllowPolicyExampleCLI* を、先ほど作成した一意のポリシーに置き換えます。

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF ポリシーを Azure Front Door のフロントエンド ホストにリンクする

[az network front-door update](/cli/azure/ext/front-door/network/front-door#ext-front-door-az-network-front-door-update) コマンドを使用して、Azure Front Door の *WebApplicationFirewallPolicyLink* ID をポリシー ID に設定します。 *IPAllowPolicyExampleCLI* を、前に作成した一意のポリシーに置き換えます。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
この例では、WAF ポリシーは **FrontendEndpoints[0]** に適用されます。 WAF ポリシーは、お使いのどのフロントエンドにもリンクできます。
> [!Note]
> WAF ポリシーを Azure Front Door フロント エンドにリンクするには、**WebApplicationFirewallPolicyLink** プロパティを 1 回だけ設定する必要があります。 それ以降のポリシーの更新は、自動的にフロントエンドに適用されます。

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Azure PowerShell を使用して WAF ポリシーを構成する

### <a name="prerequisites"></a>[前提条件]
IP 制限ポリシーの構成を始める前に、PowerShell 環境をセットアップして、Azure Front Door プロファイルを作成します。

#### <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースを管理するために [Azure Resource Manager](../../azure-resource-manager/management/overview.md) モデルを使用する一連のコマンドレットが用意されています。

[Azure PowerShell](/powershell/azure/) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 ページの手順に従い、Azure の資格情報を使用して PowerShell にサインインし、次に Az モジュールをインストールします。

1. 次のコマンドを使用して Azure に接続し、次に対話型ダイアログを使用してサインインします。
    ```
    Connect-AzAccount
    ```
 2. Azure Front Door モジュールをインストールする前に、PowerShellGet モジュールの最新バージョンがインストールされていることを確認します。 次のコマンドを実行し、それから PowerShell を再度開きます。

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 次のコマンドを使用して Az.FrontDoor モジュールをインストールします。 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Azure Front Door プロファイルを作成する
Azure Front Door プロファイルを作成するには、「[クイック スタート:高可用性グローバル Web アプリケーション向けのフロント ドアを作成する](../../frontdoor/quickstart-create-front-door.md)」で説明されている手順に従ってください。

### <a name="define-an-ip-match-condition"></a>IP の一致条件を定義する
IP の一致条件を定義するには、[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) コマンドを使います。
次の例で、*ip-address-range-1* と *ip-address-range-2* を独自の範囲に置き換えます。    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>カスタム IP 許可規則を作成する

アクションを定義して優先順位を設定するには、[New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) コマンドを使用します。 次の例では、リストに一致する、クライアント IP からではない要求が禁止されます。

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF ポリシーを構成する
`Get-AzResourceGroup` を使用して、Azure Front Door プロファイルが含まれているリソース グループの名前を見つけます。 次に、[New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用して、IP 規則で WAF ポリシーを構成します。

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF ポリシーを Azure Front Door のフロントエンド ホストにリンクする

WAF ポリシー オブジェクトを既存のフロントエンド ホストにリンクして、Azure Front Door のプロパティを更新します。 最初に、[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) を使用して Azure Front Door オブジェクトを取得します。 次に、[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) コマンドを使用して、**WebApplicationFirewallPolicyLink** プロパティを、前のステップで作成した *$IPAllowPolicyExamplePS* のリソース ID に設定します。

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> この例では、WAF ポリシーは **FrontendEndpoints[0]** に適用されます。 WAF ポリシーは、お使いのどのフロントエンドにもリンクできます。 WAF ポリシーを Azure Front Door フロント エンドにリンクするには、**WebApplicationFirewallPolicyLink** プロパティを 1 回だけ設定する必要があります。 それ以降のポリシーの更新は、自動的にフロントエンドに適用されます。


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用して WAF ポリシーを構成する
カスタム IP 制限規則を使用して Azure Front Door ポリシーと WAF ポリシーを作成するテンプレートを表示するには、[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip) にアクセスしてください。


## <a name="next-steps"></a>次のステップ

- [Azure Front Door プロファイルを作成する](../../frontdoor/quickstart-create-front-door.md)方法について学習します。
