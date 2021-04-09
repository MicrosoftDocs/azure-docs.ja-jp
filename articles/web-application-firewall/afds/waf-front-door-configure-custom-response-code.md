---
title: Azure Front Door で Web アプリケーション ファイアウォール (WAF) に対するカスタム応答を構成する
description: WAF で要求がブロックされたときのカスタムの応答コードとメッセージを構成する方法について説明します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563683"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Azure Web アプリケーション ファイアウォール (WAF) に対するカスタム応答を構成する

既定では、規則が一致したために WAF で要求がブロックされると、403 状態コードと "**要求はブロックされました**" というメッセージが返されます。 既定のメッセージには、要求の[ログ エントリ](./waf-front-door-monitor.md)へのリンクに使用できる追跡参照文字列も含まれています。  ユース ケースの参照文字列を使用して、カスタム応答状態コードとカスタム メッセージを構成することができます。 この記事では、要求が WAF によってブロックされたときのカスタム応答ページを構成する方法について説明します。

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>カスタム応答状態コードとメッセージ使用ポータルを構成する

WAF ポータルの [ポリシー設定] で、カスタム応答状態コードと本文を構成できます。

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF ポリシーの設定":::

上記の例では、応答コードを 403 のままにして、次の図に示すように簡単な "Please contact us"(お問い合わせください) メッセージを構成しました。

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="カスタム応答の例":::

"{{azure-ref}}" によって、応答本文に一意の参照文字列が挿入されます。 この値は `FrontdoorAccessLog` および `FrontdoorWebApplicationFirewallLog` ログの TrackingReference フィールドと一致します。

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>カスタム応答状態コードとメッセージ使用 PowerShell を構成する

### <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする

Azure PowerShell には、Azure リソースの管理に [Azure Resource Manager](../../azure-resource-manager/management/overview.md) モデルを使う一連のコマンドレットが用意されています。 

[Azure PowerShell](/powershell/azure/) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 リンク先のページの手順に従って Azure の資格情報でサインインし、Az PowerShell モジュールをインストールします。

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>サインインのための対話型ダイアログを使用して Azure に接続する

```
Connect-AzAccount
Install-Module -Name Az

```
最新バージョンの PowerShellGet がインストールされていることを確認します。 次のコマンドを実行して、PowerShell を再度開きます。
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Az.FrontDoor モジュールをインストールする 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure で、関連するリソースをリソース グループに割り当てます。 ここでは、[New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) を使用してリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>カスタム応答が設定された新しい WAF ポリシーの作成 

[New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用して、カスタム応答の状態コードが 405、メッセージが "**You are blocked**" に設定された新しい WAF ポリシーを作成する例を次に示します

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

[Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy) を使用して、既存の WAF ポリシーのカスタム応答コードまたは応答本文の設定を変更します。

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>次のステップ
- [Azure Front Door での Web アプリケーション ファイアウォール](../afds/afds-overview.md)について詳しく確認します