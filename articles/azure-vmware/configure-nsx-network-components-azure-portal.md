---
title: Azure VMware Solution での NSX ネットワークコンポーネントの構成
description: Azure VMware Solution コンソールを使用して、NSX-T ネットワーク セグメントを構成する方法について説明します。
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 0478582a9bc4fb77a1784c27ec4f5c302d6b89fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716990"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Azure VMware Solution での NSX ネットワークコンポーネントの構成

Azure VMware Solution プライベート クラウドには、既定では、ソフトウェア定義ネットワーク (SDDC) として、NSX-T が付属しています。 これには、アクティブ/アクティブ モードでは NSX-T Tier-0 ゲートウェイが、アクティブ/スタンバイ モードでは既定の NSX-T Tier-1 ゲートウェイが事前プロビジョニングされています。  これらのゲートウェイを使用すると、セグメント (論理スイッチ) に接続し、East-West および North-South 接続を提供できます。 

Azure VMware Solution プライベート クラウドをデプロイした後、Azure VMware Solution コンソールの **[ワークロード ネットワーク]** から必要な NSX-T オブジェクトを構成できます。  このコンソールには、VMware 管理者が毎日必要とする NSX-T 操作のうち、NSX-T に慣れていないユーザーを対象とするものを簡略化したビューが表示されます。  

Azure VMware Solution コンソールで NSX-T コンポーネントを構成するための 4 つのオプションがあります。
- **[セグメント]** - NSX-T Manager と vCenter に表示されるセグメントを作成します。
- **[DHCP]** - DHPC を使用する予定の場合は、DHCP サーバーまたは DHCP リレーを作成します。
- **[ポート ミラーリング]** - ポート ミラーリングを作成して、ネットワークの問題のトラブルシューティングに役立てます。
- **[DNS]** - 指定された DNS サーバーに解決のために DNS 要求を送信する DNS フォワーダーを作成します。  

>[!NOTE]
>引き続き、NSX-T Manager コンソールにアクセスして、前述の詳細設定とその他の NSX-T 機能を使用できます。 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="NSX-T を構成するための Azure VMware Solution コンソールの 4 つのオプションを示すスクリーンショット。":::

## <a name="prerequisites"></a>前提条件
Azure VMware Solution プライベート クラウドに作成または移行された仮想マシン (VM) は、セグメントに接続される必要があります。 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Azure portal で NSX-T セグメントを作成する
Azure portal の Azure VMware Solution コンソールから、NSX-T セグメントを作成し、構成することができます。  これらのセグメントは既定の Tier-1 ゲートウェイに接続され、これらのセグメントのワークロードは East-West および North-South 接続を実現します。 セグメントを作成すると、NSX-T Manager と vCenter に表示されます。

>[!NOTE]
>DHCP の使用を計画している場合は、NSX-T セグメントを作成して構成する前に、[DHCP サーバーまたは DHCP リレーを構成](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal)する必要があります。

