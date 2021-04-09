---
title: Azure VMware Solution のデプロイの計画
description: この記事では、Azure VMware Solution のデプロイ ワークフローの概要について説明します。  最終的な結果として、仮想マシン (VM) の作成と移行に向けて環境の準備が整います。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f1895f14361b7121ae0d78950cdf8eca3cf7eb52
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462424"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Azure VMware Solution のデプロイの計画

この記事では、デプロイ中に使用される情報を特定して収集するための計画プロセスについて説明します。 デプロイを計画する際は、収集した情報をデプロイ中に参照しやすいよう必ず文書化してください。

このクイック スタートで説明されているプロセスにより、仮想マシン (VM) の作成と移行のための運用準備ができた環境が得られます。 

>[!IMPORTANT]
>Azure VMware Solution リソースを作成する前に、「[Azure VMware Solution リソースを有効にする方法](enable-azure-vmware-solution.md)」に従い、サポート チケットを提出してホストの割り当てを依頼してください。 サポート チームがリクエストを受け取った後、リクエストが確認され、ホストが割り当てられるまでに、最大 5 営業日かかります。 Azure VMware Solution の既存のプライベート クラウドがあり、さらに多くのホストを割り当てる必要がある場合は、同じプロセスを実行します。 


## <a name="subscription"></a>サブスクリプション

Azure VMware Solution のデプロイに使用する予定のサブスクリプションを特定します。  新しいサブスクリプションを作成することも、既存のものを再利用することもできます。

>[!NOTE]
>サブスクリプションは、マイクロソフト エンタープライズ契約またはクラウド ソリューション プロバイダー Azure プランに関連付けられている必要があります。 詳細については、「[Azure VMware Solution リソースを有効にする方法](enable-azure-vmware-solution.md)」を参照してください。

## <a name="resource-group"></a>Resource group

Azure VMware Solution のために使用するリソース グループを特定します。  一般に、リソース グループは Azure VMware Solution 専用に作成されますが、既存のリソース グループを使用することもできます。

## <a name="region"></a>リージョン

Azure VMware Solution のデプロイ先リージョンを特定します。  詳細については、[リージョン別の利用可能な製品に関する Azure のガイド](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)を参照してください。

## <a name="resource-name"></a>リソース名

デプロイ時に使用するリソース名を定義します。  リソース名はわかりやすく説明的な名前であり、その中で Azure VMware Solution プライベート クラウドにタイトルを付けます。

>[!IMPORTANT]
>名前は 40 文字を超えてはいけません。 名前がこの制限を超えていると、プライベート クラウドで使用するパブリック IP アドレスを作成できません。 

## <a name="size-hosts"></a>サイズ ホスト

