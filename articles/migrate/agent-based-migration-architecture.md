---
title: Azure Migrate Server Migration のエージェントベースの移行アーキテクチャ
description: Azure Migrate Server Migration を使用したエージェントベースの VMware VM の移行について概要を示します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: f5ad3aa0fc51f47942750d3745ffef1d6e4a087d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232582"
---
# <a name="agent-based-migration-architecture"></a>エージェントベースの移行アーキテクチャ

この記事では、Azure Migrate Server Migration ツールを使用したエージェントベースのレプリケーションに対して使用されるアーキテクチャとプロセスの概要を示します。

[Azure Migrate](migrate-services-overview.md) では、オンプレミスのアプリとワークロード、および AWS/GCP VM インスタンスの検出、評価、それらの Azure への移行を追跡するための中央ハブが提供されます。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。

## <a name="agent-based-replication"></a>エージェントベースのレプリケーション

Azure Migrate Server レプリケーション ツールでのエージェントベースのレプリケーションは、オンプレミスの VMware VM と物理サーバーを Azure に移行するために使用されます。 また、その他のオンプレミスの仮想サーバーや、AWS インスタンスや GCP VM などのプライベートおよびパブリック クラウド VM の移行にも使用できます。

VMware の移行のために、Azure Migrate Server Migration ツールはいくつかのオプションを提供しています。

- この記事で説明されているように、エージェントベースのレプリケーションを使用して移行する。
- VM に何もインストールする必要がないエージェントレス レプリケーションを使用して VM を移行する。

[VMware の移行方法の選択](server-migrate-overview.md)について詳細を確認してください。

## <a name="server-migration-and-azure-site-recovery"></a>Server Migration と Azure Site Recovery

Azure Migrate Server Migration は、オンプレミスとパブリック クラウドのワークロードを Azure に移行するためのツールです。 これは移行のために最適化されています。 Site Recovery は、ディザスター リカバリー ツールです。 Azure Server Migration と Site Recovery では、データ レプリケーションに使用されるいくつかの共通のテクノロジ コンポーネントが共有されますが、それぞれの目的は異なります。

## <a name="architectural-components"></a>アーキテクチャ コンポーネント

![アーキテクチャ](./media/agent-based-replication-architecture/architecture.png)

次の表で、エージェントベースの移行のために使用されるコンポーネントについて説明します。

**コンポーネント** | **詳細** | **インストール**
--- | --- | ---
**レプリケーション アプライアンス** | レプリケーション アプライアンス (構成サーバー) は、オンプレミス環境と Azure Migrate Server Migration ツールのブリッジとして機能するオンプレミス マシンです。 アプライアンスはオンプレミスの VM インベントリを検出するため、Azure Server Migration はレプリケーションと移行を調整できます。 アプライアンスには、次の 2 つのコンポーネントがあります。<br/><br/> **構成サーバー**:Azure Migrate Server Migration に接続し、レプリケーションを調整します。<br/> **プロセス サーバー**:データ レプリケーションを処理します。 VM データを受信し、それを圧縮および暗号化し、Azure サブスクリプションに送信します。 そこで、Server Migration はデータをマネージド ディスクに書き込みます。 | 既定では、プロセス サーバーは構成サーバーとともにレプリケーション アプライアンスにインストールされます。
**モビリティ サービス** | モビリティ サービスは、レプリケートし、移行する各マシンにインストールされるエージェントです。 マシンからプロセス サーバーにレプリケーション データを送信します。 さまざまなモビリティ サービス エージェントを使用できます。 | モビリティ サービスのインストール ファイルは、レプリケーション アプライアンスにあります。 レプリケートするマシンのオペレーティング システムとバージョンに従い、必要なエージェントをダウンロードし、インストールします。

### <a name="mobility-service-installation"></a>モビリティ サービスのインストール

次の方法でモビリティ サービスをデプロイできます。

- **プッシュ インストール**:マシンの保護を有効にすると、プロセス サーバーによってモビリティ サービスがインストールされます。 
- **手動インストール**:UI またはコマンド プロンプトを使用して、各マシンにモビリティ サービスを手動でインストールできます。

モビリティ サービスは、レプリケーション アプライアンスおよびレプリケート対象のマシンと通信します。 レプリケーション アプライアンス、プロセス サーバー、またはレプリケート対象のマシンでウイルス対策ソフトウェアを実行している場合、次のフォルダーをスキャンから除外する必要があります。


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (モビリティ サービスがインストールされている Windows マシン)

## <a name="replication-process"></a>レプリケーション プロセス

1. VM のレプリケーションを有効にすると、Azure への初回のレプリケーションが開始されます。
2. 初回のレプリケーション中、モビリティ サービスはマシンのディスクからデータを読み取り、それをプロセス サーバーに送信します。
3. このデータは、Azure サブスクリプションでディスクのコピーをシードするために使用されます。 
4. 初回のレプリケーションの終了後、Azure への差分変更のレプリケーションが開始されます。 レプリケーションは、ブロック レベルで、ほぼ連続的です。
4. モビリティ サービスは、オペレーティング システムのストレージ サブシステムと統合することで、VM ディスク メモリへの書き込みをインターセプトします。 この方法では、増分レプリケーションに対するレプリケートするマシンでのディスク I/O 操作が回避されます。 
5. マシンの追跡対象の変更は、受信ポート HTTPS 9443 でプロセス サーバーに送信されます。 このポートは変更可能です。 プロセス サーバーは、圧縮と暗号化を行い、Azure に送信します。 

