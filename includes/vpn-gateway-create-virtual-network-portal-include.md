---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b585a47d3950d232eb3e8047c12ee8949030c95
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780217"
---
Azure Portal を使用して Resource Manager デプロイ モデルで VNet を作成するには、次の手順に従います。 これらの手順をチュートリアルとして使用する場合は、**例として示されている値**を使用してください。 これらの手順をチュートリアルとして使用しない場合は、必ず独自の値に置き換えてください。 仮想ネットワークの操作の詳細については、「 [仮想ネットワークの概要](../articles/virtual-network/virtual-networks-overview.md)」を参照してください。

>[!NOTE]
>この VNet をオンプレミスの場所に接続するには、オンプレミスのネットワーク管理者と調整を行って、この仮想ネットワーク専用に使用できる IP アドレスの範囲を見つけ出す必要があります。 VPN 接続の両側に重複するアドレス範囲が存在する場合、トラフィックが期待どおりにルーティングされない可能性があります。 また、この VNet を別の VNet に接続する場合、アドレス空間を他の VNet と重複させることはできません。 したがって、慎重にネットワーク構成を計画してください。
>

1. ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。
2. **[リソースの作成]** をクリックします。 **[Marketplace を検索]** フィールドに「仮想ネットワーク」と入力します。 検索結果の一覧から **[仮想ネットワーク]** を探してクリックし、 **[仮想ネットワーク]** ページを開きます。
3. **Create** をクリックしてください。 **[仮想ネットワークの作成]** ページが開きます。
4. **[仮想ネットワークの作成]** ページで、VNet の設定を構成します。 フィールドへの入力時、入力された文字が有効であれば、赤色の感嘆符が緑色のチェック マークに変わります。 次の値を使用します。

   - **[名前]** :VNet1
   - **[アドレス空間]** : 10.1.0.0/16
   - **サブスクリプション**:一覧表示されているサブスクリプションが、使用するものであることを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。
   - **[リソース グループ]** :TestRG1 ( **[新規作成]** をクリックして新しいグループを作成します)
   - **[場所]** :East US
   - **サブネット**:フロントエンド
   - **アドレス範囲**:10.1.0.0/24

   ![[仮想ネットワークの作成] ページ](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. DDoS は Basic、サービス エンドポイントは無効、ファイアウォールを無効のままにします。
6. **[作成]** をクリックして VNet を作成します。