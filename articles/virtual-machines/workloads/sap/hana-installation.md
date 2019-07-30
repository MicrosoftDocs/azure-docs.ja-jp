---
title: SAP HANA on Azure (L インスタンス) への SAP HANA のインストール | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) への SAP HANA のインストール方法を説明します。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5bfd278cc4252167aace3aca52fec65fb3c6367f
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869137"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>SAP HANA on Azure (L インスタンス) のインストールと構成の方法

この記事を読む前に、[HANA L インスタンスの一般的な用語](hana-know-terms.md)と [HANA L インスタンスの SKU](hana-available-skus.md) について理解しておいてください。

SAP HANA のインストールはユーザーが行う必要があります。 Azure 仮想ネットワークと HANA L インスタンス ユニット間の接続を確立したら、新しい SAP HANA on Azure (L インスタンス) サーバーのインストールを開始できます。 

> [!Note]
> SAP ポリシーに従い、SAP HANA のインストールは、SAP HANA インストールの認定試験である Certified SAP Technology Associate の合格者または SAP 認定システム インテグレーター (SI) によって実施される必要があります。

HANA 2.0 をインストールする予定の場合は、「[SAP support note #2235581 - SAP HANA: Supported operating systems (SAP サポート ノート #2235581 - SAP HANA: サポートされているオペレーティング システム)](https://launchpad.support.sap.com/#/notes/2235581/E)」を参照して、インストールする SAP HANA リリースで OS がサポートされていることを確認してください。 HANA 2.0 でサポートされる OS は、HANA 1.0 でサポートされる OS より限られています。 

> [!IMPORTANT] 
> Type II ユニットの場合、現在は SLES 12 SP2 OS バージョンのみがサポートされています。 

HANA のインストールを開始する前に、以下について検証します。
- [HLI ユニット](#validate-the-hana-large-instance-units)
- [オペレーティング システムの構成](#operating-system)
- [ネットワーク構成](#networking)
- [ストレージの構成](#storage)


## <a name="validate-the-hana-large-instance-units"></a>HANA L インスタンス ユニットの検証

Microsoft から HANA L インスタンス ユニットを受け取ったら、以下の設定を検証し、必要に応じて調整します。

**手順 1**: HANA L インスタンスを受信し、インスタンスへのアクセスと接続を確立した後に、Azure portal で、インスタンスが正しい SKU および OS と共に表示されているかどうかを確認します。 チェックを実行するために必要な手順については、「[Azure portal を介した Azure HANA L インスタンスの制御](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal)」をお読みください。

**手順 2**: HANA L インスタンスを受け取り、インスタンスへのアクセスと接続を確立したら、そのインスタンスの OS をご利用の OS プロバイダーに登録します。 この手順には、Azure の VM にデプロイされている SUSE SMT のインスタンスに SUSE Linux OS を登録することが含まれます。 

HANA L インスタンス ユニットは、この SMT インスタンスに接続できます。 (詳細については、[SUSE Linux 用 SMT サーバーの設定方法](hana-setup-smt.md)に関するページを参照してください。) 別の方法として、Red Hat OS を、接続先にする必要がある Red Hat Subscription Manager に登録する必要があります。 詳細については、[SAP HANA on Azure (L インスタンス) とは何であるか](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページの説明を参照してください。 

この手順は、OS に修正プログラムを適用する際に必要で、ユーザー側で実行する必要があります。 SUSE については、[SMT のインストールに関するこのページ](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)で、SMT のインストールと構成についてのドキュメントを見つけてください。

**手順 3**: 特定の OS リリース/バージョンに関する新しいパッチと修正プログラムの有無をチェックします。 HANA L インスタンスのパッチ レベルが最新の状態になっていることを確認してください。 最新のパッチが含まれていない場合があります。 HANA L インスタンス ユニットを引き継いだら、修正プログラムを適用する必要があるかどうかを必ず確認してください。

**手順 4**: 特定の OS リリース/バージョンへの SAP HANA のインストールと構成についての SAP ノートを確認します。 推奨事項は絶えず変化します。また SAP ノートや構成は個々のインストール シナリオによって異なるため、Microsoft が常に HANA L インスタンス ユニットを完璧に構成できるとは限りません。 

そのため、お客様ご自身でご使用の Linux リリースの SAP HANA に関する SAP ノートを確認することが必須となります。 また、OS リリース/バージョンの構成を確認し、まだ適用していない構成設定を適用します。

具体的には、以下のパラメーターを確認し、最終的に次のように調整してください。

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

SLES12 SP1 および RHEL 7.2 以降では、これらのパラメーターを /etc/sysctl.d ディレクトリにある構成ファイルで設定する必要があります。 たとえば、91-NetApp-HANA.conf という名前の構成ファイルを作成する必要があります。 それより前にリリースされた SLES と RHEL では、これらのパラメーターを /etc/sysctl.conf で設定する必要があります。

RHEL 6.3 以降のすべての RHEL リリースについては、次のことに留意してください。 
- /etc/modprobe.d/sunrpc-local.conf で、sunrpc.tcp_slot_table_entries = 128 パラメーターを設定する必要があります。 このファイルが存在しない場合は、最初に次のエントリを追加し、ファイルを作成する必要があります。 
    - options sunrpc tcp_max_slot_table_entries=128

**手順 5**: HANA L インスタンス ユニットのシステム時刻をチェックします。 インスタンスは、システムのタイム ゾーンでデプロイされます。 このタイム ゾーンは、HANA L インスタンスのスタンプが位置する Azure リージョンの場所を表します。 インスタンスのシステム時刻やタイム ゾーンは、ユーザーが自由に変更できます。 

テナントにインスタンスを追加注文する場合は、新たに提供されるインスタンスのタイム ゾーンを調整する必要があります。 Microsoft では、提供後にお客様がインスタンスに設定したシステム タイム ゾーンについては情報を持っていません。 したがって、新たにデプロイされたインスタンスのタイム ゾーンは、お客様が変更したタイムゾーンと異なる場合があります。 提供されたインスタンスのタイム ゾーンは、必要に応じてお客様が適合させてください。 

**手順 6**: etc/hosts をチェックします。 ブレードが提供されるときには、さまざまな目的のために割り当てられた、さまざまな IP アドレスが設定されています。 etc/hosts ファイルを確認します。 既存のテナントにユニットが追加されるときには、新しくデプロイされたシステムの etc/hosts が、以前に提供されたシステムの IP アドレスを指定して正しく管理されていると思わないでください。 お客様ご自身で、新しくデプロイされたインスタンスが、テナントに以前デプロイされたユニットとやり取りし、名前を解決できることを確認してください。 


## <a name="operating-system"></a>オペレーティング システム

> [!IMPORTANT] 
> Type II ユニットの場合、現在は SLES 12 SP2 OS バージョンのみがサポートされています。 

提供される OS イメージのスワップ領域は、「[SAP support note #1999997 - FAQ: SAP HANA memory (SAP サポート ノート #1999997 - FAQ: SAP HANA のメモリ)](https://launchpad.support.sap.com/#/notes/1999997/E)」に従って、2 GB に設定されています。 異なる設定が必要な場合は、お客様ご自身で設定する必要があります。

[SAP アプリケーション用の SUSE Linux Enterprise Server 12 SP1](https://www.suse.com/products/sles-for-sap/download/) は、SAP HANA on Azure (L インスタンス) 用にインストールされる Linux ディストリビューションです。 このディストリビューションでは、SAP 特有の機能を "そのままで使う" ことができます (SAP on SLES を効果的に実行するための事前設定されたパラメーターを含む)。

SUSE Web サイトの[リソース ライブラリにあるホワイト ペーパー](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)や、SAP Community Network (SCN) の [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) に関するページでは、SAP HANA on SLES のデプロイに関連するいくつかの便利なリソース (高可用性の設定や、SAP の運用に特有のセキュリティ強化など) を参照できます。

以下に、その他の便利な SAP on SUSE 関連のリンクを示します。

- [SUSE Linux での SAP HANA に関するサイト](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP のベスト プラクティス: レプリケーションのエンキュー – SUSE Linux Enterprise 12 での SAP NetWeaver についてのページ](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SAP に対する SLES でのウイルス保護](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (SAP アプリケーション用の SLES 12 を含む)

SAP HANA on SLES 12 の実装に適用できる SAP サポート ノートは次のとおりです。

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation (SAP サポート ノート #1944799 – SLES オペレーティング システムのインストールに関する SAP HANA ガイドライン)](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications (SAP サポート ノート #2205917 – SAP HANA DB: 推奨される SLES 12 for SAP Applications 向け OS 設定)](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes (SAP サポート ノート #1984787 – SUSE Linux Enterprise Server 12: インストールに関する注意事項)](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP support note #171356 – SAP software on Linux:General Information (SAP サポート ノート #171356 – SAP Software on Linux: 一般情報)](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP Support Note #1391070 – Linux UUID Solutions (SAP サポート ノート #1391070 – Linux UUID ソリューション)](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) は、HANA L インスタンスで SAP HANA を実行するためのもう 1 つのプランです。 RHEL 6.7 および 7.2 のリリースが利用できます。 RHEL 7.2 以降のリリースのみがサポートされているネイティブの Azure VM とは対照的に、HANA L インスタンスは RHEL 6.7 もサポートしていることに注意してください。 ただし、RHEL 7.x リリースを使用することをお勧めします。

その他の便利な SAP on Red Hat 関連のリンクは次のとおりです。
- [SAP HANA on Red Hat Linux のサイト](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)。

SAP HANA on Red Hat の実装に適用できる SAP サポート ノートは次のとおりです。

- [SAP Support Note #2009879 – SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System (SAP サポート ノート #2009879 – Red Hat Enterprise Linux (RHEL) オペレーティング システムに関する SAP HANA ガイドライン)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP support note #2292690 - SAP HANA DB:Recommended OS settings for RHEL 7 (SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP Support Note #2247020 - SAP HANA DB:Recommended OS settings for RHEL 6.7 (SAP サポート ノート #2247020 – SAP HANA DB: RHEL 6.7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2247020)
- [SAP Support Note #1391070 – Linux UUID Solutions (SAP サポート ノート #1391070 – Linux UUID ソリューション)](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP support note #2228351 - Linux:SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11 (SAP サポート ノート #2228351 - Linux: RHEL 6 または SLES 11 の SAP HANA データベース SPS 11 リビジョン 110 以上)](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP support note #2397039 - FAQ: SAP on RHEL (SAP サポート ノート #2397039 - FAQ: SAP on RHEL)](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP support note #1496410 - Red Hat Enterprise Linux 6.x: Installation and Upgrade (SAP サポート ノート #1496410 - Red Hat Enterprise Linux 6.x: インストールとアップグレード)](https://launchpad.support.sap.com/#/notes/1496410)
- [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade (SAP サポート ノート #2002167 - Red Hat Enterprise Linux 7.x: インストールとアップグレード)](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>時刻同期

SAP NetWeaver アーキテクチャを基盤として構築された SAP アプリケーションは、SAP システムを構成するさまざまなコンポーネントの時刻のずれの影響を受けやすくなっています。 おそらく、ZDATE\_LARGE\_TIME\_DIFF というエラー タイトルが付いた SAP ABAP のショート ダンプを見たことがあるでしょう。 これらのショート ダンプが表示されるのは、異なるサーバーまたは VM のシステム時刻が、あまりにも違っているためです。

SAP HANA on Azure (L インスタンス) の場合、Azure で行われる時刻同期は、L インスタンス スタンプ内のコンピューティング ユニットには適用されません。 Azure ではシステム時刻が正しく同期されるため、ネイティブの Azure VM で SAP アプリケーションを実行する場合、この同期は適用されません。 

結果として、Azure VM 上で実行される SAP アプリケーション サーバーと、HANA L インスタンス上で実行される SAP HANA データベース インスタンスで使用できる別のタイム サーバーを設定する必要があります。 L インスタンス スタンプ内のストレージ インフラストラクチャの時刻は NTP サーバーと同期されます。


## <a name="networking"></a>ネットワーク
Azure 仮想ネットワークの設計と、それらの仮想ネットワークの HANA L インスタンスへの接続が、以下のドキュメントで説明されている推奨事項に従って行われていることが前提です。

- [SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

単一ユニットのネットワーキングに関して、触れておくべき注意点がいくつかあります。 どの HANA L インスタンス ユニットにも 2 ～ 3 個の IP アドレスが備わっており、それらのアドレスは 2 ～ 3 個の NIC ポートに割り当てられています。 HANA スケールアウト構成と HANA システム レプリケーション シナリオでは、3 つの IP アドレスが使用されます。 ユニットの NIC に割り当てられている IP アドレスの 1 つは、「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)」で説明されているサーバー IP プールから割り当てられたものです。

アーキテクチャのイーサネットの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)についてのページを参照してください。

## <a name="storage"></a>Storage

SAP HANA on Azure (L インスタンス) のストレージ レイアウトは、SAP が推奨するガイドラインに従う SAP HANA on Azure `service management` によって構成されます。 これらのガイドラインについては、ホワイト ペーパー「[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)」に記載されています。 

各種 HANA L インスタンス SKU で提供されるさまざまなボリュームのおおよそのサイズについては、「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

次の表に、ストレージ ボリュームの名前付け規則を示します。

| ストレージの使用法 | マウント名 | ボリューム名 | 
| --- | --- | ---|
| HANA データ | /hana/data/SID/mnt0000\<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA ログ | /hana/log/SID/mnt0000\<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA ログ バックアップ | /hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA 共有 | /hana/shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* は、HANA インスタンスのシステム ID です。 

*Tenant* は、テナント配置時の操作の内部列挙です。

HANA usr/sap は、同じボリュームを共有します。 マウント ポイントの命名には、HANA インスタンスのシステム ID とマウント番号が含まれます。 スケールアップ配置には、mnt00001 のように、マウントは 1 つしか存在しません。 一方スケールアウト配置には、使用している worker ノードおよびマスター ノードと同じ数のマウントが存在します。 

スケールアウト環境では、データ、ログ、ログ バックアップの各ボリュームが共有され、スケールアウト構成の各ノードに接続されます。 複数の SAP インスタンスである構成の場合、異なるボリューム セットが作成され、HANA L インスタンス ユニットに接続されます。 実際のシナリオのストレージ レイアウトの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)に関する記事を参照してください。

HANA L インスタンス ユニットを確認すると、HANA/data 用に大きなディスク ボリュームを備えていること、また、HANA/log/backup というボリュームがあることが分ります。 HANA/data をこれほど大きくしているのは、お客様に提供されるストレージ スナップショットが同じディスク ボリュームを使用しているためです。 ストレージ スナップショットを数多く実行するほど、割り当てられたストレージ ボリュームでは、スナップショットがより多くの領域を消費します。 

HANA/log/backup ボリュームは、データベース バックアップ用のボリュームになることは想定されていません。 そのサイズは、HANA トランザクション ログ バックアップ用のバックアップ ボリュームとして使用される設定となっています。 詳細については、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

お客様は、提供されているストレージに加え、追加のストレージ容量を 1 TB 単位で購入することができます。 この追加ストレージは、新しいボリュームとして HANA L インスタンスに追加できます。

SAP HANA on Azure `service management` によるオンボード中、お客様は sidadm ユーザーと sapsys グループのユーザー ID (UID) とグループ ID (GID) を指定します (例: 1000,500)。 SAP HANA システムのインストール時には、これらと同じ値を使用する必要があります。 1 つのユニットに複数の HANA インスタンスをデプロイするために、複数のボリューム セット (インスタンスごとに 1 セット) を取得します。 そのため、デプロイ時に以下を定義する必要があります。

- 各 HANA インスタンスの SID (sidadm はそれからから派生されます)。
- 各 HANA インスタンスのメモリ サイズ。 インスタンスごとのメモリ サイズによって、個々のボリューム セットのボリュームのサイズが定義されます。

ストレージ プロバイダーの推奨事項に基づいて、マウントされるすべてのボリューム (ブート LUN を除く) に対して次のマウント オプションが構成されます。

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

次の画像に示すように、これらのマウント ポイントは /etc/fstab に構成されます。

![HANA L インスタンス ユニットのマウントされたボリュームの fstab](./media/hana-installation/image1_fstab.PNG)

S72m HANA L インスタンス ユニットに対する df -h コマンドの出力は次のようになります。

![HANA L インスタンス ユニットのマウントされたボリュームの fstab](./media/hana-installation/image2_df_output.PNG)


L インスタンス スタンプ内のストレージ コントローラーとノードは NTP サーバーに同期されます。 SAP HANA on Azure (L インスタンス) のユニットと Azure VM を NTP サーバーに対して同期するときには、Azure と L インスタンス スタンプのインフラストラクチャやコンピューティング ユニットの間に大きな時間のずれがあってはいけません。

内部で使用されているストレージに対して SAP HANA を最適化するため、以下の SAP HANA 構成パラメーターを設定する必要があります。

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
SAP HANA 1.0 バージョンから SPS12 までは、これらのパラメーターが SAP HANA データベースのインストール中に設定されます (「[SAP Note #2267798 - Configuration of the SAP HANA Database (SAP ノート #2267798 - SAP HANA データベースの構成)](https://launchpad.support.sap.com/#/notes/2267798)」を参照)。

SAP HANA データベースのインストール後に、hdbparam フレームワークを使ってパラメーターを構成することもできます。 

HANA L インスタンスで使用されるストレージには、ファイル サイズの制限があります。 ファイルごとの[サイズの制限は 16 TB です](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)。 EXT3 ファイル システムのファイル サイズ制限とは異なり、HANA は、HANA L インスタンスのストレージによって強制されるストレージ制限を暗黙的に認識しません。 そのため、HANA ではファイル サイズ制限の16 TB に達したときに新しいデータ ファイルが自動的に作成されません。 HANA は 16 TB を超えるサイズへファイルを拡大しようとするため、HANA でエラーが報告され、最終的にはインデックス サーバーがクラッシュします。

> [!IMPORTANT]
> HANA L インスタンス ストレージの 16 TB のファイル サイズ制限を超えて、HANA がデータ ファイルの拡大を試行するのを防ぐには、SAP HANA global.ini 構成ファイルで以下のパラメーターを設定する必要があります
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005) も参照してください
> - SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285)に注意してください


SAP HANA 2.0 では、hdbparam フレームワークが非推奨となりました。 この結果、これらのパラメーターは SQL コマンドを使用して設定する必要があります。 詳しくは、「[SAP Note #2399079: Elimination of hdbparam in HANA 2 (SAP ノート #2399079: HANA 2 で hdbparam を廃止)](https://launchpad.support.sap.com/#/notes/2399079)」をご覧ください。

アーキテクチャのストレージ レイアウトの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)に関する記事を参照してください。


**次のステップ**

- [HLI への HANA のインストール](hana-example-installation.md)に関するページを参照してください










































 







 




