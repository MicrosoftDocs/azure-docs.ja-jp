---
title: Azure Site Recovery を使用して多層 SAP NetWeaver アプリ デプロイのディザスター リカバリーを設定する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して SAP NetWeaver アプリケーション デプロイのディザスター リカバリーを設定する方法について説明します。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 68efc039c5de5d7f61b7ce34e74c6c2cf4bad027
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670878"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>多層 SAP NetWeaver アプリ デプロイのディザスター リカバリーを設定する

多くの場合、大規模および中規模の SAP デプロイには、何らかの形式のディザスター リカバリー ソリューションを使用します。 SAP のようなアプリケーションに移行するコア ビジネス プロセスが増えているため、堅牢でテスト可能なディザスター リカバリー ソリューションの重要性は高まっています。 Azure Site Recovery は SAP アプリケーションに対するテストを実施したうえで統合済みです。 また、Site Recovery はほとんどのオンプレミス ディザスター リカバリー ソリューションよりも優れた機能で、競合するソリューションよりも低い総保有コスト (TCO) です。

Site Recovery を使用すると、次のことができます。
* コンポーネントを Azure にレプリケートして、**オンプレミスで実行される SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にする**。
* コンポーネントを他の Azure データセンターにレプリケートして、**Azure で実行される SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にする**。
* Site Recovery を使用して SAP デプロイを Azure に移行することで、**クラウド移行を簡略化**します。
* SAP アプリケーションをテストするためのオンデマンドの運用クローンを作成して、**SAP プロジェクトのアップグレード、テスト、およびプロトタイプ作成を簡略化する**。

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して SAP NetWeaver アプリケーションのデプロイを保護する方法について説明します。 この記事では、Site Recovery を使用して別の Azure データセンターにレプリケートすることで、Azure 上で 3 層の SAP NetWeaver デプロイを保護するためのベスト プラクティスを取り上げています。 サポートされるシナリオと構成、およびテスト フェールオーバー (ディザスター リカバリーの訓練) と実際のフェイル オーバーを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件
この記事の内容を学習するには、次のタスクの実行方法を知っている必要があります。

* [仮想マシンを Azure にレプリケートする](azure-to-azure-walkthrough-enable-replication.md)
* [復旧ネットワークを設計する](site-recovery-azure-to-azure-networking-guidance.md)
* [Azure へのテスト フェールオーバーを実行する](azure-to-azure-walkthrough-test-failover.md)
* [Azure へのフェールオーバーを実行する](site-recovery-failover.md)
* [ドメイン コントローラーをレプリケートする](site-recovery-active-directory.md)
* [SQL Server のレプリケート](site-recovery-sql.md)

