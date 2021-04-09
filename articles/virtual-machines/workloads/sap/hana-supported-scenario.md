---
title: SAP HANA on Azure (L インスタンス) のサポートされるシナリオ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) のサポートされるシナリオとそのアーキテクチャの詳細
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a49c55da0bb8018b3faa7c6f70b02e79d9478603
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666681"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA L インスタンスのサポートされるシナリオ
この記事では、HANA L インスタンス (HLI) のサポートされるシナリオとアーキテクチャの詳細について説明します。

>[!NOTE]
>必要なシナリオがこの記事に記載されていない場合は、お客様の要件を評価できるように Microsoft Service Management チームに問い合わせてください。
HLI ユニットを設定する前に、SAP またはサービス実装パートナーと共に設計を検証してください。

## <a name="terms-and-definitions"></a>用語と定義
この記事で使用されている用語と定義について理解しましょう。

- **SID**:HANA システムのシステム識別子
- **HLI**:HANA L インスタンス
- **DR**:障害復旧
- **標準 DR**:DR 目的にのみ使用される専用リソースによるシステム設定
- **多目的 DR**:DR イベント用に構成された実稼働インスタンスと共に非実稼働環境を使用するように構成された DR サイトのシステム 
- **単一 SID**:1 つのインスタンスがインストールされているシステム
- **マルチ SID**:複数のインスタンスが構成されているシステム。MCOS 環境とも呼ばれます
- **HSR**:SAP HANA システム レプリケーション

## <a name="overview"></a>概要
HANA L インスタンスは、ビジネス要件を達成するために役立つさまざまなアーキテクチャをサポートしています。 次の各セクションでは、アーキテクチャのシナリオとその構成の詳細について説明します。 

導き出されたアーキテクチャ設計は、純粋にインフラストラクチャの観点から見た設計であり、HANA の展開については、SAP または実装パートナーに相談する必要があります。 実際のシナリオがこの記事に記載されていない場合は、そのアーキテクチャを確認し、お客様に合ったソリューションを導き出せるように、Microsoft アカウント チームに問い合わせてください。

> [!NOTE]
> これらのアーキテクチャは TDI (Tailored Data Integration) 設計に完全に準拠しており、SAP によってサポートされています。

この記事では、サポートされている各アーキテクチャの 2 つのコンポーネントの詳細について説明します。

- イーサネット
- ストレージ

### <a name="ethernet"></a>イーサネット

プロビジョニングされた各サーバーには、一連のイーサネット インターフェイスが事前に構成されています。 各 HLI ユニットに構成されているイーサネット インターフェイスは、次の 4 種類に分類されます。

- **A**: クライアント アクセスで使用されます。
- **B**: ノード間通信に使用されます。 このインターフェイスは、(要求されるトポロジに関係なく) すべてのサーバー上に構成されますが、スケールアウト シナリオにのみ使用されます。
- **C**: ノードからストレージへの接続に使用されます。
- **D**: STONITH の設定のために、ノードから iSCSI デバイスへの接続に使用されます。 このインターフェイスは、HSR 設定が要求されたときにのみ構成されます。  

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | ノード間|
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | STONITH |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間|
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

HLI ユニット上に構成されているトポロジに基づいてインターフェイスを選択します。 たとえば、インターフェイス "B" はノード間通信用に設定されています。これは、スケールアウト トポロジが構成されている場合に便利です。 単一ノードのスケールアップ構成では、このインターフェイスを使用しません。 インターフェイスの使用方法の詳細については、実際に必要なシナリオ (この記事で後述します) を確認してください。 

必要に応じて、追加の NIC カードを独自に定義することができます。 ただし、既存の NIC の構成を変更することは *できません*。

>[!NOTE]
>物理インターフェイスまたはボンディングである追加のインターフェイスが見つかる場合があります。 実際の使用事例に合わせて前述のインターフェイスのみを検討するようにしてください。 その他のインターフェイスは無視して構いません。

2 つの IP アドレスが割り当てられているユニットの区分は次にようになっています。

- イーサネット "A" には、お客様が Microsoft に提出したサーバー IP プールのアドレス範囲内の IP アドレスが割り当てられています。 この IP アドレスは、OS の */etc/hosts* ディレクトリに保持されてい必要があります。

