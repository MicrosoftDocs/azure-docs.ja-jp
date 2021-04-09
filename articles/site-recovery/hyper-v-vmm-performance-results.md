---
title: Azure Site Recovery を使用した VMM によるセカンダリ サイトへの Hyper-V VM レプリケーションをテストする
description: この記事では、Azure Site Recovery を使用して VMM クラウド内の Hyper-V VM をセカンダリ サイトにレプリケートする場合のパフォーマンス テストについて説明します。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 6c8219214e7053dcf6b119f6cd5dc97daaa355f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92327639"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>セカンダリ サイトへの Hyper-V レプリケーションのテスト結果


この記事では、System Center Virtual Machine Manager (VMM) クラウドの Hyper-V VM をセカンダリ データセンターにレプリケートする場合のパフォーマンス テストの結果について説明します。

## <a name="test-goals"></a>テストの目的

テストの目的は、安定的にレプリケーションが実行されている間の Site Recovery のパフォーマンスを確認することです。

- 安定状態のレプリケーションは、VM が初期レプリケーションを完了し、差分変更を同期しているときに発生します。
- 予期しない停止が発生しない限り、ほとんどの VM は安定状態になるため、安定状態を利用してパフォーマンスを測定することが重要です。
- テスト デプロイは、各サイトに VMM サーバーをデプロイした 2 つのオンプレミスのサイトで構成しました。 このテスト デプロイは、プライマリ サイトとして機能する本店と、セカンダリまたは復旧サイトとして機能するブランチ オフィスで構成される、本店/ブランチ オフィス デプロイの一般的な例です。

## <a name="what-we-did"></a>実行した内容

テスト パスで実行した内容を次に示します。

1. VMM テンプレートを使用して作成された VM。
2. VM を起動し、12 時間にわたって基準のパフォーマンス メトリックを取得しました。
3. プライマリ VMM サーバーおよび復旧 VMM サーバー上にクラウドを作成しました。
4. Site Recovery で、ソース クラウドとリカバリ クラウド間のマッピングを含むレプリケーションを構成しました。
5. VM の保護を有効にして、初期レプリケーションを完了できるようにしました。
6. システムが安定するまで数時間待機しました。
7. 12 時間にわたってパフォーマンス メトリックを取得し、すべての VM がこの 12 時間に、期待されたレプリケーションの状態を維持していることを確認しました。
8. 基準となるパフォーマンス メトリックとレプリケーションのパフォーマンス メトリックの差を測定しました。


## <a name="primary-server-performance"></a>プライマリ サーバーのパフォーマンス

