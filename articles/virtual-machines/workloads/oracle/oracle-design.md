---
title: Azure での Oracle データベースの設計と実装 | Microsoft Docs
description: ご利用の Azure 環境で Oracle データベースを設計および実装します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: ebe6f27818df8407504e4254f16d952aa298b6cc
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348325"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Azure での Oracle データベースの設計と実装

## <a name="assumptions"></a>前提条件

- オンプレミスから Azure への Oracle データベースの移行を計画している
- 移行先となる Oracle Database 用の[診断パック](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm)がある
- Oracle AWR レポートの各種メトリックを理解している
- アプリケーションのパフォーマンスとプラットフォームの使用率に関する基礎知識がある

## <a name="goals"></a>目標

- Azure で Oracle のデプロイを最適化する方法を理解する
- Azure 環境での Oracle データベースのパフォーマンスを調整するオプションを確認する

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>オンプレミスと Azure 実装の違い 

オンプレミス アプリケーションを Azure に移行するときに注意が必要な点を説明します。 

重要な違いの 1 つは、Azure 実装では、VM、ディスク、仮想ネットワークなどのリソースが他のクライアントとの間で共有される点です。 また、要件に基づいてリソースをスロットルできます。 Azure では、失敗の回避 (MTBF) に焦点を当てる代わりに、失敗の克服 (MTTR) に焦点を当てています。

オンプレミスの実装と Oracle データベースの Azure 実装の違いの一部を次の表に示します。

