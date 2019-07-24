---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c1b5560e16b68565c37365ac9c2cba217d9b1b90
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444230"
---
接続が成功したことを確認するには、"Get-AzureVNetConnection" コマンドレットを使用します。

1. 次のコマンドレットを使用します。値は実際の値に置き換えてください。 仮想ネットワークの名前にスペースが含まれる場合は、名前を引用符で囲む必要があります。

   ```azurepowershell
   Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
   ```
2. コマンドレットの実行後、値を確認します。 以下の例では、接続状態は "Connected" と表示されており、受信バイトと送信バイトを確認できます。

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal