---
title: Azure Site Recovery を使用して SAP NetWeaver のディザスター リカバリーを設定する
description: Azure Site Recovery を使用して SAP NetWeaver のディザスター リカバリーを設定する方法について説明します。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: e639762cd1adb7bbbc3fb2ec31f4ce52710e46f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711945"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>多層 SAP NetWeaver アプリ デプロイのディザスター リカバリーを設定する

多くの場合、大規模および中規模の SAP デプロイには、何らかの形式のディザスター リカバリー ソリューションを使用します。 SAP のようなアプリケーションに移行するコア ビジネス プロセスが増えているため、堅牢でテスト可能なディザスター リカバリー ソリューションの重要性は高まっています。 Azure Site Recovery は SAP アプリケーションに対するテストを実施したうえで統合済みです。 Site Recovery はほとんどのオンプレミス ディザスター リカバリー ソリューションよりも優れた機能で、競合するソリューションよりも総保有コストが低くなります。

Site Recovery を使用すると、次のことができます。
* コンポーネントを Azure にレプリケートして、オンプレミスで実行される SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
* コンポーネントを他の Azure データセンターにレプリケートして、Azure で実行される SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
* Site Recovery を使用して SAP デプロイを Azure に移行することで、クラウド移行を簡略化します。
* SAP アプリケーションをテストするためのオンデマンドの運用クローンを作成して、SAP プロジェクトのアップグレード、テスト、およびプロトタイプ作成を簡略化します。

[Azure Site Recovery](site-recovery-overview.md) を使用して SAP NetWeaver アプリケーションのデプロイを保護することができます。 この記事では、Site Recovery を使用して別の Azure データセンターにレプリケートするときに、Azure 上で 3 層の SAP NetWeaver デプロイを保護するためのベスト プラクティスを取り上げています。 この記事では、サポートされるシナリオと構成、およびテスト フェールオーバー (ディザスター リカバリーの訓練) と実際のフェイル オーバーを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事の内容を学習するには、次のタスクの実行方法を知っている必要があります。

* [仮想マシンを Azure にレプリケートする](./azure-to-azure-tutorial-enable-replication.md)
* [復旧ネットワークを設計する](./azure-to-azure-about-networking.md)
* [Azure へのテスト フェールオーバーを実行する](./azure-to-azure-tutorial-dr-drill.md)
* [Azure へのフェールオーバーを実行する](site-recovery-failover.md)
* [ドメイン コントローラーをレプリケートする](site-recovery-active-directory.md)
* [SQL Server インスタンスをレプリケートする](site-recovery-sql.md)

## <a name="supported-scenarios"></a>サポートされるシナリオ

Site Recovery を使用して、次のシナリオのディザスター リカバリー ソリューションを実装できます。
* ある Azure データセンターで実行されている SAP システムを、別の Azure データセンターにレプリケートしています (Azure 間のディザスター リカバリー)。 
   詳細については、「[Azure から Azure へのレプリケーション アーキテクチャ](./azure-to-azure-architecture.md)」をご覧ください。
* オンプレミスの VMware (または物理) サーバーで実行されている SAP システムがあります。 また、SAP システムを Azure データセンターのディザスター リカバリー サイトにレプリケートしています (VMware から Azure へのディザスター リカバリー)。 
   このシナリオでは、いくつかの追加コンポーネントが必要です。 詳細については、「[VMware から Azure へのレプリケーション アーキテクチャ](./vmware-azure-architecture.md)」をご覧ください。
* SAP システムをオンプレミスの Hyper-V で実行しています。 また、SAP システムを Azure データセンターのディザスター リカバリー サイトにレプリケートしています (Hyper-V から Azure へのディザスター リカバリー)。
   このシナリオでは、いくつかの追加コンポーネントが必要です。 詳細については、「[Hyper-V から Azure へのレプリケーション アーキテクチャ](./hyper-v-azure-architecture.md)」をご覧ください。

この記事では、**Azure から Azure** へのディザスター リカバリー シナリオを使用します。 このシナリオでは、Site Recovery の SAP ディザスター リカバリー機能について説明します。 Site Recovery レプリケーションはアプリケーション固有ではないため、説明されているプロセスの他のシナリオへの適用も期待できます。

### <a name="required-foundation-services"></a>必要な基礎サービス
この記事で説明するシナリオでは、次の基盤サービスがデプロイされます。
* Azure ExpressRoute または Azure VPN Gateway
* Azure で実行されている少なくとも 1 つの Azure Active Directory ドメイン コントローラーと DNS サーバー

Site Recovery をデプロイする前に、このインフラストラクチャを確立することをお勧めします。

## <a name="reference-sap-application-deployment"></a>参照用 SAP アプリケーションのデプロイ

