---
title: Azure での Oracle データベースの設計と実装 | Microsoft Docs
description: ご利用の Azure 環境で Oracle データベースを設計および実装します。
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669987"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Azure での Oracle データベースの設計と実装

## <a name="assumptions"></a>前提条件

- オンプレミスから Azure への Oracle データベースの移行を計画している
- 移行先となる Oracle Database 用の [Diagnostics Pack](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) または [Automatic Workload Repository](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) がある
- Oracle の各種メトリックを理解している
- アプリケーションのパフォーマンスとプラットフォームの使用率に関する基礎知識がある

## <a name="goals"></a>目標

- Azure で Oracle のデプロイを最適化する方法を理解する
- Azure 環境での Oracle データベースのパフォーマンスを調整するオプションを確認する
- アーキテクチャを介した物理的なチューニングの限界、データベース コード (SQL) の利点または論理的なチューニング、およびデータベース全体の設計を踏まえて明確な予測を立てる

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>オンプレミスと Azure 実装の違い 

オンプレミス アプリケーションを Azure に移行するときに注意が必要な点を説明します。 

重要な違いの 1 つは、Azure 実装では、VM、ディスク、仮想ネットワークなどのリソースが他のクライアントとの間で共有される点です。 また、要件に基づいてリソースをスロットルできます。 Azure では、失敗の回避 (MTBF) に焦点を当てる代わりに、失敗の克服 (MTTR) に焦点を当てています。

オンプレミスの実装と Oracle データベースの Azure 実装の違いの一部を次の表に示します。


