---
title: ロード バランサーの TCP リセットおよびアイドル タイムアウトを構成する
titleSuffix: Azure Load Balancer
description: この記事では Azure Load Balancer TCP アイドル タイムアウトとリセットを構成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747198"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>Azure Load Balancer の TCP リセットとアイドル タイムアウトを構成する

Azure Load Balancer のアイドルには、次のタイムアウト範囲があります。

* 送信規則の場合は、4 分から 100 分
* Load Balancer 規則と受信 NAT 規則の場合は、4 分から 30 分

既定では 4 分に設定されています。 アイドル時間がタイムアウト値よりも長い場合、クライアントとサービス間の TCP または HTTP セッションが維持されるという保証はありません。 

次のセクションでは、ロード バランサー リソースのアイドル タイムアウトと TCP リセット設定を変更する方法について説明します。

## <a name="set-tcp-reset-and-idle-timeout"></a>TCP リセットとアイドル タイムアウトを設定する
---
# <a name="portal"></a>[**ポータル**](#tab/tcp-reset-idle-portal)

ロード バランサーのアイドル タイムアウトと TCP リセットを設定するには、負荷分散規則を編集します。 

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューで、 **[リソース グループ]** を選択します。

3. ロード バランサーのリソース グループを選択します。 この例では、リソース グループの名前は **myResourceGroup** です。

4. ロード バランサーを選択します。 この例では、ロード バランサーの名前は **myLoadBalancer** です。

5. **[設定]** で、 **[負荷分散規則]** を選択します。

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="負荷分散規則を編集します。" border="true":::

6. 負荷分散規則を選択します。 この例では、負荷分散規則の名前は **myLBrule** になります。

7. 負荷分散規則で、 **[アイドル タイムアウト (分)]** のスライダーをタイムアウト値に移動します。  

8. **[TCP リセット]** で、 **[有効]** を選択します。

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="アイドル タイムアウトと TCP リセットを設定します。" border="true":::

9. **[保存]** を選択します。

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

アイドル タイムアウトと TCP リセットを設定するには、次の負荷分散規則パラメーターの値を [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) に設定します。

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

次の例をリソースの値に置き換えます。

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Azure CLI**](#tab/tcp-reset-idle-cli)

アイドル タイムアウトと TCP リセットを設定するには、[az network lb rule update](/cli/azure/network/lb/rule?az_network_lb_rule_update) に次のパラメーターを使用します。

* **--idle-timeout**
* **--enable-tcp-reset**

開始する前に、環境を検証します。

* Azure portal にサインインし、`az login` を実行して、ご利用のサブスクリプションがアクティブであることを確認します。
* `az --version` を実行して、ターミナルまたはコマンド ウィンドウの Azure CLI のバージョンを確認します。 最新バージョンについては、[最新のリリース ノート](/cli/azure/release-notes-azure-cli?tabs=azure-cli)を参照してください。
  * 最新バージョンを使用していない場合は、[オペレーティング システムまたはプラットフォーム用のインストール ガイド](/cli/azure/install-azure-cli)に従ってインストールを更新します。

次の例をリソースの値に置き換えます。

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>次のステップ

TCP アイドル タイムアウトとリセットの詳細については、「[Load Balancer の TCP リセットおよびアイドルのタイムアウト](load-balancer-tcp-reset.md)」を参照してください。

ロード バランサー分散モードの構成の詳細については、[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)に関するページを参照してください。
