---
title: Azure VPN Gateway:P2S VPN クライアント用のカスタム ルートをアドバタイズする
description: ポイント対サイト クライアントにカスタム ルートをアドバタイズするための手順
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: a02bd5519b776a063646c11be2a34366fe429f99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89392393"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>P2S VPN クライアント用のカスタム ルートをアドバタイズする

すべてのポイント対サイト VPN クライアントへのカスタム ルートをアドバタイズできます。 たとえば、VNet でストレージ エンドポイントを有効にしているときに、リモート ユーザーが VPN 接続経由でこれらのストレージ アカウントにアクセスできるようにすることができます。 ストレージ エンドポイントの IP アドレスをすべてのリモート ユーザーにアドバタイズして、ストレージ アカウントへのトラフィックをパブリック インターネットではなく VPN トンネル経由にすることができます。

![Azure VPN Gateway マルチサイト接続の例](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>カスタム ルートをアドバタイズするには

カスタム ルートをアドバタイズするには、`Set-AzVirtualNetworkGateway cmdlet` を使用します。 次の例では、[Contoso ストレージ アカウント テーブル](https://contoso.table.core.windows.net)の IP をアドバタイズする方法を示します。

1. *contoso.table.core.windows.net* を ping し、IP アドレスをメモします。 次に例を示します。

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 次の PowerShell コマンドを実行します。

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 複数のカスタム ルートを追加するには、コンマとスペースを使用してアドレスを区切ります。 次に例を示します。

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>カスタム ルートを表示するには

カスタム ルートを表示するには、次の例を使用します。

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>カスタム ルートを削除するには

カスタム ルートを削除するには、次の例を使用します。

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>次のステップ

その他の P2S ルーティング情報については、「[ポイント対サイト VPN ルーティングについて](vpn-gateway-about-point-to-site-routing.md)」を参照してください。
