---
title: Azure SQL Database のリソース制限 - マネージド インスタンス| Microsoft Docs
description: この記事では、マネージド インスタンスに対する Azure SQL Database のリソース制限の概要を示します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 10/02/2019
ms.openlocfilehash: 74fd8abbe78395a75d9c0a49eb717fb8ceecd11e
ms.sourcegitcommit: 387da88b8262368c1b67fffea58fe881308db1c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "71982783"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Azure SQL Database マネージド インスタンスのリソース制限の概要

この記事では、Azure SQL Database Managed Instance の技術特性とリソース制限の概要を示し、これらの制限の引き上げを要求する方法について説明します。

> [!NOTE]
> サポートされている機能と T-SQL ステートメントの違いについては、[機能の違い](sql-database-features.md)と [T-SQL ステートメントのサポート](sql-database-managed-instance-transact-sql-information.md)に関するページをご覧ください。 単一データベースとマネージド インスタンスのサービス レベル間の一般的な違いについては、「[サービス レベルの比較](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)」を参照してください。

## <a name="hardware-generation-characteristics"></a>ハードウェアの世代の特性

マネージド インスタンスには、基本のインフラストラクチャとアーキテクチャによって異なる特性とリソース制限があります。 Azure SQL Database マネージド インスタンスは、2 つのハードウェアの世代 (Gen4 と Gen5) でデプロイできます。 ハードウェアの世代には、次の表に示したさまざまな特性があります。

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| ハードウェア | Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサ、接続されている SSD 仮想コア = 1 PP (物理コア) | Intel E5-2673 v4 (Broadwell) 2.3-GHz プロセッサ、高速 NVMe SSD、仮想コア=1 LP (ハイパー スレッド) |
| 仮想コアの数 | 8、16、24 の仮想コア | 4、8、16、24、32、40、64、80 の仮想コア |
| 最大メモリ (メモリ/コア比) | 仮想コアあたり 7 GB<br/>メモリ量を増やすには、仮想コアを追加します。 | 仮想コアあたり 5.1 GB<br/>メモリ量を増やすには、仮想コアを追加します。 |
| 最大インメモリ OLTP メモリ | インスタンスの制限:仮想コアあたり 1 から 1.5 GB| インスタンスの制限:仮想コアあたり 0.8 から 1.65 GB |
| インスタンスの予約済み最大ストレージ |  汎用:8 TB<br/>Business Critical:1 TB (テラバイト) | 汎用:8 TB<br/> Business Critical: コアの数に応じて 1 TB、2 TB、または 4 TB |

> [!IMPORTANT]
> - Gen4 ハードウェアは段階的に廃止されています。Gen5 ハードウェアに新しいマネージド インスタンスをデプロイすることをお勧めします。
> - 現時点で、Gen4 ハードウェアは引き続き次のリージョンでのみ利用できます。北ヨーロッパ、西ヨーロッパ、米国東部、米国中南部、米国中北部、米国西部 2、米国中部、カナダ中部、インド南部、東南アジア、韓国中部。

### <a name="in-memory-oltp-available-space"></a>使用可能なインメモリ OLTP 領域 

[Business Critical](sql-database-service-tier-business-critical.md) サービス レベルのインメモリ OLTP 領域は、仮想コアの数とハードウェアの世代によって異なります。 次の表に、インメモリ OLTP オブジェクトに使用できるメモリの制限をリストします。

| インメモリ OLTP 領域  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 仮想コア  | 3.14 GB | |   
| 8 仮想コア  | 6.28 GB | 8 GB |
| 16 仮想コア | 15.77 GB | 20 GB |
| 24 仮想コア | 25.25 GB | 36 GB |
| 32 仮想コア | 37.94 GB | |
| 40 仮想コア | 52.23 GB | |
| 64 仮想コア | 99.9 GB    | |
| 80 仮想コア | 131.68 GB| |

## <a name="service-tier-characteristics"></a>サービス レベルの特性

マネージド インスタンスには 2 つのサービス レベルがあります。[General Purpose](sql-database-service-tier-general-purpose.md) と [Business Critical](sql-database-service-tier-business-critical.md) です。 これらのレベルでは、次の表に示すように、[別の機能](sql-database-service-tiers-general-purpose-business-critical.md)が提供されます。

