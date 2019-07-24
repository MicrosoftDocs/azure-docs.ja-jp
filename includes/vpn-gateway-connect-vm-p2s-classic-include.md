---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6d0737a7300b2a6025f776c1ed65a05cacf2141a
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323697"
---
VNet にデプロイされた VM に接続するためのリモート デスクトップ接続を作成します。 VM に接続できることを確認する最善の方法は、その VM のコンピューター名ではなく、プライベート IP アドレスで接続してみることです。 この方法であれば、名前の解決が適切に構成されているかではなく、VM に接続できるかどうかをテストすることができます。 

1. ご利用の VM のプライベート IP アドレスを特定します。 VM のプライベート IP アドレスを確認するには、Azure portal で VM のプロパティを表示するか、PowerShell を使用します。
2. ポイント対サイト VPN 接続を使って VNet に接続していることを確認します。 
3. リモート デスクトップ接続を開くには、タスク バーの検索ボックスに「*RDP*」または「*リモート デスクトップ接続*」と入力し、**[リモート デスクトップ接続]** を選択します。 このほか、PowerShell で "**mstsc**" コマンドを使って開くこともできます。 
3. **リモート デスクトップ接続**で、VM のプライベート IP アドレスを入力します。 必要に応じて **[オプションの表示]** を選択し、追加の設定を行ってから接続します。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>VM に対する RDP 接続をトラブルシューティングするには

VPN 接続を使って仮想マシンに接続する際に問題が発生した場合には、いくつかの点を確認する必要があります。 

- VPN 接続が成功したことを確認します。
- VM のプライベート IP アドレスに接続していることを確認します。
- 「**ipconfig**」と入力して、接続元のコンピューターのイーサネット アダプターに割り当てられている IPv4 アドレスをチェックします。 その IP アドレスが、接続先の VNet のアドレス範囲内または VPNClientAddressPool のアドレス範囲内にある場合、アドレス空間の重複が起こります。 アドレス空間がこのように重複していると、ネットワーク トラフィックが Azure に到達せずローカル ネットワーク上に留まることになります。
- プライベート IP アドレスを使って VM に接続できるものの、コンピューター名では接続できない場合には、DNS が正しく構成されているかどうかを確認します。 VM の名前解決の動作について詳しくは、[VM の名前解決](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)に関するページを参照してください。
- VNet に対して DNS サーバーの IP アドレスを指定した後に VPN クライアント構成パッケージが生成されたことを確認します。 DNS サーバーの IP アドレスを更新した場合は、新しい VPN クライアント構成パッケージを生成してインストールしてください。

トラブルシューティングの詳細については、[VM へのリモート デスクトップ接続のトラブルシューティング](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)に関するページを参照してください。