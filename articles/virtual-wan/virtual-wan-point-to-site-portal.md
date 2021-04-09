---
title: チュートリアル:Azure Virtual WAN を使用し、Azure へのポイント対サイト接続を作成する
description: このチュートリアルでは、Azure Virtual WAN を使用して Azure へのポイント対サイト VPN 接続を作成する方法を学習します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: db7345906605ce117f0d57deb80f9d26ebf84179
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102430525"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>チュートリアル:Azure Virtual WAN を使用してユーザー VPN 接続を作成する

このチュートリアルでは、Virtual WAN を使用して IPsec/IKE (IKEv2) または OpenVPN VPN 接続経由で Azure のリソースに接続する方法を示します。 この種類の接続を使うには、クライアント コンピューター上で VPN クライアントを構成する必要があります。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想 WAN を作成する
> * P2S の構成を作成する
> * 仮想ハブを作成する
> * クライアント アドレス プールを選択する
> * DNS サーバーの指定
> * VPN クライアント プロファイル構成パッケージを生成する
> * VPN クライアントの構成
> * 仮想 WAN を表示する

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>仮想 WAN を作成する

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>P2S の構成を作成する

ポイント対サイト (P2S) 構成では、リモート クライアントを接続するためのパラメーターを定義します。

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>仮想ハブとゲートウェイを作成する

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="choose-p2s-client-address-pools"></a><a name="chooseclientpools"></a>P2S クライアント アドレス プールを選択する

[!INCLUDE [Choose pools](../../includes/virtual-wan-allocating-p2s-pools.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>DNS サーバーを指定する

この設定はハブの作成時に構成するか、後で変更することができます。 変更するには、仮想ハブを見つけます。 **[ユーザー VPN (ポイントからサイトへ)]** で **[構成]** を選択し、 **[Custom DNS Servers]\(カスタム DNS サーバー\)** ボックスに DNS サーバーの IP アドレスを入力します。 最大 5 個の DNS サーバーを指定することができます。

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="カスタム DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>VPN クライアント プロファイル パッケージを生成する

VPN クライアント プロファイル パッケージを生成してダウンロードし、VPN クライアントを構成します。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>VPN クライアントの構成

ダウンロードしたプロファイル パッケージを使用して、リモート アクセス VPN クライアントを構成します。 オペレーティング システムごとに手順は異なります。 お使いシステムに適用される指示に従ってください。
クライアントの構成が完了したら、接続できます。

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>仮想 WAN を表示する

1. 仮想 WAN に移動します。
1. **[概要]** ページで、マップ上の各ポイントはハブを表します。
1. **[ハブと接続]** セクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、入出力バイト数を確認できます。

## <a name="clean-up-resources"></a><a name="cleanup"></a>リソースをクリーンアップする

作成したリソースは、不要になったら削除してください。 Virtual WAN リソースのいくつかは、依存関係に応じた特定の順序で削除する必要があります。 削除が完了するまでに 30 分程度かかる場合があります。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>次のステップ

次に、Virtual WAN の詳細については下記を参照してください。

> [!div class="nextstepaction"]
> * [Virtual WAN の FAQ](virtual-wan-faq.md)
