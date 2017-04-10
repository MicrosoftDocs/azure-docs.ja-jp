---
title: "クイックスタート ガイド: Azure VM への単一インスタンスの SAP HANA の手動インストール | Microsoft Docs"
description: "Azure VM に単一インスタンスの SAP HANA を手動でインストールするためのクイックスタート ガイド"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 24a01d243cda0d04b6b179ab3f87f1d5fcdce61b
ms.lasthandoff: 03/25/2017


---
# <a name="quick-start-guide-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>クイックスタート ガイド: Azure VM への単一インスタンスの SAP HANA の手動インストール
## <a name="introduction"></a>はじめに
このクイックスタート ガイドは、SAP NetWeaver 7.5 と SAP HANA SP12 を手動でインストールする場合に、Azure 仮想マシン (VM) に単一インスタンスの SAP HANA プロトタイプまたはデモ システムを設定するのに役立ちます。

このガイドは、次のような Azure IaaS の基本事項について理解していることを前提としています。
 * Azure Portal または PowerShell を使って仮想マシンまたは仮想ネットワークをデプロイする方法。
 * JSON (JavaScript Object Notification) テンプレートを使うためのオプションなど、Azure クロスプラットフォーム コマンドライン ツール (CLI)。

このガイドでは、SAP HANA と SAP NetWeaver、またそれらをオンプレミスでインストールする方法について理解していることも想定しています。

また、このガイドの末尾の「General information」(一般情報) セクションに記載されている SAP Azure ドキュメントもご覧ください。

ガイドの内容は運用環境以外のシステムに制限されるので、高可用性 (HA)、バックアップ障害復旧 (DR)、高パフォーマンス、特別なセキュリティの考慮事項などのトピックについては説明しません。

SAP-Linux-Azure は、Azure Resource Manager でのみサポートされ、クラシック デプロイメント モデルではサポートされません。 そのため、Azure Resource Manager モデルを通じて分散 SAP NetWeaver インストールを実行するために 2 つの仮想マシンを使ってサンプル セットアップを実行しました。 Resource Manager について詳しくは、このガイドの末尾の「General information」(一般情報) セクションをご覧ください。

サンプルのインストールでは、次の 2 つのテスト VM を使いました。

* NetWeaver 7.5 ABAP SSAP Central Services (ASCS) インスタンスと PAS をホストする hana-appsrv (タイプ DS3)
* HANA SP12 をホストする hana-dbsrv (タイプ GS4)

VM はどちらも 1 つの Azure 仮想ネットワーク (azure-hana-test-vnet) に属し、どちらの場合も OS は SLES 12 SP1 でした。

2016 年 7 月時点では、SAP HANA が完全にサポートされているのは Azure VM タイプ GS5 上の OLAP (BW) 運用システムのみです。 公式の SAP サポートを想定していないテスト目的の場合は、GS4 などの比較的小規模なタイプを使ってもかまいません。 Azure 上の SAP HANA では、HANA データ ファイルとログ ファイルのための Azure Premium Storage を常に使います (このガイドで後述する「ディスクのセットアップ」セクションをご覧ください)。 Azure でサポートされている SAP 製品について詳しくは、このガイドの末尾にある「General information」(一般情報) セクションをご覧ください。

このガイドでは、Azure VM に SAP HANA を手動でインストールする 2 とおりの方法について説明します。

* NetWeaver 分散インストールの一環として、"データベース インスタンスのインストール" の手順で SAP Software Provisioning Manager (SWPM) を使う。
* HANA ライフサイクル管理ツール hdblcm を使い、その後 NetWeaver をインストールする。

SWPM を使ってすべてのコンポーネント (SAP HANA、SAP アプリケーション サーバー、ASCS インスタンス、SAP GUI) を単一の VM にインストールすることもできます。 このガイドではこのオプションについて説明しませんが、考慮する必要のある事項は同じです。

インストールを開始する前に、SAP HANA のインストールに関する 2 つのチェックリストの直後にある「SAP HANA を手動でインストールするための Azure VM の準備」を必ずお読みください。 そうすると、既定の Azure VM 構成のみを使う場合に発生する可能性のあるいくつかの基本的なミスを防ぐのに役立ちます。

## <a name="checklist-for-sap-hana-installation-using-sap-swpm"></a>SAP SWPM を使った SAP HANA のインストールのチェックリスト
ここでは、SAP SWPM を使って SAP NetWeaver 7.5 の分散インストールを実行するときに、デモやプロトタイプ作成の目的で単一インスタンスの SAP HANA を手動でインストールするための主要な手順を示します。 個々の項目については、ガイドの中で示されているスクリーンショットでより詳しく説明しています。

