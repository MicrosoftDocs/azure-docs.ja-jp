---
title: サポートされるシナリオの SAP HANA on Azure (L インスタンス) | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) のサポートされるシナリオとそのアーキテクチャの詳細
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ed63f5caa6b1f1c0072a92f6a60ad43c5431af0
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538349"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA L インスタンスのサポートされるシナリオ
このドキュメントでは、HANA L インスタンス (HLI) のサポートされるシナリオとそのアーキテクチャの詳細について説明します。

>[!NOTE]
>必要なシナリオがここに記載されていない場合は、お客様の要件を評価できるように Microsoft Service Management チームに問い合わせてください。
HLI ユニットのプロビジョニングに進む前に、SAP またはサービス実装パートナーと共に設計を検証してください。

## <a name="terms-and-definitions"></a>用語と定義
このドキュメントで使用されている用語と定義について理解しましょう。

- SID:HANA システムのシステム識別子。
- HLI:HANA L インスタンス。
- DR:ディザスター リカバリー サイト。
- 標準 DR:DR 目的にのみ使用される専用ソースによるシステム設定。
- 多目的 DR:DR イベントに使用するように構成された実稼働インスタンスと共に、非実稼働環境を使用するように構成された DR サイトのシステム。 
- 単一 SID:1 つのインスタンスがインストールされているシステム。
- マルチ SID:複数のインスタンスが構成されているシステム。 MCOS 環境とも呼ばれます。
- HSR:SAP HANA システム レプリケーション。

## <a name="overview"></a>概要
HANA L インスタンスは、ビジネス要件を達成するためにさまざまなアーキテクチャをサポートしています。 以下の一覧は、シナリオとその構成の詳細をまとめたものです。 

導き出されアーキテクチャ設計は、純粋にインフラストラクチャの観点から見た設計であり、HANA の展開については、SAP または実装パートナーに相談する必要があります。 実際のシナリオが一覧にない場合は、そのアーキテクチャを確認し、お客様に合ったソリューションを導き出せるように、Microsoft アカウント チームに問い合わせてください。

**これらのアーキテクチャは TDI (Tailored Data Integration) 設計に完全に準拠しており、SAP によってサポートされています。**

このドキュメントでは、サポートされている各アーキテクチャの 2 つのコンポーネントの詳細について説明します。

- イーサネット
- Storage

### <a name="ethernet"></a>イーサネット

プロビジョニングされる各サーバーには、イーサネット インターフェイスのセットが事前に構成されています。 各 HLI ユニットに構成されているイーサネット インターフェイスの詳細は次のとおりです。

- **A**: このインターフェイスは、クライアント アクセスに使用されます。
- **B**: このインターフェイスは、ノード間通信に使用されます。 このインターフェイスは (要求されるトポロジには関係なく) すべてのサーバー上に構成されますが、 
- スケールアウト シナリオにのみ使用されます。
- **C**: このインターフェイスは、ノードからストレージへの接続に使用されます。
- **D**: このインターフェイスは、STONITH の設定のために、ノードから ISCSI デバイスへの接続に使用されます。 このインターフェイスは、HSR 設定が要求されたときにのみ構成されます。  

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | ノード間 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | STONITH |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

HLI ユニットに構成されたトポロジに基づいてインターフェイスを使用します。 たとえば、インターフェイス "B" はノード間通信用に設定されています。これは、スケールアウト トポロジが構成されている場合に便利です。 単一ノードのスケールアップ構成の場合、このインターフェイスは使用されません。 インターフェイスの使用方法の詳細については、実際に必要なシナリオを確認してください (このドキュメントで後述します)。 

必要に応じて、追加の NIC カードを独自に定義することができます。 ただし、既存の NIC の構成を変更することはできません。

>[!NOTE]
>物理インターフェイスまたはボンディングである追加のインターフェイスを見つけられる場合もあります。 実際のユース ケースに合わせて前述のインターフェイスを検討することをお勧めします。その他のインターフェイスは無視しても、調整しなくても構いません。