## <a name="supported-scenarios"></a>サポートされるシナリオ
Site Recovery を使用して、次のシナリオのディザスター リカバリー ソリューションを実装できます。
* ある Azure データセンターで実行されている SAP システムを、別の Azure データセンターにレプリケートします (Azure 間の ディザスター リカバリー)。 詳細については、「[Azure から Azure へのレプリケーション アーキテクチャ](https://aka.ms/asr-a2a-architecture)」をご覧ください。
* VMware (または物理) サーバーのオンプレミス上で実行されている SAP システムを、Azure データセンターのディザスター リカバリー サイトにレプリケートします (VMware から Azure へのディザスター リカバリー)。 このシナリオでは、いくつかの追加コンポーネントが必要です。 詳細については、「[VMware から Azure へのレプリケーション アーキテクチャ](https://aka.ms/asr-v2a-architecture)」をご覧ください。
* Hyper-V のオンプレミス上で実行されている SAP システムを、Azure データセンターのディザスター リカバリー サイトにレプリケートします (Hyper-V から Azure へのディザスター リカバリー)。 このシナリオでは、いくつかの追加コンポーネントが必要です。 詳細については、「[Hyper-V から Azure へのレプリケーション アーキテクチャ](https://aka.ms/asr-h2a-architecture)」をご覧ください。

この記事では、**Azure 間**のディザスター リカバリー シナリオを使用して Site Recovery の SAP ディザスター リカバリー機能を示します。 Site Recovery レプリケーションはアプリケーション固有ではないため、説明されているプロセスの他のシナリオへの適用も期待できます。

### <a name="required-foundation-services"></a>必要な基礎サービス
この記事で説明するシナリオでは、次の基盤サービスがデプロイされます。
* Azure ExpressRoute または Azure VPN Gateway
* Azure で実行されている少なくとも 1 つの Active Directory ドメイン コントローラーと DNS サーバー

Site Recovery をデプロイする前に、このインフラストラクチャを確立することをお勧めします。

## <a name="reference-sap-application-deployment"></a>参照用 SAP アプリケーションのデプロイ

この参照用アーキテクチャは、高可用性を備えた Azure の Windows 環境での SAP NetWeaver の実行を示しています。  このアーキテクチャは特定の仮想マシン (VM) サイズでデプロイされ、お客様の組織のニーズに合わせて変更できます。

![一般的な SAP デプロイ パターンの図](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>ディザスター リカバリーの考慮事項

ディザスター リカバリー (DR) を確保するために、セカンダリ リージョンにフェールオーバーできる必要があります。 各層では、さまざまな戦略を利用して、ディザスター リカバリー (DR) の保護を提供しています。

#### <a name="vms-running-sap-web-dispatcher-pool"></a>SAP Web Dispatcher プールを実行する VM 
Web Dispatcher コンポーネントは、SAP アプリケーション サーバー間の SAP トラフィックのロード バランサーとして使用されます。 Web Dispatcher コンポーネントの高可用性を実現するために、Azure Load Balancer を使って Web Dispatcher セットアップがラウンドロビン構成で並列に実装されます。これにより、HTTP (S) トラフィックはバランサー プール内の使用可能 Web Dispatcher 間で分散されます。 これは、Azure Site Recovery (ASR) を使ってレプリケートされます。ディザスター リカバリー リージョンのロード バランサーの構成には、オートメーション スクリプトが使用されます。 

#### <a name="vms-running-application-servers-pool"></a>アプリケーション サーバー プールを実行する VM
ABAP アプリケーション サーバーのログオン グループの管理には、SMLG トランザクションが使用されます。 この場合、セントラル サービスのメッセージ サーバー内の負荷分散機能を使って、SAPGUI および RFC トラフィックの SAP アプリケーション サーバーのプールにワークロードが分散されます。 これは、Azure Site Recovery を使ってレプリケートされます。 

#### <a name="vms-running-sap-central-services-cluster"></a>SAP セントラル サービス クラスターを実行する VM
この参照アーキテクチャでは、アプリケーション層の VM でセントラル サービスが実行されます。 セントラル サービスは、1 つの VM にデプロイすると単一障害点 (SPOF) になる可能性があります (これは高可用性が不要な場合の一般的なデプロイです)。<br>

高可用性ソリューションの実装には、共有ディスク クラスターまたはファイル共有クラスターのいずれかを使用できます。VM を共有ディスク クラスター用に構成するには、Windows Server フェールオーバー クラスターを使用します。 クォーラム監視としてクラウド監視をお勧めします。 
 > [!NOTE]
 > Azure Site Recovery ではクラウド監視はレプリケートされないため、ディザスター リカバリー リージョンにクラウド監視をデプロイすることをお勧めします。

フェールオーバー クラスター環境をサポートするために、[SIOS DataKeeper クラスター エディション](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8)では、クラスター ノードが所有する独立したディスクをレプリケートすることによって、クラスターの共有ボリューム機能が実行されます。 Azure では共有ディスクがネイティブでサポートされていないため、SIOS 提供のソリューションが必要です。 

ファイル共有クラスターを実装してクラスタリングを処理することもできます。 [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) では、UNC パスを介して /sapmnt グローバル ディレクトリにアクセスするための、セントラル サービス デプロイ パターンを最近変更しました。 ただし、/sapmnt UNC 共有の高可用性は確保することをお勧めします。 セントラル サービス インスタンスでこれを実現するには、Windows Server フェールオーバー クラスターと、Windows Server 2016 のスケールアウト ファイル サーバー (SOFS) および記憶域スペース ダイレクト (S2D) 機能を使用します。 
 > [!NOTE]
 > 現在 Azure Site Recovery でサポートされるのは、記憶域スペース ダイレクトと SIOS Datakeeper のパッシブ ノードを使ったクラッシュ整合性ポイント レプリケーションのみです


## <a name="disaster-recovery-considerations"></a>ディザスター リカバリーの考慮事項

Azure Site Recovery を使用することで、複数の Azure リージョンにまたがるフル SAP デプロイを対象としたフェールオーバーのオーケストレーションを行うことができます。
ディザスター リカバリーの設定手順は次のとおりです。 

1. 仮想マシンのレプリケート 
2. 復旧ネットワークを設計する
3.  ドメイン コントローラーをレプリケートする
4.  データベース層をレプリケートする 
5.  テスト フェールオーバーを実行する 
6.  フェールオーバーを実行する 

この例で使用される各層のディザスター リカバリーの推奨事項は次のとおりです。 

 **SAP 層** | **推奨事項**
 --- | ---
**SAP Web Dispatcher プール** |  Site Recovery を使ったレプリケーション 
**SAP アプリケーション サーバー プール** |  Site Recovery を使ったレプリケーション 
**SAP セントラル サービス クラスター** |  Site Recovery を使ったレプリケーション 
**Active Directory 仮想マシン** |  Active Directory レプリケーション 
**SQL データベース サーバー** |  SQL Always On レプリケーション

## <a name="replicate-virtual-machines"></a>仮想マシンのレプリケート

Azure ディザスター リカバリー データ センターへのすべての SAP アプリケーション仮想マシンのレプリケートを開始するには、「[仮想マシンを Azure にレプリケートする](azure-to-azure-walkthrough-enable-replication.md)」セクションの手順に従います。


* Active Directory と DNS の保護に関するガイダンスは、[Protect Active Directory and DNS](site-recovery-active-directory.md)ドキュメントを参照してください。

* SQL Server 上で動作するデータベース層の保護に関するガイダンスは、[Protect SQL Server](site-recovery-active-directory.md) ドキュメントを参照してください。

## <a name="networking-configuration"></a>ネットワーク構成

静的 IP アドレスを使用する場合は、仮想マシンに割り当てる IP アドレスを指定できます。 IP アドレスを設定するには、**[コンピューティングとネットワーク] の設定** > **[ネットワーク インターフェイス カード]** に移動します。

![Site Recovery のネットワーク インターフェイス カードのウィンドウで、プライベート IP アドレスを設定する方法を示したスクリーンショット](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>復旧計画の作成
復旧計画では、フェールオーバー時における多層アプリケーション内の各種階層の順序付けがサポートされます。 順序付けは、アプリケーションの一貫性の保守に役立ちます。 多層 Web アプリケーションの復旧計画を作成する際には、[復旧計画の作成](site-recovery-create-recovery-plans.md)に関するページで説明されている手順を完了します。

### <a name="adding-virtual-machines-to-failover-groups"></a>フェールオーバー グループへの仮想マシンの追加

1.  アプリケーション サーバー、Web Dispatcher、SAP セントラル サービス VM を追加して復旧計画を作成します。
2.  [カスタマイズ] をクリックして仮想マシンをグループ化します｡ 既定では､すべての仮想マシンが｢グループ 1｣のメンバーです｡



### <a name="add-scripts-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加
お使いのアプリケーションを正常に機能させるには、フェールオーバー後、またはテスト フェールオーバー時に、Azure の仮想マシンに対して一定の操作を実行することが必要な場合があります。 フェールオーバー後の操作は一部自動化することもできます。 たとえば、対応するスクリプトを復旧計画に追加することで、DNS エントリを更新したり、バインドと接続を変更したりできます。


次の [Deploy to Azure] ボタンをクリックすると、オートメーション アカウントによく使われる Azure Site Recovery のスクリプトをデプロイできます。 公開されているスクリプトを使用する場合は､必ず､そのスクリプトのガイダンスに従ってください｡

[![Azure へのデプロイ](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. SQL 可用性グループをフェールオーバーするための前処理スクリプトを｢グループ 1｣に追加します｡ サンプル スクリプトで公開されている｢ASR-SQL-FailoverAG｣スクリプトを使用してください｡ 必ずスクリプトのガイダンスに従い､適宜､スクリプトの内容を変更します｡
2. フェールオーバー後の Web 層仮想マシン (グループ 1) にロード バランサーをアタッチするための後処理スクリプトを追加します｡ サンプル スクリプトで公開されている｢ASR-AddSingleLoadBalancer｣スクリプトを使用してください｡ 必ずスクリプトのガイダンスに従い､適宜､スクリプトの内容を変更します｡

![SAP 復旧計画](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1.  Azure Portal で､Recovery Service コンテナーを選択します。
2.  SAP アプリケーション用に作成した復旧計画を選択します。
3.  **[テスト フェールオーバー]** を選択します。
4.  テスト フェールオーバー プロセスを開始するには、復旧ポイントと Azure 仮想ネットワークを選択します。
5.  セカンダリ環境が立ち上がったら、検証を実行します。
6.  検証が完了したら、フェールオーバー環境をクリーニングするために、**[テスト フェールオーバーのクリーンアップ]** を選択します。

詳しくは、「[Site Recovery での Azure へのフェールオーバーをテストする](site-recovery-test-failover-to-azure.md)」をご覧ください。

## <a name="run-a-failover"></a>フェールオーバーの実行

1.  Azure Portal で､Recovery Service コンテナーを選択します。
2.  SAP アプリケーション用に作成した復旧計画を選択します。
3.  **[フェールオーバー]** を選択します。
4.  フェールオーバー プロセスを開始するには、復旧ポイントを選択します。

詳しくは、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
* Site Recovery を使用して SAP NetWeaver デプロイ用のディザスター リカバリー ソリューションを構築する方法の詳細については、「[SAP NetWeaver: Azure Site Recovery を使用したディザスター リカバリー ソリューションの構築](https://aka.ms/asr-sap)」に関するダウンロード可能なホワイト ペーパーをご覧ください。 このホワイトペーパーでは、さまざまな SAP アプリケーションに関する推奨事項、Azure 上の SAP でサポートされるアプリケーションと VM の種類、ディザスター リカバリー ソリューションのテスト計画のオプションについて説明しています。
* Site Recovery を使用した[他のワークロードのレプリケート](site-recovery-workload.md)に関する記事をご覧ください。