* 2 つのテスト VM が含まれる Azure 仮想ネットワークを作成する。
* Azure Resource Manager モデルに従って OS SLES/SLES-for-SAP Applications 12 SP1 を備えた 2 つの Azure VM をデプロイする。
* 2 つの Standard Storage ディスク (たとえば 75 GB または 500 GB のディスク) をアプリ サーバー VM にアタッチする。
* 4 つのディスクを HANA DB サーバー VM にアタッチする (アプリ サーバー VM に使うのと同様の 2 つの Standard Storage ディスクと、2 つの Premium Storage ディスク (たとえば、2 つの 512 GB ディスク))。
* サイズまたはスループットの要件に応じて、複数のディスクをアタッチし、VM 内部の OS レベルで論理ボリューム管理 (LVM) または複数デバイス管理ユーティリティ (mdadm) を使ってストライプ ボリュームを作成する。
* アタッチされたディスク/論理ボリューム上に XFS ファイル システムを作成する。
* 新しい XFS ファイル システムを OS レベルでマウントする。 一方のファイル システムは SAP ソフトウェア全体を保持するために使います。もう一方のファイル システムは /sapmnt ディレクトリに使うほか、場合によってはバックアップに使います。 SAP HANA DB サーバーで、Premium Storage ディスクに XFS ファイル システムを /hana および /usr/sap としてマウントします。 このプロセスは、Linux Azure VM では容量が少ないルート ファイル システムがいっぱいになるのを防ぐために必要です。
* テスト VM のローカル IP アドレスを /etc/hosts に入力する。
* /etc/fstab に nofail パラメーターを入力する。
* HANA SLES 12 SAP Note に従ってカーネル パラメーターを設定する。 詳しくは、「カーネルパラメーター」のセクションをご覧ください。
* スワップ領域を追加する。
* 必要に応じて、テスト VM にグラフィカル デスクトップをインストールする。 インストールしない場合は、リモート SAPinst インストールを使います。
* SAP サービス マーケットプレースで SAP ソフトウェアをダウンロードする。
* SAP ASCS インスタンスをアプリ サーバー VM にインストールする。
* NFS を使って、テスト VM 間で /sapmnt ディレクトリを共有する。 アプリ サーバー VM は NFS サーバーです。
* SWPM を使って、HANA が含まれたデータベース インスタンスを DB サーバー VM にインストールする。
* アプリ サーバー VM に、プライマリ アプリケーション サーバー (PAS) をインストールする。
* SAP 管理コンソール (MC) を起動し、たとえば SAP GUI/HANA Studio に接続する。

## <a name="checklist-for-sap-hana-installation-using-hdblcm"></a>hdblcm を使った SAP HANA のインストールのチェックリスト
ここでは、SAP hdblcm を使って SAP NetWeaver 7.5 の分散インストールを実行するときに、デモやプロトタイプ作成の目的で単一インスタンスの SAP HANA を手動でインストールするための主要な手順を示します。 個々の項目については、ガイドの中で示されているスクリーンショットでより詳しく説明しています。

