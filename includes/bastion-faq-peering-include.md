---
title: インクルード ファイル
description: インクルード ファイル
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94356654"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>ピアリングされた仮想ネットワークで複数の Bastion ホストをデプロイすることはできますか?

はい。 既定では、ユーザーには、VM が存在している同じ仮想ネットワークにデプロイされている Bastion ホストが表示されます。 ただし、 **[接続]** メニューには、ピアリングされたネットワークで検出された複数の Bastion ホストが表示されます。 仮想ネットワークにデプロイされている VM への接続に使用する Bastion ホストを選択できます。

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>ピアリングされた VNet が別のサブスクリプションにデプロイされている場合、Bastion 経由の接続は機能しますか?

はい、Bastion 経由の接続は、1 つのテナントの異なるサブスクリプション間でピアリングされた Vnet に対して引き続き機能します。 2 つの異なるテナントにまたがるサブスクリプションはサポートされていません。 **[接続]** ドロップダウン メニューにある Bastion を表示するには、 **[サブスクリプション] > [グローバル サブスクリプション]** で、アクセス権を持つサブスクリプションを選択する必要があります。

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="グローバル サブスクリプション フィルター" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>ピアリングされた VNet へのアクセス権を持っているにも関わらず、デプロイされている VM が表示されません。

ユーザーが VM とピアリングされた VNet の両方への **読み取り** アクセスを持っていることを確認します。 さらに、ユーザーが次のリソースへの **読み取り** アクセスを持っていることを IAM で確認します。

* 仮想マシンに対する閲覧者ロール。
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。
* Azure Bastion リソースに対する閲覧者ロール。
* Virtual Network の閲覧者ロール (ピアリングされた仮想ネットワークがない場合は不要)。

|アクセス許可|説明|アクセス許可の種類|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |踏み台ホストを取得します|アクション|
|Microsoft.Network/virtualNetworks/BastionHosts/action |仮想ネットワーク内の踏み台ホストの参照を取得します。|アクション|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|仮想ネットワーク内の踏み台ホストの参照を取得します。|アクション|
|Microsoft.Network/networkInterfaces/read|ネットワーク インターフェイスの定義を取得します。|アクション|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|ネットワーク インターフェイスの IP 構成定義を取得します。|アクション|
|Microsoft.Network/virtualNetworks/read|仮想ネットワークの定義を取得します。|アクション|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|仮想ネットワーク サブネットのすべての仮想マシンへの参照を取得します。|アクション|
|Microsoft.Network/virtualNetworks/virtualMachines/read|仮想ネットワークのすべての仮想マシンへの参照を取得します。|アクション|