## <a name="ports"></a>Port

**デバイス** | **Connection**
--- | --- 
VM | VM で実行されるモビリティ サービスは、レプリケーション管理のために、受信ポート HTTPS 443 でオンプレミスのレプリケーション アプライアンスと通信します。<br/><br/> VM は、受信ポート HTTPS 9443 でプロセス サーバー (既定ではレプリケーション アプライアンスで実行) にレプリケーション データを送信します。 このポートは変更可能です。
レプリケーション アプライアンス | レプリケーション アプライアンスは、アウトバウンド ポート HTTPS 443 経由で Azure によるレプリケーションを調整します。
プロセス サーバー | プロセス サーバーは、レプリケーション データを受信し、データを最適化して暗号化し、送信ポート 443 経由で Azure ストレージに送信します。


## <a name="performance-and-scaling"></a>パフォーマンスとスケーリング

既定では、構成サーバーとプロセス サーバーの両方を実行する単一のレプリケーション アプライアンスをデプロイします。 少数のマシンのみをレプリケートする場合は、このデプロイで十分です。 ただし、数百台のマシンをレプリケートして移行する場合、単一のプロセス サーバーでは、すべてのレプリケーション トラフィックを処理できない可能性があります。 この場合、追加のスケールアウト プロセス サーバーをデプロイできます。

### <a name="site-recovery-deployment-planner-for-vmware"></a>VMware 用の Site Recovery Deployment Planner

VMware VM をレプリケートする場合、VMware 用の [Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md) を使用して、日次データ変化率や必要なプロセス サーバーなどのパフォーマンス要件を特定できます。

### <a name="replication-appliance-capacity"></a>レプリケーション アプライアンスのキャパシティ

デプロイで追加のプロセス サーバーが必要かどうか判断するために、この表の値を使用できます。

- 日次変化率 (チャーン レート) が 2 TB を超える場合、追加のプロセス サーバーをデプロイします。
- 200 台を超えるマシンをレプリケートする場合、追加のレプリケーション アプライアンスをデプロイします。

**CPU** | **メモリ** | **データ キャッシュ用の空き領域** | **チャーン レート** | **レプリケーションの制限**
--- | --- | --- | --- | ---
8 vCPU (2 ソケット * 4 コア \@ 2.5 GHz) | 16 GB | 300 GB | 500 GB 以下 | 100 台未満のマシン 
12 vCPU (2 ソケット * 6 コア \@ 2.5 GHz) | 18 GB | 600 GB | 501 GB ～ 1 TB | 100 台 ～ 150 台のマシン
16 vCPU (2 ソケット * 8 コア \@ 2.5 GHz) | 32 G1 |  1 TB (テラバイト) | 1 TB ～ 2 TB | 151 台 ～ 200 台のマシン

### <a name="scale-out-process-server-sizing"></a>スケールアウト プロセス サーバーのサイズ

スケールアウト プロセス サーバーをデプロイする必要がある場合、この表を参考にして、サーバーのサイズを判断してください。

**プロセス サーバー** | **データ キャッシュ用の空き領域** | **チャーン レート** | **レプリケーションの制限**
--- | --- | --- | --- 
4 vCPU (2 ソケット * 2 コア \@ 2.5 GHz)、8 GB メモリ | 300 GB | 250 GB 以下 | 最大 85 台のマシン 
8 vCPU (2 ソケット * 4 コア \@ 2.5 GHz)、12 GB メモリ | 600 GB | 251 GB ～ 1 TB    | 86 台 ～ 150 台のマシン
12 vCPU (2 ソケット * 6 コア \@ 2.5 GHz)、24 GB メモリ | 1 TB (テラバイト) | 1 ～ 2 TB | 151 台 ～ 225 台のマシン

## <a name="control-upload-throughput"></a>クラウド アップロードのスループット


 Azure にレプリケートされる VMware トラフィックは、特定のプロセス サーバーを経由します。 プロセス サーバーとして実行されているマシンで帯域幅を調整することにより、アップロードのスループットを制限できます。 次のレジストリ キーを使用して、帯域幅に影響を与えることができます。

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM レジストリ値では、ディスクのデータ転送 (初期レプリケーションまたは差分レプリケーション) に使用されるスレッドの数を指定します。 値を大きくすると、レプリケーションに使用されるネットワーク帯域幅が増加します。 既定値は 4 です。 最大値は 32 です。 トラフィックを監視して値を最適化できます。
- さらに、プロセス サーバー コンピューターの帯域幅を次のようにして調整できます。

    1. プロセス サーバー コンピューターで、Azure Backup MMC スナップインを開きます。 デスクトップまたはフォルダー C:\Program Files\Microsoft Azure Recovery Services Agent\bin に、ショートカットがあります。 
    2. スナップインで **[プロパティの変更]** を選択します。
    3. **[調整]** で **[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** を選択します。 勤務時間用と勤務時間外用の制限値を設定します。 有効な範囲は、512 Kbps から 1,023 Mbps です。


## <a name="next-steps"></a>次の手順

Azure Migrate Server Migration を使用して、エージェントベースの [VMware VM の移行](tutorial-migrate-vmware-agent.md)を実行します。