* 2 つのテスト VM が含まれる Azure 仮想ネットワークを作成する。
* Azure Resource Manager モデルに従って OS SLES/SLES-for-SAP Applications 12 SP1 を備えた 2 つの Azure VM をデプロイする。
* 2 つの Standard Storage ディスク (たとえば 75 GB または 500 GB のディスク) をアプリ サーバー VM にアタッチする。
* 4 つのディスクを HANA DB サーバー VM にアタッチする (アプリ サーバー VM に使うのと同様の 2 つの Standard Storage ディスクと、2 つの Premium Storage ディスク (たとえば、2 つの 512 GB ディスク))。
* サイズまたはスループットの要件に応じて、複数のディスクをアタッチし、VM 内部の OS レベルで論理ボリューム管理 (LVM) または複数デバイス管理ユーティリティ (mdadm) を使ってストライプ ボリュームを作成する。
* アタッチされたディスク/論理ボリューム上に XFS ファイル システムを作成する。
* 新しい XFS ファイル システムを OS レベルでマウントする。 一方のファイル システムは SAP ソフトウェア全体を保持するために使います。もう一方のファイル システムは /sapmnt ディレクトリに使うほか、場合によってはバックアップに使います。 SAP HANA DB サーバーで、Premium Storage ディスクに XFS ファイル システムを /hana および /usr/sap としてマウントします。 このプロセスは、Linux Azure VM では容量が少ないルート ファイル システムがいっぱいになるのを防ぐために必要です。
* テスト VM のローカル IP アドレスを /etc/hosts に入力する。
* /etc/fstab に nofail パラメーターを入力する。
* HANA SLES 12 SAP Note に従ってカーネル パラメーターを設定する。 詳しくは、「カーネルパラメーター」のセクションをご覧ください。
* スワップ領域を追加する。
* 必要に応じて、テスト VM にグラフィカル デスクトップをインストールする。 インストールしない場合は、リモート SAPinst インストールを使います。
* SAP サービス マーケットプレースで SAP ソフトウェアをダウンロードする。
* HANA DB サーバー VM で、グループ ID が 1001 のグループ "sapsys" を作成する。
* HANA データベース ライフサイクル マネージャー (HDBLCM) を使って、DB サーバー VM に SAP HANA をインストールする。
* SAP ASCS インスタンスをアプリ サーバー VM にインストールする。
* NFS を使って、テスト VM 間で /sapmnt ディレクトリを共有する。 アプリ サーバー VM は NFS サーバーです。
* SWPM を使って、HANA が含まれたデータベース インスタンスを HANA DB サーバー VM にインストールする。
* アプリ サーバー VM に、プライマリ アプリケーション サーバー (PAS) をインストールする。
* SAP MC を起動し、SAP GUI/HANA Studio を使って接続する。

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>SAP HANA を手動でインストールするための Azure VM の準備
このセクションは、次のトピックで構成されています。

* OS の更新
* ディスクのセットアップ
* カーネル パラメーター
* ファイル システム
* /etc/hosts
* /etc/fstab

### <a name="os-updates"></a>OS の更新
SUSE は OS の更新と修正を提供することで、セキュリティを維持し、操作を円滑にしているため、ソフトウェアを追加でインストールする前に、利用可能な更新がないかを確認するのが賢明です。
システムが現時点でのパッチ レベルであれば、サポートに何度も問い合わせることを回避できます。

