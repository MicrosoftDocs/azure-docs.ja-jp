---
title: Azure PowerShell のサンプル スクリプト - RDP ポート範囲の変更 | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - デプロイしたクラスターの RDP ポート範囲を変更します。
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.custom: sfrev
ms.openlocfilehash: 5cc72423d7e901f82cdf7e40f5de72bd54e9cec1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076171"
---
# <a name="update-the-rdp-port-range-values"></a>RDP ポート範囲の値の更新

このサンプル スクリプトは、クラスターをデプロイした後に、クラスター ノードの VM の RDP ポート範囲の値を変更します。  基になる VM が循環しないように、Azure PowerShell を使用します。  スクリプトはクラスターのリソース グループ内で `Microsoft.Network/loadBalancers` リソースを取得し、`inboundNatPools.frontendPortRangeStart` と `inboundNatPools.frontendPortRangeEnd` の値を更新します。 必要に応じてパラメーターをカスタマイズします。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/)の手順に従って Azure PowerShell をインストールします。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | `Microsoft.Network/loadBalancers` リソースを取得します。 |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|`Microsoft.Network/loadBalancers` リソースを更新します。|

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の Azure Service Fabric 用 Azure PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
