---
title: WAF カスタム ルールを使用する Azure PowerShell のサンプル スクリプト
description: Azure PowerShell のサンプル スクリプト - Application Gateway のカスタム ルールに基づいて Web アプリケーション ファイアウォールを作成する
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8d800e77ed0e609091d31d9b3177af447ac06f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079508"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Azure PowerShell での WAF カスタム ルールの作成

このスクリプトは、カスタム ルールを使用する Application Gateway の Web アプリケーション ファイアウォールを作成します。 要求ヘッダーに User-Agent *evilbot* が含まれている場合、カスタム規則はトラフィックをブロックします。

## <a name="prerequisites"></a>前提条件

### <a name="azure-powershell-module"></a>Azure PowerShell モジュール

Azure PowerShell をローカルにインストールして使用する場合、このスクリプトでは Azure PowerShell モジュール バージョン 2.1.0 以降が必要になります。

1. バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。
2. Azure との接続を作成するには、`Connect-AzAccount` を実行します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、アプリケーション ゲートウェイ、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | サブネット構成を作成します。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | サブネット構成を使用して仮想ネットワークを作成します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | アプリケーション ゲートウェイのパブリック IP アドレスを作成します。 |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | サブネットをアプリケーション ゲートウェイと関連付ける構成を作成します。 |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | アプリケーション ゲートウェイにパブリック IP アドレスを割り当てる構成を作成します。 |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | アプリケーション ゲートウェイにアクセスするために使用するポートを割り当てます。 |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | アプリケーション ゲートウェイのバックエンド プールを作成します。 |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | バックエンド プールの設定を構成します。 |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | リスナーを作成します。 |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | ルーティング規則を作成します。 |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | アプリケーション ゲートウェイの階層と容量を指定します。 |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | アプリケーション ゲートウェイを作成します。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|アプリケーション ゲートウェイに自動スケーリング構成を作成します。|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|ファイアウォールの条件用の一致変数を作成します。|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|カスタム ルール用の一致条件を作成します。|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|アプリケーション ゲートウェイのファイアウォール ポリシー用の新しいカスタム ルールを作成します。|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|アプリケーション ゲートウェイのファイアウォール ポリシーを作成します。|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|アプリケーション ゲートウェイの WAF 構成を作成します。|

## <a name="next-steps"></a>次のステップ

- WAF のカスタム ルールの詳細については、[Web アプリケーション ファイアウォールのカスタム ルール](../ag/custom-waf-rules-overview.md)に関するページを参照してください。
- Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。