この参照用アーキテクチャは、高可用性を備えた Azure の Windows 環境で SAP NetWeaver を実行しています。 このアーキテクチャは特定の仮想マシン (VM) サイズでデプロイされ、お客様の組織のニーズに合わせて変更できます。

![一般的な SAP デプロイ パターンの図](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>ディザスター リカバリーの考慮事項

ディザスター リカバリーを確保するために、セカンダリ リージョンにフェールオーバーできる必要があります。 各層では、さまざまな戦略を利用して、ディザスター リカバリーの保護を提供しています。

#### <a name="vms-running-sap-web-dispatcher-pools"></a>SAP Web Dispatcher プールを実行する VM

Web Dispatcher コンポーネントは、SAP アプリケーション サーバー間の SAP トラフィックのロード バランサーとして機能します。 Web Dispatcher コンポーネントの高可用性は、Azure Load Balancer で並列 Web Dispatcher セットアップを実装することで実現します。 Web Dispatcher は、バランサー プール内で使用可能な Web Dispatcher での HTTP (S) トラフィック分散のために、ラウンドロビン構成を使用します。

#### <a name="vms-running-application-servers-pools"></a>アプリケーション サーバー プールを実行する VM
ABAP アプリケーション サーバーのログイン グループの管理は、SMLG トランザクションで行われます。 この場合、セントラル サービスのメッセージ サーバー内の負荷分散機能を使って、SAPGUI および RFC トラフィックの SAP アプリケーション サーバーのプールにワークロードが分散されます。 この管理をレプリケートするには、Site Recovery を使用します。

#### <a name="vms-running-sap-central-services-clusters"></a>SAP セントラル サービス クラスターを実行する VM
この参照アーキテクチャでは、アプリケーション層の VM でセントラル サービスが実行されます。 セントラル サービスは、単一の VM で単一障害点となる可能性があります。 一般的なデプロイと高可用性は求められません。

高可用性ソリューションを実装するには、共有ディスク クラスターまたはファイル共有クラスターのいずれかを使用できます。 共有ディスク クラスター用に VM を構成するには、Windows Server フェールオーバー クラスターを使用します。 クラウド監視を、クォーラム監視として使用することをお勧めします。

 > [!NOTE]
 > Site Recovery ではクラウド監視はレプリケートされないため、ディザスター リカバリー リージョンにクラウド監視をデプロイすることをお勧めします。

フェールオーバー クラスター環境をサポートするために、[SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) がクラスター共有ボリューム機能を実行します。 この機能では、SIOS DataKeeper Cluster が、クラスター ノードで所有する独立したディスクをレプリケートします。 Azure では共有ディスクがネイティブでサポートされていないため、SIOS 提供のソリューションが必要です。

ファイル共有クラスターを実装することで、クラスタリングを処理することもできます。 [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) では、UNC パスを介して /sapmnt グローバル ディレクトリにアクセスするための、セントラル サービス デプロイ パターンを最近変更しました。 ただし、/sapmnt UNC 共有の高可用性は確保することをお勧めします。 セントラル サービス インスタンスを確認することができます。 Windows Server フェールオーバー クラスターと、Windows Server 2016 のスケールアウト ファイル サーバー (SOFS) および記憶域スペース ダイレクト (S2D) 機能を使用します。

 > [!NOTE]
 > 現在 Site Recovery でサポートされるのは、記憶域スペース ダイレクトと SIOS Datakeeper のパッシブ ノードを使用する仮想マシンのクラッシュ整合性ポイント レプリケーションのみです。


## <a name="more-disaster-recovery-considerations"></a>ディザスター リカバリーのその他の考慮事項

Site Recovery を使用することで、複数の Azure リージョンにまたがるフル SAP デプロイを対象としたフェールオーバーのオーケストレーションを行うことができます。
ディザスター リカバリーの設定手順は次のとおりです。

1. 仮想マシンのレプリケート
1. 復旧ネットワークを設計する
1. ドメイン コントローラーをレプリケートする
1. データベース層をレプリケートする
1. テスト フェールオーバーを実行する
1. フェールオーバーを実行する

この例で使用される各層のディザスター リカバリーの推奨事項は次のとおりです。

 **SAP 層** | **推奨**
 --- | ---
**SAP Web Dispatcher プール** |  Site Recovery を使ったレプリケーション 
**SAP アプリケーション サーバー プール** |  Site Recovery を使ったレプリケーション 
**SAP セントラル サービス クラスター** |  Site Recovery を使ったレプリケーション 
**Active Directory 仮想マシン** |  Active Directory レプリケーションを使用する 
**SQL Database サーバー** |  SQL Server の Always On レプリケーションを使用する

## <a name="replicate-virtual-machines"></a>仮想マシンのレプリケート

Azure ディザスター リカバリー データ センターへのすべての SAP アプリケーション仮想マシンのレプリケートを開始するには、「[仮想マシンを Azure にレプリケートする](./azure-to-azure-tutorial-enable-replication.md)」セクションの手順に従います。

* Active Directory と DNS の保護については、[Active Directory と DNS の保護の方法](site-recovery-active-directory.md)を学習してください。

* SQL Server 上で動作するデータベース層の保護については、[SQL Server の保護の方法](site-recovery-sql.md)を学習してください。

## <a name="networking-configuration"></a>ネットワーク構成

静的 IP アドレスを使用する場合は、仮想マシンに割り当てる IP アドレスを指定できます。 IP アドレスを設定するには、 **[コンピューティングとネットワーク] の設定** >  **[ネットワーク インターフェイス カード]** に移動します。

![Site Recovery のネットワーク インターフェイス カードのウィンドウで、プライベート IP アドレスを設定する方法を示したスクリーンショット](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>復旧計画の作成

復旧計画では、フェールオーバー時における多層アプリケーション内の各種階層の順序付けがサポートされます。 順序付けは、アプリケーションの一貫性の保守に役立ちます。 多層 Web アプリケーションの復旧計画を作成する際には、[復旧計画の作成](site-recovery-create-recovery-plans.md)に関するページで説明されている手順を完了します。

### <a name="add-virtual-machines-to-failover-groups"></a>フェールオーバー グループへの仮想マシンの追加

1. アプリケーション サーバー、Web Dispatcher、SAP セントラル サービス VM を追加して復旧計画を作成します。
1. **[カスタマイズ]** を選択して VM をグループ化します。 既定では、すべての VM がグループ 1 のメンバーです。

### <a name="add-scripts-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加
お使いのアプリケーションを正常に機能させるには、Azure の仮想マシンに対して一定の操作を実行することが必要な場合があります。 フェールオーバー後、またはテスト フェールオーバー中に、これらの操作を実行します。 フェールオーバー後の操作は一部自動化することもできます。 たとえば、対応するスクリプトを復旧計画に追加することで、DNS エントリを更新したり、バインドと接続を変更します。

**[Azure に配置する]** を選択すると、Azure Automation アカウントによく使われる Site Recovery のスクリプトをデプロイできます。 公開されているスクリプトを使用する場合は、そのスクリプトのガイダンスに従ってください。

[![Azure へのデプロイ](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. SQL Server 可用性グループをフェールオーバーするための前処理スクリプトをグループ 1 に追加します。 サンプル スクリプトで公開されている ASR-SQL-FailoverAG スクリプトを使用してください。 スクリプトのガイダンスに従い、適宜、スクリプトの内容を変更します。
1. フェールオーバー後の Web 層仮想マシン (グループ 1) にロード バランサーをアタッチするための後処理スクリプトを追加します。 サンプル スクリプトで公開されている ASR-AddSingleLoadBalancer スクリプトを使用してください。 スクリプトのガイダンスに従い、必要に応じてスクリプトの内容を変更します。

![SAP 復旧計画](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1. Azure Portal で､Recovery Service コンテナーを選択します。
1. SAP アプリケーション用に作成した復旧計画を選択します。
1. **[テスト フェールオーバー]** を選択します。
1. テスト フェールオーバー プロセスを開始するには、復旧ポイントと Azure 仮想ネットワークを選択します。
1. セカンダリ環境が立ち上がったら、検証を実行します。
1. 検証が完了したら、フェールオーバー環境をクリーニングするために、 **[テスト フェールオーバーのクリーンアップ]** を選択します。

詳しくは、「[Site Recovery での Azure へのフェールオーバーをテストする](site-recovery-test-failover-to-azure.md)」をご覧ください。

## <a name="run-a-failover"></a>フェールオーバーの実行

1. Azure Portal で､Recovery Service コンテナーを選択します。
1. SAP アプリケーション用に作成した復旧計画を選択します。
1. **[フェールオーバー]** を選択します。
1. フェールオーバー プロセスを開始するには、復旧ポイントを選択します。

詳しくは、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* Site Recovery を使用して、SAP NetWeaver デプロイ用のディザスター リカバリー ソリューションを構築する方法を確認してください。 [SAP NetWeaver: Site Recovery を使用したディザスター リカバリー ソリューションの構築](/samples/browse/?redirectedfrom=TechNet-Gallery)」に関するダウンロード可能なホワイト ペーパーをご覧ください。 このホワイトペーパーでは、さまざまな SAP アーキテクチャに関する推奨事項について説明しています。 SAP on Azure でサポートされているアプリケーションと VM の種類を確認できます。 ディザスター リカバリー ソリューションをテストするためのプラン オプションもあります。
* Site Recovery を使用した[他のワークロードのレプリケート](site-recovery-workload.md)に関する記事をご覧ください。