- イーサネット "C" には、NFS への通信に使用される IP アドレスが割り当てられています。 テナント内のインスタンスどおしがトラフィックをやり取りできるようにするために、このアドレスが *etc/hosts* ディレクトリに保持されている必要は *ありません*。

HANA システム レプリケーションまたは HANA スケールアウト配置の場合、2 つの IP アドレスが割り当てられたブレード構成は適していません。 IP アドレスを 2 つだけ割り当てたうえで、そのような構成をデプロイする場合は、Azure サービス管理の SAP HANA に連絡して、 3 つ目の VLAN 内で 3 つ目の IP アドレスを割り当ててもらってください。 3 つの NIC ポートに 3 つの IP アドレスが割り当てられている HANA L インスタンス ユニットでは、次の使用規則が適用されます。

- イーサネット "A" には、お客様が Microsoft に提出したサーバー IP プールのアドレス範囲外の IP アドレスが割り当てられています。 この IP アドレスは、OS の */etc/hosts* ディレクトリに保持しないでください。

- イーサネット "B" だけが *etc/hosts* ディレクトリに格納され、さまざまなインスタンス間の通信に使用されます。 これらのアドレスは、スケールアウト HANA 構成において、HANA によるノード間の構成用に保持すべき IP アドレスです。

- イーサネット "C" には、NFS ストレージへの通信に使用される IP アドレスが割り当てられています。 この種類のアドレスは、 */etc/hosts* ディレクトリに保持しないでください。

- イーサネット "D" は、ペースメーカー用 STONITH デバイスにアクセスするためにのみ使用する必要があります。 このインターフェイスは、HANA システム レプリケーション を構成し、SBD ベースのデバイスを使用してオペレーティング システムの自動フェールオーバーを実現する場合に必要です。


### <a name="storage"></a>ストレージ
ストレージは、要求されたトポロジに基づいて事前に構成されています。 ボリュームのサイズとマウント ポイントは、サーバーの数、SKU の数、および構成されているトポロジに応じて変わります。 詳細については、実際に必要なシナリオ (この記事で後述します) を確認してください。 さらにストレージが必要な場合は、1 TB ずつ購入できます。

>[!NOTE]
>マウント ポイント /usr/sap/\<SID> は、/hana/shared マウント ポイントへのシンボリック リンクです。


## <a name="supported-scenarios"></a>サポートされるシナリオ

次のセクションのアーキテクチャ図では、次の表記が使用されています。

