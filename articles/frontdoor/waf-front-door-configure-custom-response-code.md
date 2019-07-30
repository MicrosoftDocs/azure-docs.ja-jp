---
title: Azure Front Door で Web アプリケーション ファイアウォールに対するカスタム応答を構成する
description: Web アプリケーション ファイアウォール (WAF) が要求をブロックした場合のカスタム応答コードとメッセージを構成する方法について説明します。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 657dc3a43302d16bc403d790bf2c34c2d147dd6c
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846368"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Web アプリケーション ファイアウォールに対するカスタム応答を構成する

既定では、Azure Front Door を使用した Azure Web アプリケーション ファイアウォール (WAF) がルールの一致により要求をブロックした場合、403 状態コードが "**The request is blocked**" というメッセージと共に返されます。 この記事では、要求が WAF でブロックされた場合のカスタム応答の状態コードと応答メッセージを構成する方法について説明します。

## <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースの管理に [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) モデルを使う一連のコマンドレットが用意されています。 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 リンク先のページの手順に従って Azure の資格情報でサインインし、Az PowerShell モジュールをインストールします。

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

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure で、関連するリソースをリソース グループに割り当てます。 この例では、[New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) を使用してリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>カスタム応答が設定された新しい WAF ポリシーの作成 

カスタム応答の状態コードが 405、メッセージが "**You are blocked**" に設定されている新しい WAF ポリシーを作成する例を次に示します。 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用します。

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
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>次の手順
- [Front Door](front-door-overview.md) の詳細を確認する