> [!Important]
> Business Critical サービス レベルには、読み取り専用ワークロードに使用できるインスタンス (セカンダリ レプリカ) の追加の組み込みコピーが用意されています。 読み取り/書き込みクエリと読み取り専用/分析/レポート クエリとを分離できる場合は、同じ価格で仮想コアとメモリを 2 度取得することになります。 セカンダリ レプリカは、プライマリ インスタンスよりも数秒遅れる場合があるため、データの正確な現在の状態を必要としないレポート/分析ワークロードをオフロードするように設計されています。 次の表で、**読み取り専用クエリ**は、セカンダリ レプリカ上で実行されるクエリです。

| **機能** | **汎用** | **Business Critical** |
| --- | --- | --- |
| 仮想コアの数\* | Gen4: 8、16、24<br/>Gen5:4、8、16、24、32、40、64、80 | Gen4:8、16、24 <br/> Gen5:4、8、16、24、32、40、64、80 <br/>\*同じ数の仮想コアが読み取り専用クエリに割り当てられます。 |
| 最大メモリ | Gen4:56 GB - 168 GB (7 GB/仮想コア)<br/>Gen5:20.4 GB から 408 GB (5.1 GB/仮想コア)<br/>メモリ量を増やすには、仮想コアを追加します。 | Gen4:56 GB - 168 GB (7 GB/仮想コア)<br/>Gen5:20.4 GB - 読み取り/書き込みクエリの場合は 408 GB (5.1 GB/仮想コア)<br/>+ 追加の 20.4 GB - 読み取り専用クエリの場合は 408 GB (5.1 GB/仮想コア)。<br/>メモリ量を増やすには、仮想コアを追加します。 |
| インスタンスの最大ストレージ サイズ (予約済み) | - 4 仮想コアの場合は 2 TB (Gen5 のみ)<br/>- その他のサイズの場合は 8 TB | Gen4:1 TB (テラバイト) <br/> Gen5: <br/>- 4、8、16 仮想コアの場合は 1 TB<br/>- 24 仮想コアの場合は 2 TB<br/>- 32、40、64、80 仮想コアの場合は 4 TB |
| 最大データベース サイズ | 現在利用可能なインスタンスのサイズまで (仮想コア数に応じて最大 2 TB から 8 TB)。 | 現在利用可能なインスタンスのサイズまで (仮想コア数に応じて最大 1 TB から 4 TB)。 |
| 最大 tempDB サイズ | 24 GB/仮想コア (96 から 1,920 GB) と現在利用可能なインスタンスのストレージ サイズに制限されています。<br/>tempdb 領域を増やすには、仮想コアを追加します。 | 現在利用可能なインスタンスのストレージ サイズまで。 現在、termdb ログ ファイルのサイズは、仮想コアあたり 24 GB に制限されています。 |
| インスタンスごとの最大データベース数 | インスタンスのストレージ サイズの制限に達していない限り、100 個。 | インスタンスのストレージ サイズの制限に達していない限り、100 個。 |
| インスタンスごとの最大データベース ファイル数 | インスタンスのストレージ サイズまたは [Azure Premium ディスクの記憶域割り当ての領域](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files)の上限に達していない限り、最大で 280 個。 | インスタンスのストレージ サイズの上限に達していない限り、データベースごとに 32,767 ファイル。 |
| データ ファイルの最大サイズ | 現在利用可能なインスタンスのストレージ サイズ (最大 2 TB から 8 TB) と [Azure Premium ディスクの記憶域割り当ての領域](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files)に制限されています。 | 現在利用可能なインスタンスのストレージ サイズ (最大 1 TB から 4 TB) に制限されています。 |
| 最大ログ ファイル サイズ | 2 TB と現在利用可能なインスタンスのストレージ サイズに制限されています。 | 2 TB と現在利用可能なインスタンスのストレージ サイズに制限されています。 |
| データ/ログの IOPS (概算) | インスタンス* あたり最大で 30 から 40 K IOPS、ファイルあたり 500 から 7500<br/>\*[IOPS を増やすには、ファイル サイズを大きくします](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5.5 K から 110 K (1375 IOPS/仮想コア)<br/>IO パフォーマンスを向上させるには、仮想コアを追加します。 |
| ログ書き込みのスループット制限 (インスタンスあたり) | 仮想コアあたり 3 MB/秒<br/>最大 22 MB/秒 | 仮想コアあたり 4 MB/秒<br/>最大 48 MB/秒 |
| データ スループット (概算) | ファイルあたり 100 ～ 250 MB/秒<br/>\*[IO パフォーマンスを向上させるには、ファイル サイズを増やします](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | 制限なし。 |
| ストレージ IO 待機時間 (概算) | 5 ～ 10 ms | 1 ～ 2 ms |
| インメモリ OLTP | サポートされていません | 利用可能、[サイズは仮想コアの数に依存](#in-memory-oltp-available-space) |
| 最大セッション数 | 30000 | 30000 |
| [読み取り専用レプリカ](sql-database-read-scale-out.md) | 0 | 1 (価格に含まれます) |

> [!NOTE]
> - **現在利用可能なインスタンスのストレージ サイズ**は、予約インスタンスのサイズと使用されているストレージ スペースの差です。
> - ユーザー データベースとシステム データベースのデータ ファイルおよびログ ファイルのサイズはどちらも、最大ストレージ サイズの制限と比較されるインスタンス ストレージ サイズに含まれます。 データベースによって使用される合計領域を確認するには、<a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> システム ビューを使用します。 エラー ログは保持されず、サイズには含まれません。 バックアップは、ストレージ サイズに含まれません。
> - スループットと IOPS も、マネージド インスタンスによって明示的に制限されないページ サイズに依存します。
> 自動フェールオーバー グループを利用すれば、異なる Azure リージョンで別の読み取り可能レプリカを作成できます。
> - 最大インスタンス IOPS は、ワークロードにおけるファイルのレイアウトおよび分散に依存します。 たとえば、それぞれ最大 5K IOPS を持つ 1 GB ファイルを 7 個と、それぞれ 500 IOPS を持つ小さいファイル (128 GB 未満) を 7 個作成したとすると、ワークロードですべてのファイルを使用できる場合、インスタンスあたり 38500 IOPS (7 x 5000 + 7 x 500) を取得できます。 IOPS の値によっては、自動バックアップにも使用されることに注意してください。

> [!NOTE]
> 詳しくは、この記事の[マネージド インスタンス プールのリソースの制限事項](sql-database-instance-pools.md#instance-pools-resource-limitations)をご覧ください。

### <a name="file-io-characteristics-in-general-purpose-tier"></a>General Purpose サービス レベルでのファイル IO 特性

General Purpose サービス レベルでは、すべてのデータベース ファイルが、ファイルのサイズに依存する専用の IOPS とスループットを取得します。 ファイルのサイズが大きいほど、IOPS とスループットが向上します。 次の表に、データベース ファイルの IO 特性を示します。

| ファイル サイズ           | 0 から 128 GiB | 128 から 256 GiB | 256 から 512 GiB | 0.5 から 1 TiB    | 1 から 2 TiB    | 2 から 4 TiB | 4 から 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| ファイルあたりの IOPS       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12,500   |
| ファイルあたりのスループット | 100 MiB/秒 | 125 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒 | 480 MiB/s | 

何らかのデータベース ファイルに対する IO 待機時間が長いことに気付いた場合、または IOPS/スループットが上限に達している場合は、[ファイルのサイズを大きくする](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)ことで、パフォーマンスを改善できる場合があります。

ログ書き込みスループットの上限 (22 MB/s) などのインスタンスレベルの制限もあります。結果として、インスタンスのスループット制限に達するために、ログ ファイルにおいてファイル全体にアクセスできない場合があります。

## <a name="supported-regions"></a>サポートされているリージョン

マネージド インスタンスは、[サポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)のみで作成できます。 現在サポートされていないリージョンでマネージド インスタンスを作成するには、[Azure portal 経由でサポート要求を送信](#obtaining-a-larger-quota-for-sql-managed-instance)できます。

## <a name="supported-subscription-types"></a>サポートされているサブスクリプションの種類

マネージド インスタンスは現在、次の種類のサブスクリプションのみでデプロイをサポートしています。

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [クラウド サービス プロバイダー (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio サブスクライバー向けの毎月の Azure クレジット付きサブスクリプション](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>リージョンのリソース制限

サポートされているサブスクリプションの種類には、リージョンごとのリソース数の制限を組み入れることができます。 マネージド インスタンスには、サブスクリプションの種類に応じて、(特別な[サポート要求を Azure portal で](#obtaining-a-larger-quota-for-sql-managed-instance)作成することによって、オンデマンドで増加する可能性がある) Azure リージョンごとに 2 つの既定の制限があります。

- **サブネットの制限**: マネージド インスタンスが単一リージョンにデプロイされているサブネットの最大数。
- **仮想コア ユニットの制限**: 単一リージョン内のすべてのインスタンスを超えてデプロイできる仮想コア ユニットの最大数。 1 つの GP 仮想コアでは仮想コア ユニットを 1 つ使用し、1 つの BC 仮想コアでは仮想コア ユニットを 4 つ使用します。 インスタンスの合計数は、仮想コア ユニットの制限内である限り、制限されません。

> [!Note]
> これらの制限は既定の設定であり、技術的な制限ではありません。 現在のリージョンでさらに多くのマネージド インスタンスが必要な場合、[Azure portal でサポート要求](#obtaining-a-larger-quota-for-sql-managed-instance)を特別に作成して、これらの制限をオンデマンドで引き上げることができます。 サポート要求を送信せずに、代わりに、別の Azure リージョンに新しいマネージド インスタンスを作成することも可能です。

次の表は、サポートされている種類のサブスクリプションを対象に、**既定のリージョン別制限**についてまとめたものです (既定の制限は、下に説明がある、サポート リクエストを利用して拡張できます)。

|サブスクリプションの種類| マネージド インスタンスのサブネットの最大数 | 最大仮想コア ユニット数* |
| :---| :--- | :--- |
|従量課金制|3|320|
|CSP |8 (リージョンによっては 15**)|960 (リージョンによっては 1440**)|
|開発テスト用の従量課金制プラン|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (リージョンによっては 15**)|960 (リージョンによっては 1440**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional および MSDN Platforms|2|32|

\* デプロイの計画では、Business Critical (BC) サービス レベルの場合、仮想コアの容量が General Purpose (GP) サービス レベルより 4 倍多い必要があることを考慮してください。 例: 1 つの GP 仮想コア = 1 つの仮想コア ユニット、1 つの BC 仮想コア = 4 つの仮想コア ユニットとなります。 既定の制限に対する消費量分析を簡素化するために、マネージド インスタンスがデプロイされているリージョン内のすべてのサブネットの仮想コア ユニットを集計して、その結果をサブスクリプションの種類のインスタンス ユニットの制限と比較します。 「**最大仮想コア ユニット数**」の制限は、リージョン内の各サブスクリプションに適用されます。 個々のサブネットあたりの制限はありませんが、複数のサブネット全体のデプロイされたすべての仮想コアの合計は、「**最大仮想コア ユニット数**」以下である必要があります。

\*\* 次のリージョンには、より大きなサブネットと仮想コアの制限があります。オーストラリア東部、米国東部、米国東部 2、北ヨーロッパ、米国中南部、東南アジア、英国南部、西ヨーロッパ、米国西部 2。

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>SQL マネージド インスタンスでのより大きなクォータの取得

現在のリージョンでより多くのマネージド インスタンスが必要な場合、Azure portal を使用してクォータを拡張するためのサポート要求を送信します。
より大きなクォータを取得するプロセスを開始するには、次の手順を実行します。

1. **[ヘルプとサポート]** を開き、 **[新しいサポート要求]** をクリックします。

   ![ヘルプとサポート](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 新しいサポート要求の [基本] タブで、次の手順を実行します。
   - **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
   - **[サブスクリプション]** で、ご使用のサブスクリプションを選択します。
   - **[クォータの種類]** で、 **[SQL Database Managed Instance]** を選択します。
   - **[サポート プラン]** で、お使いのサポート プランを選択します。

     ![[問題の種類] クォータ](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. **[次へ]** をクリックします。
4. 新しいサポート リクエストの **[問題]** タブで、次の手順を実行します。
   - **[重大度]** で、問題の重大度を選択します。
   - **[詳細]** で、エラー メッセージなど、問題に関する追加情報を指定します。
   - **[ファイルのアップロード]** で、より詳細な情報を含むファイル (最大 4 MB) を添付します。

     ![問題の詳細](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 有効な要求には、次の情報を含める必要があります。
     > - サブスクリプションの制限を引き上げる必要があるリージョン。
     > - クォータが増加した後の既存のサブネットで、サービス レベルごとに必要な仮想コア数 (既存のサブネットのいずれかを拡張する必要がある場合)。
     > - 必要な新しいサブネット数と、新しいサブネット内でのサービス レベルあたりの仮想コア合計 (新しいサブネットにマネージド インスタンスをデプロイする必要がある場合)

5. **[次へ]** をクリックします。
6. 新しいサポート要求の [連絡先情報] タブで、希望する連絡方法 (電子メールまたは電話) と連絡先の詳細を入力します。
7. **Create** をクリックしてください。

## <a name="next-steps"></a>次の手順

- マネージド インスタンスの詳細については、[マネージド インスタンスとは何か](sql-database-managed-instance.md)に関する記事を参照してください。
- 料金情報については、[SQL Database マネージド インスタンスの価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。
- 最初のマネージド インスタンスを作成する方法については、[クイック スタート ガイド](sql-database-managed-instance-get-started.md)を参照してください。