* (Site Recovery に使用される) Hyper-V レプリカは、プライマリ サーバーでのストレージ オーバーヘッドを最小限にしながら、ログ ファイルへの変更を非同期的に追跡します。
* Hyper-V レプリカは、自己保持型のメモリ キャッシュを活用し、追跡に対する IOPS オーバーヘッドを最小化します。 復旧サイトにログを送信する時刻になる前に、メモリ内に VHDX への書き込みを格納し、ログ ファイルにフラッシュします。 あらかじめ設定された制限に書き込みが達した場合も、ディスク フラッシュが発生します。
* 次のグラフは、レプリケーションに対する安定状態の IOPS オーバーヘッドを示しています。 レプリケーションによる IOPS オーバーヘッドは約 5% と、非常に低いことがわかります。

  ![グラフは、レプリケーションに対する安定状態の IOPS オーバーヘッドを示しています。](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V レプリカは、ディスク パフォーマンスを最適化するために、プライマリ サーバー上のメモリを利用します。 次のグラフに示すように、プライマリ クラスター内にあるすべてのサーバー上のメモリ オーバーヘッドはわずかです。 示されているメモリ オーバーヘッドは、Hyper-V サーバーに装着されたメモリの合計と比較した、レプリケーションによって使用されるメモリの割合です。

![プライマリの結果](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V レプリカの CPU オーバーヘッドは最小限です。 グラフに示すように、レプリケーションのオーバーヘッドは 2 ～ 3% の範囲内です。

![グラフはレプリケーションのオーバーヘッドが 2 から 3% の範囲内にあることを示しています。](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>セカンダリ サーバーのパフォーマンス

Hyper-V レプリカが使用する復旧サーバーのメモリは少なく、ストレージ操作の数を最適化できます。 グラフは、復旧サーバーのメモリ使用量をまとめたものです。 示されているメモリ オーバーヘッドは、Hyper-V サーバーに装着されたメモリの合計と比較した、レプリケーションによって使用されるメモリの割合です。

![グラフは、復旧サーバーのメモリ使用量をまとめたものです。](./media/hyper-v-vmm-performance-results/IC744916.png)

復旧サイトでの I/O 操作数は、プライマリ サイトでの書き込み操作の数と相関関係があります。 復旧サイトでの合計 I/O 操作数を、プライマリ サイトでの合計 I/O 操作数および書き込み操作数と比較してみましょう。 グラフでは、復旧サイトでの合計 IOPS は次のとおりになっています。

* プライマリ サイトでの書き込み IOPS の約 1.5 倍
* プライマリ サイトでの合計 IOPS の約 37%

![グラフは、プライマリとセカンダリのサイトでの IOPS の比較を示します。](./media/hyper-v-vmm-performance-results/IC744917.png)

![セカンダリの結果](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>ネットワーク使用率への影響

プライマリ ノードおよび復旧ノード間で、既存の 1 秒あたり 5 Gb の帯域幅に対して、1 秒あたり平均 275 Mb のネットワーク帯域幅が使用されました (圧縮有効時)。

![結果のネットワーク使用率](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>VM パフォーマンスへの影響

重要な考慮事項として、仮想マシンで実行されている運用ワークロードに対するレプリケーションの影響が挙げられます。 プライマリ サイトがレプリケーション用に十分にプロビジョニングされている場合は、これらのワークロードに影響はありません。 Hyper-V レプリカの軽量な追跡メカニズムにより、安定状態のレプリケーション中に、仮想マシンで実行されているワークロードは影響を受けません。 このことを、次のグラフで示します。

このグラフは、レプリケーションを有効にする前と後で、さまざまなワークロードを実行する仮想マシンが実行した IOPS を示しています。 2 つの間に差がないことを確認できます。

![レプリカ効果の結果](./media/hyper-v-vmm-performance-results/IC744920.png)

次のグラフは、レプリケーションを有効にする前と後で、さまざまなワークロードを実行する仮想マシンのスループットを示しています。 レプリケーションが大きな影響を与えないことを確認できます。

![結果のレプリカ効果](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>まとめ

結果では、Hyper-V レプリカと組み合わせた Site Recovery には、大規模なクラスター構成でオーバーヘッドを最小限にする優れた拡張性があることが明確に示されました。 Site Recovery は、シンプルなデプロイ、レプリケーション、管理および監視を実現します。 Hyper-V レプリカは、レプリケーションを正常にスケーリングするために必要なインフラストラクチャを提供します。 

## <a name="test-environment-details"></a>テスト環境の詳細

### <a name="primary-site"></a>プライマリ サイト

* プライマリ サイトは、470 台の仮想マシンを実行している 5 つの Hyper-V サーバーを含むクラスターです。
* VM は、さまざまなワークロードを実行し、すべて Site Recovery 保護を有効にしています。
* クラスター ノード用のストレージは、iSCSI SAN により提供されます。 モデル – Hitachi HUS130。
* 各クラスター サーバーは、1 Gbps の 4 つのネットワーク カード (NIC) を搭載しています。
* 2 つのネットワーク カードは、iSCSI プライベート ネットワークに接続され、2 つは外部のエンタープライズ ネットワークに接続されています。 外部ネットワークのうち 1 つは、クラスター通信専用として予約されています。

![プライマリのハードウェア要件](./media/hyper-v-vmm-performance-results/IC744922.png)

| サーバー | RAM | モデル | プロセッサ | プロセッサの数 | NIC | ソフトウェア |
| --- | --- | --- | --- | --- | --- | --- |
| クラスター内の Hyper-V サーバー: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB HOST25 に 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@(2.20 GHz) |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V ロール |
| VMM サーバー |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>セカンダリ サイト

* セカンダリ サイトは 6 ノードのフェールオーバー クラスターです。
* クラスター ノード用のストレージは、iSCSI SAN により提供されます。 モデル – Hitachi HUS130。

![プライマリのハードウェア仕様](./media/hyper-v-vmm-performance-results/IC744923.png)

| サーバー | RAM | モデル | プロセッサ | プロセッサの数 | NIC | ソフトウェア |
| --- | --- | --- | --- | --- | --- | --- |
| クラスター内の Hyper-V サーバー: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@(2.30 GHz) |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V ロール |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@(2.20 GHz) |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V ロール |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@(2.20 GHz) |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V ロール |
| VMM サーバー |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>サーバー ワークロード

* テストのために、企業の顧客のシナリオでよく使用されるワークロードを選択しました。
* シミュレーションの表にまとめたワークロードの特性を利用して、 [IOMeter](http://www.iometer.org) を使用しました。
* IOMeter のすべてのプロファイルは、ランダムにバイトを書き込むよう設定して、ワークロードに対して、最悪の場合の書き込みパターンをシミュレートしました。

| ワークロード | I/O サイズ (KB) | アクセスの割合 | 読み取りの割合 | 処理待ち I/O 数 | I/O パターン |
| --- | --- | --- | --- | --- | --- |
| ファイル サーバー |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |すべて 100% ランダム |
| SQL Server (ボリューム 1)<br />SQL Server (ボリューム 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% ランダム<br />100% シーケンシャル |
| Exchange |32 |100% |67% |8 |100% ランダム |
| ワークステーション/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |どちらも 100% ランダム |
| Web ファイル サーバー |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |すべて 75% ランダム |

### <a name="vm-configuration"></a>VM 構成

* プライマリ クラスターに 470 台の VM。
* すべての VM に VHDX ディスクを搭載。
* VM では、次の表に要約したワークロードを実行。 すべて VMM テンプレートを使用して作成されました。

| ワークロード | VM 数 | 最小 RAM 容量 (GB) | 最大 RAM 容量 (GB) | VM あたりの論理ディスク サイズ (GB) | 最大 IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| ファイル サーバー |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web サーバー |149 |.5 |1 |80 |6 |
| 合計 |470 | | |96.83 TB (テラバイト) |4108 |

### <a name="site-recovery-settings"></a>Site Recovery 設定

* Site Recovery をオンプレミス間の保護用に構成しました。
* VMM サーバーには、Hyper-V クラスター サーバーとその VM を含むクラウドが 4 つ構成されています。

| プライマリ VMM クラウド | 保護対象の VM | レプリケーションの頻度 | 追加の復旧ポイント |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 分 |なし |
| PrimaryCloudRpo30s |47 |30 秒 |なし |
| PrimaryCloudRpo30sArp1 |47 |30 秒 |1 |
| PrimaryCloudRpo5m |235 |5 分 |なし |

### <a name="performance-metrics"></a>パフォーマンス メトリック

この表では、このデプロイにおいて測定されたパフォーマンス メトリックとカウンターの概要を示します。

| メトリック | カウンター |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| 使用可能なメモリ |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM 読み取り (IOPS) 操作数/秒 |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM 書き込み (IOPS) 操作数/秒 |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM 読み取りスループット |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM 書き込みスループット |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## <a name="next-steps"></a>次のステップ

[レプリケーションの設定](hyper-v-vmm-disaster-recovery.md)
