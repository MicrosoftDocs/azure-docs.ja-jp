---
title: '障害が発生した回線をリセットする - ExpressRoute: PowerShell:Azure | Microsoft Docs'
description: この記事は、障害状態になっている ExpressRoute 回線をリセットする際に役立ちます。
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: 0332f189feb1b266eac29c9ea8a7f3ed86d6207a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116573"
---
# <a name="reset-a-failed-expressroute-circuit"></a>障害が発生した ExpressRoute 回線のリセット

ExpressRoute 回線での操作が正常に完了しないと、回線が "障害" 状態になることがあります。 この記事は、障害状態の ExpressRoute 回線をリセットする際に役立ちます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

## <a name="next-steps"></a>次の手順

問題が解消しない場合は [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) にサポート チケットを申請する