2 つの IP アドレスが割り当てられているユニットの区分は次にようになっています。

- イーサネット "A" には、お客様が Microsoft に提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 OS の /etc/hosts には、この IP アドレスが記述されます。

- イーサネット "C" には、NFS への通信に使用される IP アドレスが割り当てられます。 そのため、テナント内のインスタンスどうしがトラフィックをやり取りするために、これらのアドレスが etc/hosts に記述されている必要は**ありません**。

HANA システム レプリケーションまたは HANA スケールアウトのデプロイの場合、2 つの IP アドレスが割り当てられたブレード構成は適していません。 IP アドレスを 2 つだけ割り当てたうえで、そのような構成をデプロイする場合は、SAP HANA on Azure サービス管理に連絡して、3 つ目の VLAN で 3 つ目の IP アドレスを割り当ててください。 3 つの NIC ポートに 3 つの IP アドレスが割り当てられている HANA L インスタンス ユニットでは、次の使用規則が適用されます。

- イーサネット "A" には、お客様が Microsoft に提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 したがって OS の /etc/hosts には、この IP アドレスが記述されません。

- イーサネット "B" だけが etc/hosts に記述され、さまざまなインスタンス間の通信に使用されます。 これらのアドレスは、HANA がノード間の構成に使用する IP アドレスとして、スケールアウト HANA 構成で保持する必要がある IP アドレスでもあります。

- イーサネット "C" には、NFS ストレージへの通信に使用される IP アドレスが割り当てられます。 そのため、このタイプのアドレスは etc/hosts には記述されません。

- イーサネット "D" は、ペースメーカー用 STONITH デバイスにアクセスするためにのみ使用する必要があります。 このインターフェイスは、HANA System Replication (HSR) を構成し、SBD ベースのデバイスを使用してオペレーティング システムで自動フェールオーバーを実現する場合に必要です。


### <a name="storage"></a>Storage
ストレージは、要求されたトポロジに基づいて事前に構成されています。 ボリュームのサイズとマウントポイントは、構成されているサーバー、SKU、およびトポロジの数に応じて変わります。 詳細については、実際に必要なシナリオを確認してください (このドキュメントで後述します)。 さらにストレージが必要な場合は、1 TB ずつ購入できます。

>[!NOTE]
>マウントポイント /usr/sap/\<SID> は、/hana/shared マウントポイントへのシンボリック リンクです。


## <a name="supported-scenarios"></a>サポートされるシナリオ

アーキテクチャ図では、以下の表記がグラフィックに使用されています。

