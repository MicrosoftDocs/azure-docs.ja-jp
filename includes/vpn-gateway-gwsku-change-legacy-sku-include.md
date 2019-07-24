---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908690"
---
Resource Manager デプロイ モデルを使用している場合は、新しいゲートウェイ SKU に変更することができます。 従来のゲートウェイ SKU から新しい SKU に変更するときに、既存の VPN ゲートウェイを削除し、新しい VPN ゲートウェイを作成します。

ワークフロー:

1. 仮想ネットワーク ゲートウェイに対する接続を削除します。
2. 古い VPN ゲートウェイを削除します。
3. 新しい VPN ゲートウェイを作成します。
4. (サイト間接続の) 新しい VPN ゲートウェイの IP アドレスでオンプレミス VPN デバイスを更新します。
5. このゲートウェイに接続する VNet 間ローカル ネットワーク ゲートウェイのゲートウェイ IP アドレスの値を更新します。
6. この VPN ゲートウェイを介して仮想ネットワークに接続している P2S クライアント用に新しいクライアント VPN 構成パッケージをダウンロードします。
7. 仮想ネットワーク ゲートウェイとの接続を作成し直します。

考慮事項:

* 新しい SKU に移行するには、ご使用の VPN ゲートウェイが Resource Manager デプロイ モデルでなければなりません。
* クラシック VPN ゲートウェイを使用している場合は、引き続きそのゲートウェイ用の古いレガシ SKU を使用する必要がありますが、レガシ SKU の間でサイズ変更することはできます。 新しい SKU に変更することはできません。
* レガシ SKU から新しい SKU に変更した場合は、接続のダウンタイムが発生します。
* 新しいゲートウェイ SKU に変更すると、使用している VPN ゲートウェイのパブリック IP アドレスが変わります。 これは、以前に使用したのと同じパブリック IP アドレス オブジェクトを指定した場合でも発生します。