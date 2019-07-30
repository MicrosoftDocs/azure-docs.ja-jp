---
title: Azure Virtual WAN を使用して Azure 環境とオンプレミス環境への ExpressRoute 接続を作成する | Microsoft Docs
description: このチュートリアルでは、Azure Virtual WAN を使用して、Azure 環境とオンプレミス環境への ExpressRoute 接続を作成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: edf5e04b7cf9b5c79666c54fbeca49858cf21079
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077502"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>チュートリアル:Azure Virtual WAN を使用して ExpressRoute の関連付けを作成する (プレビュー)

このチュートリアルでは、Virtual WAN で ExpressRoute 回線と関連付けを使用して Azure リソースに接続する方法を示します。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * vWAN を作成する
> * ハブを作成する
> * 回線を検索してハブに関連付ける
> * 回線をハブを関連付ける
> * VNet をハブに接続する
> * 仮想 WAN を表示する
> * リソースの正常性を表示する
> * 接続を監視する

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>この機能を登録する

Virtual WAN を構成する前に、まずプレビューにサブスクリプションを登録する必要があります。 これを行わないと、ポータルで Virtual WAN を使用できません。 登録を行うには、サブスクリプション ID を記載したメールを **azurevirtualwan\@microsoft.com** までお送りください。 サブスクリプションが登録されると、メールが届きます。

**プレビュー考慮事項:**

  * [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported) がサポートされている国/地域では、ExpressRoute 回線を有効にする必要があります。
  * Virtual WAN ハブに接続するには、ExpressRoute 回線は Premium 回線でなければなりません。 

## <a name="vnet"></a>1.仮想ネットワークの作成

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2.仮想 WAN を作成する

ブラウザーから、[Azure Portal (プレビュー)](https://aka.ms/azurevirtualwanpreviewfeatures) に移動し、Azure アカウントでサインインします。

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>[作業の開始] ページ

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3.ハブを作成する

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4.回線を検索してハブに関連付ける

1. vWAN を選択し、 **[仮想 WAN のアーキテクチャ]** で **[ExpressRoute 回線]** を選択します。
1. ExpressRoute 回線が vWAN と同じサブスクリプション内にある場合は、お使いのサブスクリプションで **[ExpressRoute 回線の選択]** をクリックします。 
1. プルダウンを使用して、ハブに関連付ける ExpressRoute を選択します。
1. ExpressRoute 回線が同じサブスクリプション内に存在しない場合、または[認証キーとピア ID](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md) が提供されている場合は、 **[Find a circuit redeeming an authorization key]\(認可キーを利用する回線の検出\)** を選択します
1. 次の詳細を入力します。
1. **承認キー** - 前述のように、回線の所有者によって生成されます。
1. **ピア回線 URI** - 回線の所有者によって指定された回線 URI であり、回線の一意の識別子。
1. **ルーティングの重み** - [ルーティングの重み](../expressroute/expressroute-optimize-routing.md)によって、異なるピアリング場所から複数の回線が同じハブに接続される場合に、特定のパスを優先させることができます
1. **[Find circuit]\(回線の検索\)** をクリックし、回線を選択します (見つかった場合)。
1. ドロップダウンから 1 つ以上のハブを選択し、 **[保存]** をクリックします。

## <a name="vnet"></a>5.VNet をハブに接続する

この手順では、ハブと VNet の間にピアリング接続を作成します。 接続する VNet ごとにこれらの手順を繰り返します。

1. 仮想 WAN のページで、 **[仮想ネットワーク接続]** をクリックします。
2. 仮想ネットワーク接続のページで、 **[+ 接続の追加]** をクリックします。
3. **[接続の追加]** ページで、次のフィールドに入力します。

    * **[接続名]** - 接続に名前を付けます。
    * **[ハブ]** - この接続に関連付けるハブを選択します。
    * **[サブスクリプション]** - サブスクリプションを確認します。
    * **[仮想ネットワーク]** - このハブに接続する仮想ネットワークを選択します。 仮想ネットワークに既存の仮想ネットワーク ゲートウェイを設定することはできません。


## <a name="viewwan"></a>6.仮想 WAN を表示する

1. 仮想 WAN に移動します。
2. [概要] ページのマップ上の各ポイントは、ハブを表します。 任意のポイントにカーソルを置くと、ハブの正常性の概要が表示されます。
3. ハブと接続のセクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、および入出力バイト数を表示できます。

## <a name="viewhealth"></a>7.リソースの正常性を表示する

1. WAN に移動します。
2. WAN のページの **[サポート + トラブルシューティング]** セクションで、 **[正常性]** をクリックしてリソースを表示します。

## <a name="connectmon"></a>8.接続を監視する

Azure VM とリモート サイト間の通信を監視するための接続を作成します。 接続モニターを設定する方法については、[ネットワーク通信の監視](~/articles/network-watcher/connection-monitor.md)に関するページを参照してください。 ソース フィールドは Azure の VM IP で、宛先 IP はサイト IP です。

## <a name="cleanup"></a>9.リソースのクリーンアップ

これらのリソースが不要になったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * vWAN を作成する
> * ハブを作成する
> * 回線を検索してハブに関連付ける
> * 回線をハブを関連付ける
> * VNet をハブに接続する
> * 仮想 WAN を表示する
> * リソースの正常性を表示する
> * 接続を監視する

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