[ ![ アーキテクチャ図の表](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

サポートされているシナリオを次に示します。

* SID が 1 つの単一ノード
* 単一ノードの MCOS
* DR (標準) ありの単一ノード
* DR (多目的) ありの単一ノード
* STONITH ありの HSR
* DR (標準/多目的) ありの HSR 
* ホストの自動フェールオーバー (1+1) 
* スタンバイありのスケールアウト
* スタンバイなしのスケールアウト
* DR ありのスケールアウト

## <a name="single-node-with-one-sid"></a>SID が 1 つの単一ノード

このトポロジは、1 つの SID を使用してスケールアップ構成で 1 つのノードをサポートします。

### <a name="architecture-diagram"></a>アーキテクチャの図  

![SID が 1 つの単一ノード](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|/hana/shared/SID | HANA のインストール | 
|/hana/data/SID/mnt00001 | データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | ログ ファイルのインストール | 
|/hana/logbackups/SID | 再実行ログ |

### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。

## <a name="single-node-mcos"></a>単一ノードの MCOS

このトポロジは、複数の SID を使用してスケールアップ構成で 1 つのノードをサポートします。

### <a name="architecture-diagram"></a>アーキテクチャの図  

![単一ノードの MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|/hana/shared/SID1 | SID1 用 HANA のインストール | 
|/hana/data/SID1/mnt00001 | SID1 用データ ファイルのインストール | 
|/hana/log/SID1/mnt00001 | SID1 用ログ ファイルのインストール | 
|/hana/logbackups/SID1 | SID1 の再実行ログ |
|/hana/shared/SID2 | SID2 用 HANA のインストール | 
|/hana/data/SID2/mnt00001 | SID2 用データ ファイルのインストール | 
|/hana/log/SID2/mnt00001 | SID2 用ログ ファイルのインストール | 
|/hana/logbackups/SID2 | SID2 の再実行ログ |

### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- ボリューム サイズの分布は、メモリ内のデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](./hana-overview-architecture.md)に関するページを参照してください。

## <a name="single-node-with-dr-using-storage-replication"></a>ストレージ レプリケーションを使用した DR の単一ノード
 
このトポロジは、プライマリ SID の DR サイトへのストレージベースのレプリケーションを使用し、1 つまたは複数の SID があるスケールアップ構成で 1 つのノードをサポートします。 この図では、プライマリ サイトに 1 つの SID システムしか描かれていませんが、MCOS システムもサポートされています。

### <a name="architecture-diagram"></a>アーキテクチャの図  

![ストレージ レプリケーションを使用した DR の単一ノード](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|/hana/shared/SID | SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | SID の再実行ログ |


### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- MCOS の場合:ボリューム サイズの分布は、メモリ内のデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](./hana-overview-architecture.md)に関するページを参照してください。
- DR サイト:ボリュームとマウント ポイントは、DR HLI ユニットでの実稼働 HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。 
- DR サイト:データ、ログ バックアップ、および共有ボリューム ("ストレージ レプリケーション" とマークされています) は、実稼働サイトのスナップショットを介してレプリケートされます。 これらのボリュームは、フェールオーバー中にのみマウントされます。 詳細については、[ディザスター リカバリーのフェールオーバー手順](./hana-overview-high-availability-disaster-recovery.md)に関するページを参照してください。
- *SKU Type I クラス* のブート ボリュームは DR ノードにレプリケートされます。


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>ストレージ レプリケーションを使用した DR (多目的) の単一ノード
 
このトポロジは、プライマリ SID の DR サイトへのストレージベースのレプリケーションを使用し、1 つまたは複数の SID があるスケールアップ構成で 1 つのノードをサポートします。 この図では、プライマリ サイトに 1 つの SID システムしか描かれていませんが、マルチ SID (MCOS) システムもサポートされています。 DR サイトでは、実稼働操作がプライマリ サイトから実行されているときに、HLA ユニットが QA インスタンスに使用されます。 DR のフェールオーバー (またはフェールオーバー テスト) 中に、DR サイトの QA インスタンスが停止されます。

### <a name="architecture-diagram"></a>アーキテクチャの図  

![ストレージ レプリケーションを使用した DR (多目的) の単一ノード](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**プライマリ サイト**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|**DR サイト**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/shared/QA-SID | QA SID 用 HANA のインストール | 
|/hana/data/QA-SID/mnt00001 | QA SID 用データ ファイルのインストール | 
|/hana/log/QA-SID/mnt00001 | QA SID 用ログ ファイルのインストール |
|/hana/logbackups/QA-SID | QA SID 用再実行ログ |

### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- MCOS の場合:ボリューム サイズの分布は、メモリ内のデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](./hana-overview-architecture.md)に関するページを参照してください。
- DR サイト:ボリュームとマウント ポイントは、DR HLI ユニットでの実稼働 HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。 
- DR サイト:データ、ログ バックアップ、および共有ボリューム ("ストレージ レプリケーション" とマークされています) は、実稼働サイトのスナップショットを介してレプリケートされます。 これらのボリュームは、フェールオーバー中にのみマウントされます。 詳細については、[ディザスター リカバリーのフェールオーバー手順](./hana-overview-high-availability-disaster-recovery.md)に関するページを参照してください。 
- DR サイト:QA インスタンスのインストール用に、QA のデータ、ログ バックアップ、ログ、および共有ボリューム ("QA インスタンスのインストール" とマークされています) が構成されています。
- *SKU Type I クラス* のブート ボリュームは DR ノードにレプリケートされます。

## <a name="hsr-with-stonith-for-high-availability"></a>HSR と STONITH を使用した高可用性
 
このトポロジは、HANA システム レプリケーション構成用に 2 つのノードをサポートします。 この構成は、ノード上の単一 HANA インスタンスでのみサポートされています。 つまり、MCOS シナリオはサポートされて *いません*。

> [!NOTE]
> 2019 年 12 月現在、このアーキテクチャは SUSE オペレーティング システムでのみサポートされています。


### <a name="architecture-diagram"></a>アーキテクチャの図  

![HSR と STONITH を使用した高可用性](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | STONITH 用に使用 |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH 用に使用 |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**プライマリ ノード上**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|**セカンダリ ノード上**|
|/hana/shared/SID | セカンダリ SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | セカンダリ SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | セカンダリ SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | セカンダリ SID 用再実行ログ |

### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- MCOS の場合:ボリューム サイズの分布は、メモリ内のデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](./hana-overview-architecture.md)に関するページを参照してください。
- STONITH: SBD は STONITH 設定用に構成されています。 ただし、STONITH の使用は省略可能です。


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>HSR と DR を使用した高可用性とストレージ レプリケーション
 
このトポロジは、HANA システム レプリケーション構成用に 2 つのノードをサポートします。 標準 DR と多目的 DR の両方がサポートされています。 これらの構成は、ノード上の単一 HANA インスタンスでのみサポートされています。 つまり、これらの構成では MCOS シナリオはサポートされて *いません*。

この図では、DR サイトに多目的シナリオが描かれています。DR サイトでは、実稼働操作がプライマリ サイトから実行されているときに、HLI ユニットが QA インスタンスに使用されます。 DR のフェールオーバー (またはフェールオーバー テスト) 中に、DR サイトの QA インスタンスが停止されます。 

### <a name="architecture-diagram"></a>アーキテクチャの図  

![HSR と DR を使用した高可用性とストレージ レプリケーション](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | STONITH 用に使用 |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH 用に使用 |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**プライマリ サイトのプライマリ ノード上**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|**プライマリ サイトのセカンダリ ノード上**|
|/hana/shared/SID | セカンダリ SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | セカンダリ SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | セカンダリ SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | セカンダリ SID 用再実行ログ |
|**DR サイト**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/shared/QA-SID | QA SID 用 HANA のインストール | 
|/hana/data/QA-SID/mnt00001 | QA SID 用データ ファイルのインストール | 
|/hana/log/QA-SID/mnt00001 | QA SID 用ログ ファイルのインストール |
|/hana/logbackups/QA-SID | QA SID 用再実行ログ |

### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- MCOS の場合:ボリューム サイズの分布は、メモリ内のデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](./hana-overview-architecture.md)に関するページを参照してください。
- STONITH: SBD は STONITH 設定用に構成されています。 ただし、STONITH の使用は省略可能です。
- DR サイト:プライマリおよびセカンダリ ノードのレプリケーションには、*2 セットのストレージ ボリュームが必要です*。
- DR サイト:ボリュームとマウント ポイントは、DR HLI ユニットでの実稼働 HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。 
- DR サイト:データ、ログ バックアップ、および共有ボリューム ("ストレージ レプリケーション" とマークされています) は、実稼働サイトのスナップショットを介してレプリケートされます。 これらのボリュームは、フェールオーバー中にのみマウントされます。 詳細については、[ディザスター リカバリーのフェールオーバー手順](./hana-overview-high-availability-disaster-recovery.md)に関するページを参照してください。 
- DR サイト:QA インスタンスのインストール用に、QA のデータ、ログ バックアップ、ログ、および共有ボリューム ("QA インスタンスのインストール" とマークされています) が構成されています。
- *SKU Type I クラス* のブート ボリュームは DR ノードにレプリケートされます。


## <a name="host-auto-failover-11"></a>ホストの自動フェールオーバー (1+1)
 
このトポロジは、ホストの自動フェールオーバー構成で 2 つのノードをサポートします。 マスター/worker ロールを持つノードが 1 つあり、スタンバイとしてもう 1 つあります。 *SAP は、S/4 HANA に対してのみ、このシナリオをサポートしています。* 詳細については、[OSS の注記「2408419 - SAP S/4HANA - Multi-Node Support」](https://launchpad.support.sap.com/#/notes/2408419)(2408419 - SAP S/4HANA - マルチノードのサポート) を参照してください。



### <a name="architecture-diagram"></a>アーキテクチャの図  

![ホストの自動フェールオーバー (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**マスターおよびスタンバイ ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |



### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- スタンバイ: ボリュームとマウント ポイントは、スタンバイ ユニットでの HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。
 

## <a name="scale-out-with-standby"></a>スタンバイありのスケールアウト
 
このトポロジは、スケールアウト構成で複数のノードをサポートします。 マスター ロールを持つ 1 つのノード、worker ロールを持つ 1 つまたは複数のノード、およびスタンバイとしての 1 つまたは複数のノードがあります。 ただし、同時に存在できるマスター ノードは 1 つのみです。


### <a name="architecture-diagram"></a>アーキテクチャの図  

![スタンバイありのスケールアウト](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**マスター、worker、およびスタンバイ ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |


## <a name="scale-out-without-standby"></a>スタンバイなしのスケールアウト
 
このトポロジは、スケールアウト構成で複数のノードをサポートします。 マスター ロールを持つ 1 つのノードと、worker ロールを持つ 1 つまたは複数のノードがあります。 ただし、同時に存在できるマスター ノードは 1 つのみです。


### <a name="architecture-diagram"></a>アーキテクチャの図  

![スタンバイなしのスケールアウト](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**マスターおよび worker ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |


### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。

## <a name="scale-out-with-dr-using-storage-replication"></a>ストレージ レプリケーションを使用した DR のスケールアウト
 
このトポロジは、DR ありのスケールアウト構成で複数のノードをサポートします。 標準 DR と多目的 DR の両方がサポートされています。 この図では、単一目的の DR のみが描かれています。 このトポロジは、スタンバイ ノードありまたはなしで依頼できます。


### <a name="architecture-diagram"></a>アーキテクチャの図  

![ストレージ レプリケーションを使用した DR のスケールアウト](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| B | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**プライマリ ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|**DR ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 


### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
-  DR サイト:ボリュームとマウント ポイントは、DR HLI ユニットでの実稼働 HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。 
- DR サイト:データ、ログ バックアップ、および共有ボリューム ("ストレージ レプリケーション" とマークされています) は、実稼働サイトのスナップショットを介してレプリケートされます。 これらのボリュームは、フェールオーバー中にのみマウントされます。 詳細については、[ディザスター リカバリーのフェールオーバー手順](./hana-overview-high-availability-disaster-recovery.md)に関するページを参照してください。 
- *SKU Type I クラス* のブート ボリュームは DR ノードにレプリケートされます。


## <a name="single-node-with-dr-using-hsr"></a>HSR を使用した DR の単一ノード
 
このトポロジは、プライマリ SID の DR サイトへの HANA システム レプリケーションを使用し、SID が 1 つあるスケールアップ構成で 1 つのノードをサポートします。 この図では、プライマリ サイトに 1 つの SID システムしか描かれていませんが、マルチ SID (MCOS) システムもサポートされています。

### <a name="architecture-diagram"></a>アーキテクチャの図  

![HSR を使用した DR の単一ノード](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは、両方の HLI ユニット (プライマリと DR) で事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|/hana/shared/SID | SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | SID の再実行ログ |


### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- MCOS の場合:ボリューム サイズの分布は、メモリ内のデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](./hana-overview-architecture.md)に関するページを参照してください。
- プライマリ ノードは、HANA システム レプリケーションを使用して DR ノードと同期します。 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。



## <a name="single-node-hsr-to-dr-cost-optimized"></a>単一ノード HSR から DR (コスト最適化) 
 
 このトポロジは、プライマリ SID の DR サイトへの HANA システム レプリケーションを使用し、SID が 1 つあるスケールアップ構成で 1 つのノードをサポートします。 この図では、プライマリ サイトに 1 つの SID システムしか描かれていませんが、マルチ SID (MCOS) システムもサポートされています。 DR サイトでは、実稼働操作がプライマリ サイトから実行されているときに、HLI ユニットが QA インスタンスに使用されます。 DR のフェールオーバー (またはフェールオーバー テスト) 中に、DR サイトの QA インスタンスが停止されます。

### <a name="architecture-diagram"></a>アーキテクチャの図  

![単一ノード HSR から DR (コスト最適化)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**プライマリ サイト**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|**DR サイト**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|/hana/shared/QA-SID | QA SID 用 HANA のインストール | 
|/hana/data/QA-SID/mnt00001 | QA SID 用データ ファイルのインストール | 
|/hana/log/QA-SID/mnt00001 | QA SID 用ログ ファイルのインストール |
|/hana/logbackups/QA-SID | QA SID 用再実行ログ |

### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- MCOS の場合:ボリューム サイズの分布は、メモリ内のデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](./hana-overview-architecture.md)に関するページを参照してください。
- DR サイト:ボリュームとマウント ポイントは、DR HLI ユニットでの実稼働 HANA インスタンスのインストール用に構成されています ("DR サイトの実稼働インスタンス" とマークされています)。 
- DR サイト:QA インスタンスのインストール用に、QA のデータ、ログ バックアップ、ログ、および共有ボリューム ("QA インスタンスのインストール" とマークされています) が構成されています。
- プライマリ ノードは、HANA システム レプリケーションを使用して DR ノードと同期します。 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>HSR の高可用性とディザスター リカバリー 
 
 このトポロジは、ローカル リージョンの高可用性のために HANA システム レプリケーション構成用に 2 つのノードをサポートします。 DR の場合、DR リージョンの 3 番目のノードは、HSR (非同期モード) を使用してプライマリ サイトと同期します。 

### <a name="architecture-diagram"></a>アーキテクチャの図  

![HSR の高可用性とディザスター リカバリー](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**プライマリ サイト**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|**DR サイト**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |


### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- DR サイト:ボリュームとマウント ポイントは、DR HLI ユニットでの実稼働 HANA インスタンスのインストール用に構成されます ("実稼働 DR インスタンス" とマークされています)。 
- プライマリ サイトのノードは、HANA システム レプリケーションを使用して DR ノードと同期します。 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>HSR の高可用性とディザスター リカバリー (コスト最適化)
 
 このトポロジは、ローカル リージョンの高可用性のために HANA システム レプリケーション構成用に 2 つのノードをサポートします。 DR の場合、DR リージョンの 3 番目のノードは、HSR (非同期モード) を使用してプライマリ サイトと同期しますが、別のインスタンス (QA など) は既に DR ノード外で実行されています。 

### <a name="architecture-diagram"></a>アーキテクチャの図  

![HSR の高可用性とディザスター リカバリー (コスト最適化)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**プライマリ サイト**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|**DR サイト**|
|/hana/shared/SID | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|/hana/shared/QA-SID | QA SID 用 HANA のインストール | 
|/hana/data/QA-SID/mnt00001 | QA SID 用データ ファイルのインストール | 
|/hana/log/QA-SID/mnt00001 | QA SID 用ログ ファイルのインストール |
|/hana/logbackups/QA-SID | QA SID 用再実行ログ |

### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- DR サイト:ボリュームとマウント ポイントは、DR HLI ユニットでの実稼働 HANA インスタンスのインストール用に構成されます ("実稼働 DR インスタンス" とマークされています)。 
- DR サイト:QA インスタンスのインストール用に、QA のデータ、ログ バックアップ、ログ、および共有ボリューム ("QA インスタンスのインストール" とマークされています) が構成されています。
- プライマリ サイトのノードは、HANA システム レプリケーションを使用して DR ノードと同期します。 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。

## <a name="scale-out-with-dr-using-hsr"></a>HSR を使用した DR ありのスケールアウト
 
このトポロジは、DR ありのスケールアウト構成で複数のノードをサポートします。 このトポロジは、スタンバイ ノードありまたはなしで依頼できます。 プライマリ サイトのノードは、HANA システム レプリケーション (非同期モード) を使用して DR サイトのノードと同期します。


### <a name="architecture-diagram"></a>アーキテクチャの図  

[ ![HSR を使用した DR ありのスケールアウト](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | 使用事例|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| B | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| B | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>ストレージ
次のマウント ポイントは事前に構成されています。

| マウント ポイント | 使用事例 | 
| --- | --- |
|**プライマリ ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |
|**DR ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |


### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- DR サイト:ボリュームとマウント ポイントは、DR HLI ユニットでの実稼働 HANA インスタンスのインストール用に構成されます。 
- プライマリ サイトのノードは、HANA システム レプリケーションを使用して DR ノードと同期します。 
- [Global Reach](../../../expressroute/expressroute-global-reach.md) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。


## <a name="next-steps"></a>次のステップ
- HANA L インスタンスの[インフラストラクチャと接続](./hana-overview-infrastructure-connectivity.md)
- HANA L インスタンスの[高可用性とディザスター リカバリー](./hana-overview-high-availability-disaster-recovery.md)
