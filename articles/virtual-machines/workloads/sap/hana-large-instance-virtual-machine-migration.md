---
title: SAP HANA on Azure (Large Instances) を Azure Virtual Machines に移行する | Microsoft Docs
description: SAP HANA on Azure (Large Instances) を Azure Virtual Machines に移行する方法
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd1cfb0cc8e1868e78b4d284d1b1f4e7e85aa318
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677037"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA on Azure Large Instance の Azure Virtual Machines への移行
この記事では、Azure Large Instance で使用できるデプロイ シナリオと、移行時のダウンタイムが最小になる計画と移行の方法について説明します

## <a name="overview"></a>概要
2016 年 9 月の Azure Large Instances for SAP HANA (HLI) の発表以降、多くのお客様が、このハードウェアをメモリ内コンピューティング プラットフォーム用のサービス内容として導入されています。  近年では、Azure VM のサイズ拡張機能と HANA スケールアウト デプロイのサポートの組み合わせは、ほとんどの企業ユーザーが求める ERP データベース容量を超えています。  SAP HANA ワークロードを物理サーバーから Azure VM への移行に関心を持たれるお客様が増えています。
このガイドは、構成の詳細な手順に関するドキュメントではありません。 ここでは、一般的なデプロイ モデルについて説明し、計画と移行に関するアドバイスを提供します。  目的は、移行のダウンタイムを最小限に抑えるために必要な考慮事項を示すことです。

## <a name="assumptions"></a>外部からの影響
この記事は以下を前提としています。
- 検討対象となるのは、ソフトウェアの大規模なアップグレードや修正を必要としない、Hana Large Instance (HLI) から Azure VM への同種の HANA データベース コンピューティング サービスの移行だけです。 このようなマイナーな更新には、関連する SAP ノートでサポート対象として明示されている、より新しい OS バージョンや HANA バージョンの使用が含まれます。 
- 更新とアップグレードのすべてのアクティビティが、移行の前または後に行われる必要があります。  たとえば、SAP HANA MCOS の MDC デプロイへの変換などです。 
- ダウンタイムを最小限に抑える移行アプローチは、SAP HANA システム レプリケーションです。 他の移行方法については、このドキュメントでは説明しません。
- このガイダンスは、HLI の Rev3 と Rev4 両方の SKU に適用されます。
- HANA のデプロイ アーキテクチャが移行の間に変更されることは基本的にありません。  つまり、単一インスタンスの DR を備えたシステムは、移行先でも同じように維持されます。
- お客様は、ターゲット (移行後の) アーキテクチャのサービス レベル アグリーメント (SLA) を確認し、理解しています。 
- HLI と VM では商用使用条件が異なります。 お客様は、VM の使用状況を監視して、コストを管理する必要があります。
- お客様は、HLI は専用のコンピューティング プラットフォームであるのに対し、VM は共有されてはいても分離されたインフラストラクチャ上で実行されることを理解している必要があります。
- お客様は、ターゲット VM で目的のアーキテクチャがサポートされていることを確認しています。 SAP HANA のデプロイについて認定されているサポート対象のすべての VM SKU を確認するには、[SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)をご覧ください。
- お客様は、設計と移行計画の検証が済んでいます。
- プライマリ サイトと共にディザスター リカバリー VM を計画します。  お客様は、移行後、VM で実行されているプライマリ サイトの DR ノードとして HLI を使用することはできません。
- お客様は、ビジネスの復旧可能性とコンプライアンス要件に基づいて、必要なバックアップ ファイルをターゲット VM にコピーしてあります。 VM がアクセス可能なバックアップを使用すると、移行期間中に特定の時点への復旧が可能になります。
- HSR HA の場合、お客様は、[SLES](./high-availability-guide-suse-pacemaker.md) および [RHEL](./high-availability-guide-rhel-pacemaker.md) に関する SAP HANA HA ガイドに従って、STONITH デバイスを設定し、構成する必要があります。  HLI の場合のように事前に構成されることはありません。
- この移行アプローチでは、Optane 構成の HLI SKU については説明しません。

