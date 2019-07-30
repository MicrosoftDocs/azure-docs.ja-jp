---
title: クイック スタート:Azure Virtual Machines への単一インスタンスの SAP HANA の手動インストール | Microsoft Docs
description: Azure Virtual Machines に単一インスタンスの SAP HANA を手動でインストールするためのクイック スタート ガイド
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 914da98359d11ff25709164d6301737404b3b011
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707662"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>クイック スタート:Azure Virtual Machines への単一インスタンスの SAP HANA の手動インストール
## <a name="introduction"></a>はじめに
このガイドは、SAP NetWeaver 7.5 と SAP HANA 1.0 SP12 を手動でインストールする場合に、Azure Virtual Machines に単一インスタンスの SAP HANA を設定するのに役立ちます。 このガイドでは、Azure での SAP HANA のデプロイ方法を中心に説明します。 このガイドは SAP のマニュアルに代わるものではありません。 

> [!NOTE]
> このガイドでは、Azure VM への SAP HANA のデプロイについて説明します。 SAP HANA を HANA の大きなインスタンスにデプロイする方法については、[Azure Virtual Machines における SAP の使用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)に関するページを参照してください。
 
## <a name="prerequisites"></a>前提条件
このガイドは、次のようなサービスとしてのインフラストラクチャ (IaaS) の基本事項について理解していることを前提としています。
 * Azure portal または PowerShell を使って仮想マシン (VM) または仮想ネットワークをデプロイする方法。
 * JSON (JavaScript Object Notation) テンプレートを使うためのオプションなど、Azure クロスプラットフォーム コマンドライン インターフェイス (CLI)。

このガイドは、次の事項を熟知していることを前提としています。
* SAP HANA と SAP NetWeaver およびそれらをオンプレミスにインストールする方法。
* Azure 上で SAP HANA および SAP アプリケーション インスタンスをインストールして操作する方法。
* 次の概念および手順:
   * Azure Virtual Network の計画と Azure Storage の使用を含む、Azure 上での SAP デプロイの計画。 [Azure Virtual Machines での SAP NetWeaver の計画と実装に関するガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)を参照してください。
   * デプロイの基本原則と Azure で VM をデプロイする方法。 「[SAP のための Azure Virtual Machines のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)」を参照してください。
   * Azure での SAP NetWeaver ABAP SAP Central Services (ASCS)、SAP Central Services (SCS)、および Enqueue Replication Server (ERS) の高可用性。 「[Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide)」を参照してください。
   * Azure で ASCS/SCS のマルチ SID インストールの効率を向上させる方法の詳細。 「[SAP NetWeaver マルチ SID 構成の作成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid)」を参照してください。 
   * Azure での Linux 駆動型 VM ベースの SAP NetWeaver の実行の原則。 [Microsoft Azure SUSE Linux VM での SAP NetWeaver の実行](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)に関するページを参照してください。 このガイドでは Azure VM の Linux に固有の設定について説明します。 Azure ストレージ ディスクを Linux VM に正しく接続する方法についても説明します。

運用環境シナリオで使用できる Azure VM の種類は、[IAAS の SAP マニュアル](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)に記載されています。 運用環境以外のシナリオでは、さまざまなネイティブの Azure VM の種類が使用できます。
VM の構成と運用の詳細については、「[Azure における SAP HANA インフラストラクチャの構成と運用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)」を参照してください。
SAP HANA の高可用性については、「[Azure 仮想マシンの SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)」を参照してください。

