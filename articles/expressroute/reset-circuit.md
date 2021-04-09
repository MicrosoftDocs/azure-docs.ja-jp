---
title: '障害が発生した回線をリセットする - ExpressRoute: PowerShell:Azure | Microsoft Docs'
description: この記事は、障害状態になっている ExpressRoute 回線をリセットする際に役立ちます。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 84c8275b7a7257530a735e8612047ef42e2f8a7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011341"
---
# <a name="reset-a-failed-expressroute-circuit"></a>障害が発生した ExpressRoute 回線のリセット

ExpressRoute 回線での操作が正常に完了しないと、回線が "障害" 状態になることがあります。 この記事は、障害状態の ExpressRoute 回線をリセットする際に役立ちます。

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>回線のリセット

1. Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 詳細については、[Azure PowerShell のインストールおよび構成](/powershell/azure/install-az-ps)をご覧ください。

2. 昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. 複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. 使用するサブスクリプションを指定します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. 次のコマンドを実行して、障害状態の回線をリセットします。

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

回線はすぐに正常になります。 回線が障害状態のままである場合は、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に対してサポート チケットを申請します。

## <a name="next-steps"></a>次のステップ

それでも問題が解決されない場合は、[Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)でサポート チケットを開いてください。