## <a name="deployment-scenarios"></a>デプロイメント シナリオ
次の表は、HLI のお客様による一般的なデプロイ モデルをまとめたものです。  すべての HLI シナリオで Azure VM への移行が可能です。  Azure の補完的なサービスを利用できるようにするには、アーキテクチャの小規模な変更が必要になることがあります。

| シナリオ ID | HLI シナリオ | そのまま VM に移行できるか? | 注記 |
| --- | --- | --- | --- |
| 1 | [SID が 1 つの単一ノード](./hana-supported-scenario.md#single-node-with-one-sid) | はい | - |
| 2 | [MCOS の単一ノード](./hana-supported-scenario.md#single-node-mcos) | はい | - |
| 3 | [ストレージ レプリケーションを使用する DR を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | いいえ | ストレージ レプリケーションは、Azure 仮想プラットフォームでは使用できません。現在の DR ソリューションを、HSR またはバックアップと復元のいずれかに変更します |
| 4 | [ストレージ レプリケーションを使用する DR (多目的) を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | いいえ | ストレージ レプリケーションは、Azure 仮想プラットフォームでは使用できません。現在の DR ソリューションを、HSR またはバックアップと復元のいずれかに変更します |
| 5 | [HSR と STONITH を使用した高可用性](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | はい | ターゲット VM に対して構成済みの SBD はありません。  STONITH ソリューションを選択してデプロイします。  使用可能なオプション: Azure フェンス エージェント ([RHEL](./high-availability-guide-rhel-pacemaker.md) と [SLES](./high-availability-guide-suse-pacemaker.md) の両方でサポートされます)、SBD |
| 6 | [HSR による HA、ストレージ レプリケーションによる DR](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | いいえ | DR のためのストレージ レプリケーションを、HSR またはバックアップと復元のいずれかに置き換えます |
| 7 | [ホストの自動フェールオーバー (1+1)](./hana-supported-scenario.md#host-auto-failover-11) | はい | Azure VM での共有ストレージには ANF を使用します |
| 8 | [スタンバイありのスケールアウト](./hana-supported-scenario.md#scale-out-with-standby) | はい | ストレージに対してのみ ANF を使用する M128s、M416s、M416ms の VM での BW/4HANA |
| 9 | [スタンバイなしのスケールアウト](./hana-supported-scenario.md#scale-out-without-standby) | はい | M128s、M416s、M416ms の VM での BW/4HANA (ストレージ用に ANF を使用する場合と、使用しない場合) |
| 10 | [ストレージ レプリケーションを使用しする DR を備えたスケールアウト](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | いいえ | DR のためのストレージ レプリケーションを、HSR またはバックアップと復元のいずれかに置き換えます |
| 11 | [HSR を使用する DR を備えた単一ノード](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | はい | - |
| 12 | [単一ノードの HSR から DR (コスト最適化)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | はい | - |
| 13 | [HSR を使用する HA と DR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | はい | - |
| 14 | [HSR を使用する HA と DR (コスト最適化)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | はい | - |
| 15 | [HSR を使用する DR を備えたスケールアウト](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | はい | M128s での BW/4HANA。 M416s、M416ms VM (ストレージ用に ANF を使用する場合と、使用しない場合) |


## <a name="source-hli-planning"></a>ソース (HLI) の計画
HLI サーバーをオンボードするときは、Microsoft のサービス管理とお客様の双方が、SAP HANA データベースを実行するためのコンピューティング、ネットワーク、ストレージ、OS 固有の設定を計画しました。  Azure VM への移行についても、同様の計画を行う必要があります。

### <a name="sap-hana-housekeeping"></a>SAP HANA のハウスキープ処理 
データベースの内容を整理し、不要なデータや古いログが新しいデータベースに移行されないように運用することをお勧めします。  一般に、ハウスキープ処理では、古いデータ、期限切れのデータ、または非アクティブなデータを削除またはアーカイブします。  これらの "データ検疫" アクションを、運用環境で使用する前に非運用システムでテストし、データのトリミングの有効性を検証する必要があります。

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>新しい VM と仮想ネットワークにネットワーク接続できるようにする 
お客様の HLI デプロイでは、「[SAP HANA (L インスタンス) のネットワーク アーキテクチャ](./hana-network-architecture.md)」で説明されている情報に基づいて、ネットワークが設定されています。 また、ネットワーク トラフィックのルーティングは、「Azure でのルーティング」で説明されている方法で行われます。
- 移行ターゲットとしての新しい VM の設定では、HLI に接続するためのアクセス許可が既にある IP アドレス範囲を持つ既存の仮想ネットワークに VM を配置する場合は、接続をそれ以上更新する必要はありません
- 新しい Azure VM を新しい Microsoft Azure 仮想ネットワーク (別のリージョンに存在していてもかまいません) に配置し、既存の仮想ネットワークとピアリングする場合は、元の HLI プロビジョニングの ExpressRoute サービス キーとリソース ID を使用して、この新しい仮想ネットワーク IP 範囲にアクセスできます。  Microsoft のサービス管理と協力して、仮想ネットワークが HLI に接続できるようにします。  注: アプリケーションとデータベース層の間のネットワーク待機時間を最小限にするには、アプリケーションとデータベース層の両方を同じ仮想ネットワーク上に配置する必要があります。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>既存のアプリ層可用性セット、Availability Zones、近接通信配置グループ (PPG)
現在のデプロイ モデルは、特定のサービス レベル目標を達成するために行われています。  この移動では、ターゲット インフラストラクチャが設定されている目標を満たすか、それを超えることが保証されます。  
おそらく、お客様の SAP アプリケーション サーバーは可用性セットに配置されているでしょう。  現在のデプロイ サービス レベルが十分であり 
- ターゲット VM で HLI 論理名のホスト名が想定されている場合、SAP プロファイルを更新することなく、VM の IP が指されているドメイン ネーム サービス (DNS) のアドレス解決が更新されます。
- PPG を使用していない場合は、すべてのアプリケーション サーバーと DB サーバーを同じゾーンに配置して、ネットワーク待機時間を最小限に抑えるようにしてください。
- PPG を使用している場合は、こちらのドキュメントを参照してください: 「移行先の計画、可用性セット、Availability Zones、近接通信配置グループ(PPG)」。

### <a name="storage-replication-discontinuance-process-if-used"></a>ストレージ レプリケーション停止プロセス (使用されている場合)
ストレージ レプリケーションが DR ソリューションとして使用されている場合は、SAP アプリケーションをシャットダウンした後で、それを終了 (スケジュール解除) する必要があります。  さらに、最新の SAP HANA カタログ、ログ ファイル、およびデータ バックアップが、リモート DR HLI ストレージ ボリュームにレプリケートされています。  これは、物理サーバーから Azure VM への移行の間に障害が発生した場合の予防措置として行われます。

### <a name="data-backups-preservation-consideration"></a>データ バックアップの保持に関する考慮事項
Azure VM 上の SAP HANA に切り替えた後、HLI 上のスナップショット ベースのすべてのデータ バックアップまたはログ バックアップは、必要になっても、簡単にアクセスしたり、VM に復元したりすることはできません。 移行の初期段階で、Azure ベースのバックアップによって特定時点への復旧の要件を満たすのに十分な履歴が構築される前は、HLI のスナップショットだけでなく、ファイル レベルのバックアップを実行することをお勧めします (切り替えの数日または数週前)。  これらのバックアップを、新しい SAP HANA VM からアクセスできる Azure ストレージ アカウントにコピーします。
HLI コンテンツをバックアップするだけでなく、ロールバックが必要な場合に備えて、SAP ランドスケープの完全バックアップにすぐにアクセスできるようにすることをお勧めします。

### <a name="adjusting-system-monitoring"></a>システム監視を調整する 
SAP ランドスケープ内のシステムの監視とアラート通知の送信には、さまざまなツールが使用されています。  この項目は、監視のための変更を組み込み、必要に応じてアラート通知の受信者を更新するよう、適切な対処について注意を促すものです。

### <a name="microsoft-operations-team-involvement"></a>Microsoft 運用チームの関与 
既存の HLI インスタンスで Azure portal ベースからチケットを開きます。  サポート チケットが作成されると、サポート エンジニアからメールで連絡があります。  

### <a name="engage-microsoft-account-team"></a>Microsoft アカウント チームを参加させる
コンピューティング リソースに対する不要な費用を最小限に抑えるために、HLI コントラクトの更新日の近くに移行を計画します。 HLI ブレードを使用停止にするには、コントラクトの終了と、ユニットの実際のシャットダウンを調整する必要があります。

## <a name="destination-planning"></a>移行先の計画
既存のインフラストラクチャの代わりに新しいインフラストラクチャを立ち上げるには、新しい追加機能が大きな構造に確実に適合するよう考慮する必要があります。  以下では重要な考慮ポイントを示します。

### <a name="resource-availability-in-the-target-region"></a>ターゲット リージョンでのリソースの可用性 
現在の SAP アプリケーション サーバーのデプロイ リージョンは、通常、関連付けられている HLI と非常に近接しています。  ただし、HLI は、利用可能な Azure リージョンより少い場所でしか提供されていません。  物理 HLI を Azure VM に移行するときは、パフォーマンスを最適化するために、関連するすべてのサービスの近さを "微調整" するのに適したタイミングでもあります。  これを行うときの重要な考慮事項の 1 つは、選択したリージョンに必要なリソースを確保することです。  たとえば、特定の VM ファミリや、高可用性セットアップのための Azure ゾーンのオファリングがあるかどうかです。

### <a name="virtual-network"></a>仮想ネットワーク 
お客様は、新しい HANA データベースを既存の仮想ネットワークで実行するか、新しい仮想ネットワークを作成するかを、選択する必要があります。  主な決定要因は、SAP ランドスケープの現在のネットワーク レイアウトです。  また、インフラストラクチャが 1 つのゾーンから 2 つのゾーンになり、PPG を使用している場合は、アーキテクチャの変更が必要になります。 詳しくは、「[SAP アプリケーションで最適なネットワーク待ち時間を実現するための Azure 近接通信配置グループ](./sap-proximity-placement-scenarios.md)」をご覧ください。   

### <a name="security"></a>セキュリティ
新しい SAP HANA VM のデプロイに新規と既存どちらの仮想ネットワークとサブネットを使用するかにかかわらず、保護を必要とする新しいビジネス クリティカルなサービスを表します。  会社の情報セキュリティ ポリシーに準拠しているアクセス制御を評価し、この新しいサービスのクラスに対してデプロイする必要があります。

### <a name="vm-sizing-recommendation"></a>VM のサイズ設定に関する推奨事項
この移行は、HANA コンピューティング エンジンを適切なサイズに変更する機会でもあります。  Hana [システム ビュー](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)を HANA Studio と組み合わせて使用すると、システム リソースの消費量を把握できます。これにより、適切なサイズに調整して支出の効率を向上させることができます。

### <a name="storage"></a>ストレージ 
ストレージのパフォーマンスは、SAP アプリケーションのユーザー エクスペリエンスに影響する要因の 1 つです。  特定の VM SKU に基づく最小ストレージ レイアウトについては、「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」を参照してください。 これらの最小仕様を確認し、既存の HLI システムの統計と比較して、新しい HANA VM の適切な IO 容量とパフォーマンスを確保することをお勧めします。

新しい HANA VM とそれに関連するサーバーに対して PPG を構成する場合は、ストレージと VM の併置を調べて確保するため、サポート チケットを提出します。 バックアップ ソリューションの変更が必要な場合があるため、運用費を見て驚かないよう、ストレージ コストも再検討する必要があります。

### <a name="storage-replication-for-disaster-recovery"></a>ディザスター リカバリーのためのストレージ レプリケーション
HLI では、ディザスター リカバリーの既定のオプションとして、ストレージ レプリケーションが提供されていました。 この機能は、Azure VM での SAP HANA に対する既定のオプションではありません。 HSR、バックアップと復元、または他のサポートされているソリューションを使用して、ビジネス ニーズを満たすことを検討します。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性セット、Availability Zones、近接通信配置グループ 
ネットワークの待機時間を最小限に抑えるため、アプリケーション層と SAP HANA の距離を最小にするには、新しいデータベース VM と現在の SAP アプリケーション サーバーを PPG に置く必要があります。 SAP デプロイでの Azure 可用性セットおよび Availability Zones と PPG の連携方法については、[近接通信配置グループ](./sap-proximity-placement-scenarios.md)に関する記事をご覧ください。
ターゲット HANA システムのメンバーが複数の Azure ゾーンにデプロイされている場合は、選択したゾーンの待機時間プロファイルを明確に把握する必要があります。 SAP システム コンポーネントの配置は、SAP アプリケーションとデータベースの間の距離が短い場合に最適です。  パブリック ドメインの[可用性ゾーン待機時間テスト ツール](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)を使用すると、簡単に測定することができます。  


### <a name="backup-strategy"></a>バックアップ戦略
多くのお客様は既に、HLI の SAP HANA 用にサードパーティ製のバックアップ ソリューションを使用しています。  その場合は、VM と HANA データベースに追加の保護を構成するだけで十分です。  移行後にコンピューターを使用しなくなる場合、現在行われている HLI バックアップ ジョブのスケジュールを解除できます。
現在、VM 上の SAP HANA に対する Azure Backup は一般提供されています。  こちらのリンクで、Azure VM での SAP HANA の[バックアップ](../../../backup/backup-azure-sap-hana-database.md)、[復元](../../../backup/sap-hana-db-restore.md)、[管理](../../../backup/sap-hana-db-manage.md)に関する詳細情報を参照してください。

### <a name="dr-strategy"></a>DR 戦略
サービス レベルの目標が長い復旧時間への対応である場合、BLOB ストレージへの簡単なバックアップと、インプレースまたは新規 VM への復元は、最もシンプルでコストのかからない DR 戦略です。  
HANA DR が通常 HSR で実行される大規模なインスタンス プラットフォームと同様に、Azure VM では、HSR は最も自然でネイティブな SAP HANA DR ソリューションでもあります。  移行元のデプロイが単一インスタンスかクラスターかに関係なく、移行元インフラストラクチャのレプリカが DR リージョン内に必要です。
この DR レプリカは、プライマリ HLI から VM への移行が完了した後で構成されます。  DR HANA DB は、セカンダリ レプリケーション サイトとして、VM インスタンス上のプライマリ SAP HANA に登録されます。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP アプリケーション サーバーの接続先の変更
HSR 移行により、新しい HANA DB ホストが作成され、したがってアプリケーション層の新しい DB ホスト名が作成されるので、新しいホスト名を反映するように SAP プロファイルを変更する必要があります。  ホスト名を維持する名前解決によって切り替えが行われる場合は、プロファイルを変更する必要はありません。

### <a name="operating-system"></a>オペレーティング システム
HLI と VM のオペレーティング システム イメージは、同じリリース レベルの場合でも (SLES 12 SP4 など)、同一ではありません。 お客様は、HLI で必要なパッケージ、修正プログラム、パッチ、カーネル、セキュリティ修正プログラムを検証して、同じパッケージをターゲットにインストールする必要があります。  HSR を使用して、古い OS から新しい OS バージョンの VM にレプリケートすることがサポートされています。  サポートされている具体的なバージョンについては、[SAP ノート 2763388](https://launchpad.support.sap.com/#/notes/2763388) をご確認ください。

### <a name="new-sap-license-request"></a>新しい SAP ライセンスの要求
VM に移行されたので、新しい HANA システムの新しい SAP ライセンスを要求する必要があることについての簡単な注意です。

### <a name="service-level-agreement-sla-differences"></a>サービス レベル アグリーメント (SLA) の違い
HLI と Azure VM の間の可用性 SLA の違いについての注意です。  たとえば、クラスター化された HLI HA ペアの可用性は 99.99% です。 同じ SLA を実現するには、可用性ゾーンに VM をデプロイする必要があります。 こちらの[記事](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)では関連するデプロイ アーキテクチャでの可用性について説明されており、お客様はそれに応じてターゲット インフラストラクチャを計画できます。


## <a name="migration-strategy"></a>移行戦略
このドキュメントでは、HLI から Azure VM への移行に対する HANA システム レプリケーション アプローチについてのみ説明します。  デプロイするターゲット ストレージ ソリューションによっては、プロセスが若干異なります。 以下ではおおまかな手順を説明します。

### <a name="vm-with-premiumultra-disks-for-data"></a>データ用に Premium または Ultra ディスクを使用する VM
Premium または Ultra ディスクを使用してデプロイされた VM の場合、標準の SAP HANA システム レプリケーション構成を HSR のセットアップに適用できます。  [SAP ヘルプ記事](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)では、システム レプリケーションの設定、セカンダリ システムへのフェール オーバー、プライマリへのフェールバック、システム レプリケーションの無効化に関する手順の概要が説明されています。  移行のために必要なのは、セットアップ、引き継ぎ、レプリケーションの無効化の手順だけです。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>データとログボリュームに ANF を使用する VM
大まかに言えば、完全なデータ ボリュームとログ ボリュームの最新の HLI ストレージ スナップショットを、ターゲットの HANA VM によってアクセス可能で復旧可能な Azure Storage にコピーする必要があります。  コピー処理は、任意のネイティブ Linux コピー ツールで実行できます。  

> [!IMPORTANT]
> HANA データベースのサイズとネットワーク帯域幅によっては、コピーとデータ転送に時間がかかることがあります。  コピー処理の大部分は、プライマリ HANA DB のダウンタイムより前に行っておく必要があります。

### <a name="mcos-to-mdc-conversion"></a>MCOS から MDC への変換
HLI の一部のお客様は、1 つのシステムに複数のコンポーネントが含まれる (MCOS) デプロイ モデルを使用しています。  それは、SAP HANA の初期のバージョンにあった複数データベース コンテナー (MDC) のストレージ スナップショットに関する制限を回避するためでした。  MCOS モデルでは、複数の独立した SAP HANA インスタンスが、1 つの HLI ブレードに積み重ねられます。  HSR を使用した移行は問題なく機能しますが、それぞれが 1 つのテナント DB を備える複数の HANA VM になります。  この最終状態は、お客様が慣れているものよりビジーな状況になります。  MDC を使用する SAP HANA 2.0 の既定のデプロイでの有効な代替策は、HSR の移行の後で [HANA テナントの移動](https://launchpad.support.sap.com/#/notes/2472478)を実行することです。  このプロセスにより、これらの独立した HANA データベースが、1 つの HANA コンテナー内の共同テナントに統合されます。  

### <a name="application-layer-consideration"></a>アプリケーション層についての考慮事項
DB サーバーが SAP システムの中心と見なされます。  すべてのアプリケーション サーバーを、SAP HANA DB の近くに配置する必要があります。  PPG を新しく使用するのが望ましい場合は、HANA VM が存在する PPG への既存アプリケーション サーバーの再配置が必要になる場合があります。  既に展開テンプレートを使用している場合は、新しいアプリケーション サーバーの構築が簡単になる可能性があります。  
既存のアプリケーション サーバーと新しい HANA VM を理想的に配置した場合、追加容量が必要でない限り、新しいアプリケーション サーバーを構築する必要はありません。  
サービスの可用性を高めるために新しいインフラストラクチャを構築する場合は、既存のアプリケーション サーバーが不要になり、シャットダウンして削除することが必要になる可能性があります。
ターゲット VM のホスト名を変更し、HLI のホスト名と異なる場合は、新しいホストを参照するように、SAP アプリケーション サーバーのプロファイルを調整する必要があります。  HANA DB の IP アドレスのみを変更した場合は、着信接続を新しい HANA VM に誘導するために、DNS レコードの更新が必要になります。

### <a name="acceptance-test"></a>受け入れテスト
異種の移行とは異なり、HLI から VM への移行では、データベースの内容に対する重要な変更はありませんが、新しいセットアップの主要な機能とパフォーマンスの面を検証することをお勧めします。  

### <a name="cutover-plan"></a>切り替え計画
この移行は簡単なものですが、既存の DB の使用停止が関係します。  フォールバックが必要な場合のため、ソース システムとそれに関連するコンテンツおよびバックアップ イメージが保持されるように慎重に計画することが不可欠です。  適切に計画すれば、より迅速に元に戻すことができます。   


## <a name="post-migration"></a>移行後の処理
データの整合性を保証するため、HLI に依存するサービスまたは接続を安全に分離するまでは、移行ジョブを行いません。  また、不要なサービスはシャットダウンします。  ここでは、いくつかの重要な項目について説明します。

### <a name="decommissioning-the-hli"></a>HLI の使用停止
HANA DB から Azure VM への移行が成功した後、HLI DB で運用ビジネス トランザクションが確実に実行されないようにします。  ただし、ローカル バックアップの保有期間だけ HLI を実行させておくことは、必要な場合に迅速に復旧するための安全な方法です。  その後でのみ、HLI ブレードの使用を停止する必要があります。  お客様は、Microsoft の担当者に連絡して、Microsoft との HLI コミットメントの契約を終了する必要があります。

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>HLI 用に構成されたプロキシ (例: Iptables、BIGIP) を削除する 
IPTable のようなプロキシ サービスを使用して HLI との間のオンプレミス トラフィックをルーティングしている場合、VM への移行が成功した後、サービスは不要になります。  ただし、HLI ブレードがまだスタンバイになっている限り、この接続サービスを保持する必要があります。  HLI ブレードの使用を完全に停止した後でのみ、サービスをシャットダウンします。

### <a name="remove-global-reach-for-hli"></a>HLI の Global Reach の削除 
Global Reach は、お客様の ExpressRoute ゲートウェイと HLI ExpressRoute ゲートウェイを接続するために使用されます。  プロキシ サービスを使用しなくても、お客様のオンプレミスのトラフィックは HLI テナントに直接到達することができます。  移行後は HLI ユニットが存在しないので、この接続はもう必要ありません。  IPTable プロキシ サービスの場合と同様に、HLI ブレードが完全に使用停止になるまで、Global Reach も保持する必要があります。

### <a name="operating-system-subscription--movereuse"></a>オペレーティング システムのサブスクリプション - 移動と再利用
VM サーバーを稼働させ、HLI ブレードの使用を停止したら、OS ライセンスの二重払いを防ぐため、OS サブスクリプションを置き換えたり再利用したりすることができます。



## <a name="next-steps"></a>次のステップ
次の記事を参照してください。
- [Azure における SAP HANA インフラストラクチャの構成と運用](./hana-vm-operations.md)
- [Azure での SAP ワークロードの計画とデプロイに関するチェックリスト](./sap-deployment-checklist.md)。