[ ![Legends.PNG](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

以下の一覧は、サポートされているシナリオです。

1. SID が 1 つの単一ノード
2. 単一ノードの MCOS
3. DR (標準) ありの単一ノード
4. DR (多目的) ありの単一ノード
5. STONITH ありの HSR
6. DR (標準/多目的) ありの HSR 
7. ホストの自動フェールオーバー (1+1) 
8. スタンバイありのスケールアウト
9. スタンバイなしのスケールアウト
10. DR ありのスケールアウト



## <a name="1-single-node-with-one-sid"></a>1.SID が 1 つの単一ノード

このトポロジは、1 つの SID を使用してスケールアップ構成で 1 つのノードをサポートします。

### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
| --- | --- |
|/hana/shared/SID | HANA のインストール | 
|/hana/data/SID/mnt00001 | データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | ログ ファイルのインストール | 
|/hana/logbackups/SID | 再実行ログ |

### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。

## <a name="2-single-node-mcos"></a>2.単一ノードの MCOS

このトポロジは、複数の SID を使用してスケールアップ構成で 1 のノードをサポートします。

### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
| --- | --- |
|/hana/shared/SID1 | SID1 の HANA のインストール | 
|/hana/data/SID1/mnt00001 | SID1 のデータ ファイルのインストール | 
|/hana/log/SID1/mnt00001 | SID1 のログ ファイルのインストール | 
|/hana/logbackups/SID1 | SID1 の再実行ログ |
|/hana/shared/SID2 | SID2 の HANA のインストール | 
|/hana/data/SID2/mnt00001 | SID2 のデータ ファイルのインストール | 
|/hana/log/SID2/mnt00001 | SID2 のログ ファイルのインストール | 
|/hana/logbackups/SID2 | SID2 の再実行ログ |

### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- ボリューム サイズの分布は、メモリのデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)に関するセクションを参照してください。

## <a name="3-single-node-with-dr-using-storage-replication"></a>3.ストレージ レプリケーションを使用した DR の単一ノード
 
このトポロジは、プライマリ SID の DR サイトに対するストレージベースのレプリケーション使用し、1 つまたは複数の SID を使用してスケールアップ構成で 1 つのノードをサポートします。 この図では、プライマリ サイトには 1 つの SID しか表示されていませんが、マルチ SID (MCOS) もサポートされています。

### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![Single-node-with-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
| --- | --- |
|/hana/shared/SID | SID の HANA のインストール | 
|/hana/data/SID/mnt00001 | SID のデータ ファイルのインストール | 
|/hana/log/SID/mnt00001 | SID のログ ファイルのインストール | 
|/hana/logbackups/SID | SID の再実行ログ |


### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- MCOS の場合:ボリューム サイズの分布は、メモリのデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)に関するセクションを参照してください。
- DR: ボリュームとマウントポイントは、DR HLI ユニットの実稼働 HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。 
- DR: データ、ログバックアップ、および共有ボリューム ("ストレージ レプリケーション" とマークされています) は、実稼働サイトのスナップショットを介してレプリケートされます。 これらのボリュームは、フェールオーバー時にのみマウントされます。 詳細については、「[ディザスター リカバリーのフェールオーバー手順](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)」のドキュメントを参照してください。
- **SKU Type I クラス**のブート ボリュームは DR ノードです。


## <a name="4-single-node-with-dr-multipurpose-using-storage-replication"></a>4.ストレージ レプリケーションを使用した DR (多目的) の単一ノード
 
このトポロジは、プライマリ SID の DR サイトに対するストレージベースのレプリケーション使用し、1 つまたは複数の SID を使用してスケールアップ構成で 1 つのノードをサポートします。 この図では、プライマリ サイトには 1 つの SID しか表示されていませんが、マルチ SID (MCOS) もサポートされています。 DR サイトでは、実稼働操作がプライマリ サイトから実行されているときに、HLA ユニットが QA インスタンスに使用されます。 DR のフェールオーバー (またはフェールオーバー テスト) 時には、DR サイトの QA インスタンスが停止されます。

### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
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
- MCOS の場合:ボリューム サイズの分布は、メモリのデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)に関するセクションを参照してください。
- DR: ボリュームとマウントポイントは、DR HLI ユニットの実稼働 HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。 
- DR: データ、ログバックアップ、および共有ボリューム ("ストレージ レプリケーション" とマークされています) は、実稼働サイトのスナップショットを介してレプリケートされます。 これらのボリュームは、フェールオーバー時にのみマウントされます。 詳細については、「[ディザスター リカバリーのフェールオーバー手順](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)」のドキュメントを参照してください。 
- DR: QA インスタンスのインストール用に、QA のデータ、ログバックアップ、ログ、共有ボリューム ("QA インスタンスのインストール" とマークされています) が構成されています。
- **SKU Type I クラス**のブート ボリュームは DR ノードです。

## <a name="5-hsr-with-stonith-for-high-availability"></a>5.HSR と STONITH を使用した高可用性
 
このトポロジは、HANA System Replication (HSR) 構成の 2 つのノードをサポートします。 この構成は、ノード上の単一 HANA インスタンスに対してのみサポートされます。 つまり、MCOS シナリオはサポートされません。

**現在のところ、このアーキテクチャは SUSE オペレーティング システムでのみサポートされています。**


### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | STONITH 用に使用 |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH 用に使用 |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
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
- MCOS の場合:ボリューム サイズの分布は、メモリのデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)に関するセクションを参照してください。
- STONITH: SBD は STONITH 設定用に構成されています。 ただし、STONITH の使用は省略可能です。