Azure VMware Solution のデプロイ時に使用するサイズ ホストを特定します。  完全な一覧については、[Azure VMware Solution プライベート クラウドとクラスター](concepts-private-clouds-clusters.md#hosts)に関するドキュメントを参照してください。

## <a name="number-of-clusters-and-hosts"></a>クラスターとホストの数

Azure VMware Solution では、1 つのプライベート クラウドをデプロイし、複数のクラスターを作成します。 実際のデプロイでは、各クラスターにデプロイするクラスターとホストの数を定義する必要があります。 クラスターあたりのホストの最小数は 3 で、最大数は 16 です。 プライベート クラウドあたりのクラスターの最大数は 4 です。 プライベート クラウドあたりのノードの最大数は 64 です。

詳細については、[Azure VMware Solution プライベート クラウドとクラスター](concepts-private-clouds-clusters.md#clusters)に関するドキュメントを参照してください。

>[!TIP]
>当初のデプロイ数を超える数にする必要がある場合は、後でいつでもクラスターを拡張できます。

## <a name="vcenter-admin-password"></a>vCenter 管理者パスワード
vCenter 管理者パスワードを定義します。 デプロイ時に vCenter 管理者パスワードを作成します。 このパスワードは、vCenter のビルド時に管理者アカウント (cloudadmin@vsphere.local) に割り当られます。 これらの資格情報を使用して vCenter にサインインします。

## <a name="nsx-t-admin-password"></a>NSX-T 管理者パスワード
NSX-T 管理者パスワードを定義します。 デプロイ時に NSX-T 管理者パスワードを作成します。 パスワードは、NSX のビルド時に、NSX アカウントの管理者ユーザーに割り当てられます。 これらの資格情報を使用して NSX-T Manager にサインインします。

## <a name="ip-address-segment-for-private-cloud-management"></a>プライベート クラウド管理用の IP アドレス セグメント

デプロイを計画する際の最初の手順は、IP のセグメント化を計画することです。 Azure VMware Solution には、/22 CIDR ネットワークが必要です。 このアドレス空間が小さなネットワーク セグメント (サブネット) に分割され、vCenter、VMware HCX、NSX-T、vMotion の各機能に使用されます。

この /22 CIDR ネットワーク アドレス ブロックは、既にオンプレミスまたは Azure にある既存のネットワーク セグメントと重複することはできません。

**例:** 10.0.0.0/22

Azure VMware Solution は、内部の ExpressRoute Global Reach 回線 (下の図の D-MSEE) を介して Microsoft Azure Virtual Network に接続します。 この機能は Azure VMware Solution サービスの一部であり、課金されることはありません。

詳細については、「[ネットワーク計画のチェックリスト](tutorial-network-checklist.md#routing-and-subnet-considerations)」を参照してください。

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="特定する - IP アドレス セグメント" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>仮想マシンのワークロードの IP アドレス セグメント

プライベート クラウド内にワークロード用の最初のネットワーク (NSX セグメント) を作成するための IP セグメントを特定します。 言い換えると、Azure VMware Solution に VM をデプロイできるように、Azure VMware Solution 上にネットワーク セグメントを作成する必要があります。

オンプレミスから Azure VMware Solution (L2) にネットワークを拡張する予定であっても、環境を検証するネットワーク セグメントを作成する必要があります。

作成されるどの IP セグメントも、Azure とオンプレミスのフットプリント全体で一意である必要があることに注意してください。
  
**例:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="特定する - 仮想マシンのワークロードの IP アドレス セグメント" border="false":::     

## <a name="optional-extend-networks"></a>(省略可能) ネットワークを拡張する

ネットワーク セグメントは、オンプレミスから Azure VMware Solution まで拡張できます。実施する場合は、ここでそれらのネットワークを特定します。  

次の点に留意します。

- オンプレミスからネットワークを拡張する予定の場合、これらのネットワークはオンプレミスの VMware 環境内の [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) に接続する必要があります。  
- 拡張したいネットワークが [vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html) 上で機能している場合、それらは拡張できません。

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Azure Virtual Network を Azure VMware Solution に接続する

この手順では、ExpressRoute 仮想ネットワーク ゲートウェイと、Azure VMware Solution の ExpressRoute 回線の接続に使用される、サポートする Azure 仮想ネットワークを特定します。  ExpressRoute 回路により、Azure VMware Solution プライベート クラウドと他の Azure サービス、Azure リソース、オンプレミス環境との間の接続が容易になります。

使用する ExpressRoute 仮想ネットワーク ゲートウェイは、"*既存の*" ものでも "*新しい*" ものでもかまいません。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="特定する - Azure VMware Solution を接続するための Azure Virtual Network" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>既存の ExpressRoute 仮想ネットワーク ゲートウェイを使用する

"*既存の*" ExpressRoute 仮想ネットワーク ゲートウェイを使用する場合は、プライベート クラウドをデプロイした後に、Azure VMware Solution の ExpressRoute 回線が確立されます。 この場合、 **[仮想ネットワーク]** フィールドは空白のままにしておきます。  

使用する ExpressRoute 仮想ネットワーク ゲートウェイをメモしておき、次の手順に進みます。

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>新しい ExpressRoute 仮想ネットワーク ゲートウェイを作成する

"*新しい*" ExpressRoute 仮想ネットワーク ゲートウェイを作成するときは、既存の Azure 仮想ネットワークを使用することも、新しく作成することもできます。  

- 既存の Azure 仮想ネットワークの場合:
   1. 仮想ネットワークに既存の ExpressRoute 仮想ネットワーク ゲートウェイがないことを確認します。 
   1. **[仮想ネットワーク]** の一覧から、既存の Azure 仮想ネットワークを選択します。

- 新しい Azure 仮想ネットワークの場合、事前に作成することも、デプロイ時に作成することもできます。 **[仮想ネットワーク]** の一覧の下にある **[新規作成]** リンクを選択します。

次の画像は、 **[仮想ネットワーク]** フィールドが強調表示された **[プライベート クラウドを作成する]** デプロイ画面を示しています。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="[仮想ネットワーク] フィールドが強調表示された Azure VMware Solution のデプロイ画面のスクリーンショット。":::

>[!NOTE]
>使用または作成される仮想ネットワークは、オンプレミス環境と Azure VMware Solution から認識される可能性があるため、この仮想ネットワークで使用する IP セグメントとサブネットが重複しないようにしてください。

## <a name="vmware-hcx-network-segments"></a>VMware HCX のネットワーク セグメント

VMware HCX は、Azure VMware Solution にバンドルされているテクノロジです。 VMware HCX の主なユース ケースは、ワークロードの移行とディザスター リカバリーです。 いずれかの実施を計画する場合は、ここでネットワークを計画するのが最適です。   そうしない場合は、次の手順までスキップして続行できます。

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>次の手順
これで必要な情報を収集して文書化したので、次のセクションに進み、Azure VMware Solution プライベート クラウドを作成します。

> [!div class="nextstepaction"]
> [Azure VMware Solution のデプロイ](deploy-azure-vmware-solution.md)
