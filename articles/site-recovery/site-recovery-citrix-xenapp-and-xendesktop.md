---
title: Azure Site Recovery を使用して Citrix XenDesktop と XenApp のディザスター リカバリーを設定する
description: この記事では、Azure Site Recovery を使用して、Citrix XenDesktop および XenApp デプロイのディザスター リカバリーを設定する方法について説明します。
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 90d54a8ded99dd8ab43aed688036add6aede20ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134833"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>多層 XenApp および Citrix XenDesktop デプロイのディザスター リカバリーを設定する



Citrix XenDesktop は、デスクトップとアプリケーションを提供するデスクトップ仮想化ソリューションを、任意の場所の任意のユーザーに、オンデマンド サービスとして提供します。 XenDesktop では、FlexCast 配信テクノロジによって、迅速かつ安全にアプリケーションとデスクトップをユーザーに提供することができます。
現在、Citrix XenApp には、ディザスター リカバリー機能が用意されていません。

優れたディザスター リカバリー ソリューションでは、上記の複雑なアプリケーション アーキテクチャの復旧計画をモデル化できる必要があります。また、さまざまな階層間のアプリケーション マッピングを処理するカスタム手順を追加できる必要もあります。それによって、障害が発生した場合に備えたワンクリックの確実なソリューションを提供し、RTO を短縮します。

このドキュメントでは、Hyper-V プラットフォームおよび VMware vSphere プラットフォームに、オンプレミスの Citrix XenApp デプロイのディザスター リカバリー ソリューションを構築する手順について詳しく説明します。 また、復旧計画を使用して、Azure へのテスト フェールオーバー (ディザスター リカバリー ドリル) と計画されていないフェールオーバーを実行する方法、サポートされる構成、および前提条件についても説明します。


## <a name="prerequisites"></a>前提条件

開始する前に、以下を理解していることを確認してください。

1. [Azure への仮想マシンのレプリケート](./vmware-azure-tutorial.md)
1. [復旧ネットワークの設計](./concepts-on-premises-to-azure-networking.md)方法
1. [Azure へのテスト フェールオーバーの実行](site-recovery-test-failover-to-azure.md)
1. [Azure へのフェールオーバーの実行](site-recovery-failover.md)
1. [ドメイン コントローラーのレプリケート](site-recovery-active-directory.md)方法
1. [SQL Server のレプリケート](site-recovery-sql.md)方法

## <a name="deployment-patterns"></a>デプロイ パターン

Citrix XenApp と XenDesktop ファームは、通常、次のデプロイ パターンに従います。

**デプロイ パターン**

AD DNS サーバー、SQL データベース サーバー、Citrix Delivery Controller、StoreFront サーバー、XenApp マスター (VDA)、Citrix XenApp ライセンス サーバーを使用した Citrix XenApp および XenDesktop デプロイ

