---
title: Azure Site Recovery を使用した VMware のディザスター リカバリー
description: この記事では、Azure Site Recovery サービスを使用した VMware VM から Azure へのディザスター リカバリーの概要について説明します。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: dfbdff01064b483085233ece47d1d3b635b68743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87021462"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>VMware VM から Azure へのディザスター リカバリーの概要

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用したオンプレミスの VMware VM から Azure へのディザスター リカバリーの概要について説明します。

## <a name="what-is-bcdr"></a>BCDR とは

事業継続とディザスター リカバリー (BCDR) 戦略は、ビジネスを維持するために役立ちます。 計画されたダウンタイム時と予想外の停止時に、BCDR はデータの安全性と可用性を維持し、アプリケーションの継続的な実行を確保します。 リージョンのペアリングや高可用性ストレージなどのプラットフォーム BCDR 機能に加え、Azure は BCDR ソリューションの重要な部分として Recovery Services を提供しています。 Recovery Services には以下が含まれています。 

- [Azure Backup](../backup/backup-overview.md): オンプレミスと Azure VM のデータをバックアップします。 ファイルとフォルダー、特定のワークロード、または VM 全体をバックアップできます。 
- [Azure Site Recovery](site-recovery-overview.md): オンプレミス マシンまたは Azure IaaS VM 上で実行されているアプリとワークロードに復元機能とディザスター リカバリー機能を提供します。 Site Recovery は、障害の発生時にレプリケーションを調整して Azure へのフェールオーバーを処理します。 また、Azure からプライマリ サイトへの復旧も処理します。 

> [!NOTE]
> Site Recovery では、ソース マシンのディザスター リカバリーがセットアップされているターゲット リージョンから顧客データを移動または格納することはありません。 お客様は、必要に応じて、別のリージョンから Recovery Services コンテナーを選択することもできます。 Recovery Services コンテナーにはメタデータが含まれますが、実際の顧客データは含まれません。

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Site Recovery によるディザスター リカバリーのしくみ

1. Azure とオンプレミス サイトを準備したら、オンプレミス マシンのレプリケーションを設定して有効にします。
2. Site Recovery によって、ポリシー設定に従ってマシンの最初のレプリケーションが調整されます。
3. 最初のレプリケーション後は、Site Recovery によって差分の変更が Azure にレプリケートされます。 
4. すべてが想定どおりにレプリケートされたら、ディザスター リカバリー訓練を実行します。
    - 訓練によって、実際に必要になったときに想定どおりにフェールオーバーが機能することを確認できます。
    - 訓練では、運用環境に影響を与えずにテスト フェールオーバーを実行します。
5. 障害が発生した場合、Azure への完全なフェールオーバーを実行します。 1 台のマシンをフェールオーバーすることも、複数のマシンを同時にフェールオーバーする復旧プランを作成することもできます。
6. フェールオーバー時に、Azure VM はマネージド ディスクまたはストレージ アカウントの VM データから作成されます。 ユーザーは、引き続き Azure VM からアプリケーションとワークロードにアクセスできます。
7. オンプレミス サイトが再び使用可能になったら、Azure からフェールバックします。
8. フェールバックし、再びプライマリ サイトから作業した後に、またオンプレミス VM から Azure へのレプリケートを開始します。


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>自分の環境が Azure へのディザスター リカバリーに適しているかどうかを確認する方法

Site Recovery を使用して、サポートされている VMware VM または物理サーバー上で実行されているワークロードをレプリケートできます。 環境について、次の点を確認する必要があります。

