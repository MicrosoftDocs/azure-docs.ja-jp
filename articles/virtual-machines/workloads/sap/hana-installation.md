---
title: "SAP HANA on Azure (L インスタンス) への SAP HANA のインストール | Microsoft Docs"
description: "SAP HANA on Azure (L インスタンス) への SAP HANA のインストール方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 280001f9057825b9dcd98c5180340a54e2e239cf
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>SAP HANA on Azure (L インスタンス) のインストールと構成の方法

このガイドを読む前に、確認しておく必要がある重要な定義を以下に示します。 「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)」で、HANA L インスタンス ユニットの次の 2 種類のクラスを紹介しました。

- S72、S72m、S144、S144m、S192、S192m - "Type I クラス" の SKU と呼びます。
- S384、S384m、S384xm、S576、S768、S960 - "Type II クラス" の SKU と呼びます。

クラス指定子は、HANA L インスタンス SKU に基づいてさまざまな機能や要件を最終的に参照するために、HANA L インスタンスのドキュメント全体にわたって使用されます。

頻繁に使用される他の定義は次のとおりです。
- **L インスタンス スタンプ:** SAP HANA TDI 認定のハードウェア インフラストラクチャ スタック。Azure 内で SAP HANA インスタンスを実行するための専用のスタックです。
- **SAP HANA on Azure (L インスタンス):** 異なる Azure リージョンの L インスタンス スタンプにデプロイされる SAP HANA TDI 認定ハードウェアで HANA インスタンスを実行する、Azure におけるサービスの正式名称。 関連用語の **HANA L インスタンス**は、SAP HANA on Azure (L インスタンス) の省略形であり、このテクニカル デプロイ ガイドで広く使用されています。


SAP HANA はお客様がインストールする必要があります。アクティビティは、新しい SAP HANA on Azure (L インスタンス) サーバーのハンドオフ後に開始できます。 その後、Azure VNet と HANA L インスタンス ユニット間の接続が確立されます。 

> [!Note]
> SAP ポリシーに従い、SAP HANA のインストールは、SAP HANA のインストールを実行する資格のある担当者が行う必要があります。 具体的には、SAP HANA インストールの認定試験である Certified SAP Technology Associate の合格者、または SAP 認定システム インテグレーター (SI) がインストールを実行します。

