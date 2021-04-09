---
title: SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ | Microsoft Docs
description: SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 430af1dfcbd9c7faabfca2f3f5b80b3b3106a889
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675796"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ

このドキュメントでは、Azure IaaS での SAP ASE をデプロイするときに考慮すべきいくつかの異なる領域について説明します。 このドキュメントの前提条件として、「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](dbms_guide_general.md)」ドキュメントと [Azure 上の SAP ワークロードに関するドキュメント](./get-started.md)の中の他のガイドを読んでいる必要があります。 このドキュメントでは、Linux および Windows オペレーティング システムで実行されている SAP ASE について説明します。 Azure でサポートされている最小リリースは SAP ASE 16.0.02 (リリース 16 サポート パック 2) です。 最新バージョンの SAP と最新のパッチ レベルをデプロイすることをお勧めします。  SAP ASE 16.0.03.07 (リリース 16 サポート パック 3 パッチ レベル 7) 以降をお勧めします。  最新バージョンの SAP は、「[Targeted ASE 16.0 Release Schedule and CR list Information](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)」 (対象の ASE 16.0 リリース スケジュールと CR リスト情報) にあります。

SAP アプリケーションでサポートされるリリースやインストール メディアの場所に関する追加情報は、次の場所にある SAP 製品の可用性マトリックスにあります。

