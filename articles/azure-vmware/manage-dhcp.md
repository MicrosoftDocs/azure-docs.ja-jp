---
title: DHCP を作成して管理する方法
description: この記事では、Azure VMware Solution で DHCP を管理する方法について説明します。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 3fc3de228179925afdf1b7c1015c577fd9c4c924
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752162"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Azure VMWare Solution で DHCP を作成して管理する方法

NSX-T には、プライベート クラウドに DHCP を構成する機能が用意されています。 NSX-T を使用して DHCP サーバーをホストする予定の場合は、「[DHCP サーバーの作成](#create-dhcp-server)」を参照してください。 あるいは、ネットワークにサード パーティの外部 DHCP サーバーがあり、その DHCP サーバーに要求をリレーする場合は、「[DHCP リレー サービスの作成](#create-dhcp-relay-service)」を参照してください。

## <a name="create-dhcp-server"></a>DHCP サーバーの作成

以下の手順を使用して、NSX-T で DHCP サーバーを構成します。

NSX マネージャーから **[ネットワーク]** タブに移動し、 **[IP Management]\(IP 管理\)** の下で **[DHCP]** を選択します。 **[サーバーの追加]** ボタンを選択します。 次に、サーバー名とサーバー IP アドレスを指定します。 完了したら、 **[保存]** を選択します。

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP サーバーの追加" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>DHCP サーバーを第 1 層ゲートウェイに接続します。

1. **[Tier 1 Gateways]\(第 1 層ゲートウェイ\)** を選択し、ゲートウェイを選択して **[編集]** を選択します

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="使用するゲートウェイの選択" border="true":::

1. **[No IP Allocation Set]\(IP 割り当てセットなし\)** を選択して、サブネットを追加します

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="サブネットの追加" border="true":::

1. 次の画面で、 **[種類]** ドロップダウンから **[DHCP Local Server]\(DHCP ローカル サーバー\)** を選択します。 **[DHCP サーバー]** では、 **[Default DHCP]\(既定の DHCP\)** を選択し、 **[保存]** を選択します。

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="DHCP サーバーのオプションの選択" border="true":::

1. **[Tier 1 Gateways]\(第 1 層ゲートウェイ\)** ウィンドウで、 **[保存]** を選択します。 次の画面で、 **[変更が保存されました]** を確認し、 **[Close Editing]\(編集を閉じる\)** を選択して完了します。

### <a name="add-a-network-segment"></a>ネットワーク セグメントの追加

DHCP サーバーを作成したら、それにネットワーク セグメントを追加する必要があります。

1. NSX-T で、 **[ネットワーク]** タブを選択し、 **[接続]** の下で **[セグメント]** を選択します。 **[ADD SEGMENT]\(セグメントの追加\)** を選択します。 セグメントと第 1 層ゲートウェイへの接続に名前を付けます。 次に、 **[Set Subnets]\(サブネットの設定\)** を選択して、新しいサブネットを構成します。 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="新しいネットワーク セグメントの追加" border="true":::

1. **[Set Subnets]\(サブネットの設定\)** ウィンドウで、 **[サブネットの追加]** を選択します。 ゲートウェイ IP アドレスと DHCP 範囲を入力し、 **[追加]** 、 **[適用]** の順に選択します

   :::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="ネットワーク セグメントの追加" border="true":::

1. 完了したら、 **[保存]** を選択して、ネットワーク セグメントの追加を完了します。

   :::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="セグメントの完成" border="true":::

## <a name="create-dhcp-relay-service"></a>DHCP リレー サービスの作成

1. NXT-T ウィンドウで **[ネットワーク]** タブを選択し、 **[IP Management]\(IP 管理\)** の下で **[DHCP]** を選択します。 **[サーバーの追加]** を選択します。 **[サーバーの種類]** で [DHCP Relay]\(DHCP リレー\) を選択し、中継サーバーのサーバー名と IP アドレスを入力します。 **[保存]** を選択して変更を保存します。

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP 中継サーバーの作成" border="true":::

1. **[接続]** の下で **[Tier 1 Gateways]\(第 1 層ゲートウェイ\)** を選択します。 第 1 層ゲートウェイで縦方向の省略記号を選択し、 **[編集]** を選択します。

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="第 1 層ゲートウェイの編集" border="true":::

1. IP アドレスの割り当てを定義するには、 **[No IP Allocation Set]\(IP 割り当てセットなし\)** を選択します。

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="IP アドレスの割り当ての編集" border="true":::

1. ダイアログ ボックスで、 **[種類]** に **[DHCP Relay Server]\(DHCP 中継サーバー\)** を選択します。 **[DHCP Relay]\(DHCP リレー\)** ドロップダウンで、DHCP 中継サーバーを選択します。 終了したら、 **[保存]** を選択します

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="IP アドレス管理 の設定" border="true":::

## <a name="specify-a-dhcp-range-ip-on-segment"></a>セグメントに DHCP 範囲 IP を指定する

> [!NOTE]
> この構成は、DHCP クライアント セグメントで DHCP リレー機能を実現するために必要です。 

1. **[接続]** 下で **[セグメント]** を選択します。 縦方向の省略記号を選択し、 **[編集]** を選択します。 代わりに、新しいセグメントを追加する場合は **[Add Segment]\(セグメントの追加\)** を選択して新しいセグメントを作成できます。

   :::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="ネットワーク サブネットの編集" border="true":::

1. セグメントに関する詳細を追加します。  **[サブネット]** または **[Set Subnets]\(サブネットの設定\)** の下で値を選択して、サブネットを追加または変更します。

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="ネットワーク セグメント" border="true":::

1. 縦方向の省略記号を選択し、 **[編集]** を選択します。 新しいサブネットを作成する必要がある場合は、 **[サブネットの追加]** を選択し、ゲートウェイを作成して DHCP 範囲を構成します。 IP プールの範囲を指定して **[適用]** を選択し、 **[保存]** を選択します

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="サブネットの編集" border="true":::

1. これで、DHCP サーバー プールがセグメントに割り当てられました。

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="セグメントに割り当てられた DHCP サーバープール" border="true":::
