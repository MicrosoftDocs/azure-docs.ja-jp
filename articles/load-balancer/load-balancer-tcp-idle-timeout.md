---
title: Azure でロード バランサーの TCP アイドル タイムアウトを構成する
titleSuffix: Azure Load Balancer
description: この記事では Azure Load Balancer TCP アイドル タイムアウトを構成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 317f6a73812b0e4284564ca9b5593e09e22edf12
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048722"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Azure Load Balancer の TCP アイドル タイムアウト設定を構成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="tcp-idle-timeout"></a>TCP アイドル タイムアウト
Azure Load Balancer では、アイドル タイムアウトは 4 分から 30 分に設定されています。 既定では 4 分に設定されています。 アイドル時間がタイムアウト値よりも長い場合、クライアントとクラウド サービス間の TCP または HTTP セッションが維持されるという保証はありません。

接続が閉じられると、クライアント アプリケーションは、次のエラー メッセージを受信する場合があります。"基になる接続が閉じられました: 維持される必要があった接続が、サーバーによって切断されました。"

一般的な方法として、TCP keep-alive を使用します。 この方法を使用すると、接続が長時間アクティブ状態に維持されます。 詳細については、こちらの [.NET の例](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)をご覧ください。 keep-alive を有効にすると、接続のアイドル時間にパケットが送信されます。 keep-alive パケットにより、アイドル タイムアウト値に達することがなくなり、接続が長時間維持されます。

設定は、着信接続に対してのみ有効です。 接続の切断を避けるためには、アイドル タイムアウト設定よりも小さい間隔で、TCP keep-alive を構成するか、アイドル タイムアウト値を大きくします。 これらのシナリオをサポートするために、構成可能なアイドル タイムアウトのサポートが追加されました。

TCP keep-alive は、バッテリーの寿命に制約がないシナリオに適しています。 モバイル アプリケーションでは推奨されません。 モバイル アプリケーションで TCP keep-alive を使用すると、デバイスのバッテリーの消耗を速める可能性があります。

![TCP タイムアウト](./media/load-balancer-tcp-idle-timeout/image1.png)

次のセクションでは、パブリック IP とロード バランサー リソースのアイドル タイムアウト設定を変更する方法について説明します。

>[!NOTE]
> TCP アイドル タイムアウトは、UDP プロトコルの負荷分散規則には影響しません。


## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>インスタンスレベル パブリック IP の TCP タイムアウトを 15 分で構成します。

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` はオプションです。 設定しない場合、既定のタイムアウト時間は 4 分です。 設定できるタイムアウトの範囲は 4 ～ 30 分です。

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>負荷分散された規則での TCP タイムアウトを 15 分に設定する

ロード バランサーにアイドル タイムアウトを設定するために、'IdleTimeoutInMinutes' は負荷分散規則された規則に設定されています。 次に例を示します。

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>次のステップ

[内部ロード バランサーの概要](load-balancer-internal-overview.md)

[インターネットに接続するロード バランサーの構成の開始](quickstart-load-balancer-standard-public-powershell.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)