- [SAP サポート ノート #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP サポート ノート #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP サポート ノート #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP サポート ノート #1973241](https://launchpad.support.sap.com/#/notes/1973241)

注記:SAP の内部および外部のドキュメント全体で、Sybase ASE または SAP ASE、場合によってはその両方が製品名として参照されています。 一貫性を保つために、このドキュメントでは、**SAP ASE** という名前を使用します。

## <a name="operating-system-support"></a>オペレーティング システムのサポート
SAP 製品の可用性マトリックスには、各 SAP アプリケーションでサポートされているオペレーティング システムと SAP カーネルの組み合わせが含まれています。  Linux ディストリビューション SUSE 12.x、SUSE 15.x、Red Hat 7 は完全にサポートされています。  SAP ASE のオペレーティング システムとして Oracle Linux はサポートされていません。  利用可能な最新の Linux リリースを使用することをお勧めします。 Windows のお客様は、Windows Server 2016 または Windows Server 2019 のリリースを使用する必要があります。  Windows 2012 などの Windows の以前のリリースは技術的にはサポートされていますが、最新の Windows バージョンを常にお勧めします。


## <a name="specifics-to-sap-ase-on-windows"></a>Windows 上の SAP ASE の仕様
Microsoft Azure 以降では、既存の SAP ASE アプリケーションを Azure Virtual Machines に移行できます。 Azure Virtual Machine の SAP ASE では、さまざまなエンタープライズ アプリケーションを Microsoft Azure に簡単に移行することで、これらのアプリケーションのデプロイメント、管理、およびメンテナンスの総保有コストを削減できます。 Azure Virtual Machine の SAP ASE では、管理者および開発者はオンプレミスで提供されているものと同じ開発ツールや管理ツールを引き続き使用できます。

Microsoft Azure では、最小のものから、何千ものユーザーが使用する大規模なものまで、さまざまな規模の SAP システムとランドス ケープの実行を可能にする仮想マシンの種類が多数提供されています。 さまざまな SAP 認定 VM SKU の SAP サイズ設定 SAPS 番号は、[SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されています。

Windows に SAP ASE をインストールするためのドキュメントは、「[SAP ASE Installation Guide for Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)」 (Windows 用 SAP ASE のインストール ガイド) にあります。

[Lock Pages in Memory]\(メモリ内のページのロック\) は、SAP ASE データベース バッファーがページアウトされないようにする設定です。この設定は、大量のメモリを使用する大規模なシステムに役立ちます。 詳細については、BC-DB-SYB にお問い合わせください。 


## <a name="linux-operating-system-specific-settings"></a>Linux オペレーティング システム固有の設定
Linux VM では、プロファイル SAP-ASE Linux Huge Pages を使用した `saptune` の実行を既定で有効にする必要があり、コマンドを使用して検証できます。  

`cat /proc/meminfo` 

ページ サイズは、通常は 2048 KB です。 詳細については、「[Huge Pages on Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html)」 (Linux の Huge Pages) を参照してください。 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>SAP ASE デプロイのための VM とディスク構造に関する推奨事項

SAP NetWeaver Applications 用の SAP ASE は、[SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されているすべての VM の種類でサポートされています。中規模の SAP ASE データベース サーバーに使用される一般的な VM の種類には、Esv3 が含まれています。  数テラバイトの大規模なデータベースでは、M シリーズの VM の種類を利用できます。 M シリーズの書き込みアクセラレータを有効にすると、SAP ASE のトランザクション ログのディスク書き込みパフォーマンスが向上する場合があります。 SAP ASE がログ書き込みを実行する方法により、SAP ASE で書き込みアクセラレータを慎重にテストする必要があります。  [SAP サポート ノート #2816580](../../how-to-enable-write-accelerator.md) を確認し、パフォーマンス テストを実行することを検討してください。  
書き込みアクセラレータは、トランザクション ログのディスク専用に設計されています。 ディスク レベルのキャッシュは "NONE" に設定する必要があります。 Azure 書き込みアクセラレータで他の DBMS と同様の機能強化が示されなくても、驚くことはありません。 SAP ASE がトランザクション ログに書き込む方法によっては、Azure 書き込みアクセラレータによる高速化がほとんどない場合があります。
データ デバイス用とログ デバイス用に個別のディスクを使用することをお勧めします。  システム データベース sybsecurity と `saptools` には、専用のディスクは必要ありません。SAP データベースのデータとログ デバイスを含むディスクに配置できます。 

![SAP ASE 用のストレージ構成](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>ファイル システム、ストライプ サイズと IO 分散 
SAP ASE では、特に構成されていない限り、ディスク ストレージ デバイスにデータが順番に書き込まれます。 つまり、4 台のデバイスを使用する空の SAP ASE データベースでは、最初のデバイスのみにデータが書き込まれます。  他のディスク デバイスは、最初のデバイスがいっぱいになった場合にのみ書き込まれます。  各 SAP ASE デバイスに対する読み取り IO と書き込み IO の量は異なる可能性があります。 使用可能なすべての Azure ディスクでディスク IO を分散させるには、Windows 記憶域スペースまたは Linux LVM2 を使用する必要があります。 Linux では、XFS ファイル システムを使用してディスクをフォーマットすることをお勧めします。 LVM ストライプ サイズは、パフォーマンス テストでテストする必要があります。 128 KB のストライプ サイズから始めることをお勧めします。 Windows では、NTFS アロケーション ユニット サイズ (AUS) をテストする必要があります。 開始値として 64 KB を使用できます。 

「[Configuring Automatic Database Space Expansion in SAP Adaptive Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)」 (SAP Adaptive Server Enterprise で Automatic Database Space Expansion を構成する) の記事および [SAP サポート ノート #1815695](https://launchpad.support.sap.com/#/notes/1815695) で説明されているように、Automatic Database Expansion を構成することをお勧めします。 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Azure 仮想マシン、ディスクおよびファイル システムでの SAP ASE のサンプル構成 
以下のテンプレートは、Linux と Windows の両方のサンプル構成を示しています。 仮想マシンとディスクの構成を確認する前に、個々の VM のネットワークとストレージの帯域幅クォータがビジネス要件を満たすのに十分であることを確認してください。 また、Azure VM の種類によって、VM に接続できるディスクの最大数が異なることにも留意してください。 たとえば、E4s_v3 の VM には、48 MB/秒のストレージ IO スループットの制限があります。 データベース バックアップ アクティビティで 48 MB/秒を超えるストレージ スループットが必要な場合は、より多くのストレージ帯域幅スループットを持つより大きい VM の種類を使用することは避けられません。 Azure ストレージを構成する場合は、特に [Azure Premium ストレージ](../../premium-storage-performance.md)では、容量の GB あたりのスループットと IOPS が変化することにも留意する必要があります。 このトピックについて詳しくは、「[Azure で利用できるディスクの種類](../../disks-types.md)」の記事を参照してください。 Azure VM の特定の種類のクォータについては、「[メモリ最適化済み仮想マシンのサイズ](../../sizes-memory.md)」の記事と、それにリンクされている記事に記載されています。 

> [!NOTE]
>  DBMS システムがオンプレミスから Azure に移行されている場合は、VM での監視を実行し、CPU、メモリ、IOPS、およびストレージのスループットを評価することをお勧めします。 観察されたピーク時の値を、前述の記事に記載されている VM クォータの制限と比較します。

次に示す例は、説明を目的としたものであり、個々のニーズに基づいて変更できます。 SAP ASE の設計により、データ デバイスの数は他のデータベースほど重要ではありません。 このドキュメントで詳しく説明されているデータ デバイスの数は、目安の意味しかありません。 

SAP ソリューション マネージャーなど、データベース サイズが 50 GB - 250 GB の小規模な SAP ASE DB サーバーの構成例を次に示します。

| 構成 | Windows | Linux | 説明 |
| --- | --- | --- | --- |
| VM の種類 | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| 高速ネットワーク | 有効化 | 有効化 | ---|
| SAP ASE のバージョン | 16.0.03.07 以降 | 16.0.03.07 以降 | --- |
| データ デバイスの数 | 4 | 4 | ---|
| ログ デバイスの数 | 1 | 1 | --- |
| 一時デバイスの数 | 1 | 1 | SAP BW ワークロードの場合はこれより多い |
| オペレーティング システム | Windows Server 2019 | SUSE 12 SP4/15 SP1 または RHEL 7.6 | --- |
| ディスク集計 | 記憶域スペース | LVM2 | --- |
| ファイル システム | NTFS | XFS |
| ブロック サイズのフォーマット | ワークロード テストが必要 | ワークロード テストが必要 | --- |
| データ ディスクの数と種類 | Premium Storage:2 x P10 (RAID0) | Premium Storage:2 x P10 (RAID0)| キャッシュ = 読み取り専用 |
| ログ ディスクの数と種類 | Premium Storage:1 x P20  | Premium Storage:1 x P20 | キャッシュ = NONE |
| ASE MaxMemory パラメーター | 物理 RAM の 90% | 物理 RAM の 90% | 1 つのインスタンスを想定 |
| バックアップ デバイスの数 | 4 | 4| --- |
| バックアップ ディスクの数と種類 | 1 | 1 | --- |


より小規模な SAP Business Suite システムなど、データベース サイズが 250 GB - 750 GB の中規模の SAP ASE DB サーバーの構成例を次に示します。

| 構成 | Windows | Linux | 説明 |
| --- | --- | --- | --- |
| VM の種類 | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| 高速ネットワーク | 有効化 | 有効化 | ---|
| SAP ASE のバージョン | 16.0.03.07 以降 | 16.0.03.07 以降 | --- |
| データ デバイスの数 | 8 | 8 | ---|
| ログ デバイスの数 | 1 | 1 | --- |
| 一時デバイスの数 | 1 | 1 | SAP BW ワークロードの場合はこれより多い |
| オペレーティング システム | Windows Server 2019 | SUSE 12 SP4/15 SP1 または RHEL 7.6 | --- |
| ディスク集計 | 記憶域スペース | LVM2 | --- |
| ファイル システム | NTFS | XFS |
| ブロック サイズのフォーマット | ワークロード テストが必要 | ワークロード テストが必要 | --- |
| データ ディスクの数と種類 | Premium Storage:4 x P20 (RAID0) | Premium Storage:4 x P20 (RAID0)| キャッシュ = 読み取り専用 |
| ログ ディスクの数と種類 | Premium Storage:1 x P20  | Premium Storage:1 x P20 | キャッシュ = NONE |
| ASE MaxMemory パラメーター | 物理 RAM の 90% | 物理 RAM の 90% | 1 つのインスタンスを想定 |
| バックアップ デバイスの数 | 4 | 4| --- |
| バックアップ ディスクの数と種類 | 1 | 1 | --- |

より大規模な SAP Business Suite システムなど、データベース サイズが 750 GB - 2000 GB の小規模な SAP ASE DB サーバーの構成例を次に示します。

| 構成 | Windows | Linux | 説明 |
| --- | --- | --- | --- |
| VM の種類 | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| 高速ネットワーク | 有効化 | 有効化 | ---|
| SAP ASE のバージョン | 16.0.03.07 以降 | 16.0.03.07 以降 | --- |
| データ デバイスの数 | 16 | 16 | ---|
| ログ デバイスの数 | 1 | 1 | --- |
| 一時デバイスの数 | 1 | 1 | SAP BW ワークロードの場合はこれより多い |
| オペレーティング システム | Windows Server 2019 | SUSE 12 SP4/15 SP1 または RHEL 7.6 | --- |
| ディスク集計 | 記憶域スペース | LVM2 | --- |
| ファイル システム | NTFS | XFS |
| ブロック サイズのフォーマット | ワークロード テストが必要 | ワークロード テストが必要 | --- |
| データ ディスクの数と種類 | Premium Storage:4 x P30 (RAID0) | Premium Storage:4 x P30 (RAID0)| キャッシュ = 読み取り専用 |
| ログ ディスクの数と種類 | Premium Storage:1 x P20  | Premium Storage:1 x P20 | キャッシュ = NONE |
| ASE MaxMemory パラメーター | 物理 RAM の 90% | 物理 RAM の 90% | 1 つのインスタンスを想定 |
| バックアップ デバイスの数 | 4 | 4| --- |
| バックアップ ディスクの数と種類 | 1 | 1 | --- |


より大規模なグローバルに使用される SAP Business Suite システムなど、データベース サイズが 2 TB 以上の小規模な SAP ASE DB サーバーの構成例を次に示します。

| 構成 | Windows | Linux | 説明 |
| --- | --- | --- | --- |
| VM の種類 | M-Series (1.0 - 4.0 TB の RAM)  | M-Series (1.0 - 4.0 TB の RAM) | --- |
| 高速ネットワーク | 有効化 | 有効化 | ---|
| SAP ASE のバージョン | 16.0.03.07 以降 | 16.0.03.07 以降 | --- |
| データ デバイスの数 | 32 | 32 | ---|
| ログ デバイスの数 | 1 | 1 | --- |
| 一時デバイスの数 | 1 | 1 | SAP BW ワークロードの場合はこれより多い |
| オペレーティング システム | Windows Server 2019 | SUSE 12 SP4/15 SP1 または RHEL 7.6 | --- |
| ディスク集計 | 記憶域スペース | LVM2 | --- |
| ファイル システム | NTFS | XFS |
| ブロック サイズのフォーマット | ワークロード テストが必要 | ワークロード テストが必要 | --- |
| データ ディスクの数と種類 | Premium Storage:4+ x P30 (RAID0) | Premium Storage:4+ x P30 (RAID0)| キャッシュ = 読み取り専用、Azure Ultra Disk を検討 |
| ログ ディスクの数と種類 | Premium Storage:1 x P20  | Premium Storage:1 x P20 | キャッシュ = NONE、Azure Ultra Disk を検討 |
| ASE MaxMemory パラメーター | 物理 RAM の 90% | 物理 RAM の 90% | 1 つのインスタンスを想定 |
| バックアップ デバイスの数 | 16 | 16 | --- |
| バックアップ ディスクの数と種類 | 4 | 4 | LVM2 または記憶域スペースを使用 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Azure での SAP ASE のバックアップと復元に関する考慮事項
データとバックアップのデバイスの数を増やすと、バックアップと復元のパフォーマンスが向上します。 前の表に示したように、SAP ASE バックアップ デバイスをホストしている Azure ディスクをストライピングすることをお勧めします。 バックアップ デバイスとディスクの数のバランスを取り、バックアップのスループットが VM スループット クォータ合計の 40% - 50% を超えないように注意する必要があります。 既定として SAP バックアップ圧縮を使用することをお勧めします。 詳細については次の記事を参照してください。

- [SAP サポート ノート #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP サポート ノート #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP サポート ノート #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

データベースまたはログのダンプ先として、D:\ ドライブまたは /temp 空間を使用しないでください。

### <a name="impact-of-database-compression"></a>データベースの圧縮の影響
I/O 帯域幅が制限要因になる構成では、IOPS を削減する手段が Azure のような IaaS シナリオで実行できるワークロードを拡張するのに役立つ場合があります。 そのため、既存の SAP データベースを Azure にアップロードする前に、SAP ASE 圧縮が使用されていることを確認するようお勧めします。

次の理由により、Azure にアップロードする前に圧縮を適用することをお勧めします。

* Azure にアップロードするデータの量が少なくなります。
* オンプレミスよりも CPU が多いまたは I/O 帯域幅が大きい、または I/O 待機時間が少ない強力なハードウェアを使用できるため、圧縮の実行にかかる時間が短い
* 比較的小さなサイズのデータベースでは、ディスク割り当てのコストが少なくなる可能性があります。

データベースと LOB の圧縮は、オンプレミスと同様に Azure Virtual Machines でも動作します。 既存の SAP ASE データベースで既に圧縮を使用しているかどうかを確認する方法について詳しくは、[SAP サポート ノート 1750510](https://launchpad.support.sap.com/#/notes/1750510) をご覧ください。 SAP ASE データベースの圧縮チェックの詳細については、[SAP サポート ノート #2121797](https://launchpad.support.sap.com/#/notes/2121797) を参照してください。

## <a name="high-availability-of-sap-ase-on-azure"></a>Azure での SAP ASE の高可用性 
HADR ユーザー ガイドでは、2 ノードの SAP ASE "Always-on" ソリューションのセットアップと構成について詳しく説明されています。  さらに、3 つ目のディザスター リカバリー ノードもサポートされています。 SAP ASE では、共有ディスクやネイティブ OS クラスタリング (フローティング IP) など、多くの高可用性構成がサポートされています。 Azure でサポートされている構成は、Floating IP なしで Fault Manager を使用することだけです。  Floating IP Address メソッドは、Azure では機能しません。  SAP カーネルは "HA 対応" アプリケーションであり、プライマリとセカンダリの SAP ASE サーバーを認識します。 SAP ASE と Azure の間に密接な統合がないため、Azure 内部ロード バランサーは使用されません。 そのため、標準の SAP ASE のドキュメントは、「[SAP ASE HADR Users Guide](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)」 (SAP ASE HADR ユーザー ガイド) から始める必要があります。 

> [!NOTE]
> Azure でサポートされている構成は、Floating IP なしで Fault Manager を使用することだけです。  Floating IP Address メソッドは、Azure では機能しません。 

### <a name="third-node-for-disaster-recovery"></a>ディザスター リカバリーのための 3 番目のノード
ローカルの高可用性のために SAP ASE Always-On を使用する以外に、構成を別の Azure リージョンの非同期にレプリケートされたノードに拡張することが必要になる場合があります。 このようなシナリオのドキュメントは、[こちら](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)を参照してください。

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE データベース暗号化と SSL 
SAP Software Provisioning Manager (SWPM) には、インストール時にデータベースを暗号化するオプションが用意されています。  暗号化を使用する場合は、SAP Full Database Encryption を使用することをお勧めします。  詳細については、次のドキュメントを参照してください。

- [SAP サポート ノート #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP サポート ノート #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP サポート ノート #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP サポート ノート #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> SAP ASE データベースが暗号化されている場合、Backup Dump Compression は機能しません。 [SAP サポート ノート #2680905](https://launchpad.support.sap.com/#/notes/2680905) も参照してください 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Azure での SAP ASE のデプロイのチェックリスト
 
- SAP ASE 16.0.03.07 以降をデプロイする
- FaultManager と SAPHostAgent の最新バージョンおよびパッチに更新する
- Windows 2019、Suse 15.1、Redhat 7.6 以降など、利用可能な最新の認定 OS にデプロイする
- SAP 認定 VM の使用。Es_v3 などの高メモリ Azure VM SKU、または非常に大規模なシステム用の M シリーズ VM SKU を推奨
- ディスクの IOPS と VM の合計 VM 合計スループットのクォータをディスクの設計と一致させる  十分な数のディスクをデプロイする
- 正しいストライプ サイズとファイル システムを持つ Windows 記憶域スペースまたは Linux LVM2 を使用して、ディスクを集約する
- データ、ログ、一時、およびバックアップ用に十分な数のデバイスを作成する
- 非常に大規模なシステムには UltraDisk の使用を検討する 
- Linux OS で SAP-ASE `saptune` を実行する 
- DB 暗号化を使用してデータベースをセキュリティで保護する。キーは Azure Key Vault に手動で格納する 
- [SAP on Azure チェックリスト](./sap-deployment-checklist.md)を完成させる 
- ログ バックアップと完全バックアップを構成する 
- HA/DR のテスト、バックアップと復元、ストレスとボリューム テストを実行する 
- データベースの自動拡張が機能していることを確認する 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit を使用してデータベースのインスタンスを監視する
データベース プラットフォームとして SAP ASE を使用する SAP システムの場合、DBACockpit はトランザクション DBACockpit に埋め込まれたブラウザー ウィンドウとして、または Webdynpro としてアクセスできます。 ただし、データベースの管理と管理を行うための完全な機能は、DBACockpit の Webdynpro の実装でのみ使用可能です。

オンプレミス システムと同様に、DBACockpit の Webdynpro 実装で使用されるすべての SAP NetWeaver 機能を有効にするために、いくつかの手順が必要です。 [SAP サポート ノート #1245200](https://launchpad.support.sap.com/#/notes/1245200) に従って、Webdynpro の使用を有効にし、必要なものを生成します。 上記のノートの手順に従う際には、インターネット通信マネージャー (`ICM`) に加えて、http 接続や https 接続に使用するポートも構成します。 http の既定の設定は次のようになります。

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

トランザクション DBACockpit で生成されたリンクは次のようになります。

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP システムをホストする Azure Virtual Machine が AD や DNS に接続されている方法に従って、DBACockpit を開こうとしているコンピューターで解決できる完全修飾ホスト名を ICM が使用していることを確認する必要があります。 ICM がプロファイルのパラメーターに応じて完全修飾ホスト名を判定し、必要な場合に icm/host_name_full に明示的にパラメーターを設定する方法について詳しくは、[SAP サポート ノート #773830](https://launchpad.support.sap.com/#/notes/773830) をご覧ください。

オンプレミスと Azure 間のクロスプレミス接続を使用しないクラウドのみのシナリオで VM をデプロイした場合は、パブリック IP アドレスと `domainlabel` を定義する必要があります。 VM のパブリック DNS 名の形式は、次のようになります。

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS 名の詳細については、[こちら][virtual-machines-azurerm-versus-azuresm] をご覧ください。

SAP プロファイル パラメーター icm/host_name_full を Azure VM のリンクの DNS 名に設定すると、リンクは次のようになる場合があります。

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

この場合、次のことを行う必要があります。

* ICM との通信に使用される TCP/IP ポート用に、Azure Portal でネットワーク セキュリティ グループへの受信規則を追加します。
* ICM との通信に使用される TCP/IP ポート用に Windows ファイアウォールの構成への受信規則を追加します。

使用可能なすべての修正を自動的にインポートする場合、使用している SAP のバージョンに該当する修正コレクション SAP Note を定期的に適用することをお勧めします。

* [SAP サポート ノート #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP サポート ノート #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP サポート ノート #1882376](https://launchpad.support.sap.com/#/notes/1882376)

SAP ASE の DBA Cockpit に関する詳細については、次の SAP Note で参照することができます。

* [SAP サポート ノート #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP サポート ノート #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP サポート ノート #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP サポート ノート #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP サポート ノート #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP サポート ノート #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP サポート ノート #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP サポート ノート #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>SAP ASE の便利なリンク、ノートとホワイトペーパー
[SAP ASE 16.0.03.07 ドキュメント](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US)の開始ページでは、次のドキュメントを含むさまざまなドキュメントへのリンクが提供されています。

- SAP ASE Learning Journey - Administration & Monitoring (SAP ASE Learning Journey - 管理と監視)
- SAP ASE Learning Journey - Installation & Upgrade (SAP ASE Learning Journey - インストールとアップグレード)

これらは有用です。 もう 1 つの有用なドキュメントは、「[SAP Applications on SAP Adaptive Server Enterprise Best Practices for Migration and Runtime](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)」 (SAP Adaptive Server Enterprise の SAP Applications の移行とランタイムに対するベスト プラクティス) です。

その他の有用な SAP サポート ノートは次のとおりです。

- [SAP サポート ノート #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP サポート ノート #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP サポート ノート #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP サポート ノート #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP サポート ノート #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP サポート ノート #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP サポート ノート #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP サポート ノート #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP サポート ノート #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP サポート ノート #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP サポート ノート #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP サポート ノート #1588316](https://launchpad.support.sap.com/#/notes/158831) 

その他の情報は以下に公開されています。 

- [SAP Applications on SAP Adaptive Server Enterprise](https://community.sap.com/topics/applications-on-ase)
- [SAP ASE Infocenter](http://infocenter.sybase.com/help/index.jsp) 
- [3 番目の DR ノード セットアップを使用した SAP ASE Always-on](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

毎月のニュースレターは、[SAP サポート ノート #2381575](https://launchpad.support.sap.com/#/notes/2381575) を通じて公開されます。 


## <a name="next-steps"></a>次のステップ
「[Azure での SAP ワークロード: 計画とデプロイに関するチェックリスト](./sap-deployment-checklist.md)」の記事を確認します。