Azure のオンデマンド イメージは、SUSE 更新インフラストラクチャに自動的に接続され、そこから追加のソフトウェアや更新を入手します。
BYOS イメージを SUSE カスタマー センター ( https://scc.suse.com ) に登録する必要があります。

使用可能なパッチを簡単にチェックする方法は次の通りです。

 `sudo zypper list-patches`

不具合の種類に基づいて、パッチはカテゴリ別および重大度別に分類されます。

一般的に使用されるカテゴリの値は、セキュリティ、推奨、オプション、機能、ドキュメント、または YaST です。

一般的に使用される重大度の値は、重大、重要、中、低、または未指定です。

zypper はインストールされているパッケージに必要な更新プログラムのみを検索します。

以下はコマンド例です。

`sudo zypper patch  --category=security,recommended --severity=critical,important`

*--dry-run* パラメーターを追加する場合は、更新プログラムのテストは可能ですが、実際のシステムの更新は行われません。


### <a name="disk-setup"></a>ディスクのセットアップ
Azure 上の Linux VM のルート ファイル システムには、サイズの制限があります。 このため、SAP を実行するための VM に追加のディスク領域をアタッチする必要があります。 SAP アプリ サーバー VM が純粋なプロトタイプまたはデモ環境で使われる場合は、Azure Standard Storage ディスクを使ってもかまいません。 SAP HANA DB データ ファイルとログ ファイルについては、非運用環境のランドスケープであっても Azure Premium Storage ディスクを使います。

詳しくは、[Linux VM にディスクをアタッチする方法](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

Azure ディスク キャッシュでは、HANA トランザクション ログの保存に使うディスクに「**None**」と入力します。 HANA データ ファイルについては、読み取りキャッシュを使ってかまいません。 HANA はインメモリ データベースであるため、Azure ディスク レベルの読み取りキャッシュによるパフォーマンス向上 (HANA の起動、ディスクからメモリへのデータの読み取りなど) の度合いは、全体的な使用パターンに依存します。

詳しくは、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md)」をご覧ください。

VM 作成用の JSON のサンプル テンプレートを検索するには、「[Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates)」(Azure クイック スタート テンプレート) をご覧ください。
"vm-simple-sles" テンプレートは、100 GB の追加のデータ ディスクがあるストレージ セクションを含む基本テンプレートを示しています。

PowerShell または CLI を使って SUSE イメージを検索する方法と、UUID を使ってディスクをアタッチすることの重要性については、「[Microsoft Azure SUSE Linux VM での SAP NetWeaver の実行](virtual-machines-linux-sap-on-suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

システムのサイズとスループット要件に応じて、1 つではなく複数のディスクをアタッチし、後でこれらのディスクにまたがるストライプ セットを OS レベルで作成することが必要になる場合があります。 次の 2 つの理由から、複数の Azure ディスクにまたがるストライプ セットを作成します。

* スループットを高めることができる。
* 現在の Azure ディスクサイズの制限は 1 TB (2016年 7 月時点) であるため、1 TB を超える単一ファイル システムが必要である。

ストライピングを構成するための 2 つの主要なツールについて詳しくは、次の記事をご覧ください。

* [Linux でのソフトウェア RAID の構成](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure で Linux VM の LVM を構成する](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

テスト環境で、次のスクリーンショットに示すように 2 つの Azure Standard Storage ディスクが SAP アプリ サーバー VM にアタッチされています。 1 つのディスクには、インストールで SAP ソフトウェア全体が格納されます (NetWeaver 7.5、SAP GUI、SAP HANA など)。 2 つ目のディスクは、追加の要件 (バックアップとテスト データなど) に対して十分な空き領域を使用可能にするため、また同じ SAP ランドスケープに属するすべての VM 間で /sapmnt ディレクトリ (つまり、SAP プロファイル) を共有するためのものです。

![2 つのデータ ディスクとそのサイズを表示している SAP HANA アプリ サーバーの [ディスク] ウィンドウ](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

アプリ サーバー VM のシナリオとは異なり、4 つのディスクは、次のスクリーンショットに示すように SAP HANA サーバー VM にアタッチされています。 2 つのディスクは SAP ソフトウェアの格納に使われ (NFS を使って SAP ソフトウェア ディスクを共有することもできます)、十分な空き領域を利用できるようにします (たとえば、バックアップのため)。 追加の 2 つのディスクは、SAP HANA データ ファイル、ログ ファイル、/usr/sap ディレクトリが格納される Azure Premium Storage ディスクです。

![4 つのデータ ディスクとそのサイズを表示している SAP HANA アプリ サーバーの [ディスク] ウィンドウ](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

### <a name="kernel-parameters"></a>カーネル パラメーター
SAP HANA では、標準的な Azure ギャラリー イメージに含まれていない、手動で設定する必要のある特定の Linux カーネル設定が必要です。 SAP HANA Note では、SLES 12/SLES for SAP Applications 12 で推奨される OS 設定について説明しています: [SAP Note 2205917](https://launchpad.support.sap.com/#/notes/2205917)。


SLES-for-SAP Applications 12 GA と SP1 には、古い sapconf ユーティリティを置き換える新しいツールがあります。 これは tuned-adm というツールで、特殊な SAP HANA プロファイルを利用できます。 ルート ユーザーとして SAP HANA のシステムを調整する場合は、'tuned-adm profile sap-hana' と入力するだけです。

tuned-adm について詳しくは、以下の SUSE ドキュメントをご覧ください。

* [tuned-adm profile sap-hana に関する SLES-for-SAP Applications 12 SP1 ドキュメント](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)

次のスクリーンショットでは、必要とされる SAP HANA の設定に応じて transparent_hugepage と numa_balancing の値が tuned-adm によって変更されているようすがわかります。

![tuned-adm ツールは、必要な SAP HANA 設定に従って値を変更します](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

SAP HANA カーネル設定を永続的な設定にするには、SLES 12 で grub2 を使う必要があります。 grub2 について詳しくは、[SUSE ドキュメントの「Configuration File Structure」(ファイル構造の構成) セクション](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)をご覧ください。

次のスクリーンショットでは、構成ファイルでカーネル設定が変更されており、grub2-mkconfig を使ってコンパイルされていることがわかります。

![構成ファイルで変更され、grub2-mkconfig を使ってコンパイルされたカーネル設定](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

このほか、YaST で設定を変更し、**[ブート ローダー]** > **[カーネル パラメーター]** 設定を変更するという方法もあります。

![YaST ブート ローダーの [カーネル パラメーター] 設定タブ](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

### <a name="file-systems"></a>ファイル システム
次のスクリーンショットは、SAP アプリ サーバー VM にアタッチされた 2 つの Azure Standard Storage ディスクに作成された 2 つのファイル システムを示しています。 どちらのファイル システムの種類も XFS であり、/sapdata と /sapsoftware にマウントされています。

この方法でファイル システムを構成することは必須ではありません。 ディスク領域を構築するための他のオプションもあります。 最も重要な考慮事項は、ルート ファイル システムの空き領域の不足を防ぐことです。

![SAP アプリ サーバー VM に作成された 2 つのファイル システム](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

SAP HANA DB VM に関して重要なのは、SAPinst (SWPM) と単純で "一般的な" インストール オプションを使う場合、データベース インストールのときに、既定では /hana と /usr/sap にデータがインストールされるという点を理解することです。 既定の設定では、SAP HANA ログ バックアップは /usr/sap に格納されます。 この場合もルート ファイル システムの記憶域スペースの不足を防ぐことが重要であるため、SWPM を使って SAP HANA をインストールする前に /hana と /usr/sap に十分な空き領域があることを確認します。

SAP HANA の標準のファイルシステム レイアウトの説明は、「[SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)」(SAP HANA サーバーのインストールと更新のガイド) をご覧ください。
![SAP アプリ サーバー VM に作成された追加のファイル システム](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

標準的な SLES/SLES-for-SAP Applications 12 Azure ギャラリー イメージに SAP NetWeaver をインストールすると、スワップ領域がないことを示すメッセージが表示されます。 このメッセージを消すために、dd、mkswap、swapon を使ってスワップ ファイルを手動で追加できます。 その方法については、[SUSE ドキュメントの「Using the YaST Partitioner」(YaST パーティショナーの使用) セクション](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)で手動でのスワップ ファイルの追加に関する説明を検索してください。

別の方法としては、Linux VM エージェントを使ったスワップ領域の構成があります。 詳しくは、「[Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

![十分なスワップ領域がないことを示すポップアップ メッセージ](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)


### <a name="etchosts"></a>/etc/hosts
SAP のインストールを開始する前に念頭におく必要のあるもう 1 つの重要な点は、SAP VM のホスト名と IP アドレスを /etc/hosts ファイルに含めておくことです。 1 つの Azure 仮想ネットワーク内ですべての SAP VM をデプロイし、内部 IP アドレスを使います。

![/etc/hosts ファイルに表示される SAP VM のホスト名と IP アドレス](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

### <a name="etcfstab"></a>/etc/fstab

テスト フェーズ中に、nofail パラメーターを fstab に追加すると良いということがわかっています。 ディスクに何らかの問題がある場合でも VM は起動し、起動プロセス中に停止することはありません。 ただし、このシナリオと同様に、追加のディスク領域を使用できず、プロセスでルート ファイル システムがいっぱいになることがあるため、特に注意を払ってください。 /hana がない場合、SAP HANA は起動しません。

![nofail パラメーターを fstab に追加](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

## <a name="install-graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES-for-SAP Applications 12 への Gnome グラフィカル デスクトップのインストール
このセクションは、次のトピックで構成されています。

* SLES 12/SLES-for-SAP Applications 12 への Gnome デスクトップと xrdp のインストール
* SLES 12/SLES-for-SAP Applications 12 への Firefox を使った Java ベースの SAP MC の実行

Xterminal や VNC などの代替手段を使うこともできますが、2016年 9 月の時点では、ガイドでは xrdp についてのみ説明しています。

### <a name="installing-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES-for-SAP Applications 12 への Gnome デスクトップと xrdp のインストール
Microsoft Windows に関する知識がある場合は、SAP Linux VM 内でグラフィカルなデスクトップを直接使って Firefox、Sapinst、SAP GUI、SAP MC、HANA Studio のいずれかを実行し、Windows コンピューターから RDP を介して VM に接続することは簡単です。 この手順は、運用データベース サーバーには適していない場合もありますが、純粋なプロトタイプ/デモ環境で使うには問題ありません。 Azure SLES 12/SLES-for-SAP Applications 12 VM に Gnome デスクトップをインストールする方法は、次のとおりです。

(putty ウィンドウなどで) 次のコマンドを入力して gnome デスクトップをインストールします。

`zypper in -t pattern gnome-basic`

RDP を使って VM に接続できるように xrdp をインストールします。

`zypper in xrdp`

/etc/sysconfig/windowmanager を編集し、既定のウィンドウ マネージャーを Gnome に設定します。

`DEFAULT_WM="gnome"`

再起動後に xrdp が自動的に起動するように、chkconfig を実行します。

`chkconfig -level 3 xrdp on`

RDP 接続に関する問題がある場合は (おそらく putty ウィンドウから) 再起動を試みます。

`/etc/xrdp/xrdp.sh restart`

上記の xrdp の再起動がうまくいかない場合、.pid ファイルがないかチェックし、削除します。

`check /var/run` を実行し、`xrdp.pid` を検索

それを削除し、再起動を試みます。

### <a name="sap-mc"></a>SAP MC
Gnome デスクトップをインストールした後で、Azure SLES 12/SLES-for-SAP Applications 12 VM を実行している Firefox からグラフィカルな Java ベース SAP MC を開始すると、Java ブラウザー プラグインがないためエラーが表示されることがあります。

SAP MC を開始するための URL は、<server>:5<instance_number>13 です。

詳しくは、「[Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)」(Web ベースの SAP 管理コンソールの起動) をご覧ください。

次のスクリーンショットは、Java ブラウザー プラグインが見つからない場合に表示されるエラー メッセージを示しています。

![見つからない Java ブラウザー プラグインを示すエラー メッセージ](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

問題を解決する方法の 1 つは、次のスクリーン ショットに示すように、YaST を使って見つからないプラグインをインストールすることです。

![YaST を使った、見つからないプラグインのインストール](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

SAP 管理コンソールの URL を繰り返すと、プラグインのアクティブ化を求めるダイアログ ボックスが表示されます。

![プラグインのアクティブ化を要求するダイアログ ボックス](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

発生する可能性のある 1 つの別の問題は、見つからないファイル javafx.properties に関するエラー メッセージです。 これは、SAP GUI 7.4 の Oracle Java 1.8 の要件に関連しています。 [SAP Note 2059429 をご覧ください。](https://launchpad.support.sap.com/#/notes/2059424)IBM Java バージョンにも、SLES/SLES-for-SAP Applications 12 と共に配信された openjdk パッケージにも、必要な javafx は含まれていません。 解決策は、Oracle から Java SE8 をダウンロードしてインストールすることです。

OpenSUSE と openjdk の同様の問題に関する記事は「[SAPGui 7.4 Java for openSUSE 42.1 Leap (openSUSE 42.1 Leap の SAPGui 7.4 Java)](https://scn.sap.com/thread/3908306)」をご覧ください。

## <a name="install-sap-hana-manually-by-using-swpm-as-part-of-a-netweaver-75-installation"></a>NetWeaver 7.5 インストールの一環として実行する、SWPM を使った SAP HANA の手動インストール
このセクションの一連のスクリーンショットは、SWPM (SAPinst) を使う場合に SAP NetWeaver 7.5 と SAP HANA SP12 をインストールするための主要な手順を示しています。 NW 7.5 インストールの一環として、SWPM では HANA データベースを単一インスタンスとしてインストールすることもできます。

サンプルのテスト環境で、ABAP (Advanced Business Application Programming) アプリ サーバーを 1 つだけインストールしました。 次のスクリーンショットに示すように、[分散システム] オプションを使って、1 つの Azure VM に ASCS インスタンスとプライマリ アプリケーション サーバー インスタンスをインストールし、別の Azure VM に SAP HANA をデータベース システムとしてインストールしました。

![[分散システム] オプションを使ってインストールされた ASCS インスタンスとプライマリ アプリケーション サーバー インスタンス](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

ASCS インスタンスがアプリ サーバー VM にインストールされ、SAP 管理コンソールで "緑色" に変わったら、SAP プロファイル ディレクトリなどが含まれた /sapmnt ディレクトリを SAP HANA DB サーバー VM と共有させる必要があります。 DB のインストール手順では、この情報へのアクセスが必要になります。 アクセスを可能にする最善の方法は、YaST を使って構成できる NFS を使うことです。

![ASCS インスタンスがアプリ サーバー VM にインストールされ、"緑色" に変わった SAP 管理コンソール](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

アプリ サーバー VM で、**rw** オプションと **no_root_squash** オプションを使って NFS で /sapmnt ディレクトリを共有する必要があります。 既定では "ro" と "root_squash" になっており、データベース インスタンスをインストールする際に問題が生じる可能性があります。

!["rw" オプションと "no_root_squash" オプションを使った NFS による /sapmnt ディレクトリの共有](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

次のスクリーンショットに示すように、SAP HANA DB サーバー VM で、(YaST などを利用して) **NFS クライアント** を使ってアプリ サーバー VM からの /sapmnt 共有を構成する必要があります。

!["NFS クライアント" を使って構成された /sapmnt 共有](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

分散 NetWeaver 7.5 インストールを実行するには、次のスクリーンショットに示すように、**[データベース インスタンス]** で SAP HANA DB サーバー VM にサインインし、SWPM を開始します。

![SAP HANA DB サーバー VM へのサインインと SWPM の開始によるデータベース インスタンスのインストール](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

"標準" のインストールと、インストール メディアへのパスを選んだ後で、DB SID、ホスト名、インスタンス番号、DB システム管理者のパスワードを入力します。

![SAP HANA データベース システム管理者のサインイン ページ](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

DBACOCKPIT スキーマのパスワードを入力します。

![DBACOCKPIT スキーマのパスワード入力ボックス](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

SAPABAP1 スキーマ パスワードの質問を入力します。

![SAPABAP1 スキーマ パスワードの質問の入力](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

タスクの完了後に、DB インストール プロセスの各フェーズの横に緑色のチェック マークが表示されます。 [メッセージ ボックス] ウィンドウに、"Execution of ...Database Instance has completed (データベース インスタンスの...実行が完了しました)" というメッセージが表示されます。

![確認メッセージが表示されたタスク完了ウィンドウ](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

インストールの成功後、SAP 管理コンソールでは DB インスタンスが "緑色" で表示され、SAP HANA プロセス (hdbindexserver、hdbcompileserver など) の完全な一覧が表示されます。

![SAP HANA プロセスの一覧が表示された SAP 管理コンソール ウィンドウ](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

次のスクリーンショットでは、HANA のインストール中に SWPM によって作成された /hana/shared のファイル構造が部分的に示されています。 別のパスを指定するオプションがなかったため、ルート ファイル システムの空き領域の不足を防ぐために、SAP HANA インストールの前に SWPM を使って /hana にある追加のディスク領域をマウントすることが重要です。

![HANA のインストール時に作成された /hana/shared ファイル構造](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

このスクリーン ショットは、/usr/sap ディレクトリのファイル構造を示しています。

![/usr/sap ディレクトリのファイル構造](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

ABAP 分散インストールの最後の手順は、"プライマリ アプリケーション サーバー インスタンス" です。

![最後の手順としてプライマリ アプリケーション サーバー インスタンスを表示している ABAP インストール](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

プライマリ アプリケーション サーバー インスタンスと SAP GUI をインストールした後で、トランザクション "dbacockpit" を使って SAP HANA インストールが正常に完了したことを確認します。

![正常なインストールを確認する [DBA Cockpit (DBA コックピット)] ウィンドウ](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

最後の手順として、SAP HANA Studio を SAP アプリ サーバー VM にインストールし、DB サーバー VM で実行されている SAP HANA インスタンスに接続できます。

![SAP アプリ サーバー VM への SAP HANA Studio のインストール](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

## <a name="install-sap-hana-manually-by-using-the-hana-lifecycle-management-tool-hdblcm"></a>HANA ライフサイクル管理ツール (hdblcm) を使った SAP HANA の手動インストール
SWPM を使って SAP HANA を分散インストールの一部としてインストールすることに加えて、hdblcm を使って HANA スタンドアロンを最初にインストールできます。 その後、たとえば SAP NetWeaver 7.5 をインストールできます。 次のスクリーンショットは、このプロセスのしくみを示しています。

HANA hdblcm ツールに関する 3 つの情報源を次に示します。

* [タスクに適した SAP HANA HDBLCM の選択](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA ライフサイクル管理ツール](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA サーバーのインストールと更新のガイド](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

(hdblcm ツールによって作成される) \<HANA SID\>adm ユーザーの既定のグループ ID 設定に関する問題を回避するために、hdblcm を使って SAP HANA をインストールする前に、グループ ID 1001 を使って "sapsys" という新しいグループを定義します。

![グループ ID 1001 を使って定義された新しいグループ "sapsys"](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

hdblcm を初めて開始するときに、シンプルなスタート メニューが表示されます。 次のスクリーンショットに示すように項目 1、**[Install new system (新しいシステムのインストール)]** を選びます。

![hdblcm スタート ウィンドウの [Install new system (新しいシステムのインストール)] オプション](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

次のスクリーンショットには、前に選んだすべての主要なオプションが表示されています。

> [!IMPORTANT]
> HANA ログ ボリュームとデータ ボリュームに指定されたディレクトリに加えて、インストール パス (このサンプルでは /hana/shared) と /usr/sap はルート ファイル システムの一部にしないでください。 ディレクトリは、Azure VM のセットアップ セクションで説明しているように、VM にアタッチされている Azure データ ディスクに属します。 この方法は、ルート ファイル システム領域が不足するリスクを回避するために役立ちます。 HANA 管理者のユーザー ID が 1005 であり、インストール前に定義した sapsys グループ (ID 1001) に属していることもわかります。

![前に選んだすべての主要 SAP HANA コンポーネントの一覧](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

/etc/passwd で、HANA \<HANA SID\>adm (次のスクリーンショットでは azdadm) ユーザーの詳細を確認できます。

![/etc/passwd に示されている HANA \<HANA SID\>adm ユーザーの詳細](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

hdblcm を使って SAP HANA をインストールした後で、SAP HANA Studio のファイル構造を確認できます。 すべての SAP NetWeaver テーブルが含まれる SAPABAP1 スキーマは、まだ使用できません。

![SAP HANA Studio の SAP HANA ファイル構造](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

SAP HANA をインストールしたら、その SAP HANA に SAP NetWeaver をインストールできます。 このスクリーンショットに示すように、インストールは (前のセクションで説明したように) SWPM を使って "分散インストール" として実行されました。 SWPM を使ってデータベース インスタンスをインストールする場合は、hdblcm を使って同じデータを入力します (ホスト名、HANA SID、インスタンス番号など) 。 SWPM は、HANA の既存のインストールを使い、スキーマをさらに追加します。

![SWPM を使って実行される分散インストール](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

次のスクリーンショットは、DBACOCKPIT スキーマに関するデータを入力する SWPM インストール手順を示しています。

![DBACOCKPIT スキーマ データが入力された SWPM インストール手順](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

SAPABAP1 スキーマに関するデータを入力します。

![SAPABAP1 スキーマに関するデータの入力](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

SWPM データベース インスタンスのインストールが完了したら、HANA Studio で SAPABAP1 スキーマを確認できます。

![SAP HANA studio の SAPABAP1 スキーマ](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

最後に、SAP アプリ サーバーと SAP GUI のインストールの完了後に、"dbacockpit" トランザクションを使って、HANA DB インスタンスを確認できます。

!["dbacockpit" トランザクションで確認された HANA DB インスタンス](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

## <a name="about-sap-azure-certifications-and-running-sap-hana-on-azure"></a>SAP Azure 認定と Azure での SAP HANA の実行について
詳しくは、次のドキュメントをご覧ください。
* Windows OS を備えた Azure (クラシック モード) での SAP の実行に関する一般的な SAP Azure 情報: [Azure での Windows 仮想マシンにおける SAP の使用](windows/classic/sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
* ユーザーが使用できる既存の SAP テンプレートについての情報: [SAP 用の Azure クイックスタート テンプレート](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)。
* Linux OS を備えた Azure (Azure Resource Manager モデル) での SAP の実行に関する一般的な SAP Azure 情報: [Linux 仮想マシン (VM) における SAP の使用](virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 運用環境でサポートされている Azure VM の種類の一覧が記載された認定済み SAP HANA ハードウェア ディレクトリ: [認定済み SAP HANA® ハードウェア ディレクトリ](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)。
* 特に Linux ワークロード用の仮想マシン サイズについての情報: [Azure の仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* Azure でサポートされているすべての SAP 製品の一覧と、SAP でサポートされている Azure VM の種類の一覧が記載された SAP Note: [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)。
* Azure 上の Linux VM を使用した SAP の "拡張された監視機能" についての SAP Note: [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)。
* Azure の "L インスタンス" での SAP HANA サービス。 この情報が Azure VM での SAP HANA の実行に関するものではないことを理解しておくことが重要です。 これは、Azure VM で SAP アプリ サーバーが実行されるハイブリッド環境に関するものですが、SAP HANA はベアメタル サーバーで実行されます: [SAP Note 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)。
* Linux での SAPOSCOL に関する情報が記載された SAP Note: [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)。
* Microsoft Azure 上の SAP 向けの主要な監視メトリック: [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)。
* Azure Resource Manager についての情報: [Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)。
* テンプレートを使った Linux VM のデプロイに関する情報: [Azure Resource Manager テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* Azure Resource Manager デプロイメント モデルとクラシック デプロイメント モデルの比較: [Azure Resource Manager とクラシック デプロイメント: デプロイメント モデルとリソースの状態について](../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="sap-software-downloads"></a>SAP ソフトウェアのダウンロード
次のスクリーンショットに示すように、SAP Service Marketplace からソフトウェアをダウンロードできます。

* Linux/HANA 用の NetWeaver 7.5 のダウンロード:

 ![NetWeaver 7.5 をダウンロードするための [SAP Service Installation and Upgrade (SAP サービスのインストールとアップグレード)] ウィンドウ](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)
* HANA SP12 Platform Edition のダウンロード:

 ![HANA SP12 Platform Edition をダウンロードするための [SAP Service Installation and Upgrade (SAP サービスのインストールとアップグレード)] ウィンドウ](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