1. Azure VMware Solution プライベート クラウドで、 **[ワークロード ネットワーク]** の下にある **[セグメント]**  >  **[追加]** を選択します。 新しい論理セグメントの詳細を指定し、 **[OK]** を選択します。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="新しいセグメントを追加する方法を示すスクリーンショット。":::
   
   - **[セグメント名]** - vCenter に表示される論理スイッチの名前。
   - **[サブネット ゲートウェイ]** - 論理スイッチのサブネットのゲートウェイ IP アドレスとサブネット マスク。 VM は論理スイッチに接続され、このスイッチに接続されているすべての VM は同じサブネットに属しています。  また、この論理セグメントに接続されているすべての VM は、同じセグメントの IP アドレスを持つ必要があります。
   - **[DHCP]** (省略可能) - 論理セグメントの DHCP 範囲。 [DHCP サーバーまたは DHCP リレー](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal)は、セグメントで DHCP を使用するように構成されている必要があります。  
   - **[接続されているゲートウェイ]**  - *既定で選択され、読み取り専用です。*  Tier-1 ゲートウェイとセグメント情報の種類。 
      - **[T1]** - NSX-T Manager の Tier-1 ゲートウェイの名前。 Azure VMware Solution には、アクティブ/アクティブ モードでは NSX-T Tier-0 ゲートウェイが、アクティブ/スタンバイ モードでは既定の NSX-T Tier-1 ゲートウェイが用意されています。  Azure VMware Solution コンソールを使用して作成されたセグメントは、既定の Tier-1 ゲートウェイにのみ接続し、これらのセグメントのワークロードは East-West および North-South 接続を取得します。 NSX-T Manager を通じてのみ、より多くの Tier-1 ゲートウェイを作成できます。 NSX-T Manager コンソールから作成された Tier-1 ゲートウェイは、Azure VMware Solution コンソールには表示されません。 
      - **[種類]** -Azure VMware Solution でサポートされるオーバーレイ セグメント。

   このセグメントは、Azure VMware Solution コンソール、NSX-T Manager、および vCenter に表示されるようになりました。

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Azure portal で DHCP サーバーまたは DHCP リレーを作成する
Azure portal の Azure VMware Solution コンソールから、DHC サーバーまたはリレーを直接作成できます。 DHCP サーバーまたはリレーは、Azure VMware Solution をデプロイすると作成される Tier-1 ゲートウェイに接続されます。 DHCP 範囲を指定したすべてのセグメントが、この DHCP の一部になります。  DHCP サーバーまたは DHCP リレーを作成した後、それを使用するには、セグメント レベルでサブネットまたは範囲を定義する必要があります。

1. Azure VMware Solution プライベート クラウドで、 **[ワークロード ネットワーク]** の下にある **[DHCP]**  >  **[追加]** を選択します。

2. **[DHCP サーバー]** または **[DHCP リレー]** を選択してから、サーバーまたはリレーの名前と 3 つの IP アドレスを指定します。 

   >[!NOTE]
   >DHCP リレーの場合、構成を成功させるために必要な IP アドレスは 1 つだけです。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Azure VMware Solution に DHCP サーバーまたは DHCP リレーを追加する方法を示すスクリーンショット。":::