|  | オンプレミスの実装 | Azure 実装 |
| --- | --- | --- |
| **ネットワーク** |LAN/WAN  |SDN (ソフトウェアによるネットワーク)|
| **セキュリティ グループ** |IP/ポートの制限ツール |[ネットワーク セキュリティ グループ (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **回復力** |MTBF (平均故障間隔) |MTTR (平均復旧時間)|
| **定期的なメンテナンス** |修正/更新プログラム|[可用性セット](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (修正/更新プログラムは Azure によって管理) |
| **リソース** |専用  |他のクライアントと共有|
| **リージョン** |データ センター |[リージョンのペア](../../regions.md#region-pairs)|
| **Storage** |記憶域ネットワーク/物理ディスク |[Azure 管理のストレージ](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **スケール** |垂直スケール |水平スケール|


### <a name="requirements"></a>必要条件

- 実際の CPU 使用率を確認します。Oracle はコアによってライセンス供与されるため、vCPU で必要とされるサイズの設定は、コストを削減するための重要な作業である場合があります。 
- データベース サイズ、バックアップ ストレージ、増加率を確認します。
- IO 要件を確認します。これは、Oracle Statspack と AWR レポートに基づいて、または OS レベルのストレージ監視ツールによって見積もることができます。

## <a name="configuration-options"></a>構成オプション

Azure 環境でのパフォーマンスを向上させるために調整できる領域は 4 つあります。

- 仮想マシンのサイズ
- ネットワーク スループット
- ディスクの種類と構成
- ディスク キャッシュの設定

### <a name="generate-an-awr-report"></a>AWR レポートの生成

Azure への移行を予定している既存の Oracle Enterprise Edition データベースがある場合は、選択肢がいくつかあります。 Oracle インスタンス用の[診断パック](https://www.oracle.com/technetwork/oem/pdf/511880.pdf)がある場合は、Oracle AWR レポートを実行して、メトリック (IOPS、Mbps、GiB など) を取得することができます。 Diagnostics Pack ライセンスのないデータベースまたは Standard Edition データベースの場合は、手動スナップショットが収集された後に Statspack レポートを使用して同じ重要なメトリックを収集できます。  この 2 つのレポート方法の主な違いは、AWR は自動的に収集され、Statspack の前のレポート オプションよりもデータベースに関して詳しい情報を提供することです。

比較できるように、通常のワークロード時とピークのワークロード時の両方で AWR レポートを実行することを検討してください。 より正確なワークロードを収集するには、24 時間の実行ではなく、1 週間の拡張ウィンドウ レポートを検討し、AWR ではレポート内の計算の一部として平均値が提供されることを認識してください。  データセンターの移行の場合は、運用システムでのサイズ設定のためにレポートを収集し、ユーザー テスト、テスト、開発などに使用される残りのデータベース コピーをパーセンテージで見積もることをお勧めします (UAT は運用環境と同程度、テストと開発は運用環境のサイズ設定の 50% など)。

既定では、AWR リポジトリでは 8 日分のデータが保持され、1 時間間隔でスナップショットが取得されます。  コマンド ラインから AWR レポートを実行するには、ターミナルから次を実行します。

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>主要なメトリック

レポートでは、次の情報の入力が求められます。
- レポートの種類:HTML またはテキスト (12.1 の HTML では、テキスト形式よりも詳細な情報が提供されます。)
- 表示するスナップショットの日数 (1 時間間隔の場合、1 週間分のレポートは、スナップショット ID が 168 個の異なるものになります)
- レポート ウィンドウの開始 SnapshotID。
- レポート ウィンドウの終了 SnapshotID。
- AWR スクリプトによって作成されるレポートの名前。

Real Application Cluster (RAC) で AWR を実行する場合、コマンド ラインのレポートは awrrpt.sql ではなく awrgrpt.sql です。  "g" レポートでは、RAC データベース内のすべてのノードについてのレポートが 1 つのレポートで作成され、各 RAC ノードでそれぞれ実行する場合とは異なります。

AWR レポートから取得できるメトリックを次に示します。

- データベース名、インスタンス名、およびホスト名
- データベース バージョン (Oracle によるサポートの可否)
- CPU またはコア
- SGA または PGA (および、サイズが不足している場合に通知するアドバイザー)
- メモリの合計 (GB)
- CPU % ビジー
- DB の CPU
- IOPS (読み取り/書き込み)
- Mbps (読み取り/書き込み)
- ネットワーク スループット
- ネットワーク待機時間の割合 (高/低)
- 上位の待機イベント 
- データベースのパラメーター設定
- データベースが RAC であるか、Exadata であるか、高度な機能または構成を使用しているか

### <a name="virtual-machine-size"></a>仮想マシンのサイズ

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1.AWR レポートの CPU、メモリ、I/O 使用率に基づき、VM のサイズを見積もる

確認することとしては、システムのボトルネックの場所を示す、合計待機時間が長かった上位 5 つのフォアグラウンドのイベントが挙げられます。

たとえば、次の図では、ログ ファイルの同期が最上位にあります。 これは、LGWR から REDO ログ ファイルにログ バッファーを書き込むまでに必要な待機回数を示しています。 これらの結果は、パフォーマンスの高いストレージまたはディスクが必要なことを示します。 また、この図には、CPU (コア) の数とメモリの量も表示されています。

![表の上部にあるログ ファイルの同期を示すスクリーンショット。](./media/oracle-design/cpu_memory_info.png)

次の図は、読み取りと書き込みの I/O の合計数を示しています。 レポート期間中に、59 GB の読み取りと 247.3 GB の書き込みが行われました。

![読み取りと書き込みの合計 I/O を示すスクリーンショット。](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2.VM の選択

AWR レポートから収集した情報に基づき、次のステップでは要件を満たすサイズの VM を選択します。 [メモリの最適化](../../sizes-memory.md)に関する記事で、使用可能な仮想マシンの一覧を見つけることができます。

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3.ACU に基づき、同様の VM シリーズで VM のサイズを細かく調整する

VM を選択した後に、仮想マシンの ACU に注意を向けてください。 お客様の要件に合うように、ACU の値に基づいて別の VM を選択することもできます。 詳細については、「[Azure コンピューティング ユニット](../../acu.md)」をご覧ください。

![ACU ユニット ページのスクリーン ショット](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>ネットワーク スループット

次の図は、スループットと IOPS の関係を示します。

![スループットのスクリーンショット](./media/oracle-design/throughput.png)

ネットワーク スループットの合計は、次の情報に基づいて見積もられます。
- SQL*Net トラフィック
- Mbps × サーバー数 (Oracle Data Guard などの送信ストリーム)
- その他の要因 (アプリケーションのレプリケーションなど)

![SQL*Net スループットのスクリーンショット](./media/oracle-design/sqlnet_info.png)

ご利用のネットワーク帯域幅の要件に基づいて、多彩な種類からゲートウェイを選択できます。 Basic、VpnGw、および Azure ExpressRoute などもその対象に含まれます。 詳細については、「[VPN Gateway の価格](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)」のページを参照してください。

**Recommendations (推奨事項)**

- オンプレミスのデプロイと比べて、ネットワーク待機時間が比較的長くなります。 ネットワークのラウンド トリップ数を削減すると、パフォーマンスが大幅に向上します。
- ラウンドトリップ数を削減するには、同じ仮想マシン上のトランザクション数が多いアプリケーション、つまり "おしゃべりな" アプリを統合します。
- ネットワーク パフォーマンスを向上させるには、[高速ネットワーク](../../../virtual-network/create-vm-accelerated-networking-cli.md)で仮想マシンを使用してください。
- 特定の Linux ディストリビューションについては、[TRIM/UNMAP サポート](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support)を有効にすることを検討してください。
- 個別の仮想マシン上に [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) をインストールします。
- Linux では、既定では大型のページは有効になっていません。 大型のページを有効にすることを検討し、Oracle DB で `use_large_pages = ONLY` を設定します。 これは、パフォーマンスの向上に役立ちます。 詳細については、 [こちら](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)で確認できます。

### <a name="disk-types-and-configurations"></a>ディスクの種類と構成

- *既定の OS ディスク*:このディスクの種類は、永続データとキャッシュを提供します。 これらのディスクは起動時の OS アクセス用に最適化されており、トランザクション ワークロードやデータ ウェアハウス (分析) のワークロード向けには設計されていません。

- *マネージド ディスク*:Azure により、VM ディスクに使用するストレージ アカウントが管理されます。 ディスクの種類 (多くの場合、Oracle ワークロードには Premium SSD) と必要なディスクのサイズを指定します。 Azure により自動的にディスクが作成、管理されます。  Premium SSD マネージド ディスクは、メモリ最適化および特別に設計された VM シリーズでのみ使用できます。 特定の VM サイズを選択した後のメニューには、その VM サイズに基づいて使用可能な Premium Storage の SKU のみが表示されます。

![マネージド ディスク ページのスクリーンショット](./media/oracle-design/premium_disk01.png)

VM でストレージを構成した後に、データベースを作成する前にディスクのロード テストを行うことができます。 待機時間とスループットの観点から I/O 率を知ることは、仮想マシンが待機時間の目標値を達成し、期待されるスループットをサポートしているかを把握するのに役立ちます。

アプリケーションのロード テスト用ツールは、多数提供されています (Oracle Orion、Sysbench、SLOB、Fio など)。

Oracle データベースをデプロイした後に、再度ロード テストを実行します。 通常とピーク時のワークロードを開始すると、結果には、お使いの環境内のベースラインが表示されます。  現実的なワークロード テストを行ってください。実際に VM で実行する予定の内容と大きく異なっているワークロードを実行しても意味がありません。

Oracle は多くの場合 IO 集中型データベースであるため、ストレージ サイズではなく IOPS レートに基づいてストレージのサイズを設定することが非常に重要です。 たとえば、必要な IOPS が 5,000 でも、サイズは 200 GB しか必要としないとします。この場合、提供されるストレージ サイズが 200 GB を超えますが、それでも P30 クラスの Premium ディスクを利用します。

IOPS の速度は、AWR レポートから取得することができます。 IOPS の速度は、REDO ログ、物理読み取りと書き込みの速度によって決定されます。  選択した VM シリーズがワークロードの IO 要求を処理できることも必ず確認してください。  VM の IO 制限がストレージよりも低い場合は、VM によって制限最大値が設定されます。

![AWR レポート ページのスクリーンショット](./media/oracle-design/awr_report.png)

例: REDO のサイズは 12,200,000 バイト/秒で、これは 11.63 Mbps と等しく、
IOPS は 12,200,000 ÷ 2,358 = 5,174 になります。

I/O 要件を明確に把握した後に、これらの要件に最適なドライブの組み合わせを選択できます。

**Recommendations (推奨事項)**

- DATA 表領域は、管理ストレージまたは Oracle ASM を使用して、ディスク全体に I/O ワークロードを分散させます。
- I/O 削減のために Oracle の高度な圧縮を使用します (データとインデックスの両方)。
- REDO ログ、TEMP および UNDO 表領域は、それぞれ別のデータ ディスクに分離します。
- アプリケーション ファイルを既定の OS ディスク (/dev/sda) に保存しないでください。 これらのディスクは VM の高速起動用に最適化されていないため、アプリケーションに適切なパフォーマンスが提供されない可能性があります。
- Premium ストレージで M シリーズの VM を使用する場合は、再実行ログ ディスクで[書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)を有効にします。
- 待機時間が長い REDO ログを Ultra ディスクに移動することを検討します。

### <a name="disk-cache-settings"></a>ディスク キャッシュの設定

ホスト キャッシュには 3 つのオプションがありますが、Oracle データベースの場合は、データベース ワークロードに対して ReadOnly キャッシュのみをお勧めします。  ReadWrite を使用すると、データファイルに重大な脆弱性が生じるおそれがあります。データベース書き込みの目的は、情報をキャッシュすることではなく、データファイルに記録することです。

データベースの場合、ファイル システムやアプリケーションと異なり、ホスト キャッシュの推奨は *ReadOnly* です。今後の読み取りのためにすべての要求をキャッシュします。 すべての書き込みは、引き続きディスクに書き込まれます。

**Recommendations (推奨事項)**

スループットを最大にするには、ホスト キャッシュを可能な限り **ReadOnly** で開始することをお勧めします。 Premium Storage では、**ReadOnly** オプションを使用してファイル システムをマウントするときに、"バリア" を無効にする必要があることに注意してください。 UUID を使用して、/etc/fstab ファイルをディスクに更新します。

![[読み取り専用] と [なし] のオプションが表示されたマネージド ディスク ページのスクリーンショット。](./media/oracle-design/premium_disk02.png)

- OS ディスクの場合は、既定の **Read/Write** キャッシュを使用し、Oracle ワークロード VM には Premium SSD を使用します。  また、スワップに使用するボリュームも Premium SSD 上に存在するようにしてください。
- データファイルの場合はすべて、キャッシュに **ReadOnly** を使用します。 ReadOnly キャッシュは、Premium マネージド ディスク P30 以上でのみ使用できます。  ReadOnly キャッシュで使用できるボリュームには、4095 GiB の制限があります。  割り当てがそれより大きくなると、既定ではホスト キャッシュが無効になります。

ワークロードが日中と夜間で大きく異なり、IO ワークロードでそれをサポートできる場合は、バーストを備えた P1-P20 Premium SSD により、夜間のバッチ読み込みや限られた IO 要求の間に必要とされるパフォーマンスが得られる可能性があります。  

## <a name="security"></a>セキュリティ

Azure 環境のセットアップと構成が完了した後に、今度はネットワークをセキュリティ保護します。 以下に、推奨事項をいくつか示します。

- *NSG ポリシー*:NSG はサブネットまたは NIC で定義できます。 アプリケーション ファイアウォールなどのセキュリティと強制ルーティングの両方について、サブネット レベルではアクセスをより簡単に制御できます。

- *ジャンプボックス*: アクセスの安全性を高めるために、管理者がアプリケーション サービスやデータベースに直接接続することは推奨されません。 管理者のコンピューターと Azure リソース間の媒介役として、Jumpbox を使用します。
![Jumpbox トポロジ ページのスクリーンショット](./media/oracle-design/jumpbox.png)

    管理者のコンピューターでは、Jumpbox にしかアクセスできないように、IP 制限をかける必要があります。 Jumpbox では、アプリケーションとデータベースにアクセスできる必要があります。

- *プライベート ネットワーク* (サブネット):NSG ポリシーできめ細かく制御を設定できるように、アプリケーション サービスとデータベースを別個のサブネット上に配置することお勧めします。


## <a name="additional-reading"></a>その他の情報

- [Oracle ASM の構成](configure-oracle-asm.md)
- [Oracle Data Guard の構成](configure-oracle-dataguard.md)
- [Oracle Golden Gate の構成](configure-oracle-golden-gate.md)
- [Oracle のバックアップと回復](./oracle-overview.md)

## <a name="next-steps"></a>次のステップ

- [チュートリアル:高可用性 VM の作成](../../linux/create-cli-complete.md)
- [VM デプロイ Azure CLI サンプルを探索する](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