> 
> |  | **オンプレミスの実装** | **Azure 実装** |
> | --- | --- | --- |
> | **ネットワーク** |LAN/WAN  |SDN (ソフトウェアによるネットワーク)|
> | **セキュリティ グループ** |IP/ポートの制限ツール |[ネットワーク セキュリティ グループ (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **回復力** |MTBF (平均故障間隔) |MTTR (平均復旧時間)|
> | **定期的なメンテナンス** |修正/更新プログラム|[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (修正/更新プログラムは Azure によって管理) |
> | **リソース** |専用  |他のクライアントと共有|
> | **リージョン** |データ センター |[リージョンのペア](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Storage** |記憶域ネットワーク/物理ディスク |[Azure 管理のストレージ](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **スケール** |垂直スケール |水平スケール|


### <a name="requirements"></a>必要条件

- データベース サイズと増加率を決定します。
- IOPS の要件を決定します。これは、Oracle AWR レポートや他のネットワーク監視ツールに基づいて見積もることができます。

## <a name="configuration-options"></a>構成オプション

Azure 環境でのパフォーマンスを向上させるために調整できる領域は 4 つあります。

- 仮想マシンのサイズ
- ネットワーク スループット
- ディスクの種類と構成
- ディスク キャッシュの設定

### <a name="generate-an-awr-report"></a>AWR レポートの生成

既存の Oracle データベースがあるとき、Azure への移行を予定している場合は、選択肢がいくつかあります。 Oracle インスタンス用の[診断パック](https://www.oracle.com/technetwork/oem/pdf/511880.pdf)がある場合は、Oracle AWR レポートを実行して、メトリック (IOPS、Mbps、GiB など) を取得することができます。 その後、収集したメトリックに基づいて、VM を選択します。 または、自社のインフラストラクチャ チームに問い合わせて、同様の情報を取得することもできます。

比較できるように、通常のワークロード時とピークのワークロード時の両方で AWR レポートを実行することを検討してください。 これらのレポートを参考に、平均のワークロードや最大のワークロードに基づいて、仮想マシンのサイズを決めることができます。

AWR レポートを生成する方法の例を次に示します (現在のインストールに Oracle Enterprise Manager がある場合は、それを使用して AWR レポートを生成します)。

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>主要なメトリック

AWR レポートから取得できるメトリックを次に示します。

- コアの合計数
- CPU のクロック速度
- メモリの合計 (GB)
- CPU 使用率
- ピーク時のデータ転送速度
- I/O 率の変化 (読み取り/書き込み)
- REDO ログの速度 (Mbps)
- ネットワーク スループット
- ネットワーク待機時間の割合 (高/低)
- データベース サイズ (GB)
- SQL*Net を介してクライアントとの間で送受信されたバイト数

### <a name="virtual-machine-size"></a>仮想マシンのサイズ

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1.AWR レポートの CPU、メモリ、I/O 使用率に基づき、VM のサイズを見積もる

確認することとしては、システムのボトルネックの場所を示す、合計待機時間が長かった上位 5 つのフォアグラウンドのイベントが挙げられます。

たとえば、次の図では、ログ ファイルの同期が最上位にあります。 これは、LGWR から REDO ログ ファイルにログ バッファーを書き込むまでに必要な待機回数を示しています。 これらの結果は、パフォーマンスの高いストレージまたはディスクが必要なことを示します。 また、この図には、CPU (コア) の数とメモリの量も表示されています。

![AWR レポート ページのスクリーンショット](./media/oracle-design/cpu_memory_info.png)

次の図は、読み取りと書き込みの I/O の合計数を示しています。 レポート期間中に、59 GB の読み取りと 247.3 GB の書き込みが行われました。

![AWR レポート ページのスクリーンショット](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2.VM の選択

AWR レポートから収集した情報に基づき、次のステップでは要件を満たすサイズの VM を選択します。 [メモリの最適化](../../linux/sizes-memory.md)に関する記事で、使用可能な仮想マシンの一覧を見つけることができます。

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>手順 3.ACU に基づき、同様の VM シリーズで VM のサイズを細かく調整する

VM を選択した後に、仮想マシンの ACU に注意を向けてください。 お客様の要件に合うように、ACU の値に基づいて別の VM を選択することもできます。 詳細については、「[Azure コンピューティング ユニット](https://docs.microsoft.com/azure/virtual-machines/windows/acu)」をご覧ください。

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
- ネットワーク パフォーマンスを向上させるには、[高速ネットワーク](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)で仮想マシンを使用してください。
- 特定の Linux ディストリビューションについては、[TRIM/UNMAP サポート](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support)を有効にすることを検討してください。
- 個別の仮想マシン上に [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) をインストールします。
- Linux では、既定では大型のページは有効になっていません。 大型のページを有効にすることを検討し、Oracle DB で `use_large_pages = ONLY` を設定します。 これは、パフォーマンスの向上に役立ちます。 詳細については、 [こちら](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)で確認できます。

### <a name="disk-types-and-configurations"></a>ディスクの種類と構成

- *既定の OS ディスク*:このディスクの種類は、永続データとキャッシュを提供します。 これらのディスクは起動時の OS アクセス用に最適化されており、トランザクション ワークロードやデータ ウェアハウス (分析) のワークロード向けには設計されていません。

- *アンマネージド ディスク*: このディスクの種類では、VM ディスクに対応する仮想ハード ディスク (VHD) ファイルを格納するストレージ アカウントを管理します。 VHD ファイルは、Azure ストレージ アカウントにページ BLOB として格納されます。

- *マネージド ディスク*:Azure により、VM ディスクに使用するストレージ アカウントが管理されます。 ディスクの種類 (Premium または Standard) と必要なディスクのサイズを指定します。 Azure により自動的にディスクが作成、管理されます。

- *Premium Storage ディスク*:このディスクの種類は、実稼働ワークロードに最適です。 Premium Storage では、特定のサイズ シリーズ (DS、DSv2、GS、F シリーズなど) の VM に接続できる VM ディスクがサポートされています。 Premium ディスクはさまざまなサイズで提供されており、32 GB から 4,096 GB までのディスク サイズから選択できます。 各ディスク サイズは、それぞれ独自のパフォーマンス仕様があります。 アプリケーションの要件に応じて、VM には 1 つ以上のディスクを接続できます。

ポータルから新しいマネージド ディスクを作成すると、使用する種類のディスクについて**アカウントの種類**を選択できます。 使用可能なすべてのディスクがドロップダウン メニューに表示されるわけではないことに注意してください。 特定の VM サイズを選択した後のメニューには、その VM サイズに基づいて使用可能な Premium Storage の SKU のみが表示されます。

![マネージド ディスク ページのスクリーンショット](./media/oracle-design/premium_disk01.png)

VM でストレージを構成した後に、データベースを作成する前にディスクのロード テストを行うことができます。 待機時間とスループットの観点から I/O 率を知ることは、仮想マシンが待機時間の目標値を達成し、期待されるスループットをサポートしているかを把握するのに役立ちます。

アプリケーションのロード テスト用ツールは、多数提供されています (Oracle Orion、Sysbench、Fio など)。

Oracle データベースをデプロイした後に、再度ロード テストを実行します。 通常とピーク時のワークロードを開始すると、結果には、お使いの環境内のベースラインが表示されます。

ストレージ サイズではなく、IOPS の速度に基づいてストレージのサイズを決めることが重要です。 たとえば、必要な IOPS が 5,000 でも、サイズは 200 GB しか必要としないとします。この場合、提供されるストレージ サイズが 200 GB を超えますが、それでも P30 クラスの Premium ディスクを利用します。

IOPS の速度は、AWR レポートから取得することができます。 IOPS の速度は、REDO ログ、物理読み取りと書き込みの速度によって決定されます。

![AWR レポート ページのスクリーンショット](./media/oracle-design/awr_report.png)

例: REDO のサイズは 12,200,000 バイト/秒で、これは 11.63 Mbps と等しく、
IOPS は 12,200,000 ÷ 2,358 = 5,174 になります。

I/O 要件を明確に把握した後に、これらの要件に最適なドライブの組み合わせを選択できます。

**Recommendations (推奨事項)**

- DATA 表領域は、管理ストレージまたは Oracle ASM を使用して、ディスク全体に I/O ワークロードを分散させます。
- 読み取りと書き込みの集中的な操作による I/O ブロック サイズの増加に合わせて、データ ディスクを追加します。
- 大規模な順次処理に備えてブロック サイズを増やします。
- I/O 削減のためにデータ圧縮を使用します (データとインデックスの両方)。
- REDO ログ、SYSTEM 表領域、TEMP 表領域、UNDO 表領域は、それぞれ別のデータ ディスクに分離します。
- アプリケーション ファイルを既定の OS ディスク (/dev/sda) に保存しないでください。 これらのディスクは VM の高速起動用に最適化されていないため、アプリケーションに適切なパフォーマンスが提供されない可能性があります。
- Premium ストレージで M シリーズの VM を使用する場合は、再実行ログ ディスクで[書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)を有効にします。

### <a name="disk-cache-settings"></a>ディスク キャッシュの設定

ホスト キャッシュには次の 3 つのオプションがあります。

- *ReadOnly*: 今後の読み取りのためにすべての要求をキャッシュします。 すべての書き込みは、Azure Blob Strorage に直接保存されます。

- *ReadWrite*: これは "先読み" アルゴリズムです。 今後の読み取りのために読み取りと書き込みがキャッシュされます。 ライトスルー以外の書き込みは、最初にローカル キャッシュに保存されます。 また、軽量のワークロードでのディスク待機時間が最短になります。 必要なデータの永続化を処理しないアプリケーションで ReadWrite キャッシュを使用すると、VM がクラッシュした場合にデータが失われる可能性があります。

- *なし* (無効):このオプションを使用すると、キャッシュをバイパスすることができます。 すべてのデータはディスクに転送されて、Azure Storage に保存されます。 この方法を使用すると、I/O 集約型ワークロードで最も優れた I/O 率が実現します。 また、"トランザクション費用" も考慮する必要があります。

**Recommendations (推奨事項)**

スループットを最大にするには、ホスト キャッシュを **[なし]** で開始することをお勧めします。 Premium Storage では、 **[読み取り専用]** または **[なし]** のオプションでファイル システムをマウントするときに、"バリア" を無効にする必要があることに注意してください。 UUID を使用して、/etc/fstab ファイルをディスクに更新します。

![マネージド ディスク ページのスクリーンショット](./media/oracle-design/premium_disk02.png)

- OS ディスクには、既定の **[読み取り/書き込み]** キャッシュを使用します。
- SYSTEM、TEMP、UNDO には、キャッシュに **[なし]** を使用します。
- DATA には、キャッシュに **[なし]** を使用します。 ただし、お使いのデータベースが読み取り専用または読み取り集約型の場合は、 **[読み取り専用]** キャッシュを使用します。

データ ディスクの設定が保存された後に、OS レベルでドライブのマウントを解除し、変更後に再マウントするまでは、ホストのキャッシュ設定を変更できません。

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
- [Oracle のバックアップと回復](oracle-backup-recovery.md)

## <a name="next-steps"></a>次の手順

- [チュートリアル:高可用性 VM の作成](../../linux/create-cli-complete.md)
- [VM デプロイ Azure CLI サンプルを探索する](../../linux/cli-samples.md)
