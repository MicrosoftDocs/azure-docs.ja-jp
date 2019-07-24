---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109242"
---
VNet は、Resource Manager デプロイ モデルと Azure portal を使用し、以下の手順に従って作成できます。 仮想ネットワークの詳細については、[Virtual Network の概要](../articles/virtual-network/virtual-networks-overview.md)に関するページを参照してください。

>[!NOTE]
>この VNet をオンプレミスの場所に接続するには、オンプレミスのネットワーク管理者と調整を行って、この仮想ネットワーク専用に使用できる IP アドレスの範囲を見つけ出します。 VPN 接続の両側に重複するアドレス範囲が存在する場合、予期しない方法でトラフィックがルーティングされます。 また、この VNet を別の VNet に接続する場合、アドレス空間を他の VNet と重複させることはできません。 この点を踏まえてネットワーク構成を計画してください。
>
>

1. [Azure portal](http://portal.azure.com) にサインインして **[リソースの作成]** を選択します。 **[新規]** ページが開きます。

2. **[Marketplace を検索]** フィールドに「*仮想ネットワーク*」と入力して、返されたリストから **[仮想ネットワーク]** を選択します。 **[仮想ネットワーク]** ウィンドウが開きます。

   ![仮想ネットワーク リソース ページの検索](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "仮想ネットワーク リソース ページの検索")

3. このページの下の方にある **[デプロイ モデルの選択]** の一覧で、**[リソース マネージャー]** を選択し、**[作成]** を選択します。 **[仮想ネットワークの作成]** ページが開きます。

   ![[仮想ネットワークの作成] ページ](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "[仮想ネットワークの作成] ページ")

4. **[仮想ネットワークの作成]** ページで、VNet の設定を構成します。 フィールドへの入力時、入力された文字の有効性が確認されれば、赤色の感嘆符が緑色のチェック マークに変わります。 いくつかの値は自動入力されます。実際の値に置き換えてください。

   - **[名前]**: 仮想ネットワークの名前を入力します。

   - **[アドレス空間]**: アドレス空間を入力します。 追加するアドレス空間が複数ある場合、ここには、1 つ目のアドレス空間を入力してください。 その他のアドレス空間は後で、VNet を作成した後に追加できます。

   - **サブスクリプション**:一覧表示されているサブスクリプションが正しいことを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。

   - **[リソース グループ]**: 既存のリソース グループを選択するか、新しいリソース グループの名前を入力して新しく作成します。 新しいグループを作成する場合は、計画した構成値に基づいて、リソース グループに名前を付けます。 リソース グループの詳細については、「[Azure Resource Manager の概要](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)」を参照してください。

   - **[場所]**: VNet の場所を選択します。 この場所の設定によって、この VNet にデプロイしたリソースの配置先が決まります。

   - **[サブネット]**: サブネットの**名前**と**アドレス範囲**を追加します。 その他のサブネットは後で、VNet を作成した後に追加できます。 
     
5. **作成**を選択します。