## <a name="6-high-availability-with-hsr-and-dr-with-storage-replication"></a>6.HSR と DR を使用した高可用性とストレージ レプリケーション
 
このトポロジは、HANA System Replication (HSR) 構成の 2 つのノードをサポートします。 標準 DR と多目的 DR の両方がサポートされます。 これらの構成は、ノード上の単一 HANA インスタンスに対してのみサポートされます。 つまり、これらの構成では MCOS シナリオはサポートされません。

この図では多目的シナリオが描かれ、DR サイトでは、実稼働操作がプライマリ サイトから実行されているときに、HLA ユニットが QA インスタンスに使用されます。 DR のフェールオーバー (またはフェールオーバー テスト) 時には、DR サイトの QA インスタンスが停止されます。 



### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | STONITH 用に使用 |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH 用に使用 |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
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
- MCOS の場合:ボリューム サイズの分布は、メモリのデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)に関するセクションを参照してください。
- STONITH: SBD は STONITH 設定用に構成されています。 ただし、STONITH の使用は省略可能です。
- DR: プライマリおよびセカンダリ ノードのレプリケーションには、**2 セットのストレージ ボリュームが必要です**。
- DR: ボリュームとマウントポイントは、DR HLI ユニットの実稼働 HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。 
- DR: データ、ログバックアップ、および共有ボリューム ("ストレージ レプリケーション" とマークされています) は、実稼働サイトのスナップショットを介してレプリケートされます。 これらのボリュームは、フェールオーバー時にのみマウントされます。 詳細については、「[ディザスター リカバリーのフェールオーバー手順](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)」のドキュメントを参照してください。 
- DR: QA インスタンスのインストール用に、QA のデータ、ログバックアップ、ログ、共有ボリューム ("QA インスタンスのインストール" とマークされています) が構成されています。
- **SKU Type I クラス**のブート ボリュームは DR ノードです。


## <a name="7-host-auto-failover-11"></a>7.ホストの自動フェールオーバー (1+1)
 
