---
title: Azure PowerShell スクリプト サンプル - Web トラフィックの制限 | Microsoft Docs
description: Azure PowerShell スクリプトのサンプル - OWASP 規則を使用してトラフィックを制限する Web アプリケーション ファイアウォールと仮想マシン スケール セットがあるアプリケーション ゲートウェイを作成します。
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: f20e5271cdca18ad40710e5defdf8175754ef891
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078679"
---
# <a name="restrict-web-traffic-using-azure-powershell"></a>Azure PowerShell を使用して Web トラフィックを制限する

このスクリプトでは、バックエンド サーバーに仮想マシン スケール セットを使用する Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成します。 Web アプリケーション ファイアウォールは、OWASP 規則に基づいて Web トラフィックを制限します。 スクリプトを実行したら、パブリック IP アドレスを使用してアプリケーション ゲートウェイをテストできます。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss-waf.ps1 "Create application gateway with WAF")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、アプリケーション ゲートウェイ、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
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
| [New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/new-azapplicationgatewaywebapplicationfirewallconfiguration) | Web アプリケーション ファイアウォール構成を作成します。 |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | アプリケーション ゲートウェイを作成します。 |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | スケール セットのストレージ プロファイルを作成します。 |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | スケール セットのオペレーティング システムを定義します。 |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | スケール セットのネットワーク インターフェイスを定義します。 |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | 仮想マシン スケール セットを作成します。 |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | ストレージ アカウントを作成します。 |
| [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) | データを記録する診断を構成します。 |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | アプリケーション ゲートウェイのパブリック IP アドレスを取得します。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 | 
## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

追加のアプリケーション ゲートウェイの PowerShell スクリプトのサンプルは、[Azure Application Gateway のドキュメント](../powershell-samples.md)にあります。
