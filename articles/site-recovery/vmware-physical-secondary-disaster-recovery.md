---
title: Azure Site Recovery を使用したセカンダリ サイトへの VMware VM/物理サーバーのディザスター リカバリー
description: Azure Site Recovery を使用して、セカンダリ サイトへの VMware VM または Windows/Linux 物理サーバーのディザスター リカバリーをセットアップする方法を説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84711903"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>オンプレミスの VMware 仮想マシンまたは物理サーバーのセカンダリ サイトへのディザスター リカバリーをセットアップする

[Azure Site Recovery](site-recovery-overview.md) の InMage Scout は、オンプレミスの VMware サイト間のリアルタイムのレプリケーションを実現します。 InMage Scout は、Azure Site Recovery サービスのサブスクリプションに含まれています。

## <a name="end-of-support-announcement"></a>サポート終了のお知らせ

Azure Site Recovery によるオンプレミスの VMware データセンターまたは物理データセンター間のレプリケーションのシナリオのサポート終了が近づいています。

-   2018 年 8 月以降、このシナリオを Recovery Services コンテナーで構成することはできません。また、InMage Scout ソフトウェアをコンテナーからダウンロードすることもできません。 既存の展開はサポートされます。 
-   2020 年 12 月 31 日以降、このシナリオはサポートされなくなります。
- 既存のパートナーは、サポート終了まで新しいお客様にこのシナリオを提供できます。

2018 年と 2019 年中に、2 つの更新プログラムがリリースされます。 

-   更新プログラム 7:ネットワーク構成とコンプライアンスの問題を修正し、TLS 1.2 のサポートを提供します。
-   更新プログラム 8:Linux オペレーティング システム RHEL/CentOS 7.3/7.4/7.5 および SUSE 12 のサポートを追加します

更新プログラム 8 以降、更新プログラムはリリースされません。 更新プログラム 8 で追加されたオペレーティング システムに対する限定的な修正プログラムのサポートおよびベスト エフォートに基づくバグの修正が提供されます。

Azure Site Recovery では、Azure を使用したシームレスかつクラス最高の DRaaS ソリューションをディザスター リカバリー サイトとして VMware と Hyper-V のお客様に引き続き提供します。 InMage/ASR Scout を既に使用しているお客様は、ビジネス継続性のニーズを満たすために、Azure Site Recovery の VMware から Azure のシナリオの使用を検討していただくことをお勧めします。 Azure Site Recovery の VMware から Azure のシナリオは、VMware アプリケーションに対応したエンタープライズ クラスの DR ソリューションであり、分単位での RPO と RTO、マルチ VM アプリケーションのレプリケーションと復旧のサポート、シームレスなオンボード、包括的な監視、TCO の重要なメリットを提供します。

### <a name="scenario-migration"></a>シナリオの移行
別の方法として、オンプレミスの VMware VM と物理マシンを Azure にレプリケートし、それらのディザスター リカバリーを設定することをお勧めします。 これを行うには、次の手順を実行します。