- VMware VM をレプリケートする場合は、適切なバージョンの VMware 仮想化サーバーを実行していますか。 [ここで確認](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers)してください。
- レプリケートするマシンは、サポートされているオペレーティング システムを実行していますか。 [ここで確認](vmware-physical-azure-support-matrix.md#replicated-machines)してください。
- Linux のディザスター リカバリーの場合、マシンはサポートされているファイル システム/ゲスト ストレージを実行していますか。 [ここで確認](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)してください。
- レプリケートするマシンは Azure の要件に準拠していますか。 [ここで確認](vmware-physical-azure-support-matrix.md#azure-vm-requirements)してください。
- ネットワーク構成はサポートされていますか。 [ここで確認](vmware-physical-azure-support-matrix.md#network)してください。
- ストレージ構成はサポートされていますか。 [ここで確認](vmware-physical-azure-support-matrix.md#storage)してください。


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>開始する前に必要な Azure の設定内容

Azure では、以下を準備する必要があります。

1. Azure に VM を作成するアクセス許可が Azure アカウントにあることを確認します。
2. フェールオーバー後に Azure VM がストレージ アカウントまたはマネージド ディスクから作成されるときに参加する Azure ネットワークを作成します。
3. Site Recovery の Azure Recovery Services コンテナーを設定します。 コンテナーは Azure portal にあります。コンテナーは、Site Recovery のデプロイ、構成、調整、監視、およびトラブルシューティングに使用します。

*さらにサポートが必要な場合*

Azure の設定方法については、[アカウント](tutorial-prepare-azure.md#verify-account-permissions)の確認、[ネットワーク](tutorial-prepare-azure.md#set-up-an-azure-network)の作成、[コンテナーの設定](tutorial-prepare-azure.md#create-a-recovery-services-vault)に関するページを参照してください。



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>開始する前に必要なオンプレミスの設定内容

オンプレミスで実行する必要があることを次に示します。

1. いくつかのアカウントを設定する必要があります。

    - VMware VM をレプリケートする場合は、Site Recovery が vCenter Server または vSphere ESXi ホストにアクセスして VM を自動的に検出できるアカウントが必要です。
    - レプリケートする各物理マシンまたは VM に Site Recovery Mobility サービス エージェントをインストールするアカウントが必要です。

2. VMware インフラストラクチャの互換性をまだ確認していない場合は、確認する必要があります。
3. フェールオーバー後に Azure VM に接続できることを確認します。 オンプレミスの Windows マシンには RDP を、Linux マシンには SSH を設定します。

*さらにサポートが必要な場合*
- [自動検出](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)用のアカウントと[モビリティ サービスのインストール](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)用のアカウントを準備します。
- VMware 設定が互換性があることを[確認します](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements)。
- フェールオーバー後に Azure で接続するように[準備します](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover)。
- フェールオーバー後に Azure VM の IP アドレス指定を設定する場合の詳細については、[こちらの記事](concepts-on-premises-to-azure-networking.md)を参照してください。

## <a name="how-do-i-set-up-disaster-recovery"></a>ディザスター リカバリーを設定する方法

Azure とオンプレミスのインフラストラクチャの準備が完了したら、ディザスター リカバリーを設定できます。

1. デプロイする必要があるコンポーネントについては、「[VMware から Azure へのレプリケーション アーキテクチャ](vmware-azure-architecture.md)」と「[物理サーバーから Azure へのレプリケーション アーキテクチャ](physical-azure-architecture.md)」を参照してください。 多数のコンポーネントがあるので、どのように組み合わせられるかを理解することが重要です。
2. **ソース環境**:デプロイの第一歩として、レプリケーションのソース環境を設定します。 レプリケート対象とレプリケート先を指定します。
3. **構成サーバー**: オンプレミスのソース環境に構成サーバーを設定する必要があります。
    - 構成サーバーは、単一のオンプレミス マシンです。 VMware のディザスター リカバリーの場合は、ダウンロード可能な OVF テンプレートからデプロイできる VMware VM としてデプロイすることをお勧めします。
    - 構成サーバーは、オンプレミスと Azure の間の通信を調整します。
    - その他のいくつかのコンポーネントは、構成サーバー マシン上で実行されます。
        - プロセス サーバーは、レプリケーション データを受け取り、最適化して、Azure のキャッシュ ストレージ アカウントに送信します。 また、レプリケートするマシン上のモビリティ サービスの自動インストールを処理し、VMware サーバー上で VM の自動検出を実行します。
        - マスター ターゲット サーバーは、Azure からのフェールバック中にレプリケーション データを処理します。
    - 設定には、構成サーバーのコンテナーへの登録、MySQL Server と VMware PowerCLI のダウンロード、自動検出用とモビリティ サービスのインストール用に作成したアカウントの指定などがあります。
4. **ターゲット環境**:Azure サブスクリプションおよびネットワーク設定を指定して、ターゲットの Azure 環境を設定します。
5. **レプリケーション ポリシー**:レプリケーションの実行方法を指定します。 設定には、復旧ポイントを作成および保存する頻度、アプリ整合性スナップショットを作成するかどうかなどがあります。
6. **レプリケーションを有効にします**。 オンプレミス マシンのレプリケーションを有効にします。 モビリティ サービスをインストールするためのアカウントを作成した場合、マシンのレプリケーションを有効にすると、インストールされます。 

*さらにサポートが必要な場合*

- これらの手順の簡単なチュートリアルについては、[VMware のチュートリアル](vmware-azure-tutorial.md)と[物理サーバーのチュートリアル](physical-azure-disaster-recovery.md)を試してみてください。
- ソース環境の設定の詳細については、[こちら](vmware-azure-set-up-source.md)を参照してください。
- 構成サーバーの要件と、VMware レプリケーション用の OVF テンプレートを使用した構成サーバーの設定については、[こちら](vmware-azure-deploy-configuration-server.md)を参照してください。 何らかの理由でテンプレートを使用できない場合、または物理サーバーをレプリケートしている場合は、[こちらの手順を実行してください](physical-azure-set-up-source.md#set-up-the-source-environment)。
- ターゲットの設定の詳細については、[こちら](vmware-azure-set-up-target.md)を参照してください。
- レプリケーション ポリシーの設定の詳細については、[こちら](vmware-azure-set-up-replication.md)を参照してください。
- レプリケーションを有効にする方法については[こちら](vmware-azure-enable-replication.md)、レプリケーションからディスクを除外する方法については[こちら](vmware-azure-exclude-disk.md)を参照してください。


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>問題が発生した場合のトラブルシューティング方法

- まず、[デプロイを監視](site-recovery-monitor-and-troubleshoot.md)して、レプリケートされた項目、ジョブ、インフラストラクチャの問題の状態を確認し、エラーを特定します。
- 初回のレプリケーションを完了できない場合、または進行中のレプリケーションが想定どおりに機能しない場合は、[こちらの記事](vmware-azure-troubleshoot-replication.md)で一般的なエラーとトラブルシューティングのヒントを確認します。
- レプリケートするマシンにモビリティ サービスを自動インストールする際に問題がある場合は、[こちらの記事](vmware-azure-troubleshoot-push-install.md)で一般的なエラーを確認します。
- フェールオーバーが想定どおりに機能しない場合は、[こちらの記事](site-recovery-failover-to-azure-troubleshoot.md)で一般的なエラーを確認します。
- フェールバックが機能していない場合は、[こちらの記事](vmware-azure-troubleshoot-failback-reprotect.md)に問題が記載されているかどうかを確認します。



## <a name="next-steps"></a>次のステップ

レプリケーションの準備が完了したら、[ディザスター リカバリー訓練を実行](tutorial-dr-drill-azure.md)して、フェールオーバーが想定どおりに機能することを確認します。 