インストールする SAP HANA リリースで OS がサポートされていることを確認するために、「[SAP Support Note #2235581 - SAP HANA: Supported Operating Systems (SAP サポート ノート #2235581 - SAP HANA: サポートされているオペレーティング システム)](https://launchpad.support.sap.com/#/notes/2235581/E)」を再度確認してください (特に HANA 2.0 をインストールする場合)。 HANA 2.0 でサポートされている OS は、HANA 1.0 でサポートされている OS よりも限られています。 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>HANA L インスタンス ユニットを入手した後で最初に行うこと

**手順 1**: HANA L インスタンスを入手し、インスタンスへのアクセスと接続を確立したら、そのインスタンスの OS をご利用の OS プロバイダーに登録します。 たとえば、SUSE SMT のインスタンス (Azure の VM にデプロイしておく必要があります) の SUSE Linux OS を登録します。 HANA L インスタンス ユニットは、この SMT インスタンスに接続できます (このドキュメントで後述します)。 または RedHat OS を、接続先となる Red Hat Subscription Manager に登録する必要があります。 [こちらのドキュメント](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)の備考も参照してください。 OS にパッチを適用できるようにするためにも、この手順が必要となります。 これはお客様が各自で行う作業です。 SUSE については、SMT のインストールと構成に関するドキュメントを[ここから](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)参照してください。

**手順 2**: 特定の OS リリース/バージョンに関する新しいパッチと修正プログラムの有無をチェックします。 HANA L インスタンスのパッチ レベルが最新の状態になっているかどうかを確認してください。 OS のパッチ/リリースや Microsoft がデプロイ可能なイメージの変更のタイミングによっては、最新のパッチが含まれていない場合があります。 そのため、HANA L インスタンス ユニットの引き渡しが終わったら、セキュリティ、機能、可用性、パフォーマンスに関連する最新のパッチが特定の Linux ベンダーからリリースされているかどうか、また適用する必要があるかどうかを確認することが必須となります。

**手順 3**: 特定の OS リリース/バージョンへのSAP HANA のインストールと構成に関連した SAP ノートを確認します。 推奨事項は絶えず変化します。また SAP ノートや構成はインストールのシナリオによって異なるため、Microsoft が HANA L インスタンス ユニットを完璧に構成できるとは限りません。 そのため、お客様ご自身でご使用の Linux リリースの SAP HANA に関する SAP ノートを確認することが必須となります。 また、必要な OS リリース/バージョンの構成を確認し、まだ適用されていない構成設定を適用します。

具体的には、以下のパラメーターを確認し、最終的に次のように調整してください。

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

SLES12 SP1 および RHEL 7.2 以降では、これらのパラメーターを /etc/sysctl.d ディレクトリにある構成ファイルで設定する必要があります。 たとえば、91-NetApp-HANA.conf という名前の構成ファイルを作成する必要があります。 それより前にリリースされた SLES と RHEL では、これらのパラメーターを /etc/sysctl.conf で設定する必要があります。

すべてのバージョンの RHEL と SLES12 以降では、次のようにパラメーターを設定してください。 
- sunrpc.tcp_slot_table_entries = 128

このパラメーターは /etc/modprobe.d/sunrpc-local.conf で設定する必要があります。 このファイルが存在しない場合は、最初に次のエントリを追加して作成する必要があります。 
- options sunrpc tcp_max_slot_table_entries=128

**手順 4**: HANA L インスタンス ユニットのシステム時刻をチェックします。 インスタンスは、HANA L インスタンス スタンプが置かれている Azure リージョンの場所を表すシステム タイム ゾーンでデプロイされます。 自分が所有するインスタンスのタイム ゾーンとシステム時刻は、自由に変更することができます。 その場合、テナントにインスタンスを追加注文したときは、新たに納入されたインスタンスのタイム ゾーンをご自身で調整しなければならないことに注意してください。 Microsoft のオペレーションでは、インスタンスの納入後にお客様が設定したシステム タイム ゾーンは把握していません。 したがって、新たにデプロイされたインスタンスのタイム ゾーンが、お客様による変更後のタイムゾーンと異なる場合があります。 納入されたインスタンスのタイム ゾーンは、お客様ご自身でチェックし、必要に応じて調整してください。 

**手順 5**: etc/hosts をチェックします。 納入されたブレードには、さまざまな目的で異なる IP アドレスが割り当てられています (次のセクションを参照)。 etc/hosts ファイルを確認します。 既存のテナントにユニットが追加されるケースでは、新しくデプロイされたシステムの etc/hosts に、以前に納入されたシステムの IP アドレスが正しく記述されているとは限りません。 新しくデプロイされたインスタンスが、テナントにデプロイ済みのユニットと連動し、名前を解決できるように、設定が正しいことをお客様ご自身でチェックしてください。 

## <a name="networking"></a>ネットワーク
以降の説明は、次のドキュメントに記載された推奨事項に従って、Azure VNet が設計され、HANA L インスタンスに接続されていることを前提としています。

- [SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

個々の装置のネットワークに関して、いくつかの注意点があります。 すべての HANA L インスタンス ユニットに 2 ～ 3 個の IP アドレスが備わっており、それがそのユニットの 2 ～ 3 個の NIC ポートに割り当てられています。 HANA スケールアウト構成と HANA システム レプリケーション シナリオでは、3 つの IP アドレスが使用されます。 ユニットの NIC に割り当てられている IP アドレスの 1 つは、「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)」で説明されているサーバー IP プールから割り当てられたものです。

2 つの IP アドレスが割り当てられているユニットの区分は次にようになっています。

- eth0.xx には、お客様が Microsoft に提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 OS の /etc/hosts には、この IP アドレスが記述されます。
- eth1.xx には、NFS との通信用の IP アドレスが割り当てられます。 そのため、テナント内のインスタンスどうしがトラフィックをやり取りするために、これらのアドレスが etc/hosts に記述されている必要は**ありません**。

HANA システム レプリケーションまたは HANA スケールアウトのデプロイの場合、2 つの IP アドレスが割り当てられたブレード構成は適していません。 IP アドレスを 2 つだけ割り当てたうえで、そのような構成をデプロイする場合は、SAP HANA on Azure サービス管理に連絡して、3 つ目の VLAN で 3 つ目の IP アドレスを割り当ててください。 3 つの NIC ポートに 3 つの IP アドレスが割り当てられている HANA L インスタンス ユニットでは、次の使用規則が適用されます。

- eth0.xx には、お客様が Microsoft に提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 したがって OS の /etc/hosts には、この IP アドレスが記述されません。
- eth1.xx には、NFS ストレージとの通信用の IP アドレスが割り当てられます。 そのため、このタイプのアドレスは etc/hosts には記述されません。
- eth2.xx だけが etc/hosts に記述され、さまざまなインスタンス間の通信に使用されます。 これらのアドレスは、HANA がノード間の構成に使用する IP アドレスとして、スケールアウト HANA 構成で保持する必要がある IP アドレスでもあります。



## <a name="storage"></a>Storage

SAP HANA on Azure (L インスタンス) の記憶域のレイアウトは、ホワイト ペーパー『[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)』に記載されている SAP が推奨するガイドラインを使用して、SAP HANA on Azure サービス管理によって構成されています。 各種 HANA L インスタンス SKU で提供されるさまざまなボリュームのおおよそのサイズについては、「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

次の表に、ストレージ ボリュームの名前付け規則を示します。

| ストレージの使用法 | マウント名 | ボリューム名 | 
| --- | --- | ---|
| HANA データ | /hana/data/SID/mnt0000<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA ログ | /hana/log/SID/mnt0000<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA ログ バックアップ | /hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA 共有 | /hana/shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

SID は、HANA インスタンスのシステム ID です。 

tenant は、テナントの配置時の操作の内部列挙です。

ご覧のように、HANA 共有と usr/sap は同じボリュームを共有しています。 マウント ポイントの命名には、HANA インスタンスのシステム ID とマウント番号が含まれます。 スケールアップ配置には、mnt00001 のように、マウントは 1 つしか存在しません。 これに対して、スケールアウト配置には、使用している worker ノードおよびマスター ノードと同じ数のマウントが存在します。 スケールアウト環境では、データ、ログ、ログ バックアップの各ボリュームが共有され、スケールアウト構成の各ノードに接続されます。 複数の SAP インスタンスを実行する構成では、異なるボリューム セットが作成され、HANA L インスタンス ユニットに接続されます。

前述のホワイト ペーパーを読み、HANA L インスタンス ユニットを確認すると、HANA/data 用にかなり大きなディスク ボリュームを備えていること、また HANA/log/backup というボリュームがあることがわかります。 HANA/data が非常に大きなサイズに設定されているのは、お客様に提供されるストレージ スナップショットで同じディスク ボリュームが使用されるためです。 つまり、実行するストレージ スナップショットの増加に伴って、割り当てられたストレージ ボリュームでスナップショットに使用される領域が増加します。 HANA/log/backup ボリュームは、データベースのバックアップを格納するためのボリュームとして想定されているわけではありません。 HANA トランザクション ログ バックアップ用のバックアップ ボリュームとして使用するようにサイズ設定されています。 ストレージ スナップショット セルフサービスの今後のバージョンでは、このボリュームに格納するスナップショットの頻度を増やすことを目標としています。 また、ディザスター リカバリー サイトへのレプリケーションの頻度も増やす予定です (HANA L インスタンス インフラストラクチャで提供されるディザスター リカバリー機能をお客様が希望する場合)。 詳細については、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

お客様は、提供されているストレージに加え、追加のストレージ容量を 1 TB 単位で購入することができます。 この追加のストレージは、新しいボリュームとして HANA L インスタンスに追加できます。

SAP HANA on Azure サービス管理とのオンボード中、お客様は sidadm ユーザーと sapsys グループのユーザー ID (UID) とグループ ID (GID) を指定します (例: 1000,500)。SAP HANA システムのインストール時に、この同じ値を使用する必要があります。 ユニットに複数の HANA インスタンスをデプロイする場合は、複数のボリューム セット (インスタンスごとに 1 セット) を取得します。 そのため、デプロイ時に以下を定義する必要があります。

- 各 HANA インスタンスの SID (sidadm は SID から派生します)。
- 各 HANA インスタンスのメモリ サイズ。 インスタンスごとのメモリ サイズによって、個々のボリューム セットのボリュームのサイズが定義されるためです。

記憶域プロバイダーの推奨事項に基づいて、マウントされたすべてのボリューム (ブート LUN を除く) に対して次のマウント オプションが構成されます。

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

次の画像に示すように、これらのマウント ポイントは /etc/fstab に構成されます。

![HANA L インスタンス ユニットのマウントされたボリュームの fstab](./media/hana-installation/image1_fstab.PNG)

S72m HANA L インスタンス ユニットでの df -h コマンドの出力は次のようになります。

![HANA L インスタンス ユニットのマウントされたボリュームの fstab](./media/hana-installation/image2_df_output.PNG)


L インスタンス スタンプ内のストレージ コントローラーとノードは NTP サーバーに同期されます。 SAP HANA on Azure (L インスタンス) のユニットと Azure VM を NTP サーバーに対して同期する際は、Azure または L インスタンス スタンプ内のコンピューティング ユニットとインフラストラクチャの間で大きな時間のずれが発生しないようにする必要があります。

内部で使用されているストレージに SAP HANA を合わせるために、次の SAP HANA 構成パラメーターを設定する必要があります。

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
SAP HANA 1.0 バージョンから SPS12 までは、これらのパラメーターが SAP HANA データベースのインストール中に設定されます (「[SAP Note #2267798 - Configuration of the SAP HANA Database (SAP ノート #2267798 - SAP HANA データベースの構成)](https://launchpad.support.sap.com/#/notes/2267798)」を参照)。

SAP HANA データベースのインストール後に、hdbparam フレームワークを使ってパラメーターを構成することもできます。 

SAP HANA 2.0 では、hdbparam フレームワークが廃止されています。 パラメーターの設定には SQL コマンドを使ってください。 詳しくは、「[SAP Note #2399079: Elimination of hdbparam in HANA 2 (SAP ノート #2399079: HANA 2 で hdbparam を廃止)](https://launchpad.support.sap.com/#/notes/2399079)」をご覧ください。


## <a name="operating-system"></a>オペレーティング システム

「[SAP Support Note #1999997 - FAQ: SAP HANA Memory (SAP サポート ノート #1999997 - FAQ: SAP HANA のメモリ)](https://launchpad.support.sap.com/#/notes/1999997/E)」に従って、提供される OS イメージのスワップ領域は 2 GB に設定されています。 必要な設定が異なる場合は、お客様が設定する必要があります。

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) は、SAP HANA on Azure (L インスタンス) 用にインストールする Linux ディストリビューションです。 このディストリビューションでは、SAP 特有の機能を &quot;すぐに使う&quot; ことができます (SAP on SLES を効果的に実行するための事前設定されたパラメーターを含む)。

SUSE の Web サイトの[「Resource Library (リソース ライブラリ)」内の「White Papers (ホワイト ペーパー)」](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)と、SAP Community Network (SCN) の「[SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)」を参照して、SAP HANA on SLES のデプロイに関連するいくつかの便利なリソース (High Availability の設定や、SAP 運用向けのセキュリティ強化など) を確認できます。

その他の便利な SAP on SUSE 関連のリンクは次のとおりです。

- [SAP HANA on SUSE Linux のサイト](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12 (SAP のベスト プラクティス: レプリケーションのエンキュー – SAP NetWeaver on SUSE Linux Enterprise 12)](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)。
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (SLES 12 for SAP Applications を含む)。

SAP HANA on SLES 12 の実装に適用できる SAP サポート ノートは次のとおりです。

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation (SAP サポート ノート #1944799 – SLES オペレーティング システムのインストールに関する SAP HANA ガイドライン)](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)。
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications (SAP サポート ノート #2205917 – SAP HANA DB: 推奨される SLES 12 for SAP Applications 向け OS 設定)](https://launchpad.support.sap.com/#/notes/2205917/E)。
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes (SAP サポート ノート #1984787 – SUSE Linux Enterprise Server 12: インストールに関する注意事項)](https://launchpad.support.sap.com/#/notes/1984787)。
- [SAP Support Note #171356 – SAP Software on Linux:  General Information (SAP サポート ノート #171356 – SAP Software on Linux: 一般情報)](https://launchpad.support.sap.com/#/notes/1984787)。
- [SAP Support Note #1391070 – Linux UUID Solutions (SAP サポート ノート #1391070 – Linux UUID ソリューション)](https://launchpad.support.sap.com/#/notes/1391070)。

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) は、HANA L インスタンスで SAP HANA を実行するためのもう 1 つのプランです。 RHEL 6.7 および 7.2 のリリースが利用できます。 

その他の便利な SAP on Red Hat 関連のリンクは次のとおりです。
- [SAP HANA on Red Hat Linux のサイト](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)。

SAP HANA on Red Hat の実装に適用できる SAP サポート ノートは次のとおりです。

- [SAP Support Note #2009879 – SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System (SAP サポート ノート #2009879 – Red Hat Enterprise Linux (RHEL) オペレーティング システムに関する SAP HANA ガイドライン)](https://launchpad.support.sap.com/#/notes/2009879/E)。
- [SAP Support Note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7 (SAP サポート ノート #2292690 - SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2292690)。
- [SAP Support Note #2247020 - SAP HANA DB: Recommended OS settings for RHEL 6.7 (SAP サポート ノート #2247020 - SAP HANA DB: RHEL 6.7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2247020)。
- [SAP Support Note #1391070 – Linux UUID Solutions (SAP サポート ノート #1391070 – Linux UUID ソリューション)](https://launchpad.support.sap.com/#/notes/1391070)。
- [SAP Support Note #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11 (SAP サポート ノート #2228351 - Linux: RHEL 6 または SLES 11 の SAP HANA データベース SPS 11 リビジョン 110 以上)](https://launchpad.support.sap.com/#/notes/2228351)。
- [SAP Support Note #2397039 - FAQ: SAP on RHEL (SAP サポート ノート #2397039 - FAQ: SAP on RHEL)](https://launchpad.support.sap.com/#/notes/2397039)。
- [SAP Support Note #1496410 - Red Hat Enterprise Linux 6.x: Installation and Upgrade (SAP サポート ノート #1496410 - Red Hat Enterprise Linux 6.x: インストールおよびアップグレード)](https://launchpad.support.sap.com/#/notes/1496410)。
- [SAP Support Note #2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade (SAP サポート ノート #2002167 - Red Hat Enterprise Linux 7.x: インストールおよびアップグレード)](https://launchpad.support.sap.com/#/notes/2002167)。

## <a name="time-synchronization"></a>時刻同期

SAP NetWeaver アーキテクチャを基盤とする SAP アプリケーションは、SAP システムを構成するさまざまなコンポーネントの時刻のずれの影響を受けやすくなっています。 ZDATE\_LARGE\_TIME\_DIFF というエラー タイトルの SAP ABAP のショート ダンプは見慣れたものかもしれません。これらのショート ダンプは、さまざまなサーバーまたは VM のシステム時刻のずれが大きくなりすぎたときに発生するからです。

SAP HANA on Azure (L インスタンス) の場合、Azure で行われる時刻同期は、L インスタンス スタンプ内のコンピューティング ユニットには適用されません。 Azure ではシステム時刻が適切に同期されるので、ネイティブの Azure VM で SAP アプリケーションを実行する場合、この同期は適用されません。 結果として、別のタイム サーバーを、Azure VM 上で実行される SAP アプリケーション サーバーと、HANA L インスタンス上で実行される SAP HANA データベース インスタンスで使用できるように設定する必要があります。 L インスタンス スタンプ内のストレージ インフラストラクチャの時刻は NTP サーバーと同期されます。

## <a name="setting-up-smt-server-for-suse-linux"></a>SUSE Linux 用 SMT サーバーの設定
SAP HANA L インスタンスは、インターネットに直接接続していません。 そのため、このようなユニットを OS プロバイダーに登録し、パッチをダウンロードして適用するのは簡単なプロセスではありません。 SUSE Linux の場合、1 つの解決策として、Azure VM に SMT サーバーを設定します。 一方、Azure VM は、HANA L インスタンスに接続されている Azure VNet でホストする必要があります。 HANA L インスタンス ユニットは、このような SMT サーバーを使用して登録やパッチのダウンロードを実行できます。 

SUSE では、[Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) に関する包括的なガイドを提供しています。 

HANA L インスタンスのタスクを実行する SMT サーバーのインストールの前提条件として、次のものが必要です。

- HANA L インスタンスの ER 回線に接続された Azure VNet。
- 組織に関連付けられている SUSE アカウント。 組織には、有効な SUSE サブスクリプションが必要です。

### <a name="installation-of-smt-server-on-azure-vm"></a>Azure VM への SMT サーバーのインストール

この手順では、Azure VM に SMT サーバーをインストールします。 まず、[SUSE Customer Center](https://scc.suse.com/) にログインします。

ログインしたら、[Organization]\(組織\)、[Organization Credentials]\(組織の資格情報\) の順に移動します。 このセクションで、SMT サーバーを設定するために必要な資格情報を確認します。

次に、Azure VNet で SUSE Linux VM をインストールします。 VM をデプロイするには、Azure の SLES 12 SP2 ギャラリー イメージを取得します。 デプロイ プロセスでは、DNS 名を定義しないでください。また、次のスクリーンショットに示すように、静的 IP アドレスを使用しないでください。

![SMT サーバー用の VM のデプロイ](./media/hana-installation/image3_vm_deployment.png)

デプロイされた VM は小さなサイズの VM であり、Azure VNet の内部 IP アドレス (10.34.1.4) を取得しています。 VM の名前は smtserver です。 インストール後、HANA L インスタンス ユニットへの接続がチェックされます。 名前解決の構成方法によっては、Azure VM の etc/hosts で HANA L インスタンス ユニットの解決を構成することが必要な場合があります。 パッチを保持するために使用するディスクを VM に追加します。 ブート ディスク自体が小さすぎる場合があります。 次のスクリーンショットに示すように、このデモでは、/srv/www/htdocs にディスクがマウントされています。 100 GB のディスクで十分です。

![SMT サーバー用の VM のデプロイ](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

HANA L インスタンス ユニットにログインして /etc/hosts を維持し、SMT サーバーを実行することになっている Azure VM にネットワーク経由で到達できるかどうかを確認します。

このチェックが正常に完了したら、SMT サーバーを実行する Azure VM にログインする必要があります。 PuTTY を使用して VM にログインする場合は、Bash ウィンドウで、次の一連のコマンドを実行する必要があります。

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

これらのコマンドを実行したら、Bash を再起動して設定をアクティブにします。 次に YaST を起動します。

YaST で [Software Maintenance] に移動し、smt を検索します。 smt を選択すると、次のように yast2-smt に自動的に切り替わります。

![YaST 内の SMT](./media/hana-installation/image5_smt_in_yast.PNG)


smtserver にインストールするための選択内容をそのまま使用します。 インストールが完了したら、SMT サーバー構成に移動し、SUSE Customer Center で先ほど取得した組織の資格情報を入力します。 また、SMT サーバーの URL として、Azure VM のホスト名を入力します。 このデモでは、次の画像に示すように https://smtserver を使用しています。

![SMT サーバーの構成](./media/hana-installation/image6_configuration_of_smtserver1.png)

次に、SUSE Customer Center への接続が機能しているかどうかをテストする必要があります。 次の画像でおわかりのように、このデモでは接続は機能しています。

![SUSE Customer Center への接続のテスト](./media/hana-installation/image7_test_connect.png)

SMT セットアップが開始されたら、データベースのパスワードを入力する必要があります。 これは新規インストールであるため、次の画像のように、パスワードを定義する必要があります。

![データベースのパスワードの定義](./media/hana-installation/image8_define_db_passwd.PNG)

次の対話式操作は、証明書が作成されるときに発生します。 次のようなダイアログが表示され、手順が進みます。

![SMT サーバーの証明書の作成](./media/hana-installation/image9_certificate_creation.PNG)

構成の最後の "同期チェックの実行" には数分かかることがあります。 SMT サーバーのインストールと構成が完了すると、マウント ポイント /srv/www/htdocs/ の下に repo ディレクトリが作成され、repo に複数のサブディレクトリが作成されていることがわかります。 

次のコマンドを使用して、SMT サーバーと関連サービスを再起動します。

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>SMT サーバーへのパッケージのダウンロード

すべてのサービスが再起動したら、YaST を使用して SMT Management の適切なパッケージを選択します。 パッケージの選択は、SMT サーバーを実行している VM の SLES リリースまたはバージョンではなく、HANA L インスタンス サーバーの OS イメージによって異なります。 選択画面の例を次に示します。

![パッケージの選択](./media/hana-installation/image10_select_packages.PNG)

パッケージの選択が終わったら、設定した SMT サーバーへの選択したパッケージの初期コピーを開始する必要があります。 このコピーは、次のようにシェルで smt-mirror コマンドを使用することでトリガーされます。


![SMT サーバーへのパッケージのダウンロード](./media/hana-installation/image11_download_packages.PNG)

上記のように、マウント ポイント /srv/www/htdocs の下に作成されたディレクトリにパッケージがコピーされます。 このプロセスにはしばらく時間がかかることがあります。 選択したパッケージの数によっては、最大 1 時間以上かかる場合があります。
このプロセスが完了したら、SMT クライアントの設定に移ります。 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>HANA L インスタンス ユニットでの SMT クライアントの設定

この例のクライアントは、HANA L インスタンス ユニットです。 SMT サーバー セットアップによって、clientSetup4SMT.sh スクリプトが Azure VM にコピーされています。 SMT サーバーに接続する HANA L インスタンス ユニットにこのスクリプトをコピーします。 -h オプションを使用してスクリプトを開始し、SMT サーバーの名前をパラメーターとして渡します。 この例では smtserver です。

![SMT クライアントの構成](./media/hana-installation/image12_configure_client.PNG)

クライアントによるサーバーからの証明書の読み込みは成功しても、次のように登録に失敗する場合があります。

![クライアントの登録の失敗](./media/hana-installation/image13_registration_failed.PNG)

登録に失敗した場合は、こちらの [SUSE サポート ドキュメント](https://www.suse.com/de-de/support/kb/doc/?id=7006024)を参照し、ドキュメントに記載されている手順を実行します。

> [!IMPORTANT] 
> サーバー名として、完全修飾ドメイン名なしで VM の名前 (この例では smtserver) を指定する必要があります。 VM 名だけが有効です。 

これらの手順の実行後、HANA L インスタンス ユニットで次のコマンドを実行する必要があります。

```
SUSEConnect –cleanup
```

> [!Note] 
> テストでは、この手順の後、常に数分待たなければなりませんでした。 SUSE の記事に記載されている是正措置を講じた後、すぐに clientSetup4SMT.sh を実行すると、証明書がまだ有効になっていないというメッセージで終了しました。 通常は 5 ～ 10 分待ってから clientSetup4SMT.sh を実行すると、クライアント構成が正常に完了しました。

SUSE の記事の手順に基づいて修正する必要がある問題が発生した場合は、HANA L インスタンス ユニットで clientSetup4SMT.sh をもう一度開始する必要があります。 これで、次のようにスクリプトが正常に完了します。

![クライアント登録の成功](./media/hana-installation/image14_finish_client_config.PNG)

この手順では、Azure VM にインストールした SMT サーバーに接続するように、HANA L インスタンス ユニットの SMT クライアントを構成しました。 "zypper up" または "zypper in" を使用して、HANA L インスタンスへの OS パッチのインストールや、追加パッケージのインストールを実行できるようになりました。 取得できるのは、SMT サーバーで以前にダウンロードしたパッチだけであることに注意してください。


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>HANA L インスタンスへの SAP HANA のインストールの例
このセクションでは、HANA L インスタンス ユニットに SAP HANA をインストールする方法について説明します。 開始時点の状態は次のとおりです。

- SAP HANA L インスタンスをデプロイするためのすべてのデータを Microsoft に提供済み。
- Microsoft から SAP HANA L インスタンスを入手済み。
- オンプレミス ネットワークに接続された Azure VNet を作成済み。
- HANA L インスタンスの ExpressRotue 回線を同じ Azure VNet に接続済み。
- HANA L インスタンスのジャンプ ボックスとして使用する Azure VM をインストール済み。
- ジャンプ ボックスから HANA L インスタンス ユニットへの接続とその逆方向の接続が可能であることを確認済み。
- 必要なパッケージとパッチがすべてインストールされているかどうかを確認済み。
- 使用している OS への HANA のインストールに関する SAP ノートとドキュメントを読み、選択した HANA リリースが OS リリースでサポートされていることを確認済み。

次の一連の手順では、ジャンプ ボックス VM (この例では Windows OS 上で実行) への HANA インストール パッケージのダウンロード、HANA L インスタンス ユニットへのパッケージのコピー、セットアップの手順について説明します。

### <a name="download-of-the-sap-hana-installation-bits"></a>SAP HANA インストール ビットのダウンロード
HANA L インスタンス ユニットはインターネットに直接接続していないため、インストール パッケージを SAP から HANA L インスタンス VM に直接ダウンロードすることはできません。 インターネットへの直接接続がないことに対処するには、ジャンプ ボックスが必要です。 パッケージをジャンプ ボックス VM にダウンロードします。

HANA インストール パッケージをダウンロードするには、SAP S-user または SAP Marketplace にアクセスできる他のユーザーである必要があります。 ログイン後、次の一連の画面に順に移動します。

[SAP Service Marketplace](https://support.sap.com/en/index.html) に移動し、[Download Software]、[Installations and Upgrade]、[By Alphabetical Index]、[Under H – SAP HANA Platform Edition]、[SAP HANA Platform Edition 2.0]、[Installation]、[Download] の順にクリックして、次のファイルをダウンロードします。

![HANA インストールのダウンロード](./media/hana-installation/image16_download_hana.PNG)

このデモでは、SAP HANA 2.0 インストール パッケージをダウンロードしました。 Azure ジャンプ ボックス VM で、次のように自己解凍形式のアーカイブをディレクトリに展開します。

![HANA インストールの抽出](./media/hana-installation/image17_extract_hana.PNG)

アーカイブを抽出したら、抽出によって作成されたディレクトリ (上記の例では 51052030) を、HANA L インスタンス ユニットの /hana/shared ボリュームに作成したディレクトリにコピーします。

> [!Important]
> インストール パッケージをルートまたはブート LUN にコピーしないでください。領域が限られており、他のプロセスも使用する必要があるためです。


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>HANA L インスタンス ユニットへの SAP HANA のインストール
SAP HANA をインストールするには、ルート ユーザーとしてログインする必要があります。 SAP HANA をインストールための十分なアクセス許可を持つのはルートだけです。
まず、/hana/shared にコピーしたディレクトリに対するアクセス許可を設定する必要があります。 アクセス許可は次のように設定する必要があります。

```
chmod –R 744 <Installation bits folder>
```

グラフィカル セットアップを使用して SAP HANA をインストールする場合は、HANA L インスタンスに gtk2 パッケージがインストールされている必要があります。 次のコマンドを使用して、このパッケージがインストールされているかどうかを確認します。

```
rpm –qa | grep gtk2
```

以降の手順では、グラフィカル ユーザー インターフェイスを使用して、SAP HANA のセットアップのデモを行っています。 次の手順として、インストール ディレクトリに移動し、サブディレクトリ HDB_LCM_LINUX_X86_64 に移動します。 開始

```
./hdblcmgui 
```
サブディレクトリから上記のコマンドを実行します。 これで、インストールに必要なデータを入力するための一連の画面が表示されます。 このデモでは、SAP HANA データベース サーバーと SAP HANA クライアント コンポーネントをインストールします。 したがって、次のように [SAP HANA Database] を選択します。

![インストールでの HANA の選択](./media/hana-installation/image18_hana_selection.PNG)

次の画面では、[Install New System] を選択します。

![HANA の新規インストールの選択](./media/hana-installation/image19_select_new.PNG)

この手順の後、SAP HANA データベース サーバーに追加でインストールできる複数の追加コンポーネントの中から選択する必要があります。

![HANA の追加コンポーネントの選択](./media/hana-installation/image20_select_components.PNG)

このドキュメントでは、SAP HANA Client と SAP HANA Studio を選択します。 また、スケールアップ インスタンスもインストールします。 そのため、次の画面で [Single-Host System] を選択する必要があります。 

![スケールアップ インストールの選択](./media/hana-installation/image21_single_host.PNG)

次の画面では、いくつかのデータを入力する必要があります。

![SAP HANA SID の入力](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> HANA システム ID (SID) として、HANA L インスタンスのデプロイメントを注文したときに Microsoft に提供したものと同じ SID を入力する必要があります。 別の SID を選択すると、さまざまなボリュームでのアクセス許可の問題が原因でインストールが失敗します。

インストール ディレクトリとして、/hana/shared ディレクトリを使用します。 次に、HANA データ ファイルと HANA ログ ファイルの場所を指定する必要があります。


![HANA ログの場所の指定](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> この画面の前の選択画面で選択した SID を含むマウント ポイントが既に設定されているボリュームを、データ ファイルおよびログ ファイルとして定義します。 SID が前の画面で入力したものと一致しない場合は、前に戻り、SID をマウント ポイントに含まれている値に調整します。

次に、ホスト名を確認し、最終的な修正を行います。 

![ホスト名の確認](./media/hana-installation/image24_review_host_name.PNG)

次に、HANA L インスタンスのデプロイメントを注文したときに Microsoft に提供したデータも取得する必要があります。 

![システム ユーザーの UID と GID の入力](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> ユニットのデプロイメントを注文したときに Microsoft に提供したものと同じシステム ユーザー ID とユーザー グループの ID を入力する必要があります。 まったく同じ ID を入力しないと、HANA L インスタンス ユニットへの SAP HANA のインストールは失敗します。

このドキュメントには示されていませんが、次の 2 つの画面では、SAP HANA データベースの SYSTEM ユーザーのパスワードと、sapadm ユーザーのパスワードを入力する必要があります。sapadm ユーザーのパスワードは、SAP HANA データベース インスタンスの一部としてインストールされる SAP Host Agent で使用されます。

パスワードを定義すると確認画面が表示されます。 表示されているすべてのデータを確認し、インストールを続行します。 次のように、インストールの進行状況を示す進行状況画面が表示されます。

![インストールの進行状況の確認](./media/hana-installation/image27_show_progress.PNG)

インストールが終了すると、次のような画面が表示されます。

![インストールの終了](./media/hana-installation/image28_install_finished.PNG)

この時点で、SAP HANA インスタンスが起動して実行され、いつでも使用できる状態になります。 SAP HANA インスタンスには、SAP HANA Studio から接続することもできます。 また、SAP HANA の最新のパッチの有無を必ず確認し、それらのパッチを適用します。
























































 







 