4. [論理セグメントに DHCP 範囲を指定](#create-an-nsx-t-segment-in-the-azure-portal)して DHCP 構成を完了し、 **[OK]** を選択します。
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Azure portal でポート ミラーリングを構成する
各パケットのコピーをネットワーク スイッチ ポート間で転送することによってネットワーク トラフィックを監視するようにポート ミラーリングを構成することができます。 このオプションを選択すると、ミラー化されたデータを受信するポートにプロトコル アナライザーが配置されます。 ソース、VM、または VM グループからのトラフィックが分析され、定義された宛先に送信されます。 

Azure VMware Solution コンソールでポート ミラーリングを設定するには、次のようにします。

* [手順 1.ソースと宛先の VM または VM グループを作成する](#step-1-create-source-and-destination-vms-or-vm-groups) - ソース グループには、トラフィックがミラー化される 1 つまたは複数の VM があります。

* [手順 2.ポート ミラーリング プロファイルを作成する](#step-2-create-a-port-mirroring-profile) - ソースと宛先の VM グループのトラフィックの方向を定義します。

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>手順 1. ソースと宛先の VM または VM のグループを作成する

この手順では、ソース VM グループと宛先 VM グループを作成します。

1. Azure VMware Solution プライベート クラウドで、 **[ワークロード ネットワーク]** の下にある **[ポート ミラーリング]**  >  **[VM グループ]**  >  **[追加]** を選択します。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="ポート ミラーリング用の VM グループを作成する方法を示すスクリーンショット。":::

1. 新しい VM グループの名前を指定し、一覧から目的の VM、次に **[OK]** を選択します。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="VM グループに追加する VM の一覧を示すスクリーンショット。":::

1. これらの手順を繰り返して、宛先 VM グループを作成します。

### <a name="step-2-create-a-port-mirroring-profile"></a>手順 2. ポート ミラーリング プロファイルを作成する

この手順では、ソースと宛先の VM グループのトラフィックの方向についてプロファイルを定義します。

>[!NOTE]
>ソースと宛先の両方の VM グループが作成されていることを確認します。

1. **[ポート ミラーリング]**  >  **[追加]** を選択し、次のように入力します。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="ポート ミラーリング プロファイルに必要な情報を示すスクリーンショット。":::

   - **[ポート ミラーリング名]** - プロファイルのわかりやすい名前。
   - **[方向]** - [イングレス]、[エグレス]、または [双方向] から選択します。
   - **[ソース]** - ソース VM グループを選択します。
   - **[宛先]** - 宛先 VM グループを選択します。
   - **[説明]** - ポート ミラーリングの説明を入力します。

1. **[OK]** を選択してプロファイルを完了します。 

   プロファイルと VM グループは、Azure VMware Solution コンソールに表示されます。

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Azure portal で DNS フォワーダーを構成する
特定の DNS 要求を解決のために指定された DNS サーバーに転送する DNS フォワーダーを構成します。  DNS フォワーダーは **既定の DNS ゾーン** と、最大 3 つの **FQDN ゾーン** に関連付けられます。

>[!TIP]
>[NSX-T Manager コンソールを使用して、DNS フォワーダーを構成](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html)することもできます。

Azure VMware Solution コンソールで DNS フォワーダーを設定するには、次のようにします。

* [手順 1.既定の DNS ゾーンと FQDN ゾーンを構成する](#step-1-configure-a-default-dns-zone-and-fqdn-zone) - DNS フォワーダーでは、DNS クエリを受信すると、ドメイン名と FQDN DNS ゾーン内のドメイン名を比較します。 

* [手順 2.DNS サービスを構成する](#step-2-configure-dns-service) - DNS フォワーダー サービスを構成します。

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>手順 1. 既定の DNS ゾーンと FQDN ゾーンを構成する
DNS クエリをアップストリーム サーバーに送信するように、既定の DNS ゾーンと FQDN ゾーンを構成します。  DNS フォワーダーでは、DNS クエリを受信すると、クエリ内のドメイン名と FQDN DNS ゾーンのドメイン名を比較します。 一致するものが見つかった場合、クエリは FQDN DNS ゾーンで指定された DNS サーバーに転送されます。 一致するものが見つからなかった場合、クエリは 既定の DNS ゾーンで指定された DNS サーバーに転送されます。

>[!NOTE]
>FQDN ゾーンを構成する前に、既定の DNS ゾーンを定義する必要があります。 

1. Azure VMware Solution プライベート クラウドで、 **[ワークロード ネットワーク]** の下にある **[DNS]**  >  **[DNS ゾーン]**  >  **[追加]** を選択します。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="DNS ゾーンと DNS サービスを追加する方法を示すスクリーンショット。":::

1. **[既定の DNS ゾーン]** を選択し、次の情報を指定します。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="既定の DNS ゾーンを追加する方法を示すスクリーンショット。":::

   1. DNS ゾーンの名前。

   1. **8.8.8.8** の形式で、最大 3 つの DNS サーバー IP アドレス。

1. **[FQDN ゾーン]** を選択し、次の情報を指定します。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="FQDN ゾーンを追加する方法を示すスクリーンショット。":::

   1. DNS ゾーンの名前。

   1. FQDN ドメイン。

   1. **8.8.8.8** の形式で、最大 3 つの DNS サーバー IP アドレス。

1. **[OK]** を選択して、既定の DNS ゾーンと DNS サービスの追加を完了します。

### <a name="step-2-configure-dns-service"></a>手順 2. DNS サービスを構成する

1. **[DNS サービス]** タブを選択し、 **[追加]** を選択します。 詳細を入力して、 **[OK]** を選択します。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="DNS サービスに必要な情報を示すスクリーンショット。":::

   >[!TIP]
   >既定では **[Tier-1 ゲートウェイ]** が選択され、Azure VMware Solution をデプロイするときに作成されたゲートウェイが反映されます。

   DNS サービスが正常に追加されました。

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="DNS サービスが正常に追加されたことを示すスクリーンショット。":::