![デプロイ パターン 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery のサポート

この記事の目的に基づいて、DR のセットアップには、vSphere 6.0/System Center VMM 2012 R2 によって管理される VMware 仮想マシン上の Citrix デプロイが使用されました。

### <a name="source-and-target"></a>ソースとターゲット

**シナリオ** | **セカンダリ サイトへ** | **Azure へ**
--- | --- | ---
**Hyper-V** | この記事で扱わない内容 | はい
**VMware** | この記事で扱わない内容 | はい
**物理サーバー** | この記事で扱わない内容 | はい

### <a name="versions"></a>バージョン
お客様は、XenApp コンポーネントを、Hyper-V または VMware で実行されている Virtual Machines として、または物理サーバーとしてデプロイできます。 Azure Site Recovery を使用すると、物理デプロイと仮想デプロイの両方を Azure で保護できます。
Azure では XenApp 7.7 以降がサポートされています。したがって、ディザスター リカバリーまたは移行のために Azure にフェールオーバーできるのは、こうしたバージョンのデプロイのみです。

### <a name="things-to-keep-in-mind"></a>留意事項

1. XenApp 発行アプリと XenApp 発行デスクトップを提供するための、サーバー OS マシンを使用したオンプレミス デプロイの保護と復旧がサポートされています。

2. Windows 10 などのクライアント仮想デスクトップに Desktop VDI を提供するための、デスクトップ OS マシンを使用したオンプレミス デプロイの保護と復旧はサポートされていません。 これは、Site Recovery が、デスクトップ OS のマシン復旧をサポートしていないためです。  また、一部のクライアント仮想デスクトップ オペレーティング システムでは ( Windows 7 など)、Azure のライセンスがまだサポートされていません。 Azure におけるクライアント/サーバー デスクトップのライセンスについては、[こちら](https://azure.microsoft.com/pricing/licensing-faq/)を参照してください。

3.  Azure Site Recovery では、既存の オンプレミス MCS または PVS クローンをレプリケートして保護することができません。
こうしたクローンについては、配信コントローラーから Azure RM プロビジョニングを使用して、作成し直す必要があります。

4. Azure Site Recovery を使用して NetScaler を保護することはできません。NetScaler は FreeBSD に基づいており、Azure Site Recovery では、FreeBSD OS の保護がサポートされません。 Azure へのフェールオーバー後、Azure Marketplace から新しい NetScaler アプライアンスをデプロイし、構成する必要があります。


## <a name="replicating-virtual-machines"></a>仮想マシンをレプリケートする

レプリケーションと復旧を有効にするには、Citrix XenApp デプロイの次のコンポーネントを保護する必要があります。

* AD DNS サーバーの保護
* SQL データベース サーバーの保護
* Citrix 配信コントローラーの保護
* StoreFront サーバーの保護。
* XenApp マスター (VDA) の保護
* Citrix XenApp ライセンス サーバーの保護


**AD DNS サーバーのレプリケーション**

Azure でのドメイン コントローラーのレプリケートと構成に関するガイダンスについては、「[Azure Site Recovery で Active Directory と DNS を保護する](site-recovery-active-directory.md)」を参照してください。

**SQL データベース サーバーのレプリケーション**

SQL サーバーの推奨保護オプションに関する詳細な技術ガイダンスについては、[SQL Server ディザスター リカバリーおよび Azure Site Recovery を使用した SQL Server の保護](site-recovery-sql.md)に関するページをご覧ください。

[このガイダンス](./vmware-azure-tutorial.md)に従って、Azure への他のコンポーネント仮想マシンのレプリケートを開始します。

![XenApp コンポーネントの保護](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**コンピューティングとネットワークの設定**

マシンが保護されたら ([レプリケートされたアイテム] の状態に "保護" が表示されます)、コンピューティングとネットワークの設定を構成する必要があります。
[コンピューティングとネットワーク] > [コンピューティングのプロパティ] で、Azure VM の名前とターゲットのサイズを指定できます。
必要に応じて、Azure の要件に準拠するように名前を変更します。 Azure VM に割り当てられるターゲット ネットワーク、サブネット、および IP アドレスに関する情報を表示および追加することもできます。

次のことを考慮してください。

* ターゲット IP アドレスを設定できます。 アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。 フェールオーバーで使用できないアドレスを設定した場合、フェールオーバーは機能しません。 テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。

* AD/DNS サーバーについては、オンプレミス アドレスを保持すると、Azure 仮想ネットワークに対して、DNS サーバーと同じアドレスを指定できます。

ネットワーク アダプターの数は、次に示すように、ターゲット仮想マシンに指定したサイズによって異なります。

*   ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
*   ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
* たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。
*   仮想マシンにネットワーク アダプターが複数ある場合、これらのアダプターはすべて同じネットワークに接続されます。
*   仮想マシンにネットワーク アダプターが複数ある場合は、一覧で最初に表示されるアダプターが、Azure 仮想マシンの既定のネットワーク アダプターとなります。


## <a name="creating-a-recovery-plan"></a>復旧計画の作成

XenApp コンポーネント VM のレプリケーションを有効にしたら、次は復旧計画を作成します。
復旧計画によって、同様のフェールオーバーと復旧要件を持つ仮想マシンがグループ化されます。  

**復旧計画の作成手順**

1. 復旧計画で XenApp コンポーネントの仮想マシンを追加します。
2. [復旧計画] > [+ 復旧計画] の順にクリックします。 復旧計画にわかりやすい名前を付けます。
3. VMware 仮想マシンの場合:ソースに VMware プロセス サーバー、ターゲットに Microsoft Azure、デプロイ モデルに Resource Manager をそれぞれ選択し、[アイテムの選択] をクリックします。
4. Hyper-V 仮想マシンの場合:ソースに VMM サーバー、ターゲットに Microsoft Azure 、デプロイ モデルに Resource Manager をそれぞれ選択し、[アイテムの選択] をクリックして、XenApp デプロイ VM を選択します。

### <a name="adding-virtual-machines-to-failover-groups"></a>フェールオーバー グループへの仮想マシンの追加

復旧計画をカスタマイズして、特定の起動順序、スクリプト、または手動アクションのフェールオーバー グループを追加できます。 次のグループを復旧計画に追加する必要があります。

1. フェールオーバー グループ 1:AD DNS
2. フェールオーバー グループ 2:SQL Server VM
2. フェールオーバー グループ 3:VDA マスター イメージ VM
3. フェールオーバー グループ 4:配信コントローラーと StoreFront サーバー VM のフェールオーバー


### <a name="adding-scripts-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加

復旧計画の特定のグループの前後にスクリプトを実行できます。 手動アクションを指定して、フェールオーバー中に実行することもできます。

カスタマイズした復旧計画は次のようになります。

1. フェールオーバー グループ 1:AD DNS
2. フェールオーバー グループ 2:SQL Server VM
3. フェールオーバー グループ 3:VDA マスター イメージ VM

   >[!NOTE]     
   >手動またはスクリプトのアクションが含まれる手順 4、6、および 7 は、MCS/PVS カタログがあるオンプレミス XenApp 環境にのみ適用できます。

4. グループ 3 の手動またはスクリプト アクション:マスター VDA VM をシャット ダウンします。
マスター VDA VM は、Azure へのフェールオーバー時には実行中の状態になります。 Azure のホスティングを使用して新しい MCS カタログを作成するには、マスター VDA VM を停止 (割り当て解除) 状態にする必要があります。 Azure portal から VM をシャットダウンします。

5. フェールオーバー グループ 4:配信コントローラーと StoreFront サーバー VM のフェールオーバー
6. グループ 3 手動またはスクリプト アクション 1:

    "***Azure RM ホスト接続を追加する***"

    配信コントローラー マシンで Azure のホスト接続を作成し、Azure で新しい MCS カタログをプロビジョニングします。 こちらの[記事](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/)の手順に従ってください。

7. グループ 3 手動またはスクリプト アクション 2:

    "***Azure で MCS カタログを再作成する***"

    プライマリ サイトの既存の MCS または PVS クローンは、Azure にレプリケートされません。 こうしたクローンについては、配信コントローラーから、レプリケートされたマスター VDA と Azure プロビジョニングを使用して作成し直す必要があります。 この[記事](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/)で説明されている手順に従って、Azure で MCS カタログを作成します。

![XenApp コンポーネントの復旧計画](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >必要に応じて、[その場所](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts)のスクリプトを使用して、フェールオーバーされた仮想マシンの新しい IP アドレスで DNS を更新するか、フェールオーバーされた仮想マシンでロード バランサーをアタッチできます。


## <a name="doing-a-test-failover"></a>テスト フェールオーバーの実行

[このガイダンス](site-recovery-test-failover-to-azure.md)に従って、テスト フェールオーバーを実行します。

![復旧計画](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>フェールオーバーの実行

[このガイダンス](site-recovery-failover.md)に従って、フェールオーバーを実行します。

## <a name="next-steps"></a>次のステップ

このホワイト ペーパーで Citrix XenApp と XenDesktop デプロイのレプリケートの[詳細を確認](https://aka.ms/citrix-xenapp-xendesktop-with-asr)する。 Site Recovery を使用した[他のアプリケーションのレプリケート](site-recovery-workload.md)に関するガイダンスを確認する。