SAP HANA インスタンスすなわち S/4HANA または BW/4HANA システムを迅速にデプロイする必要がある場合は、[SAP Cloud Appliance Library](https://cal.sap.com) の使用を検討します。 たとえば、S/4HANA システムを SAP Cloud Appliance Library を使用して Azure にデプロイする方法については[このガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)をご覧ください。 必要なのは、Azure サブスクリプションと SAP Cloud Appliance Library に登録できる SAP ユーザーだけです。

## <a name="additional-resources"></a>その他のリソース
### <a name="sap-hana-backup"></a>SAP HANA バックアップ
Azure VM での SAP HANA データベースのバックアップ方法の詳細については以下を参照してください。
* [Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [ファイル レベルの SAP HANA Azure バックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [ストレージ スナップショットに基づいた SAP HANA のバックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
SAP Cloud Appliance Library を使用して S/4HANA または BW/4HANA をデプロイする方法については、「[Microsoft Azure に SAP S/4HANA または BW/4HANA をデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)」を参照してください。

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA でサポートされているオペレーティング システム
SAP HANA でサポートされるオペレーティング システムの詳細については、「[SAP Note 2235581 - SAP HANA:Supported operating systems (SAP ノート 2235581 - SAP HANA: サポートされているオペレーティング システム)](https://launchpad.support.sap.com/#/notes/2235581/E)」を参照してください。 Azure VM では、これらのオペレーティング システムのサブセットのみがサポートされます。 Azure への SAP HANA のデプロイについては、以下のオペレーティング システムがサポートされています。 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

SAP HANA および各種の Linux オペレーティング システムに関する追加の SAP のマニュアルについては、以下を参照してください。

* [SAP ノート 171356:Linux 上の SAP ソフトウェア:一般情報](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP ノート 1944799:SLES オペレーティング システムのインストールに関する SAP HANA ガイドライン](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [SAP ノート 2205917:SAP HANA DB: 推奨される SLES 12 for SAP Applications 向け OS 設定](https://launchpad.support.sap.com/#/notes/2205917/E)
* [SAP ノート 1391070:Linux UUID ソリューション](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP ノート 2009879:Red Hat Enterprise Linux (RHEL) オペレーティング システムに関する SAP HANA ガイドライン](https://launchpad.support.sap.com/#/notes/2009879)
* [SAP ノート 2292690:SAP HANA DB:RHEL 7 に推奨される OS 設定](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>Azure での SAP の監視
Azure での SAP の監視方法については、以下を参照してください。

* 「[SAP Note 2191498 (SAP ノート 2191498)](https://launchpad.support.sap.com/#/notes/2191498/E)」には、Azure 上の Linux VM を使用した SAP の "拡張された監視機能" についての説明が記載されています。 
* 「[SAP Note 1102124 (SAP ノート 1102124 )](https://launchpad.support.sap.com/#/notes/1102124/E)」には、Linux での SAPOSCOL に関する情報が記載されています。 
* 「[SAP Note 2178632 (SAP ノート 2178632)](https://launchpad.support.sap.com/#/notes/2178632/E)」には、Microsoft Azure 上の SAP 向けの主要な監視メトリックの説明が記載されています。

### <a name="azure-vm-types"></a>Azure VM の種類
Azure VM の種類と SAP HANA に関連して SAP でサポートされるワークロード シナリオは、「[SAP certified IaaS Platforms (SAP の認定 IaaS プラットフォーム)](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)」に記載されています。 

SAP NetWeaver または S/4HANA アプリケーション レイヤーに関して SAP によって認定されている Azure VM の種類は以下に記載されています: 「[SAP Note 1928533:SAP applications on Azure:Supported products and Azure VM types (SAP サポート ノート #1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533/E)」。

> [!NOTE]
> SAP-Linux-Azure 統合は、Azure Resource Manager でのみサポートされ、クラシック デプロイ モデルではサポートされません。 

## <a name="manual-installation-of-sap-hana"></a>SAP HANA の手動インストール

> [!IMPORTANT]
> 選択した OS が、使用している特定の VM の種類の SAP HANA に対して認定されていることを確認してください。 SAP HANA 認定 VM の種類と、それらの VM の種類に対応する OS リリースの一覧は、[SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)に関するページに記載されています。 表示されている VM の種類をクリックすると、特定の VM の種類に対して SAP HANA でサポートされている OS のリリースの一覧が表示され、詳細を確認できます。 このドキュメントの例では、M シリーズの VM で SAP HANA がサポートしていない SUSE Linux Enterprise Server (SLES) OS リリースが使用されています。
>

このガイドでは、Azure VM に SAP HANA を手動でインストールする方法を 2 とおり説明します。

* NetWeaver 分散インストールの一環として、"データベース インスタンスのインストール" の手順で SAP Software Provisioning Manager (SWPM) を使います。
* SAP HANA データベース ライフサイクル管理 (HDBLCM) ツールを使い、その後 NetWeaver をインストールします。

SWPM を使ってすべてのコンポーネント (SAP HANA、SAP アプリケーション サーバー、ASCS インスタンスなど) を単一の VM にインストールすることもできます。 手順は、この[SAP HANA ブログの通知](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)に記載されています。 このオプションはこのクイックスタート ガイドには記載されていませんが、考慮する必要がある問題は同じです。

インストールを開始する前に、このガイドの後半にある「SAP HANA の手動インストール用の Azure VM の準備」をお読みいただくことをお勧めします。 そうすると、既定の Azure VM 構成のみを使う場合に発生する可能性のあるいくつかの基本的なミスを防ぐのに役立ちます。

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>SAP HANA を使用する場合の SAP SWPM のインストールの主な手順
ここでは、SAP SWPM を使って SAP NetWeaver 7.5 の分散インストールを実行するときに、単一インスタンスの SAP HANA を手動でインストールするための主要な手順を示します。 個々の手順については、このガイドの後半でスクリーン ショットでさらに詳しく説明します。

1. 2 つのテスト VM が含まれる Azure 仮想ネットワークを作成する。
2. Azure Resource Manager モデルに従ってオペレーティング システムを備えた 2 つの Azure VM をデプロイする。 この例では、SUSE Linux Enterprise Server と SLES for SAP Applications 12 SP1 が使用されます。 
3. Standard または Premium の Azure ストレージ ディスク (たとえば 75 GB または 500 GB のディスク) を 2 つ、アプリケーション サーバー VM にアタッチする。
4. Premium Storage ディスクを HANA DB サーバー VM にアタッチする。 詳細については、このガイドの後半の「ディスクのセットアップ」セクションを参照してください。
5. サイズまたはスループットの要件に応じて、複数のディスクをアタッチする。 次にストライプ ボリュームを作成します。 VM 内の OS レベルで、論理ボリューム管理 (LVM) または複数デバイス管理 (mdadm) いずれかのツールを使います。
6. アタッチされたディスク/論理ボリューム上に XFS ファイル システムを作成する。
7. 新しい XFS ファイル システムを OS レベルでマウントする。 すべての SAP ソフトウェアで 1 つのファイル システムを使用します。 たとえば、/sapmnt ディレクトリとバックアップには他のファイル システムを使用します。 SAP HANA DB サーバーで、Premium Storage ディスクに XFS ファイル システムを /hana および /usr/sap としてマウントします。 このプロセスは、ルート ファイル システムがいっぱいになるのを防ぐために必要です。 ルート ファイル システムは Linux Azure VM では容量が大きくありません。 
8. テスト VM のローカル IP アドレスを /etc/hosts ファイルに入力する。
9. /etc/fstab ファイルに **nofail** パラメーターを入力する。
10. 使用している Linux OS のリリースに従って、Linux カーネル パラメーターを設定する。 詳細については、HANA について説明している SAP ノートおよびこのガイドの「カーネル パラメーター」セクションを参照してください。
11. スワップ領域を追加する。
12. 必要に応じて、テスト VM にグラフィカル デスクトップをインストールする。 インストールしない場合は、リモート SAPinst インストールを使います。
13. SAP サービス マーケットプレースで SAP ソフトウェアをダウンロードする。
14. SAP ASCS インスタンスをアプリ サーバー VM にインストールする。
15. NFS を使って、テスト VM 間で /sapmnt ディレクトリを共有する。 アプリケーション サーバー VM は NFS サーバーです。
16. SWPM を使って、HANA が含まれたデータベース インスタンスを DB サーバー VM にインストールする。
17. アプリケーション サーバー VM に、プライマリ アプリケーション サーバー (PAS) をインストールする。
18. SAP 管理コンソール (SAP MC) を起動する。 たとえば、SAP GUI または HANA Studio と接続します。

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>HDBLCM を使用する場合の SAP HANA のインストールの主な手順
ここでは、SAP HDBLCM を使って SAP NetWeaver 7.5 の分散インストールを実行するときに、単一インスタンスの SAP HANA を手動でインストールするための主要な手順を示します。 個々の手順については、このガイド全体でスクリーン ショットでさらに詳しく説明します。

1. 2 つのテスト VM が含まれる Azure 仮想ネットワークを作成する。
2. Azure Resource Manager モデルに従ってオペレーティング システムを備えた 2 つの Azure VM をデプロイする。 この例では、SLES および SLES for SAP Applications 12 SP1 が使用されます。
3. Standard または Premium の Azure ストレージ ディスク (たとえば 75 GB または 500 GB のディスク) を 2 つ、アプリ サーバー VM にアタッチする。
4. Premium Storage ディスクを HANA DB サーバー VM にアタッチする。 詳細については、このガイドの後半の「ディスクのセットアップ」セクションを参照してください。
5. サイズまたはスループットの要件に応じて、複数のディスクをアタッチする。 VM 内の OS レベルで論理ボリューム管理または mdadm ツールを使ってストライプ ボリュームを作成する。
6. アタッチされたディスク/論理ボリューム上に XFS ファイル システムを作成する。
7. 新しい XFS ファイル システムを OS レベルでマウントする。 すべての SAP ソフトウェアで 1 つのファイル システムを使用します。 たとえば、/sapmnt ディレクトリとバックアップには他のファイル システムを使用します。 SAP HANA DB サーバーで、Premium Storage ディスクに XFS ファイル システムを /hana および /usr/sap としてマウントします。 このプロセスは、ルート ファイル システムがいっぱいになるのを防ぐために必要です。 ルート ファイル システムは Linux Azure VM では容量が大きくありません。
8. テスト VM のローカル IP アドレスを /etc/hosts ファイルに入力する。
9. /etc/fstab ファイルに **nofail** パラメーターを入力する。
10. 使用している Linux OS のリリースに従って、カーネル パラメーターを設定する。 詳細については、HANA について説明している SAP ノートおよびこのガイドの「カーネル パラメーター」セクションを参照してください。
11. スワップ領域を追加する。
12. 必要に応じて、テスト VM にグラフィカル デスクトップをインストールする。 インストールしない場合は、リモート SAPinst インストールを使います。
13. SAP サービス マーケットプレースで SAP ソフトウェアをダウンロードする。
14. HANA DB サーバー VM で、グループ ID が 1001 のグループ "sapsys" を作成する。
15. HANA データベース ライフサイクル マネージャーを使って、DB サーバー VM に SAP HANA をインストールする。
16. SAP ASCS インスタンスをアプリ サーバー VM にインストールする。
17. NFS を使って、テスト VM 間で /sapmnt ディレクトリを共有する。 アプリケーション サーバー VM は NFS サーバーです。
18. SWPM を使って、HANA が含まれたデータベース インスタンスを HANA DB サーバー VM にインストールする。
19. アプリケーション サーバー VM に、プライマリ アプリケーション サーバーをインストールする。
20. SAP MC を起動する。 SAP の GUI または HANA Studio 経由で接続します。

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>SAP HANA を手動でインストールするための Azure VM の準備
このセクションは、次のトピックで構成されています。

* OS の更新
* ディスクのセットアップ
* カーネル パラメーター
* ファイル システム
* /etc/hosts ファイル
* /etc/fstab ファイル

### <a name="os-updates"></a>OS の更新
追加のソフトウェアをインストールする前に Linux OS の更新および修正プログラムを確認します。 修正プログラムをインストールすると、サポート デスクへの呼び出しを回避できる可能性があります。

必ず次を使用してください。
* SUSE Linux Enterprise Server for SAP Applications。
* Red Hat Enterprise Linux for SAP Applications または Red Hat Enterprise Linux for SAP HANA。 

Linux ベンダーの Linux サブスクリプションに OS のデプロイを登録します (この作業がまだの場合のみ)。 SUSE には、既にサービスが含まれた、自動的に登録される SAP アプリケーション用の OS イメージがあります。

ここでは、**zypper**コマンドを使用して SUSE Linux で使用可能な更新プログラムを確認する方法の例を示します。

 `sudo zypper list-patches`

問題の種類に基づいて、パッチはカテゴリ別および重大度別に分類されます。 カテゴリで一般的に使用される値は次のとおりです。 
- セキュリティ
- 推奨
- 省略可能
- 機能
- ドキュメント
- yast

重大度で一般的に使用される値は次のとおりです。

- 重大
- 重要
- 中
- 低
- 未指定

**Zypper** コマンドは、インストールされているパッケージに必要な更新プログラムのみを検索します。 たとえば、このコマンドを使用することができます。

`sudo zypper patch  --category=security,recommended --severity=critical,important`

パラメーター `--dry-run` を追加すると、実際にシステムを更新することなく、更新プログラムをテストすることができます。


### <a name="disk-setup"></a>ディスクのセットアップ
Azure 上の Linux VM のルート ファイル システムには、サイズの制限があります。 SAP を実行するために Azure VM に追加のディスク領域を接続する必要があります。 SAP アプリケーション サーバーの Azure VM の場合、Azure Standard Storage ディスクを使用すれば十分な可能性があります。 SAP HANA DBMS Azure VM の場合、運用環境と非運用環境のどちらの実装にも Azure Premium Storage ディスクの使用が必須です。

[SAP HANA TDI ストレージ要件](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)に基づいて、次の Azure Premium Storage 構成をお勧めします。 

| VM の SKU | RAM |  /hana/data and /hana/log <br /> LVM または mdadm によるストライピング | /hana/shared | /root ボリューム | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

推奨されるディスク構成では、HANA データ ボリュームとログ ボリュームは、LVM または mdadm を使用してストライピングされている Azure Premium Storage ディスクの同じセットに配置されます。 Azure Premium Storage では冗長性を確保するためにディスクのイメージが 3 つ保持されるため、RAID 冗長レベルを定義する必要はありません。 

構成したストレージが十分であるかどうかを確認するために、「[SAP HANA TDI storage Requirements (SAP HANA TDI ストレージ要件)](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)」および「[SAP HANA Server Installation and Update Guide (SAP HANA サーバー インストールおよびアップデート ガイド)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)」を参照してください。 「[VM 向けの高パフォーマンスの Premium Storage とマネージド ディスク](../../windows/disks-types.md)」に関するページの説明に従って、異なる Azure Premium ストレージ ディスクの異なる仮想ハード ディスク (VHD) スループット ボリュームも考慮してください。 

データベースまたはトランザクション ログのバックアップを格納するために、HANA DBMS VM に Premium Storage ディスクを追加することができます。

ストライピングを構成するために使用する 2 つの主要なツールについて詳しくは、以下をご覧ください。

* [Linux でのソフトウェア RAID の構成](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure で Linux VM の LVM を構成する](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

ゲスト OS として Linux を実行する Azure VM にディスクをアタッチする方法については、「[Linux VM へのディスクの追加](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

Azure Premium SSD をし王すると、ディスクのキャッシュ モードを定義できます。 /hana/data と /hana/log を保持するストライプ セットでは、ディスク キャッシュを無効にします。 その他のボリューム (つまりディスク) では、キャッシュ モードを **ReadOnly** に設定します。

VM の作成に使用する JSON のサンプル テンプレートを検索するには、[Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates)に関するページをご覧ください。
vm-simple-sles テンプレートは、基本的なテンプレートです。 追加 100 GB のデータ ディスクを使用する記憶域セクションが含まれています。 このテンプレートをベースとして使用します。 テンプレートは特定の構成に適用できます。

> [!NOTE]
> 「[Microsoft Azure SUSE Linux VM での SAP NetWeaver の実行](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」に記載されているように、UUID を使用して Azure Storage ディスクをアタッチすることが重要です。

テスト環境で、次のスクリーンショットに示すように 2 つの Azure Standard Storage ディスクが SAP アプリ サーバー VM にアタッチされています。 1 つのディスクには、インストール用の SAP ソフトウェア全体が格納されます (NetWeaver 7.5、SAP GUI、SAP HANA など)。 2 つ目のディスクにより、追加の要件に対して十分な空き領域が使用可能になります。 たとえば、バックアップ、テスト データ、および /sapmnt ディレクトリ (つまり SAP プロファイル) は、同じ SAP ランドスケープに属するすべての VM で共有する必要があります。

![2 つのデータ ディスクとそのサイズを表示している SAP HANA アプリ サーバーの [ディスク] ウィンドウ](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>カーネル パラメーター
SAP HANA では特定の Linux カーネル設定が必要です。 これらのカーネル設定は標準的な Azure ギャラリー イメージに含まれていません。手動で設定する必要があります。 パラメーターは、SUSE または Red Hat のどちらを使うかで異なる場合があります。 前述の SAP Note には、これらのパラメーターに関する情報が記載されています。 そこに掲載されていたスクリーンショットでは、SUSE Linux 12 SP1 が使用されていました。 

SLES for SAP Applications 12 一般提供と SLES for SAP Applications 12 SP1 には、古い **sapconf** ツールを置き換える新しいツール **tuned-adm** があります。 **tuned-adm** では、特殊な SAP HANA プロファイルを利用できます。 SAP HANA のシステムを調整するには、ルート ユーザーとして、次のプロファイルを入力します。

   `tuned-adm profile sap-hana`

**tuned-adm** について詳しくは、[tuned-adm に関する SUSE ドキュメント](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)を参照してください。

次のスクリーンショットでは、必要とされる SAP HANA の設定に応じて `transparent_hugepage` と `numa_balancing` の値が **tuned-adm** によって変更されているようすがわかります。

![tuned-adm ツールは、必要な SAP HANA 設定に従って値を変更します](./media/hana-get-started/image005.jpg)

SAP HANA カーネル設定を永続的な設定にするには、SLES 12 で **grub2** を使う必要があります。 [grub2](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) について詳しくは、SUSE ドキュメントの「**Configuration File Structure (ファイル構造の構成)** 」セクションをご覧ください。

次のスクリーンショットでは、構成ファイルでカーネル設定が変更されており、**grub2-mkconfig** を使ってコンパイルされていることがわかります。

![構成ファイルで変更され、grub2-mkconfig を使ってコンパイルされたカーネル設定](./media/hana-get-started/image006.jpg)

このほか、YaST で設定を変更し、 **[ブート ローダー]**  >  **[カーネル パラメーター]** 設定を変更するという方法もあります。

![YaST ブート ローダーの [カーネル パラメーター] 設定タブ](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>ファイル システム
次のスクリーンショットは、SAP アプリ サーバー VM にアタッチされた 2 つの Azure Standard Storage ディスクに作成された 2 つのファイル システムを示しています。 どちらのファイル システムの種類も XFS であり、/sapdata と /sapsoftware にマウントされています。

この方法でファイル システムを構成することは必須ではありません。 ディスク領域を構築する方法について他のオプションもあります。 最も重要な考慮事項は、ルート ファイル システムの空き領域の不足を防ぐことです。

![SAP アプリ サーバー VM に作成された 2 つのファイル システム](./media/hana-get-started/image008.jpg)

SAP HANA DB VM に関しては、データベースのインストール時に SWPM を使用する SAPinst と**一般的な**インストール オプションを使用した場合、すべてが /hana と /usr/sap にインストールされます。 SAP HANA ログ バックアップの既定の場所は /usr/sap です。 ここでも、重要な考慮事項は、ルート ファイル システムのストレージ領域の不足を防ぐことです。 SWPM を使用して SAP HANA をインストールする前に、/hana と /usr/sap の空き領域を十分に確保してください。

SAP HANA の標準のファイルシステム レイアウトの説明は、「[SAP HANA Server Installation and Update Guide (SAP HANA サーバーのインストールと更新のガイド)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)」をご覧ください。

![SAP アプリ サーバー VM に作成された追加のファイル システム](./media/hana-get-started/image009.jpg)

標準的な SLES/SLES for SAP Applications 12 Azure ギャラリー イメージに SAP NetWeaver をインストールすると、次のスクリーンショットに示すようにスワップ領域がないことを示すメッセージが表示されます。 このメッセージを消すために、**dd**、**mkswap**、**swapon** を使ってスワップ ファイルを手動で追加できます。 その方法については、SUSE ドキュメントの「[Using the YaST Partitioner (YaST パーティショナーの使用)](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)」セクションで手動でのスワップ ファイルの追加に関する説明を検索してください。

別の方法としては、Linux VM エージェントを使ったスワップ領域の構成があります。 詳細については、「[Azure Linux エージェント ユーザー ガイド](../../extensions/agent-linux.md)」をご覧ください。

![十分なスワップ領域がないことを示すポップアップ メッセージ](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/etc/hosts ファイル
SAP のインストールを開始する前に、SAP VM のホスト名と IP アドレスを /etc/hosts ファイルに含めたことを確認してください。 1 つの Azure 仮想ネットワーク内にすべての SAP VM をデプロイします。 その後、次に示す内部 IP アドレスを使用します。

![/etc/hosts ファイルに表示される SAP VM のホスト名と IP アドレス](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/etc/fstab ファイル

**nofail** パラメーターを fstab ファイルに追加すると役に立ちます。 このようにすると、ディスクに何らかの問題がある場合でも VM が起動プロセス中に応答を停止することがありません。 ただし、追加のディスク領域を使用できず、プロセスでルート ファイル システムがいっぱいになることがあるため、特に注意を払ってください。 /hana がない場合、SAP HANA は起動しません。

![nofail パラメーターを fstab ファイルに追加する](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES for SAP Applications 12 への GNOME グラフィカル デスクトップ
このセクションでは、次の方法について説明します。

* SLES 12/SLES for SAP Applications 12 への GNOME デスクトップと xrdp のインストール
* SLES 12/SLES for SAP Applications 12 への Firefox を使った Java ベースの SAP MC の実行

Xterminal または VNC (このガイドでは説明しません) などの代替手段を使用することもできます。

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES for SAP Applications 12 への GNOME デスクトップと xrdp のインストール
Windows に関する知識がある場合は、SAP Linux VM 内でグラフィカルなデスクトップを直接使って Firefox、SAPinst、SAP GUI、SAP MC、HANA Studio のいずれかを実行することは簡単です。 その後、Windows コンピューターからリモート デスクトップ プロトコル (RDP) を介して VM に接続できます。 GUI を運用環境と非運用環境の Linux ベースのシステムに追加する方法に関する会社のポリシーに応じて、GNOME をサーバーにインストールする場合があります。 Azure SLES 12/SLES for SAP Applications 12 VM に GNOME デスクトップをインストールするには、次の手順を実行します。

1. (PuTTY ウィンドウなどで) 次のコマンドを入力して GNOME デスクトップをインストールします。

   `zypper in -t pattern gnome-basic`

2. RDP を使って VM に接続できるように xrdp をインストールします。

   `zypper in xrdp`

3. /etc/sysconfig/windowmanager を編集し、既定のウィンドウ マネージャーを GNOME に設定します。

   `DEFAULT_WM="gnome"`

4. 再起動後に xrdp が自動的に起動するように、**chkconfig** を実行します。

   `chkconfig -level 3 xrdp on`

5. RDP 接続に関する問題がある場合は (たとえば、PuTTY ウィンドウから) 再起動を試みます。

   `/etc/xrdp/xrdp.sh restart`

6. 前の手順で説明した xrdp 再起動でうまくいかない場合は、.pid ファイルを確認します。

   `check /var/run` 

   `xrdp.pid`を参照してください。 見つかった場合は場合、それを削除してもう一度再起動してください。

### <a name="start-sap-mc"></a>SAP MC を起動する
GNOME デスクトップをインストールした後、Firefox でグラフィカルな Java ベースの SAP MC を起動します。 これが Azure SLES 12/SLES for SAP Applications 12 VM で実行する場合、エラーが表示されることがあります。 エラーは Java ブラウザー プラグインが見つからないために発生します。

SAP MC を開始するための URL は、`<server>:5<instance_number>13` です。

詳しくは、「[Starting the Web-Based SAP Management Console (Web ベースの SAP 管理コンソールの起動)](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)」をご覧ください。

次のスクリーンショットは、Java ブラウザー プラグインが見つからない場合に表示されるエラー メッセージを示しています。

![見つからない Java ブラウザー プラグインを示すエラー メッセージ](./media/hana-get-started/image013.jpg)

問題を解決する方法の 1 つは、次のスクリーン ショットに示すように、YaST を使って見つからないプラグインをインストールすることです。

![YaST を使った、見つからないプラグインのインストール](./media/hana-get-started/image014.jpg)

SAP 管理コンソールの URL を再入力すると、プラグインをアクティブ化するように求められます。

![プラグインのアクティブ化を要求するダイアログ ボックス](./media/hana-get-started/image015.jpg)

見つからないファイル javafx.properties に関するエラー メッセージが表示されることもあります。 これは、SAP GUI 7.4 の Oracle Java 1.8 の要件に関連しています。 詳しくは、「[SAP Note 2059429 (SAP ノート 2059429)](https://launchpad.support.sap.com/#/notes/2059424)」をご覧ください。
IBM Java バージョンおよび SLES/SLES for SAP Applications 12 と共に配信された openjdk パッケージには、必要な javafx.properties ファイルが含まれていません。 解決策は、Oracle から Java SE 8 をダウンロードしてインストールすることです。

openSUSE での openjdk の同様の問題については、ディスカッション スレッド「[SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)」を参照してください。

## <a name="manual-installation-of-sap-hana-swpm"></a>SAP HANA の手動インストール:SWPM
このセクションの一連のスクリーンショットは、SWPM (SAPinst) を使う場合に SAP NetWeaver 7.5 と SAP HANA SP12 をインストールする方法の主要な手順を示しています。 NW 7.5 インストールの一環として、SWPM では HANA データベースを単一インスタンスとしてインストールすることもできます。

サンプルのテスト環境で、ABAP (Advanced Business Application Programming) アプリ サーバーを 1 つインストールしました。 次のスクリーンショットに示すように、 **[分散システム]** オプションを使って、1 つの Azure VM に ASCS インスタンスとプライマリ アプリケーション サーバー インスタンスをインストールしました。 SAP HANA はデータベース システムとして別の Azure VM で使用しました。

![[分散システム] オプションを使ってインストールされた ASCS インスタンスとプライマリ アプリケーション サーバー インスタンス](./media/hana-get-started/image012.jpg)

ASCS インスタンスがアプリ サーバー VM にインストールされると、SAP 管理コンソールで緑色のアイコンで示されます。 SAP プロファイル ディレクトリを含む /sapmnt ディレクトリは、SAP HANA DB サーバー VM と共有される必要があります。 DB のインストール手順では、この情報へのアクセスが必要になります。 アクセスを可能にする最善の方法は、YaST を使って構成できる NFS を使うことです。

![ASCS インスタンスがアプリ サーバー VM にインストールされたことを緑色のアイコンで示す SAP 管理コンソール](./media/hana-get-started/image016.jpg)

アプリ サーバー VM では、**rw** オプションと **no_root_squash** オプションを使って NFS で /sapmnt ディレクトリが共有されます。 既定では **ro** と **root_squash** になっており、データベース インスタンスをインストールする際に問題が生じる可能性があります。

!["rw" オプションと "no_root_squash" オプションを使った NFS による /sapmnt ディレクトリの共有](./media/hana-get-started/image017b.jpg)

次のスクリーンショットに示すように、SAP HANA DB サーバー VM で、**NFS クライアント**および YaST を使ってアプリ サーバー VM からの /sapmnt 共有を構成する必要があります。

!["NFS クライアント" を使って構成された /sapmnt 共有](./media/hana-get-started/image018b.jpg)

分散 NetWeaver 7.5 インストールすなわち**データベース インスタンス**を実行するには、SAP HANA DB サーバー VM にサインインし、SWPM を開始します。

![SAP HANA DB サーバー VM へのサインインと SWPM の開始によるデータベース インスタンスのインストール](./media/hana-get-started/image019.jpg)

**標準**のインストールと、インストール メディアへのパスを選んだ後で、DB SID、ホスト名、インスタンス番号、DB システム管理者のパスワードを入力します。

![SAP HANA データベース システム管理者のサインイン ページ](./media/hana-get-started/image035b.jpg)

DBACOCKPIT スキーマのパスワードを入力します。

![DBACOCKPIT スキーマのパスワード入力ボックス](./media/hana-get-started/image036b.jpg)

SAPABAP1 スキーマ パスワードの質問を入力します。

![SAPABAP1 スキーマ パスワードの質問の入力](./media/hana-get-started/image037b.jpg)

各タスクの完了後に、DB インストール プロセスの各フェーズの横に緑色のチェック マークが表示されます。 "Database Instance has completed (データベース インスタンスの...実行が完了しました)" というメッセージが表示されます。

![確認メッセージが表示されたタスク完了ウィンドウ](./media/hana-get-started/image023.jpg)

インストールが成功すると、SAP 管理コンソールにも DB インスタンスと緑色のアイコンが表示されます。 ここには、hdbindexserver や hdbcompileserver など SAP HANA プロセスの完全な一覧が表示されます。

![SAP HANA プロセスの一覧が表示された SAP 管理コンソール ウィンドウ](./media/hana-get-started/image024.jpg)

次のスクリーンショットでは、HANA のインストール中に SWPM によって作成された /hana/shared ディレクトリのファイル構造が部分的に示されています。 別のパスを指定するオプションがなかったため、SAP HANA インストールの前に SWPM を使って /hana ディレクトリにある追加のディスク領域をマウントすることが重要です。 この手順により、ルート ファイル システムの空き領域の不足を防ぎます。

![HANA のインストール時に作成された /hana/shared ディレクトリ ファイル構造](./media/hana-get-started/image025.jpg)

このスクリーン ショットは、/usr/sap ディレクトリのファイル構造を示しています。

![/usr/sap ディレクトリのファイル構造](./media/hana-get-started/image026.jpg)

ABAP 分散インストールの最後の手順は、"プライマリ アプリケーション サーバー インスタンス" のインストールです。

![最後の手順としてプライマリ アプリケーション サーバー インスタンスを表示している ABAP インストール](./media/hana-get-started/image027b.jpg)

プライマリ アプリケーション サーバー インスタンスと SAP GUI をインストールした後で、**DBA Cockpit** トランザクションを使って SAP HANA インストールが正常に完了したことを確認します。

![正常なインストールを確認する [DBA Cockpit (DBA コックピット)] ウィンドウ](./media/hana-get-started/image028b.jpg)

最後の手順として SAP アプリ サーバー VM に HANA Studio を最初にインストールすることをお勧めします。 次に、DB サーバー VM で実行している SAP HANA インスタンスに接続します。

![SAP アプリ サーバー VM への SAP HANA Studio のインストール](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>SAP HANA の手動インストール:HDBLCM
SWPM を使って SAP HANA を分散インストールの一部としてインストールすることに加えて、HDBLCM を使って HANA スタンドアロンを最初にインストールできます。 たとえば、SAP NetWeaver 7.5 をインストールできます。 このセクションのスクリーンショットは、このプロセスのしくみを示しています。

HANA HDBLCM ツールの詳細については、以下を参照してください。

* [タスクに適した SAP HANA HDBLCM の選択](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA ライフサイクル管理ツール](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)
* [SAP HANA サーバーのインストールと更新のガイド](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

HDBLCM ツールによって作成される `\<HANA SID\>adm user` では既定グループ ID 設定を使用して問題を回避することをお勧めします。 SAP HANA を HDBLCM を介してインストールする前に、グループ ID `1001` を使って `sapsys` という新しいグループを定義します。

![グループ ID 1001 を使って定義された新しいグループ "sapsys"](./media/hana-get-started/image030.jpg)

HDBLCM を初めて開始するときに、シンプルなスタート メニューが表示されます。 項目 1 **[Install new system]\(新しいシステムのインストール\)** を選択します。

![HDBLCM スタート ウィンドウの [Install new system (新しいシステムのインストール)] オプション](./media/hana-get-started/image031.jpg)

次のスクリーンショットには、前に選んだすべての主要なオプションが表示されています。

> [!IMPORTANT]
> HANA ログ ボリュームとデータ ボリュームに指定されたディレクトリに加えて、インストール パス (このサンプルでは /hana/shared) と /usr/sap は、ルート ファイル システムの一部にしないでください。 これらのディレクトリは、VM にアタッチされている Azure データ ディスクに属します。 詳細については、「ディスクのセットアップ」セクションを参照してください。 

この方法は、ルート ファイル システムの空き領域の不足を防ぐために役立ちます。 HANA システム管理者がユーザー ID `1005` を持ち、インストールする前に定義された ID `1001` の `sapsys` グループに含まれていることを確認できます。

![前に選んだすべての主要 SAP HANA コンポーネントの一覧](./media/hana-get-started/image032.jpg)

/etc/passwd ディレクトリで `\<HANA SID\>adm user` の詳細を確認します。 次のスクリーンショットのように `azdadm`を探します。

![/etc/passwd ディレクトリに示されている HANA \<HANA SID\>adm ユーザーの詳細](./media/hana-get-started/image033.jpg)

HDBLCM を使って SAP HANA をインストールした後で、次のスクリーンショットに示すように SAP HANA Studio のファイル構造を確認できます。 すべての SAP NetWeaver テーブルが含まれる SAPABAP1 スキーマは、まだ使用できません。

![SAP HANA Studio の SAP HANA ファイル構造](./media/hana-get-started/image034.jpg)

SAP HANA をインストールしたら、その SAP HANA に SAP NetWeaver をインストールできます。 次のスクリーンショットに示すように、インストールは SWPM を使って "分散インストール" として実行されました。 このプロセスについては前のセクションで説明しています。 SWPM を使ってデータベース インスタンスをインストールする場合は、HDBLCM を使って同じデータを入力します。 たとえば、ホスト名、HANA SID、インスタンス番号を入力します。 SWPM は、HANA の既存のインストールを使い、スキーマをさらに追加します。

![SWPM を使って実行される分散インストール](./media/hana-get-started/image035b.jpg)

次のスクリーンショットは、DBACOCKPIT スキーマに関するデータを入力する SWPM インストール手順を示しています。

![DBACOCKPIT スキーマ データが入力された SWPM インストール手順](./media/hana-get-started/image036b.jpg)

SAPABAP1 スキーマに関するデータを入力します。

![SAPABAP1 スキーマに関するデータの入力](./media/hana-get-started/image037b.jpg)

SWPM データベース インスタンスのインストールが完了したら、SAP HANA Studio で SAPABAP1 スキーマを確認できます。

![SAP HANA studio の SAPABAP1 スキーマ](./media/hana-get-started/image038b.jpg)

最後に、SAP アプリ サーバーと SAP GUI のインストールの完了後に、**DBA Cockpit** トランザクションを使って、HANA DB インスタンスを確認できます。

![DBA Cockpit トランザクションで確認された HANA DB インスタンス](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP ソフトウェアのダウンロード
次のスクリーンショットに示すように、SAP Service Marketplace からソフトウェアをダウンロードできます。

Linux/HANA 用の NetWeaver 7.5 のダウンロード:

 ![NetWeaver 7.5 をダウンロードするための [SAP Service Installation and Upgrade]\(SAP サービスのインストールとアップグレード\) ウィンドウ](./media/hana-get-started/image001.jpg)

HANA SP12 Platform Edition のダウンロード:

 ![HANA SP12 Platform Edition をダウンロードするための [SAP Service Installation and Upgrade]\(SAP サービスのインストールとアップグレード\) ウィンドウ](./media/hana-get-started/image002.jpg)

