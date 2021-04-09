---
title: Azure Migrate での Hyper-V の移行のしくみについて
description: Azure Migrate での Hyper-V の移行について説明します。
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751021"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-V のレプリケーションのしくみ

この記事では、Azure Migrate Server Migration ツールを使用して Hyper-V VM を移行する場合に使用されるアーキテクチャとプロセスの概要を示します。

[Azure Migrate](migrate-services-overview.md) は、オンプレミスのアプリとワークロード、およびプライベート/パブリック クラウド VM の検出、評価、および Azure への移行を追跡するための中央ハブとなります。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。

## <a name="agentless-migration"></a>エージェントレス型移行

Azure Migrate Server Migration ツールでは、Hyper-V 用に最適化された移行ワークフローを使用して、オンプレミスの Hyper-V VM にエージェントレス レプリケーションが提供されます。 ソフトウェア エージェントは、Hyper-V ホストまたはクラスター ノードにのみインストールします。 Hyper-V VM には何もインストールする必要はありません。

## <a name="server-migration-and-azure-site-recovery"></a>Server Migration と Azure Site Recovery

Azure Migrate Server Migration は、オンプレミスのワークロードとクラウドベースの VM を Azure に移行するためのツールです。 Site Recovery は、ディザスター リカバリー ツールです。 このツールでは、データ レプリケーションのために共通のテクノロジ コンポーネントが一部使用されますが、それぞれの目的は異なります。 


## <a name="architectural-components"></a>アーキテクチャ コンポーネント

![図に、Microsoft Azure への HTTPS データ チャネルがある元の Hyper-V ネットワークと、表内の詳細な説明が示されています。](./media/hyper-v-replication-architecture/architecture.png)



**コンポーネント** | **デプロイ** | 
--- | --- 
**レプリケーション プロバイダー** | Microsoft Azure Site Recovery プロバイダーが Hyper-V ホストにインストールされ、Azure Migration Server Migration に登録されます。<br/> プロバイダーによって、Hyper-V VM のレプリケーションが調整されます。
**Recovery Services エージェント** | Microsoft Azure Recovery Service エージェントによってデータ レプリケーションが処理されます。 これがプロバイダーと連携して、Hyper-V VM から Azure にデータがレプリケートされます。<br/> レプリケートされたデータは、Azure サブスクリプション内のストレージ アカウントにアップロードされます。 Server Migration ツールでは、レプリケートされたデータが処理され、そのデータがサブスクリプション内のレプリカ ディスクに適用されます。 レプリカ ディスクは、移行時に Azure VM を作成するために使用されます。

- コンポーネントは、ポータル内で Azure Migrate Server Migration からダウンロードされた 1 つのセットアップ ファイルによってインストールされます。
- プロバイダーとアプライアンスでは、Azure Migrate Server Migration との通信に送信 HTTPS ポート 443 接続が使用されます。
- プロバイダーとエージェントからの通信は、セキュリティで保護され、暗号化されます。


## <a name="replication-process"></a>レプリケーション プロセス

1. Hyper-V VM のレプリケーションを有効にすると、初回のレプリケーションが開始されます。
2. Hyper-V VM スナップショットが作成されます。
3. VM 上の VHD は、すべてが Azure にコピーされるまで、1 つずつレプリケートされます。 初期レプリケーションの時間は、VM のサイズとネットワーク帯域幅によって異なります。
4. 初期レプリケーション中に発生したディスクの変更は、Hyper-V レプリカを使用して追跡され、ログ ファイル (hrl ファイル) に格納されます。
    - ログ ファイルは、ディスクと同じフォルダーにあります。
    - 各ディスクには関連付けられた hrl ファイルが存在し、これらはセカンダリ ストレージに送信されます。
    - 初期レプリケーションの進行中は、スナップショットおよびログ ファイルによってディスク リソースが消費されます。
4. 初期レプリケーションが完了すると、VM スナップショットは削除され、デルタ レプリケーションが開始されます。
5. 増分ディスクの変更は、hrl ファイル内で追跡されます。 レプリケーション ログは、Recovery Services エージェントによって Azure ストレージ アカウントに定期的にアップロードされます。


## <a name="performance-and-scaling"></a>パフォーマンスとスケーリング

Hyper-V のレプリケーション パフォーマンスは、VM サイズ、VM のデータ変更率 (チャーン)、ログ ファイル ストレージ用の Hyper-V ホスト上の使用可能領域、レプリケーション データのアップロード帯域幅、Azure 内のターゲット ストレージなどの要因の影響を受けます。

- 複数のマシンを同時にレプリケートする場合は、Hyper-V 用の [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) を使用して、レプリケーションを最適化します。
- 容量に従って、Hyper-V レプリケーションを計画し、各 Azure ストレージ アカウントにレプリケーションを分散します。

### <a name="control-upload-throughput"></a>クラウド アップロードのスループット

各 Hyper-V ホスト上の Azure にデータをアップロードするために使用される帯域幅の量を制限できます。 ご注意ください。 設定した値が小さすぎると、レプリケーションに悪影響を及ぼし、移行が遅れます。


1. Hyper-V ホストまたはクラスター ノードにサインインします。
2. **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc** を実行して、Windows Azure Backup MMC スナップインを開きます。
3. スナップインで **[プロパティの変更]** を選択します。
4. **[調整]** で **[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** を選択します。 勤務時間用と勤務時間外用の制限値を設定します。 有効な範囲は、512 Kbps から 1,023 Mbps です。
I

### <a name="influence-upload-efficiency"></a>アップロード効率への影響

レプリケーション用の予備帯域幅があり、アップロードを増やす場合、次のように、アップロード タスクに割り当てられるスレッドの数を増やすことができます。

1. Regedit でレジストリを開きます。
2. キー HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM に移動します
3. レプリケートする VM ごとに、データ アップロードに使用されるスレッド数の値を増やします。 既定値は 4 で、最大値は 32 です。 




## <a name="next-steps"></a>次のステップ

Azure Migrate Server Migration を使用して [Hyper-V の移行](tutorial-migrate-hyper-v.md)を試します。