このトポロジは、ホストの自動フェールオーバー構成で 2 つのノードをサポートします。 スタンバイとしてマスター/worker ロールを持つノードが 1 つあります。 **SAP は、S/4 HANA の場合にのみ、このシナリオをサポートしています。** 詳細については、OSS の注記「[2408419 - SAP S/4HANA - Multi-Node Support](https://launchpad.support.sap.com/#/notes/2408419)」(2408419 - SAP S/4HANA - マルチノードのサポート) を参照してください。



### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![sca](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
| --- | --- |
|**マスターおよびスタンバイ ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |



### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- スタンバイ: ボリュームとマウントポイントは、スタンバイ ユニット上の HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。
 

## <a name="8-scale-out-with-standby"></a>8.スタンバイありのスケールアウト
 
このトポロジは、スケールアウト構成で複数のノードをサポートします。 マスター ロールを持つ 1 つのノード、worker ロールを持つ 1 つまたは複数のノード、およびスタンバイとしての 1 つまたは複数のノードがあります。 ただし、同時に存在できるマスター ノードは 1 つのみです。


### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
| --- | --- |
|**マスター、worker、およびスタンバイ ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |


## <a name="9-scale-out-without-standby"></a>9.スタンバイなしのスケールアウト
 
このトポロジは、スケールアウト構成で複数のノードをサポートします。 マスター ロールを持つ 1 つのノードと、worker ロールを持つ 1 つまたは複数のノードがあります。 ただし、同時に存在できるマスター ノードは 1 つのみです。


### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
| --- | --- |
|**マスターおよび worker ノード上**|
|/hana/shared | 実稼働 SID 用 HANA のインストール | 
|/hana/data/SID/mnt00001 | 実稼働 SID 用データ ファイルのインストール | 
|/hana/log/SID/mnt00001 | 実稼働 SID 用ログ ファイルのインストール | 
|/hana/logbackups/SID | 実稼働 SID 用再実行ログ |


### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。

## <a name="10-scale-out-with-dr-using-storage-replication"></a>10.ストレージ レプリケーションを使用した DR のスケールアウト
 
このトポロジは、DR ありのスケールアウト構成で複数のノードをサポートします。 標準 DR と多目的 DR の両方がサポートされます。 この図では、単一目的の DR のみが描かれています。 このトポロジは、スタンバイ ノードありまたはなしで依頼できます。


### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI |
| b | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
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
-  DR: ボリュームとマウントポイントは、DR HLI ユニットの実稼働 HANA インスタンスのインストール用に構成されます ("HANA のインストールに必須" とマークされています)。 
- DR: データ、ログバックアップ、および共有ボリューム ("ストレージ レプリケーション" とマークされています) は、実稼働サイトのスナップショットを介してレプリケートされます。 これらのボリュームは、フェールオーバー時にのみマウントされます。 詳細については、「[ディザスター リカバリーのフェールオーバー手順](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)」のドキュメントを参照してください。 
- **SKU Type I クラス**のブート ボリュームは DR ノードです。


## <a name="11-single-node-with-dr-using-hsr"></a>11.HSR を使用した DR の単一ノード
 
このトポロジは、プライマリ SID の DR サイトに対する HANA システムのレプリケーション使用し、1 つの SID を使用してスケールアップ構成で 1 つのノードをサポートします。 この図では、プライマリ サイトには 1 つの SID しか表示されていませんが、マルチ SID (MCOS) もサポートされています。

### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![single-node-hsr-dr-111.png](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは、両方の HLI ユニット (プライマリと DR) の両方で事前に構成されています。

| マウントポイント | ユース ケース | 
| --- | --- |
|/hana/shared/SID | SID の HANA のインストール | 
|/hana/data/SID/mnt00001 | SID のデータ ファイルのインストール | 
|/hana/log/SID/mnt00001 | SID のログ ファイルのインストール | 
|/hana/logbackups/SID | SID の再実行ログ |


### <a name="key-considerations"></a>重要な考慮事項
- /usr/sap/SID は、/hana/shared/SID へのシンボリック リンクです。
- MCOS の場合:ボリューム サイズの分布は、メモリのデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)に関するセクションを参照してください。
- プライマリ ノードは、HANA システム レプリケーションを使用して DR ノードに同期します。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。



## <a name="12-single-node-hsr-to-dr-cost-optimized"></a>12.単一ノード HSR から DR (コスト最適化) 
 
 このトポロジは、プライマリ SID の DR サイトに対する HANA システムのレプリケーション使用し、1 つの SID を使用してスケールアップ構成で 1 つのノードをサポートします。 この図では、プライマリ サイトには 1 つの SID しか表示されていませんが、マルチ SID (MCOS) もサポートされています。 DR サイトでは、実稼働操作がプライマリ サイトから実行されているときに、HLA ユニットが QA インスタンスに使用されます。 DR のフェールオーバー (またはフェールオーバー テスト) 時には、DR サイトの QA インスタンスが停止されます。

### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![single-node-hsr-dr-cost-optimized-121.png](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
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
- MCOS の場合:ボリューム サイズの分布は、メモリのデータベース サイズに基づいています。 マルチ SID 環境でサポートされているメモリ内のデータベース サイズについては、[概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)に関するセクションを参照してください。
- DR: ボリュームとマウントポイントは、DR HLI ユニットの実稼働 HANA インスタンスのインストール用に構成されます ("DR サイトの実稼働インスタンス" とマークされています)。 
- DR: QA インスタンスのインストール用に、QA のデータ、ログバックアップ、ログ、共有ボリューム ("QA インスタンスのインストール" とマークされています) が構成されています。
- プライマリ ノードは、HANA システム レプリケーションを使用して DR ノードに同期します。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。

