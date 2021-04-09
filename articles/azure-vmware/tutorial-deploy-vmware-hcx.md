---
title: チュートリアル - VMware HCX をデプロイして構成する
description: Azure VMware Solution プライベート クラウドのために VMware HCX ソリューションをデプロイして構成する方法について説明します。
ms.topic: tutorial
ms.date: 11/25/2020
ms.openlocfilehash: a8b089ce834d5b49e4ad32081a58e371835e8602
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488307"
---
# <a name="deploy-and-configure-vmware-hcx"></a>VMware HCX をデプロイして構成する

この記事では、Azure VMware Solution プライベート クラウドのためにオンプレミスの VMware HCX コネクタをデプロイして構成する方法について説明します。 VMware HCX を使用すると、さまざまな移行の種類を通じて、Azure VMware Solution やその他の接続されたサイトに VMware のワークロードを移行できます。 Azure VMware Solution では HCX Cloud Manager をデプロイして構成するため、オンプレミスの VMware データセンターで HCX コネクタをダウンロードし、アクティブ化して構成する必要があります。

VMware HCX Advanced コネクタは、Azure VMware Solution にあらかじめデプロイされています。 最大 3 つのサイト接続 (オンプレミスからクラウド、またはクラウドからクラウド) がサポートされます。 3 つを超えるサイト接続が必要な場合は、[VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) アドオンを有効にするために[サポート リクエスト](https://portal.azure.com/#create/Microsoft.Support)を送信してください。  

>[!TIP]
>VMware Configuration Maximums ツールでは、オンプレミスの Connector と Cloud Manager との間におけるサイト ペアの最大数は 25 と説明されていますが、ライセンスにより、HCX Advanced Edition では 3 に、HCX Enterprise Edition では 10 に制限されます。

>[!NOTE]
>VMware HCX Enterprise は、Azure VMware Solution でプレビュー サービスとして使用できます。 これは無料で、プレビュー サービスの使用条件が適用されます。 VMware HCX Enterprise サービスが一般提供されると、課金に切り替わるという通知が 30 日前に届きます。 サービスを無効にするかオプトアウトするオプションも用意されます。 VMware HCX Enterprise から VMware HCX Advanced への単純なダウングレード パスはありません。 ダウングレードする場合は、再デプロイする必要があり、ダウンタイムが発生します。

まず、「[開始する前に](#before-you-begin)」、「[ソフトウェア バージョンの要件](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)」、および「[前提条件](#prerequisites)」を確認してください。 

では、以下のために必要なすべての手順について説明します。

> [!div class="checklist"]
> * VMware HCX コネクタ OVA をダウンロードする。
> * オンプレミスの VMware HCX OVA (VMware HCX コネクタ) をデプロイする。
> * VMware HCX コネクタをアクティブにする。
> * オンプレミスの VMware HCX コネクタを Azure VMware Solution HCX Cloud Manager とペアリングする。
> * 相互接続 (ネットワーク プロファイル、コンピューティング プロファイル、およびサービス メッシュ) を構成する。
> * アプライアンスの状態を確認し、移行が可能なことを検証して、セットアップを完了する。

完了したら、この記事の最後に示されている、推奨される次の手順に進みます。  

## <a name="before-you-begin"></a>開始する前に

デプロイを準備する際には、以下の VMware ドキュメントを確認することをお勧めします。

* [VMware HCX ユーザー ガイド](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [VMware HCX での仮想マシンの移行](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [VMware HCX のデプロイの考慮事項](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware ブログ シリーズ - クラウド移行](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [VMware HCX に必要なネットワーク ポート](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>前提条件

VMware HCX Enterprise を使用する場合は、Azure VMware Solution のサポート チャネルを介してアクティブ化を要求したことを確認してください。


### <a name="on-premises-vsphere-environment"></a>オンプレミスの vSphere 環境

オンプレミスの vSphere 環境 (ソース環境) が[最小要件](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)を満たしていることを確認します。 

### <a name="network-and-ports"></a>ネットワークとポート

* [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) は、オンプレミスと Azure VMware Solution プライベート クラウド ExpressRoute 回線との間に構成されます。

* オンプレミスのコンポーネントと Azure VMware Solution プライベートの間の通信に[必要なすべてのポート](https://ports.vmware.com/home/VMware-HCX)を開きます。

### <a name="ip-addresses"></a>IP アドレス

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>VMware HCX コネクタ OVA をダウンロードする

仮想アプライアンスをオンプレミスの vCenter にデプロイする前に、VMware HCX コネクタ OVA をダウンロードする必要があります。  

1. Azure portal で、Azure VMware Solution のプライベート クラウドを選択します。 

1. **[管理]**  >  **[接続]** の順に選択し、 **[HCX]** タブを選択して、Azure VMware Solution HCX Manager の IP アドレスを特定します。 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="VMware HCX IP アドレスのスクリーンショット。" lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. **[管理]**  >  **[ID]** を選択します。 

   プライベート クラウドの vCenter および NSX-T Manager の URL とユーザーの資格情報が表示されます。

   > [!TIP]
   > VCenter のパスワードは、プライベート クラウドの設定時に定義されました。 これは、Azure VMware Solution HCX Manager にサインインするときに使用するパスワードと同じです。 **[Generate a new password]\(新しいパスワードを生成する\)** を選択して、新しい vCenter および NSX-T のパスワードを生成できます。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報を表示する。" border="true":::


1. ブラウザー ウィンドウを開き、**cloudadmin\@vsphere.local** ユーザー資格情報を使用して、`https://x.x.x.9` のポート 443 で Azure VMware Solution HCX Manager にサインインします

1. **[Administration]\(管理\)**  >  **[System Updates]\(システム更新\)** を選択し、 **[Request Download Link]\(ダウンロード リンクを要求\)** を選択します。

1. VMware HCX コネクタ OVA ファイルをダウンロードするための任意のオプションを選択します。

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>VMware HCX コネクタ OVA をオンプレミスにデプロイする

1. オンプレミスの vCenter で、VMware HCX コネクタをオンプレミスの vCenter にデプロイするための [OVF テンプレート](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)を選択します。 

   > [!TIP]
   > 前のセクションでダウンロードした OVA ファイルを選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. 名前と場所を選択し、VMware HCX コネクタをデプロイするリソースまたはクラスターを選択します。 次に、詳細情報と必要なリソースを確認し、 **[次へ]** を選択します。  

1. ライセンス条項を確認します。 同意する場合は、必要なストレージとネットワークを選択します。次に、 **[次へ]** を選択します。

1. ストレージを選択して、 **[次へ]** を選択します。

1. 前に [IP アドレスの前提条件](#ip-addresses)に関するセクションで定義した VMware HCX 管理ネットワーク セグメントを選択します。  次に、 **[次へ]** を選択します。

1. **[テンプレートのカスタマイズ]** で必要なすべての情報を入力し、 **[次へ]** を選択します。 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="テンプレートをカスタマイズするためのボックスのスクリーンショット。" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. 構成を確認してから **[完了]** を選択して、VMware HCX コネクタ OVA をデプロイします。
   
   > [!IMPORTANT]
   > 仮想アプライアンスを手動で有効にする必要があります。  電源投入後、10 分から 15 分待ってから、次の手順に進みます。

この手順全体の概要については、動画「[Azure VMware Solution: HCX アプライアンスのデプロイ](https://www.youtube.com/embed/UKmSTYrL6AY)」を参照してください。 


## <a name="activate-vmware-hcx"></a>VMware HCX をアクティブにする

VMware HCX コネクタ OVA をオンプレミスにデプロイし、アプライアンスを起動したら、アクティブにすることができます。 まず、ライセンス キーを Azure VMware Solution ポータルから取得する必要があります。

1. Azure VMware Solution ポータルで、 **[管理]**  >  **[接続]** の順に移動し、 **[HCX]** タブを選択してから **[追加]** を選択します。

1. **管理者** の資格情報を使用して、`https://HCXManagerIP:9443` でオンプレミスの VMware HCX Manager にサインインし、サインインします。 

   > [!TIP]
   > **管理者** ユーザーのパスワードは、VMware HCX Manager OVA ファイルのデプロイ中に定義しました。

   > [!IMPORTANT]
   > VMware HCX Manager の IP アドレスと共に、必ず `9443` のポート番号を含めてください。

1. **[Licensing]\(ライセンス\)** で、 **[HCX Advanced Key]\(HCX Advanced キー\)** にキーを入力し、 **[Activate]** \(アクティブ化\) を選択します。  
   
    > [!NOTE]
    > VMware HCX Manager では、インターネット アクセスが開かれているか、プロキシが構成されている必要があります。

1. **[Datacenter Location]\(データセンターの場所\)** で、VMware HCX Manager をオンプレミスにインストールする最も近い場所を指定します。 その後 **[続行]** を選択します。

1. **[System Name]\(システム名\)** で名前を変更するか既定値をそのまま使用し、 **[続行]** を選択します。
   
1. **[Yes, Continue]\(はい、続行します\)** を選択します。

1. **[VCenter の接続]** で、お使いの vCenter サーバーの FQDN または IP アドレスと適切な資格情報を入力し、 **[続行]** を選択します。
   
   > [!TIP]
   > vCenter サーバーは、データセンターで VMware HCX コネクタをデプロイした場所です。

1. **[Configure SSO/PSC]\(SSO/PSC の構成\)** で、Platform Services Controller の FQDN または IP アドレスを入力してから、 **[Continue]\(続行\)** を選択します。
   
   > [!NOTE]
   > 一般に、これは vCenter の FQDN または IP アドレスと同じです。

1. 入力した情報が正しいことを確認し、 **[再起動]** を選択します。
    
   > [!NOTE]
   > 再起動後、次の手順の操作が求められるまでに待機時間が生じます。

サービスの再起動後は、表示される画面に vCenter が緑色で示されます。 vCenter と SSO のどちらにも、適切な構成パラメーターを指定する必要があります。それらは、前の画面と同じにする必要があります。

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="vCenter の状態が緑色のダッシュボードのスクリーンショット。" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

この手順全体の概要については、動画「[Azure VMware Solution: HCX をアクティブにする](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720)」を参照してください。

   > [!IMPORTANT]
   > HCX Advanced と HCX Enterprise のどちらを使用しているかにかかわらず、VMware の[サポート技術情報の記事 81558](https://kb.vmware.com/s/article/81558) からパッチをインストールする必要がある場合があります。 

## <a name="configure-the-vmware-hcx-connector"></a>VMware HCX コネクタを構成する

これで、サイトのペアリングを追加し、ネットワークとコンピューティングのプロファイルを作成し、移行、ネットワーク拡張、ディザスター リカバリーなどのサービスを有効にする準備ができました。 

### <a name="add-a-site-pairing"></a>サイトのペアリングを追加する

Azure VMware Solution で、VMware HCX Cloud Manager をデータセンターの VMware HCX Manager コネクタと接続またはペアリングできます。 

1. オンプレミスの vCenter にサインインし、 **[ホーム]** の下の **[HCX]** を選択します。

1. **[Infrastructure]\(インフラストラクチャ\)** で、 **[Site Pairing]\(サイトのペアリング\)** を選択し、 **[Connect To Remote Site]\(リモート サイトへの接続\)** オプション (画面の中央) を選択します。 

1. 前にメモしておいた Azure VMware Solution HCX Cloud Manager の URL または IP アドレス (`https://x.x.x.9`)、Azure VMware Solution の cloudadmin@vsphere.local のユーザー名、およびパスワードを入力します。 次に、 **[接続]\(Connect\)** を選択します。

   > [!NOTE]
   > サイト ペアを正常に確立するには、次のようにします。
   > * VMware HCX コネクタがポート 443 経由で HCX Cloud Manager IP にルーティングできる必要があります。
   >
   > * vCenter へのサインインに使用したのと同じパスワードを使用します。 このパスワードは、最初のデプロイ画面で定義したものです。

   Azure VMware Solution の VMware HCX Cloud Manager とオンプレミスの VMware HCX コネクタが接続 (ペアリング) されたことを示す画面が表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Azure VMware Solution の HCX Manager と VMware HCX コネクタのペアリングを示すスクリーンショット。":::

この手順全体の概要については、動画「[Azure VMware Solution: HCX サイトのペアリング](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720)」を参照してください。

### <a name="create-network-profiles"></a>ネットワーク プロファイルを作成する

VMware HCX コネクタによって、複数の IP セグメントを必要とする (自動化) 仮想アプライアンスのサブセットがデプロイされます。 ネットワーク プロファイルを作成するときには、IP セグメントを使用します。これらは、[VMware HCX ネットワーク セグメントのデプロイ前準備と計画段階](production-ready-deployment-steps.md#vmware-hcx-network-segments)の間に特定したものです。

4 つのネットワーク プロファイルを作成します。

   - 管理
   - vMotion
   - レプリケーション
   - アップリンク

1. **[インフラストラクチャ]** で、 **[Interconnect]\(相互接続\)**  >  **[Multi-Site Service Mesh]\(マルチサイト サービス メッシュ\)**  >  **[Network Profiles]\(ネットワーク プロファイル\)**  >  **[Create Network Profile]\(ネットワーク プロファイルの作成\)** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="ネットワーク プロファイルの作成を開始するための選択のスクリーンショット。" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. ネットワーク プロファイルごとに、ネットワークとポート グループを選択し、名前を指定して、セグメントの IP プールを作成します。 **[作成]** を選択します。 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="新しいネットワーク プロファイルの詳細のスクリーンショット。":::

この手順全体の概要については、動画「[Azure VMware Solution: HCX ネットワーク プロファイル](https://www.youtube.com/embed/O0rU4jtXUxc)」を参照してください。


### <a name="create-a-compute-profile"></a>コンピューティング プロファイルを作成する

1. **[Infrastructure]\(インフラストラクチャ\)** で、 **[Interconnect]\(相互接続\)**  >  **[Compute Profiles]\(コンピューティング プロファイル\)**  >  **[Create Compute Profile]\(コンピューティング プロファイルの作成\)** の順に選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="コンピューティング プロファイルの作成を開始するための選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. プロファイルの名前を入力し、 **[続行]** を選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="コンピューティング プロファイル名の入力と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 有効にするサービス (移行、ネットワーク拡張機能、ディザスター リカバリーなど) を選択した後、 **[Continue]\(続行\)** を選択します。
  
   > [!NOTE]
   > 通常、ここでは何も変更されません。

1. **[Select Service Resources]\(サービス リソースの選択\)** で、選択した VMware HCX サービスを有効にするためのサービス リソース (クラスター) を 1 つ以上選択します。  

1. オンプレミスのデータセンター内にクラスターが表示されたら、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="選択されたサービス リソースと [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. **[Select Datastore]\(データストアの選択\)** で、VMware HCX 相互接続アプライアンスをデプロイするためのデータストア ストレージ リソースを選択します。 その後 **[続行]** を選択します。

   複数のリソースを選択すると、VMware HCX では容量が使い果たされるまで、最初に選択されたリソースが使用されます。   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="選択されたデータ ストレージ リソースと [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. **[Select Management Network Profile]\(管理ネットワーク プロファイルの選択\)** で、前の手順で作成した管理ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="管理ネットワーク プロファイルの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. **[Select Uplink Network Profile]\(アップリンク ネットワーク プロファイルの選択\)** で、前の手順で作成したアップリンク ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="アップリンク ネットワーク プロファイルの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. **[Select vMotion Network Profile]\(vMotion ネットワーク プロファイルの選択\)** で、前の手順で作成した vMotion ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="vMotion ネットワーク プロファイルの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. **[Select vSphere Replication Network Profile]\(vSphere レプリケーション ネットワーク プロファイルの選択\)** で、前の手順で作成したレプリケーション ネットワーク プロファイルを選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="レプリケーション ネットワーク プロファイルの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. **[Select Distributed Switches for Network Extensions]\(ネットワーク拡張機能の分散スイッチの選択\)** で、レイヤー 2 拡張ネットワーク上の Azure VMware Solution に移行される仮想マシンが含まれるスイッチを選択します。 その後 **[続行]** を選択します。

   > [!NOTE]
   > レイヤー 2 拡張ネットワーク上の仮想マシンを移行しない場合は、この手順を省略できます。
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="分散仮想スイッチの選択と [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 接続ルールを確認し、 **[Continue]\(続行\)** を選択します。  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="接続ルールと [Continue]\(続行\) ボタンを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. **[Finish]\(完了\)** を選択して、コンピューティング プロファイルを作成します。


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="コンピューティング プロファイル情報を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

この手順全体の概要については、動画「[Azure VMware Solution: コンピューティング プロファイル](https://www.youtube.com/embed/e02hsChI3b8)」を参照してください。

### <a name="create-a-service-mesh"></a>サービス メッシュを作成する

ここで、オンプレミスと Azure VMware Solution プライベート クラウドの間にサービス メッシュを構成します。



> [!NOTE]
> Azure VMware Solution を使用してサービス メッシュを正常に確立するには:
>
> * オンプレミスの VMware HCX コネクタの "アップリンク" ネットワーク プロファイル アドレスと Azure VMware Solution HCX クラウドの "アップリンク" ネットワーク プロファイル アドレスの間で、ポート UDP 500/4500 を開きます。
>
> * [VMware HCX の必須ポート](https://ports.vmware.com/home/VMware-HCX)を確認してください。

1. **[インフラストラクチャ]** で、 **[相互接続]**  >  **[サービス メッシュ]**  >  **[サービス メッシュの作成]** と選択します。    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="サービス メッシュの作成を開始するための選択のスクリーンショット。" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 事前にデータが格納されたサイトを確認してから、 **[続行]** を選択します。 

   > [!NOTE]
   > これが最初のサービス メッシュ構成である場合は、この画面を変更する必要はありません。  

1. ドロップダウン リストでソースとリモートのコンピューティング プロファイルを選択してから、 **[Continue]\(続行\)** を選択します。  

   選択により、VM で VMware HCX サービスを使用できるリソースが定義されます。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="ソース コンピューティング プロファイルの選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="リモート コンピューティング プロファイルの選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 有効にするサービスを確認したら、 **[Continue]\(続行\)** を選択します。  

1. **[Advanced Configuration - Override Uplink Network profiles]\(詳細構成 - アップリンク ネットワーク プロファイルのオーバーライド\)** で、 **[Continue]\(続行\)** を選択します。  

   アップリンク ネットワーク プロファイルにより、リモート サイトの相互接続アプライアンスに到達できるネットワークに接続されます。  
  
1. **[詳細構成 - ネットワーク拡張機能アプライアンスのスケールアウト]** で、内容を見直して **[続行]** を選択します。 

   アプライアンスあたり最大 8 個の VLAN を設定できますが、もう 1 つのアプライアンスをデプロイしてさらに 8 個の VLAN を追加できます。 また、追加のアプライアンスに対処するための IP 空間が必要です。アプライアンスごとに 1 つの IP アドレスが必要になります。  詳細については、「[VMware HCX Configuration Limits (VMware HCX の構成の制限)](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0)」を参照してください。
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="VLAN 数を増やす場所を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. **[Advanced Configuration - Traffic Engineering]\(詳細構成 - トラフィック エンジニアリング\)** で、内容を見直して必要と思われる変更を加えたら、 **[Continue]\(続行\)** を選択します。

1. トポロジのプレビューを確認し、 **[Continue]\(続行\)** を選択します。

1. このサービス メッシュのためにわかりやすい名前を入力し、 **[完了]** を選択して完了します。  

1. **[タスクを表示]** を選択してデプロイを監視します。 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="タスクを表示するためのボタンを示すスクリーンショット。":::

   サービス メッシュのデプロイが正常に完了すると、サービスが緑色で表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="サービスの緑色のインジケーターを示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. アプライアンスの状態を調べてサービス メッシュの正常性を確認します。 

1. **[Interconnect]\(相互接続\)**  >  **[Appliances]\(アプライアンス\)** の順に選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="アプライアンスの状態を確認するための選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

この手順全体の概要については、動画「[Azure VMware Solution: サービス メッシュ](https://www.youtube.com/embed/COY3oIws108)」を参照してください。

### <a name="optional-create-a-network-extension"></a>(省略可能) ネットワーク拡張機能を作成する

オンプレミス環境から Azure VMware Solution までネットワークを拡張する場合は、これらの手順に従います。

1. **[Services]\(サービス\)** で、 **[Network Extension]\(ネットワーク拡張機能\)**  >  **[Create a Network Extension]\(ネットワーク拡張機能の作成\)** の順に選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="ネットワーク拡張機能の作成を開始するための選択を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Azure VMware Solution へと拡張する各ネットワークを選択し、 **[Next]\(次へ\)** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="ネットワークの選択を示すスクリーンショット。":::

1. 拡張しようとしているネットワークごとに、オンプレミス ゲートウェイの IP を入力してから、 **[送信]** を選択します。 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="ゲートウェイ IP アドレスの入力を示すスクリーンショット。":::

   ネットワークの拡張が完了するまでに数分かかります。 完了すると、状態が **[Extension complete]\(拡張完了\)** に変わります。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="拡張完了の状態を示すスクリーンショット。" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

この手順全体の概要については、ビデオ「[Azure VMware Solution: ネットワーク拡張機能](https://www.youtube.com/embed/gYR0nftKui0)」を参照してください。


## <a name="next-steps"></a>次のステップ

HCX の相互接続トンネルの状態が **[UP]\(アップ\)** で緑色である場合は、VMware HCX を使用して、Azure VMware Solution VM の移行と保護を行うことができます。 Azure VMware Solution では、ワークロードの移行がサポートされています (ネットワーク拡張機能を使用または不使用)。 vSphere 環境のワークロードも、オンプレミスにネットワークを作成し、それらのネットワークに VM をデプロイして、移行できます。  

HCX の使用の詳細については、VMware のテクニカル ドキュメントを参照してください。

* [VMware HCX のドキュメント](https://docs.vmware.com/en/VMware-HCX/index.html)
* [VMware HCX での仮想マシンの移行](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [HCX の必須ポート](https://ports.vmware.com/home/VMware-HCX)