1.  以下に示す比較早見表を確認します。 オンプレミスのマシンをレプリケートする前に、それらが Azure へのレプリケーションの[要件](./vmware-physical-azure-support-matrix.md#replicated-machines)を満たしていることを確認する必要があります。 VMware VM をレプリケートする場合は、[容量計画ガイドライン](./site-recovery-plan-capacity-vmware.md)を参照し、[Deployment Planner ツール](./site-recovery-deployment-planner.md)を実行して容量の要件を特定し、コンプライアンスを確認することをお勧めします。
2.  Deployment Planner を実行すると、レプリケーションを設定できます。o   VMware VM の場合は、これらのチュートリアルに従って [Azure の準備](./tutorial-prepare-azure.md)、[オンプレミスの VMware 環境の準備](./vmware-azure-tutorial-prepare-on-premises.md)、および[ディザスター リカバリーの設定](./vmware-azure-tutorial-prepare-on-premises.md)を行います。
o   物理マシンの場合は、この[チュートリアル](./physical-azure-disaster-recovery.md)に従います。
3.  マシンが Azure にレプリケートされたら、[ディザスター リカバリー ドリル](./site-recovery-test-failover-to-azure.md)を実行して、すべてが想定どおりに機能していることを確認できます。

### <a name="quick-comparison"></a>比較早見表

**機能** | **Azure へのレプリケーション** |**VMware データセンター間のレプリケーション**
--|--|--
**必須コンポーネント** |レプリケートされるマシン上のモビリティ サービス。 オンプレミスの構成サーバー、プロセス サーバー、マスター ターゲット サーバー。フェールバック用の一時プロセス サーバー (Azure 内)。|モビリティ サービス、プロセス サーバー、構成サーバー、マスター ターゲット
**構成とオーケストレーション** |Recovery Services コンテナー (Azure Portal 内) | vContinuum を使用 
**レプリケート** |ディスク (Windows および Linux) |ボリューム - Windows<br> ディスク - Linux
**共有ディスク クラスター** |サポートされていません|サポートされています
**データ チャーンの制限 (平均)** |ディスクあたり 10 MB/秒のデータ<br> VM あたり 25 MB/秒のデータ<br> [詳細情報](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | ディスクあたり 10 MB/秒を超えるデータ  <br> VM あたり 25 MB/秒を超えるデータ
**Monitoring** |Azure Portal から|CX (構成サーバー) から
**サポート マトリックス** | [詳しくは、ここをクリック](./vmware-physical-azure-support-matrix.md)|[ASR Scout 互換のマトリックスのダウンロード](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

- すべてのコンポーネントのサポート要件を[レビュー](vmware-physical-secondary-support-matrix.md)する。
- レプリケートするマシンが[レプリケート マシンのサポート](vmware-physical-secondary-support-matrix.md#replicated-vm-support)に準拠していることを確認する。


## <a name="download-and-install-component-updates"></a>コンポーネント更新プログラムのダウンロードとインストール

 最新の[更新プログラム](#updates)をレビューしてインストールします。 更新プログラムは、次の順序でサーバーにインストールする必要があります。

1. RX サーバー (該当する場合)
2. 構成サーバー
3. プロセス サーバー
4. マスター ターゲット サーバー
5. vContinuum サーバー
6. ソース サーバー (Windows および Linux サーバーの両方)

次のように、更新プログラムをインストールします。

> [!NOTE]
>すべての Scout コンポーネントのファイル更新バージョンが、更新プログラムの .zip ファイル内で同じとは限りません。 以前のバージョンは、前回の更新プログラムから今回の更新プログラムまでに、コンポーネントに変更がないことを示しています。

[更新プログラム](https://aka.ms/asr-scout-update7)の .zip ファイルおよび [MySQL と PHP のアップグレード](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)の構成ファイルをダウンロードします。 更新プログラムの .zip ファイルには、次のコンポーネントのすべての基本バイナリと累積的なアップグレード バイナリが含まれます。 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. .zip ファイルを抽出します。
  2. **RX サーバー**:**RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** を RX サーバーにコピーし、展開します。 抽出先のフォルダーで、 **/Install** を実行します。
  3. **構成サーバーとプロセス サーバー**:**CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** を構成サーバーとプロセス サーバーにコピーします。 ファイルをダブルクリックして実行します。<br>
  4. **Windows のマスター ターゲット サーバー**:統合エージェントを更新するには、**InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** をサーバーにコピーします。 ファイルをダブルクリックして実行します。 同じファイルを、新規インストールにも使用できます。 同じ統合エージェントの更新プログラムはソース サーバーにも適用できます。
  これは最新の変更をすべて備えた新しい GA インストーラーなので、**InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** で準備されたマスター ターゲット上で更新プログラムを適用する必要はありません。
  5. **vContinuum サーバー**:**InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** をサーバーにコピーします。  vContinuum ウィザードを閉じたことを確認してください。 ファイルをダブルクリックして実行します。
  6. **Linux マスター ターゲット サーバー**:統合エージェントを更新するには、**InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** を Linux マスター ターゲット サーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install** を実行します。
  7. **Windows ソース サーバー**:統合エージェントを更新するには、**InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** をソース サーバーにコピーします。 ファイルをダブルクリックして実行します。 
  8. **Linux ソース サーバー**:統合エージェントを更新するには、統合エージェント ファイルの対応するバージョンを Linux サーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install** を実行します。  例:RHEL 6.7 64 ビット サーバーの場合は、**InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** をサーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install** を実行します。
  9. 上記のインストーラーで構成サーバー、プロセス サーバー、RX サーバーをアップグレードした後、[クイック インストール ガイド](https://aka.ms/asr-scout-quick-install-guide)のセクション 7.4 で説明されている手順を使用して、PHP と MySQL のライブラリを手動でアップグレードする必要があります。

## <a name="enable-replication"></a>レプリケーションを有効にする

1. ソースとターゲットの VMware サイト間にレプリケーションを設定します。
2. インストール、保護、復旧について詳しくは、次のドキュメントをご覧ください。

   * [リリース ノート](https://aka.ms/asr-scout-release-notes)
   * [互換性対応表](https://aka.ms/asr-scout-cm)
   * [ユーザー ガイド](https://aka.ms/asr-scout-user-guide)
   * [RX ユーザー ガイド](https://aka.ms/asr-scout-rx-user-guide)
   * [クイック インストール ガイド](https://aka.ms/asr-scout-quick-install-guide)
   * [MYSQL と PHP のライブラリをアップグレードする](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>更新プログラム

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 更新プログラム 7 
更新:2018 年 12 月 31 日 [Scout 更新プログラム 7](https://aka.ms/asr-scout-update7) をダウンロードします。
Scout 更新プログラム 7 は完全なインストーラーであり、新規インストールにも、以前の更新プログラム (更新プログラム 1 から更新プログラム 6) に含まれる既存のエージェント/MT のアップグレードにも使用できます。 更新プログラム 1 から更新プログラム 6 までのすべての修正と、下記の新しい修正および拡張機能が含まれています。
 
#### <a name="new-features"></a>新機能
* PCI コンプライアンス
* TLS v1.2 のサポート

#### <a name="bug-and-security-fixes"></a>バグとセキュリティの修正
* 固定:Windows クラスター/スタンドアロン マシンには、復旧/DR ドリルに正しくない IP 構成があります。
* 固定:V2V クラスターのディスク追加操作が失敗することがあります。
* 修正済み: マスター ターゲットが Windows Server 2016 の場合、復旧フェーズの間に、vContinuum ウィザードがスタックします
* 固定:MySQL をバージョン 5.7.23 にアップグレードすることで、MySQL のセキュリティの問題が軽減されます

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>CS、PS、RX での PHP と MySQL の手動アップグレード
構成サーバー、プロセス サーバー、RX サーバーで、PHP スクリプト プラットフォームをバージョン 7.2.10 にアップグレードする必要があります。
構成サーバー、プロセス サーバー、RX サーバーで、MySQL データベース管理システムをバージョン 5.7.23 にアップグレードする必要があります。
[クイック インストール ガイド](https://aka.ms/asr-scout-quick-install-guide)で説明されている手動の手順に従って、PHP と MySQL のバージョンをアップグレードしてください。

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 更新プログラム 6 
更新:2017 年 10 月 12 日

[Scout 更新プログラム 6](https://aka.ms/asr-scout-update6) をダウンロードします。

Scout 更新プログラム 6 は累積的な更新プログラムです。 更新プログラム 1 から更新プログラム 5 までのすべての修正と、下記の新しい修正および拡張機能が含まれています。 

#### <a name="new-platform-support"></a>新しいプラットフォームのサポート
* ソースの Windows Server 2016 のサポートが追加されました。
* 以下の Linux オペレーティング システムのサポートが追加されました。
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* VMware Center 6.5 のサポートが追加されました。

次のように、更新プログラムをインストールします。

> [!NOTE]
>すべての Scout コンポーネントのファイル更新バージョンが、更新プログラムの .zip ファイル内で同じとは限りません。 以前のバージョンは、前回の更新プログラムから今回の更新プログラムまでに、コンポーネントに変更がないことを示しています。

[更新プログラム](https://aka.ms/asr-scout-update6) の .zip ファイルをダウンロードします。 ファイルには、以下のコンポーネントが含まれています。 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- RHEL5、OL5、OL6、SUSE 10、SUSE 11 向け UA 更新プログラム 4:UA_\<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. .zip ファイルを抽出します。
  2. **RX サーバー**:**RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** を RX サーバーにコピーし、展開します。 抽出先のフォルダーで、 **/Install** を実行します。
  3. **構成サーバーとプロセス サーバー**:**CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** を構成サーバーとプロセス サーバーにコピーします。 ファイルをダブルクリックして実行します。<br>
  4. **Windows のマスター ターゲット サーバー**:統一されたエージェントを更新するには、サーバーに **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** をコピーします。 ファイルをダブルクリックして実行します。 同じ統合エージェントの更新プログラムはソース サーバーにも適用できます。 ソースが更新プログラム 4 に更新されていない場合、統合エージェントを更新する必要があります。
  これは最新の変更をすべて備えた新しい GA インストーラーなので、**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** で準備されたマスター ターゲット上で更新プログラムを適用する必要はありません。
  5. **vContinuum サーバー**:**vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** をサーバーにコピーします。  vContinuum ウィザードを閉じたことを確認してください。 ファイルをダブルクリックして実行します。
  これは最新の変更をすべて備えた新しい GA インストーラーなので、**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** で準備されたマスター ターゲット上で更新プログラムを適用する必要はありません。
  6. **Linux マスター ターゲット サーバー**:統合エージェントを更新するには、**UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** をマスター ターゲット サーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install** を実行します。
  7. **Windows ソース サーバー**:統一されたエージェントを更新するには、ソース サーバーに **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** をコピーします。 ファイルをダブルクリックして実行します。 
  既に更新プログラム 4 に更新済みの場合や、最新の基本インストーラー **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** を使ってソース エージェントがインストールされている場合は、更新プログラム 5 のエージェントをソース サーバー上にインストールする必要はありません。
  8. **Linux ソース サーバー**:統合エージェントを更新するには、統合エージェント ファイルの対応するバージョンを Linux サーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install** を実行します。  例:RHEL 6.7 64 ビット サーバーの場合は、**UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** をサーバーにコピーし、抽出します。 抽出先のフォルダーで、 **/Install** を実行します。


> [!NOTE]
> * Windows 用の基本の Unified Agent (UA) インストーラーは、Windows Server 2016 をサポートするように更新されました。 新しいインストーラー **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** は、基本の Scout GA パッケージ (**InMage_Scout_Standard_8.0.1 GA Oct17.zip**) に同梱されています。 サポートされているすべての Windows バージョンで同じインストーラーが使用されます。 
> * 基本の Windows vContinuum およびマスター ターゲット インストーラーは、Windows Server 2016 をサポートするように更新されました。 新しいインストーラー **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** は、基本の Scout GA パッケージ (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**) に同梱されています。 Windows 2016 マスター ターゲットおよび Windows 2012R2 マスター ターゲットのデプロイには、同じインストーラーが使用されます。
> * 物理サーバー上の Windows Server 2016 は、ASR Scout でサポートされていません。 Windows Server 2016 VMware VM のみをサポートしています。 
>

#### <a name="bug-fixes-and-enhancements"></a>バグ修正と機能強化
- レプリケートされるディスクの一覧を使用した Linux VM のフェールバック保護に失敗すると、config の末尾が空になります。

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 更新プログラム 5
Scout 更新プログラム 5 は累積的な更新プログラムです。 更新プログラム 1 から更新プログラム 4 までのすべての修正と、下記の新しい修正が含まれています。
- Site Recovery Scout 更新プログラム 4 から更新プログラム 5 までの修正は、特にマスター ターゲットおよび vContinuum コンポーネント向けです。
- ソース サーバー、マスター ターゲット、構成、プロセス、および RX サーバーが既に更新プログラム 4 を実行している場合、マスター ターゲット サーバー上にのみ適用します。 

#### <a name="new-platform-support"></a>新しいプラットフォームのサポート
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 SP4 64 ビット **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** は、Scout GA 基本パッケージ (**InMage_Scout_Standard_8.0.1 GA.zip**) でパッケージ化されています。 「コンテナーの作成」の説明に従って、ポータルから GA パッケージをダウンロードします。


#### <a name="bug-fixes-and-enhancements"></a>バグ修正と機能強化

* Windows クラスターのサポート信頼性向上のための修正
    * P2V MSCS クラスター ディスクのいくつかが回復後に RAW になる問題を修正。
    * P2V MSCS クラスターの回復がディスク順序不一致のために失敗する問題を修正。
    * MSCS クラスターのディスク追加操作がディスク サイズ不一致エラーで失敗する問題を修正。
    * RDM LUN マッピングを持つソース MSCS クラスターの準備完了チェックがサイズ検証で失敗する問題を修正。
    * SCSI が一致しない問題のために単一ノード クラスター保護が失敗する問題を修正。 
    * ターゲット クラスター ディスクが存在する場合、P2V Windows クラスター サーバーの再保護が失敗する問題を修正。 
    
* 固定:フェールバック保護中に、選択されたマスター ターゲット サーバーが保護されたソース マシンと同じ ESXi サーバーにない場合 (フォワード保護中)、フェールバック回復中に vContinuum が間違ったマスター ターゲット サーバーを取得し、回復操作が失敗する。

> [!NOTE]
> * P2V クラスター修正は、Site Recovery Scout 更新プログラム 5 で新しく保護された物理 MSCS クラスターのみに適用されます。 以前の更新プログラムを持つ保護された P2V MSCS クラスターに、クラスターの修正をインストールするには、[Site Recovery Scout リリース ノート](https://aka.ms/asr-scout-release-notes)のセクション 12 に記載されているアップグレードの手順に従います。
> * 再保護の際に、各クラスター ノードで、同じ一連のディスクが最初に保護されたときのようにアクティブである場合は、物理 MSCS クラスターの再保護では既存のターゲット ディスクのみ再利用できます。 そうでない場合、[Site Recovery Scout リリース ノート](https://aka.ms/asr-scout-release-notes)のセクション 12 にある手動の手順に従って、ターゲット側のディスクを適切なデータストア パスに移動し、再保護中に再利用するようにします。 アップグレードの手順に従わずに P2V モードで MSCS クラスターを再保護すると、ターゲット ESXi サーバーに新しいディスクが作成されます。 データストアから古いディスクを手動で削除する必要が生じます。
> * ソース SLES11 またはいずれかの Service Pack の付いた SLES11 サーバーが正常に再起動されたら、再同期する **ルート** ディスクのレプリケーション ペアを手動でマークします。 CX のインターフェイスでは、通知されません。  再同期するルート ディスクをマークしないと、データ整合性の問題が起きる可能性があります。


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 4
Scout 更新プログラム 4 は累積的な更新プログラムです。 更新プログラム 1 から更新プログラム 3 までのすべての修正と、下記の新しい修正が含まれています。

#### <a name="new-platform-support"></a>新しいプラットフォームのサポート

* VCenter/vSphere 6.0、6.1、6.2 のサポートが追加されました
* これらの Linux オペレーティング システムのサポートが追加されました。
  * Red Hat Enterprise Linux (RHEL)7.0、7.1、7.2
  * CentOS 7.0、7.1、7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 ビット **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** は、Scout GA 基本パッケージ (**InMage_Scout_Standard_8.0.1 GA.zip**) でパッケージ化されています。 「コンテナーの作成」の説明に従って、ポータルから Scout GA パッケージをダウンロードします。

#### <a name="bug-fixes-and-enhancements"></a>バグ修正と機能強化

* 不要な再同期の問題を防ぐため、次の Linux オペレーティング システムおよびクローンのシャットダウン処理が強化されました。
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Linux で、統合エージェントのインストール ディレクトリのフォルダーへのすべてのアクセス許可がローカル ユーザーのみに制限されるようになりました。
* Windows で、SQL Server や SharePoint クラスターのような負荷の高い分散アプリケーションで共通の分散整合性ブックマークを発行しているときにタイムアウトになる問題を修正しました。
* 構成サーバーの基本インストーラーの、ログに関連する修正。
* VMware vCLI 6.0 へのダウンロード リンクが Windows マスター ターゲット基本インストーラーに追加されました。
* フェールオーバーとディザスター リカバリーのテスト中のネットワーク構成変更のために、追加のチェックとログが追加されました。
* リテンション期間の情報が構成サーバーにレポートされないという問題の修正。  
* 物理クラスターで、ソース ボリュームを縮小するときにボリュームのサイズ変更が vContinuum ウィザードで失敗するという問題の修正。
* クラスター ディスクが PRDM ディスクの場合、"Failed to find the disk signature" (ディスク署名が見つかりませんでした) というエラーが発生してクラスターによる保護が失敗していました。
* cxps トランスポート サーバーが範囲外の例外のためクラッシュする問題の修正。
* vContinuum ウィザードの **[プッシュ インストール]** ページで、サーバー名と IP アドレスの列をサイズ変更できるようになりました。
* RX API の機能強化:
  * 最新 5 つの共通の整合性ポイントが利用可能になりました (保証済みタグのみ)。
  * 保護されているデバイスのすべてについて、容量と空き領域の詳細が表示されます。
  * ソース サーバー上の Scout ドライバーの状態がわかります。

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** 基本パッケージには、以下が含まれます。
>     * 更新された構成サーバーの基本インストーラー (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Windows マスター ターゲット基本インストーラー (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * 新規インストールの場合はすべて、新しい構成サーバーと Windows マスター ターゲット GA ビットを使用します。
> * 更新プログラム 4 は、8.0.1 GA に直接適用できます。
> * 構成サーバーと RX の更新プログラムは、適用後にロールバックすることはできません。


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 3

Site Recovery の更新プログラムはすべて累積的です。 更新プログラム 3 には、更新プログラム 1 と 更新プログラム 2 のすべての修正が含まれています。 更新プログラム 3 は、8.0.1 GA に直接適用できます。 構成サーバーと RX の更新プログラムは、適用後にロールバックすることはできません。

#### <a name="bug-fixes-and-enhancements"></a>バグ修正と機能強化
更新プログラム 3 では、次の問題が修正されました。

* プロキシを経由する場合、構成サーバーと RX はコンテナーに登録されません。
* 正常性レポートで、目標復旧時点 (RPO) に達していない時間数が更新されません。
* ESX ハードウェアの詳細またはネットワークの詳細に UTF-8 文字が含まれている場合、構成サーバーが RX と同期しません。
* 復旧後、Windows Server 2008 R2 ドメイン コント ローラーが開始しません。
* オフライン同期が、予想どおりに動作していません。
* VM のフェールオーバー後に、構成サーバー コンソールでレプリケーション ペアの削除が長時間にわたり進行しません。 ユーザーは、フェールバックの完了または操作の再開ができません。
* SQL Server クライアントなどのアプリケーション接続の切断を減らすことを目的に、整合性ジョブによるスナップショット操作が全体的に最適化されました。
* 整合性ツール (VACP.exe) のパフォーマンスが改善されました。 Windows 上でのスナップショット作成に必要なメモリ使用量が削減されました。
* パスワードが 16 文字を超える場合、プッシュ インストール サービスがクラッシュします。
* 資格情報が変更される場合、vContinuum は新しい vCenter 資格情報を確認せず、入力を求めません。
* Linux で、マスター ターゲットのキャッシュ マネージャー (cachemgr) はプロセス サーバーからファイルをダウンロードしません。 そのため、レプリケーション ペアの調整が発生します。
* 物理フェールオーバー クラスター (MSCS) ディスクの順序がノード間で統一されていない場合、一部のクラスター ボリュームにレプリケーションが設定されません。 この修正を適用するには、クラスターを再保護しなければいけません。  
* RX が Scout 7.1 から Scout 8.0.1 にアップグレードされた後、SMTP 機能が正常に動作しません。
* ロールバック操作の完了までの時間を追跡するために、より多くの統計がロールバック操作ログに追加されました。
* ソース サーバーの Linux オペレーティング システムに追加されたサポートは次のとおりです。
  * Red Hat Enterprise Linux (RHEL) 6 update 7
  * CentOS 6 update 7
* 構成サーバーと RX コンソールが、ビットマップ モードに移行するペアの通知を表示するようになりました。
* 次のセキュリティ修正プログラムが RX に追加されました。
    * パラメーター改ざんによる承認バイパス:未承認ユーザーのアクセスを制限。
    * クロスサイト リクエスト フォージェリ:ページのトークンの概念が実装され、ページごとにランダムに生成されます。 つまり、同じユーザーに対してシングル サインイン インスタンスのみが存在し、ページの更新は機能しません。 代わりに、ダッシュボードにリダイレクトします。
    * 悪意のあるファイルのアップロード:ファイルは特定の拡張子、つまり 7z、aiff、asf、avi、bmp、csv、doc、docx、fla、flv、gif、gz、gzip、jpeg、jpg、log、mid、mov、mp3、mp4、mpc、mpeg、mpg、ods、odt、pdf、png、ppt、pptx、pxd、qt、ram、rar、rm、rmi、rmvb、rtf、sdc、sitd、swf、sxc、sxw、tar、tgz、tif、tiff、txt、vsd、wav、wma、wmv、xls、xlsx、xml、zip に制限されています。
    * 永続的なクロス サイト スクリプティング:入力の検証が追加されました。

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 2 (2015 年 12 月 3 日)

更新プログラム 2 の修正内容は次のとおりです。

* **構成サーバー**: 構成サーバーが Azure Site Recovery 資格情報コンテナーに登録される際に、31 日間の無料計測機能が予期どおりに動作しない問題。
* **統合エージェント**:マスター ターゲット サーバーをバージョン 8.0 から 8.0.1 にアップグレードする際に更新プログラムがインストールされないという更新プログラム 1 の問題の修正。

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 更新プログラム 1
更新プログラム 1 には、以下のバグの修正と新機能が含まれています。

* 各サーバー インスタンスについて 31 日間の無料保護が提供されます。 これにより、機能をテストしたり、概念実証を設定したりすることができます。
* フェールオーバーとフェールバックを含め、サーバーでのすべての操作は、31 日間無料です。 サーバーが最初に Site Recovery Scout で保護された時点から、時間がカウントされます。 32 日目以降は、お客様が所有するサイトに対する Site Recovery 保護について、各保護対象サーバーに Standard インスタンスの料金が請求されます。
* 現在課金されている保護対象サーバーの数は、いつでもコンテナーの **ダッシュボード** で確認できます。
* vSphere コマンドライン インターフェイス (vCLI) 5.5 更新プログラム 2 のサポートが追加されました。
* ソース サーバー上の次の Linux オペレーティング システムにサポートが追加されました。
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* 次の問題に対処するバグの修正が提供されます。
  * 構成サーバーまたは RX サーバーに対するコンテナー登録に失敗する。
  * クラスター化された VM を再開時に再保護すると、クラスターのボリュームが適切に表示されない。
  * マスター ターゲット サーバーがオンプレミスの運用 VM とは異なる ESXi サーバーでホストされている場合、フェールバックが失敗する。
  * 8\.0.1 にアップグレードすると構成ファイルのアクセス許可が変更される。 この変更によって保護や操作に影響が出る。
  * 再同期のしきい値が適切に適用されないため、レプリケーションの動作に不整合が生じる。
  * 構成サーバー コンソールに、RPO 設定が正しく表示されない。 圧縮されないデータ値に、誤って圧縮された値が表示される。
  * vContinuum ウィザードで削除操作を行っても適切に削除されず、レプリケーションが構成サーバー コンソールから削除されない。
  * vContinuum ウィザードで、MSCS VM の保護中にディスク ビューで **[詳細]** をクリックするとディスクの選択が自動的に解除される。
  * 物理 - 仮想 (P2V) シナリオで、VM の復旧中に必須 HP サービス (CIMnotify や CqMgHost など) が手動に移行しない。 この問題により、起動時間が長くなる。
  * マスター ターゲット サーバーに 27 個以上のディスクがある場合、Linux VM の保護が失敗する。