## <a name="13-high-availability-and-disaster-recovery-with-hsr"></a>13.HSR の高可用性とディザスター リカバリー 
 
 このトポロジは、ローカル リージョンの高可用性のための HANA System Replication (HSR) 構成の 2 つのノードをサポートします。 DR の場合、DR リージョンの 3 番目のノードは、HSR (非同期モード) を使用してプライマリ サイトから同期します。 

### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![hana-system-replication-dr-131.png](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
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
- DR: ボリュームとマウントポイントは、DR HLI ユニットの実稼働 HANA インスタンスのインストール用に構成されます ("実稼働 DR インスタンス" とマークされています)。 
- プライマリ サイトのノードは、HANA システム レプリケーションを使用して DR ノードに同期します。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。

## <a name="14-high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>14.HSR の高可用性とディザスター リカバリー (コスト最適化)
 
 このトポロジは、ローカル リージョンの高可用性のための HANA System Replication (HSR) 構成の 2 つのノードをサポートします。 DR の場合、DR リージョンの 3 番目のノードは、HSR (非同期モード) を使用してプライマリ サイトから同期しますが、 別のインスタンス (例えば QA) は既に DR ノード外で実行されています。 

### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

![hana-system-replication-dr-cost-optimized-141.png](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | 構成されているが使用されていない |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | 構成されているが使用されていない |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
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
- DR: ボリュームとマウントポイントは、DR HLI ユニットの実稼働 HANA インスタンスのインストール用に構成されます ("実稼働 DR インスタンス" とマークされています)。 
- DR: QA インスタンスのインストール用に、QA のデータ、ログバックアップ、ログ、共有ボリューム ("QA インスタンスのインストール" とマークされています) が構成されています。
- プライマリ サイトのノードは、HANA システム レプリケーションを使用して DR ノードに同期します。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。

## <a name="15-scale-out-with-dr-using-hsr"></a>15.HSR を使用した DR ありのスケールアウト
 
このトポロジは、DR ありのスケールアウト構成で複数のノードをサポートします。 このトポロジは、スタンバイ ノードありまたはなしで依頼できます。 プライマリ サイトのノードは、HANA システム レプリケーション (非同期モード) を使用して DR サイトのノードに同期されます。


### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム  

[ ![scale-out-dr-hsr-151.png](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>イーサネット
次のネットワーク インターフェイスは事前に構成されています。

| NIC 論理インターフェイス | SKU の種類 | SUSE OS の場合の名前 | RHEL OS の場合の名前 | ユース ケース|
| --- | --- | --- | --- | --- |
| A | TYPE I | eth0.tenant | eno1.tenant | クライアントから HLI/HSR |
| b | TYPE I | eth2.tenant | eno3.tenant | ノード間通信 |
| C | TYPE I | eth1.tenant | eno2.tenant | ノードからストレージ |
| D | TYPE I | eth4.tenant | eno4.tenant | 構成されているが使用されていない |
| A | TYPE II | vlan\<tenantNo> | team0.tenant | クライアントから HLI/HSR |
| b | TYPE II | vlan\<tenantNo+2> | team0.tenant+2 | ノード間通信 |
| C | TYPE II | vlan\<tenantNo+1> | team0.tenant+1 | ノードからストレージ |
| D | TYPE II | vlan\<tenantNo+3> | team0.tenant+3 | 構成されているが使用されていない |

### <a name="storage"></a>Storage
次のマウントポイントは事前に構成されています。

| マウントポイント | ユース ケース | 
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
- DR: ボリュームとマウントポイントは、DR HLI ユニットの実稼働 HANA インスタンスのインストール用に構成されます。 
- プライマリ サイトのノードは、HANA システム レプリケーションを使用して DR ノードに同期します。 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) を使用して ExpressRoute 回線を相互にリンクし、リージョンのネットワーク間にプライベート ネットワークを構築します。


## <a name="next-steps"></a>次の手順
- HLI については、[インフラストラクチャと接続](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)に関するページを参照してください
- HLI については「[高可用性とディザスター リカバリー](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)」を参照してください
