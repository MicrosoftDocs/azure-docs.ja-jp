---
title: Hybrid Cloud Extension (HCX) をインストールする
description: Azure VMware Solution のプライベート クラウド用の VMware Hybrid Cloud Extension (HCX) ソリューションを設定します
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: fb8497af33b364c1d2ab475233bd2a83ef1befad
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752319"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Azure VMware Solution 用の HCX をインストールする

この記事では、Azure VMWare Solution のプライベート クラウド用の VMWare Hybrid Cloud Extension (HCX) ソリューションを設定する手順を説明します。 HCX を使用すると、さまざまな組み込みの HCX でサポートされた移行の種類により、クラウドやその他の接続されたサイトに VMware のワークロードを移行できます。

HCX Advanced (既定のインストール) では、最大 3 つのサイト接続 (オンプレミスまたはクラウドからクラウド) がサポートされます。 3 つより多くのサイト接続が必要な場合は、サポートを通じて HCX Enterprise アドオン (現在プレビュー段階) を有効にすることができます。 HCX Enterprise では、一般提供 (GA) 後にはお客様に対して追加料金が発生しますが、[追加の機能](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)が提供されます。


「[開始する前に](#before-you-begin)」、「[ソフトウェア バージョンの要件](#software-version-requirements)」、「[前提条件](#prerequisites)」を最初によく確認してください。 

その後、次の操作を実行するために必要なすべての手順について説明します。

> [!div class="checklist"]
> * オンプレミスの HCX OVA (コネクタ) をデプロイする
> * HCX をアクティブ化して構成する
> * ネットワーク アップリンクとサービス メッシュを構成する
> * アプライアンスの状態を確認してセットアップを完了する

セットアップが完了したら、この記事の最後に記載されている推奨される次の手順に進むことができます。  

## <a name="before-you-begin"></a>開始する前に
    
* 基本的な Azure VMware Solution Software Defined Datacenter (SDDC) の[チュートリアル シリーズ](tutorial-network-checklist.md)を確認します。
* HCX ユーザー ガイドなど、[VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)を確認して参照します。
* VMware のドキュメント「[Migrating Virtual Machines with VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)」 (Vmware HCX で仮想マシンを移行する) を確認します。
* 必要に応じて、「[VMware HCX Deployment Considerations](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)」 (VMware HCX のデプロイに関する考慮事項) を確認します。
* 必要に応じて、HCX についての VMware vSphere [ブログシリーズ](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)など、HCX に関連する VMware の資料を確認します。 
* Azure VMware Solution のサポート チャネルを通じて、Azure VMware Solution の HCX Enterprise のアクティブ化を要求します。

コンピューティングとストレージのリソースに対するワークロードのサイズの決定は、Azure VMware Solution プライベート クラウドの HCX ソリューションの使用を準備する際に不可欠な計画手順です。 サイズ決定手順には、初期プライベート クラウド環境計画の一環として対処します。 

Azure Migrate ポータルで、Azure VMware Solution の評価を行って (https://docs.microsoft.com/azure/migrate/how-to-create-azure-vmware-solution-assessment) )、ワークロードのサイズを決定することもできます。

## <a name="software-version-requirements"></a>ソフトウェア バージョンの要件

インフラストラクチャ コンポーネントでは、必要な最小バージョンが実行されている必要があります。 
                                                         
| コンポーネントの種類    | ソース環境の要件    | ターゲット環境の要件   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>5\.5 U1 以前を使用している場合は、HCX 操作用にスタンドアロンの HCX ユーザー インターフェイスを使用します。  | 6.0 U2 以降   |
| ESXi   | 5.0    | ESXi 6.0 以降   |
| NSX    | ソースの論理スイッチの HCX ネットワーク拡張機能の場合: NSXv 6.2 以降または NSX-T 2.4 以降   | NSXv 6.2 以降または NSX-T 2.4 以降<br/><br/>HCX 近接ルーティングの場合: NSXv 6.4 以降 (NSX-T では近接ルーティングはサポートされていません) |
| vCloud Director   | 必須ではありません - ソース サイトの vCloud Director との相互運用性はありません | ターゲット環境を vCloud Director と統合する場合、最小値は 9.1.0.2 です。  |

## <a name="prerequisites"></a>前提条件

* オンプレミスと Azure VMware Solution SDDC ExpressRoute 回線の間に ExpressRoute Global Reach を構成する必要があります。

* オンプレミスと Azure VMware Solution SDDC の間で、すべての必要なポートを開く必要があります ([VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)を参照)。

* オンプレミスの HCX Manager 用に 1 つの IP アドレス、相互接続 (IX) とネットワーク拡張機能 (NE) アプライアンス用に 2 つ以上の IP アドレス。

* オンプレミスの HCX IX と NE アプライアンスは、vCenter と ESXi インフラストラクチャに到達できる必要があります。

* WAN 相互接続アプライアンスをデプロイするには、Azure portal で SDDC デプロイに使用される /22 CIDR ネットワーク アドレス ブロックに加えて、HCX に /29 ブロックが必要です。 この要件をネットワーク計画に組み込むようにしてください。

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>VMware HCX OVA をオンプレミスにデプロイする

1. Azure VMware Solution SDDC vCenter にサインインし、 **[HCX]** を選択します。

   :::image type="content" source="media/hybrid-cloud-extension-installation/avs-vsphere-client.png" alt-text="Azure VMware Solution SDDC vCenter にサインインし、[HCX] を選択します。":::

1. **[管理]** の下で **[システムの更新プログラム]** を選択し、次に **[Request download link]\(ダウンロードリンクを要求\)** を選択して、VMware HCX OVA ファイルをダウンロードします。

   :::image type="content" source="media/hybrid-cloud-extension-installation/administration-updates.png" alt-text="[管理] の下で [システムの更新プログラム] を選択し、次に [Request download link]\(ダウンロードリンクを要求\) を選択して、VMware HCX OVA ファイルをダウンロードします。":::

1. 次に、オンプレミスの vCenter にアクセスし、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-template.png" alt-text="次に、オンプレミスの vCenter にアクセスし、オンプレミスの vCenter にデプロイする OVF テンプレートを選択します。":::

1. 名前と場所を選択し、HCX をデプロイする必要があるリソースまたはクラスターを選択します。 次に、詳細情報と必要なリソースを確認します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-template.png" alt-text="名前と場所を選択し、HCX をデプロイする必要があるリソースまたはクラスターを選択します。次に、詳細情報と必要なリソースを確認します。":::

1. ライセンス条項を確認し、同意する場合は、必要なストレージとネットワークを選択します。 **[次へ]** を選択します。

1. **[Customize template]\(テンプレートのカスタマイズ\)** に、必要なすべての情報を入力します。 

   :::image type="content" source="media/hybrid-cloud-extension-installation/customize-template.png" alt-text="[テンプレートのカスタマイズ] で、必要な情報をすべて入力します。":::

1. **[Next]\(次へ\)** を選択し、構成を確認し、 **[Finish]\(完了\)** を選択して、HCX OVA をデプロイします。

## <a name="activate-hcx"></a>HCX をアクティブ化する

インストールが完了したら、次の手順を実行します。

1. `https://HCXManagerIP:9443` でオンプレミスの HCX Manager にログオンし、ユーザー名とパスワードを使用してサインインします。 

   > [!IMPORTANT]
   > 必ず HCX Manager の IP アドレスと共に `9443` のポート番号を含めてください。

1. **[Licensing]\(ライセンス\)** で、 **[HCX Advanced Key]\(HCX Advanced キー\)** を入力します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-key.png" alt-text="[ライセンス] で、[HCX Advanced Key]\(HCX Advanced キー\) を入力します。":::
    
    > [!NOTE]
    > HCX Manager では、インターネット アクセスが開かれているか、またはプロキシが構成されている必要があります。

1. **vCenter** で、必要に応じて、vCenter の情報を編集します。

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-vcenter.png" alt-text="[vCenter] で、必要に応じて、vCenter の情報を編集します。":::

1. **[Datacenter Location]\(データセンターの場所\)** で、必要に応じて、データセンターの場所を編集します。

   :::image type="content" source="media/hybrid-cloud-extension-installation/system-location.png" alt-text="[データセンターの場所]"::: で、必要に応じて、データセンターの場所を編集します。

## <a name="configure-hcx"></a>HCX を構成する 

1. オンプレミスの vCenter にサインインし、 **[ホーム]** の下から **[HCX]** を選択します。

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-vcenter.png" alt-text="オンプレミスの vCenter にサインインし、[ホーム] の下から [HCX] を選択します。":::

1. **[インフラストラクチャ]** の下で、 **[Site Pairing]\(サイトのペアリング\)**  >  **[Add a site pairing]\(サイトのペアリングの追加\)** を選択します。

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-pairing.png" alt-text="[インフラストラクチャ] の下で、[Site Pairing]\(サイトのペアリング\) > [Add a site pairing]\(サイトのペアリングの追加\) を選択します。":::

1. リモート HCX の URL または IP アドレス、Azure VMware Solution の CloudAdmin のユーザー名とパスワードを入力した後、 **[接続]** を選択します。

   システムに接続されたサイトが表示されます。

   :::image type="content" source="media/hybrid-cloud-extension-installation/site-connection.png" alt-text="システムに接続されたサイトが表示されます。":::

1. **[インフラストラクチャ]** で、 **[Interconnect]\(相互接続\)**  >  **[Multi-Site Service Mesh]\(マルチサイト サービス メッシュ\)**  >  **[Network Profiles]\(ネットワーク プロファイル\)**  >  **[Create Network Profile]\(ネットワーク プロファイルの作成\)** を選択します。

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-network-profile.png" alt-text="[インフラストラクチャ] で、[Interconnect]\(相互接続\) > [Multi-Site Service Mesh]\(マルチサイト サービス メッシュ\) > [ネットワーク プロファイル] > [Create Network Profile]\(ネットワーク プロファイルの作成\) を選択します。":::

1. 新しいネットワーク プロファイルでは、HCX IX および NE IP アドレス範囲を入力します (IX および NE アプライアンスには、少なくとも 2 つの IP アドレスが必要です)。

   :::image type="content" source="media/hybrid-cloud-extension-installation/enter-address-ranges.png" alt-text="新しいネットワーク プロファイルでは、HCX IX および NE IP アドレス範囲を入力します (IX および NE アプライアンスには、少なくとも 2 つの IP アドレスが必要です)。":::
  
   > [!NOTE]
   > ネットワーク拡張機能アプライアンス (HCX-NE) には、分散仮想スイッチ (DVS) との間に一対一の関係があります。  

1. 次に、 **[Compute profile]\(コンピューティング プロファイル\)**  >  **[Create compute profile]\(コンピューティング プロファイルの作成\)** を選択します。

1. コンピューティング プロファイル名を入力し、 **[Continue]\(続行\)** を選択します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/create-compute-profile.png" alt-text="コンピューティング プロファイル名を入力し、[続行] を選択します。":::

1. 有効にするサービス (移行、ネットワーク拡張機能、またはディザスター リカバリーなど) を選択し、 **[Continue]\(続行\)** を選択します。

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-services.png" alt-text="有効にするサービス (移行、ネットワーク拡張機能、ディザスター リカバリーなど) を選択した後、[続行] を選択します。":::

1. **[Select Service Resources]\(サービス リソースの選択\)** で、選択した HCX サービスを有効にする必要がある 1 つまたは複数のサービス リソースを選択します。 **[続行]** をクリックします。

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-service-resources.png" alt-text="[Select Service Resources]\(サービス リソースの選択\) で、選択した HCX サービスを有効にする必要がある 1 つまたは複数のサービス リソースを選択します。[続行] をクリックします。":::
  
   > [!NOTE]
   > ソース VM が HCX を使用した移行の対象となる特定のクラスターを選択します。

1. **[Datastore]\(データストア\)** を選択し、 **[Continue]\(続行\)** を選択します。 
      
   HCX 相互接続アプライアンスをデプロイするための各コンピューティング リソースとストレージ リソースを選択します。 複数のリソースを選択すると、HCX では容量が使い果たされるまで、最初に選択されたリソースが使用されます。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployment-resources.png" alt-text="HCX 相互接続アプライアンスをデプロイするための各コンピューティング リソースとストレージ リソースを選択します。複数のリソースを選択すると、HCX では容量が使い果たされるまで、最初に選択されたリソースが使用されます。":::

1. **[Network Profiles]\(ネットワーク プロファイル\)** で作成した管理ネットワーク プロファイルを選択し、 **[Continue]\(続行\)** を選択します。  
      
   vCenter ホストと ESXi ホストの管理インターフェイスに到達できるネットワーク プロファイルを選択します。 そのようなネットワーク プロファイルをまだ定義していない場合は、ここで作成できます。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/management-network-profile.png" alt-text="vCenter ホストと ESXi ホストの管理インターフェイスに到達できるネットワーク プロファイルを選択します。そのようなネットワーク プロファイルをまだ定義していない場合は、ここで作成できます。":::

1. **[Network Uplink]\(ネットワーク アップリンク\)** を選択し、 **[Continue]\(続行\)** を選択します。
      
   次のいずれかが満たされるように、1 つまたは複数のネットワーク プロファイルを選択します。  
   * リモート サイトの相互接続アプライアンスに、このネットワーク経由で到達できる。  
   * リモート側アプライアンスが、このネットワーク経由で、ローカル相互接続アプライアンスに到達できる。  
    
   複数のサイトで共有されない直接接続のようなポイント ツー ポイント ネットワークを使用している場合は、コンピューティング プロファイルが複数のサイトで共有されるため、このステップを省略できます。 そのような場合は、相互接続サービス メッシュの作成時に、アップリンク ネットワーク プロファイルを上書きして指定することができます。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/uplink-network-profile.png" alt-text="[Network Uplink]\(ネットワーク アップリンク\) を選択し、[続行] を選択します。":::

1. **[vMotion Network Profile]\(vMotion ネットワーク プロファイル\)** を選択し、 **[Continue]\(続行\)** を選択します。
      
   ESXi ホストの vMotion インターフェイスに到達できるネットワーク プロファイルを選択します。 そのようなネットワーク プロファイルをまだ定義していない場合は、ここで作成できます。 vMotion ネットワークがない場合は、 **[Management Network Profile]\(管理ネットワーク プロファイル\)** を選択します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vmotion-network-profile.png" alt-text="[vMotion Network Profile]\(vMotion ネットワーク プロファイル\) を選択し、[続行] を選択します。":::

1. **[vSphere レプリケーション ネットワーク プロファイルを選択する]** から、ESXi ホストの vSphere レプリケーション インターフェイスへの接続に使用するネットワーク プロファイルを選択し、 **[Continue]\(続行\)** を選択します。
      
   ほとんどの場合、このプロファイルは管理ネットワーク プロファイルと同じです。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png" alt-text="[vSphere レプリケーション ネットワーク プロファイルを選択する] から、ESXi ホストの vSphere レプリケーション インターフェイスへの接続に使用するネットワーク プロファイルを選択した後、[続行] を選択します。":::

1. **[Select Distributed Switches for Network Extensions]\(ネットワーク拡張機能の分散スイッチを選択する\)** から、統合される VM があり接続されているネットワークがある DVS を選択します。  **[続行]** をクリックします。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/distributed-switches.png" alt-text="[Select Distributed Switches for Network Extensions]\(ネットワーク拡張機能の分散スイッチを選択する\) から、統合される VM があり接続されているネットワークがある DVS を選択します。[続行] をクリックします。":::

1. 接続ルールを確認し、 **[Continue]\(続行\)** を選択します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-compute-profile.png" alt-text="接続ルールを確認し、[続行] を選択します。":::

1.  **[Finish]\(完了\)** を選択して、コンピューティング プロファイルを作成します。

## <a name="configure-network-uplink"></a>ネットワーク アップリンクを構成する

次に、ネットワーク アップリンク用の Azure VMware Solution SDDC でネットワーク プロファイルの変更を構成します。

1. SDDC NSX-T にサインインして新しい論理スイッチを作成するか、オンプレミスと Azure VMware Solution SDDC の間のネットワーク アップリンクに使用できる既存の論理スイッチを使用します。

1. オンプレミスから Azure VMware Solution SDDC への通信に使用できる HCX アップリンク用のネットワーク プロファイルを Azure VMware Solution SDDC に作成します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-profile-uplink.png" alt-text="オンプレミスから Azure VMware Solution SDDC への通信に使用できる HCX アップリンク用のネットワーク プロファイルを Azure VMware Solution SDDC に作成します。":::

1. 必要な L2 ネットワーク拡張機能に基づいて、ネットワーク プロファイルの名前と、少なくとも 4 または 5 つの空き IP アドレスを入力します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-uplink-profile.png" alt-text="必要な L2 ネットワーク拡張機能に基づいて、ネットワーク プロファイルの名前と、少なくとも 4 または 5 つの空き IP アドレスを入力します。":::

1. **[作成] を選択して、Azure VMware Solution SDDC の構成を完了します**

## <a name="configure-service-mesh"></a>サービス メッシュを構成する

次に、オンプレミスと Azure VMware Solution SDDC の間のサービス メッシュを構成します。

1. Azure VMware Solution SDDC vCenter にサインインし、 **[HCX]** を選択します。

2. **[インフラストラクチャ]** で、 **[Interconnect]\(相互接続\)**  >  **[Service Mesh]\(サービス メッシュ\)**  >  **[Create Service Mesh]\(サービス メッシュの作成\)** を選択して、前の手順で作成したネットワークとコンピューティング プロファイルを構成します。    

   :::image type="content" source="media/hybrid-cloud-extension-installation/configure-service-mesh.png" alt-text="[インフラストラクチャ] で、[相互接続] > [Service Mesh]\(サービス メッシュ\) > [Create Service Mesh]\(サービス メッシュの作成\) を選択して、前の手順で作成したネットワークとコンピューティング プロファイルを構成します。":::

3. ペアになっているサイトを選択してハイブリッド機能を有効にし、 **[Continue]\(続行\)** を選択します。   

   :::image type="content" source="media/hybrid-cloud-extension-installation/select-paired-sites.png" alt-text="ペアになっているサイトを選択してハイブリッド機能を有効にし、[続行] を選択します。":::

4. ソースとリモートのコンピューティング プロファイルを選択してハイブリッド サービスを有効にし、 **[Continue]\(続行\)** を選択します。
      
   選択により、VM が HCX サービスを使用できるリソースが定義されます。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/enable-hybridity.png" alt-text="選択により、VM が HCX サービスを使用できるリソースが定義されます。":::

5. 有効にするサービスを選択し、 **[Continue]\(続行\)** を選択します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/hcx-services.png" alt-text="有効にするサービスを選択し、[続行] を選択します。":::

6. **[Advanced Configuration - Override Uplink Network profiles]\(詳細構成 - アップリンク ネットワーク プロファイルのオーバーライド\)** で、 **[Continue]\(続行\)** を選択 ます。  
      
   アップリンク ネットワーク プロファイルは、リモート サイトの相互接続アプライアンスに到達できるネットワークに接続するために使用されます。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/override-uplink-profiles.png" alt-text="アップリンク ネットワーク プロファイルは、リモート サイトの相互接続アプライアンスに到達できるネットワークに接続するために使用されます。":::

7. **[Configure the Network Extension Appliance Scale Out]\(ネットワーク拡張機能アプライアンスのスケールアウトを構成する\)** を選択します。 

   :::image type="content" source="media/hybrid-cloud-extension-installation/network-extension-scale-out.png" alt-text="[Configure the Network Extension Appliance Scale Out]\(ネットワーク拡張機能アプライアンスのスケールアウトを構成する\) を選択します。":::

8. DVS スイッチの数に対応するアプライアンスの数を入力します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/appliance-scale.png" alt-text="DVS スイッチの数に対応するアプライアンスの数を入力します。":::

9. **[Continue]\(続行\)** を選択してスキップします。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/traffic-engineering.png" alt-text="[続行] を選択してスキップします。":::

10. トポロジのプレビューを確認し、 **[Continue]\(続行\)** を選択します。 

11. このサービス メッシュのわかりやすい名前を入力し、 **[Finish]\(完了\)** を選択して完了します。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/complete-service-mesh.png" alt-text="サービス メッシュを完了します":::

   サービス メッシュがデプロイされて構成されます。  

   :::image type="content" source="media/hybrid-cloud-extension-installation/deployed-service-mesh.png" alt-text="デプロイされたサービス メッシュ":::

## <a name="check-appliance-status"></a>アプライアンスの状態を確認する
アプライアンスの状態を確認するには、 **[Interconnect]\(相互接続\)**  >  **[Appliances]\(アプライアンス\)** を選択します。 

:::image type="content" source="media/hybrid-cloud-extension-installation/appliance-status.png" alt-text="アプライアンスの状態を確認します。":::

## <a name="next-steps"></a>次のステップ

アプライアンスの相互接続の **[Tunnel Status]\(トンネルの状態\)** が **[UP]\(アップ\)** で緑色になっている場合は、HCX を使用して Azure VMware Solution VM の移行と保護を行うことができます。 VMware のテクニカル ドキュメントの「[VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)」と「[Vmware HCX で仮想マシンを移行する](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)」を参照してください。
