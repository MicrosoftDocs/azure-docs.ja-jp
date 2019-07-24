---
title: Azure PowerShell スクリプト サンプル - ロード バランサーのアプリケーション ポートを開く | Microsoft Docs
description: Azure PowerShell スクリプト サンプル - Service Fabric アプリケーションのために Azure ロード バランサーのポートを開きます。
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 942dd995651f4c672d50163e5c380b62155b2227
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667261"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Azure ロード バランサーのアプリケーション ポートを開く

Azure で実行する Service Fabric アプリケーションは、Azure ロード バランサー上にあります。 このサンプル スクリプトは、Azure ロード バランサーのポートを開いて、Service Fabric アプリケーションが外部クライアントと通信できるようにします。 必要に応じてパラメーターをカスタマイズします。 クラスターがネットワーク セキュリティ グループに含まれている場合は、受信トラフィックを許可する[受信ネットワーク セキュリティ グループ ルールを追加](service-fabric-powershell-add-nsg-rule.md)します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要に応じて、Service Fabric PowerShell モジュールを、[Service Fabric SDK](../service-fabric-get-started.md) と共にインストールします。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Azure リソースを取得します。  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Azure ロード バランサーを取得します。 |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | プローブの構成をロード バランサーに追加します。|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | ロード バランサーのプローブの構成を取得します。 |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | 規則の構成をロード バランサーに追加します。 |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | ロード バランサーの目標状態を設定します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure Service Fabric 用 PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
