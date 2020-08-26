---
title: リソース制限
titleSuffix: Azure SQL Managed Instance
description: この記事では、Azure SQL Managed Instance に対するリソース制限の概要を示します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 08/14/2020
ms.openlocfilehash: 902fa34be149f0b876729409c530186e34c706e5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587312"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Azure SQL Managed Instance のリソース制限の概要
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、Azure SQL Managed Instance の技術特性とリソース制限の概要を示し、これらの制限の引き上げを要求する方法について説明します。

> [!NOTE]
> サポートされている機能と T-SQL ステートメントの違いについては、[機能の違い](../database/features-comparison.md)と [T-SQL ステートメントのサポート](transact-sql-tsql-differences-sql-server.md)に関するページをご覧ください。 Azure SQL Database と SQL Managed Instance でのサービス レベル間の一般的な違いについては、「[サービス レベルの比較](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison)」を参照してください。

## <a name="hardware-generation-characteristics"></a>ハードウェアの世代の特性

SQL Managed Instance には、基になるインフラストラクチャとアーキテクチャによって異なる特性とリソース制限があります。 SQL Managed Instance は、2 つのハードウェアの世代 (Gen4 と Gen5) でデプロイできます。 ハードウェアの世代には、次の表に示したさまざまな特性があります。

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| **ハードウェア** | Intel® E5-2673 v3 (Haswell) 2.4 GHz プロセッサ、接続されている SSD 仮想コア = 1 PP (物理コア) | Intel® E5-2673 v4 (Broadwell) 2.3 GHz プロセッサ、Intel® SP-8160 (Skylake) プロセッサ、および Intel® 8272CL (Cascade Lake) 2.5 GHz プロセッサ、高速 NVMe SSD、仮想コア = 1 LP (ハイパースレッド) |
| **仮想コアの数** | 8、16、24 の仮想コア | 4、8、16、24、32、40、64、80 の仮想コア |
| **最大メモリ (メモリ/コア比)** | 仮想コアあたり 7 GB<br/>メモリ量を増やすには、仮想コアを追加します。 | 仮想コアあたり 5.1 GB<br/>メモリ量を増やすには、仮想コアを追加します。 |
| **最大インメモリ OLTP メモリ** | インスタンスの制限:仮想コアあたり 1 から 1.5 GB| インスタンスの制限:仮想コアあたり 0.8 から 1.65 GB |
| **インスタンスの予約済み最大ストレージ** |  汎用:8 TB<br/>Business Critical:1 TB (テラバイト) | 汎用:8 TB<br/> Business Critical: コアの数に応じて 1 TB、2 TB、または 4 TB |

> [!IMPORTANT]
> - Gen4 ハードウェアは段階的に廃止中であり、新しいデプロイでは利用できなくなりました。 SQL Managed Instance の新しいインスタンスはすべて、Gen5 ハードウェアにデプロイする必要があります。
> - [SQL Managed Instance のインスタンスを Gen 5 のハードウェアに移動](../database/service-tiers-vcore.md)して、広範な仮想コアとストレージのスケーラビリティ、高速ネットワーク、最高の IO パフォーマンス、最小待機時間を体験することを検討してください。

### <a name="in-memory-oltp-available-space"></a>使用可能なインメモリ OLTP 領域 

[Business Critical](../database/service-tier-business-critical.md) サービス レベルのインメモリ OLTP 領域は、仮想コアの数とハードウェアの世代によって異なります。 次の表では、インメモリ OLTP オブジェクトに使用できるメモリの制限の一覧を示します。

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

SQL Managed Instance には 2 つのサービス レベルがあります。[General Purpose](../database/service-tier-general-purpose.md) と [Business Critical](../database/service-tier-business-critical.md) です。 これらのレベルでは、次の表に示すように、[別の機能](../database/service-tiers-general-purpose-business-critical.md)が提供されます。

> [!Important]
> Business Critical サービス レベルには、読み取り専用ワークロードに使用できる SQL Managed Instance の追加の組み込みコピー (セカンダリ レプリカ) が用意されています。 読み取りおよび書き込みクエリと、読み取り専用、分析、レポート クエリとを分離できる場合は、同じ価格で仮想コアとメモリを 2 度取得することになります。 セカンダリ レプリカは、プライマリ インスタンスよりも数秒遅れる場合があるため、データの正確な現在の状態を必要としないレポートと分析のワークロードをオフロードするように設計されています。 次の表で、**読み取り専用クエリ**は、セカンダリ レプリカ上で実行されるクエリです。

| **機能** | **汎用** | **Business Critical** |
| --- | --- | --- |
| 仮想コアの数\* | Gen4:8、16、24<br/>Gen5:4、8、16、24、32、40、64、80 | Gen4:8、16、24 <br/> Gen5:4、8、16、24、32、40、64、80 <br/>\*同じ数の仮想コアが読み取り専用クエリに割り当てられます。 |
| 最大メモリ | Gen4:56 GB - 168 GB (7 GB/仮想コア)<br/>Gen5:20.4 GB から 408 GB (5.1 GB/仮想コア)<br/>メモリ量を増やすには、仮想コアを追加します。 | Gen4:56 GB - 168 GB (7 GB/仮想コア)<br/>Gen5:20.4 GB - 読み取り/書き込みクエリの場合は 408 GB (5.1 GB/仮想コア)<br/>+ 追加の 20.4 GB - 読み取り専用クエリの場合は 408 GB (5.1 GB/仮想コア)。<br/>メモリ量を増やすには、仮想コアを追加します。 |
| インスタンスの最大ストレージ サイズ (予約済み) | - 4 仮想コアの場合は 2 TB (Gen5 のみ)<br/>- その他のサイズの場合は 8 TB | Gen4:1 TB (テラバイト) <br/> Gen5: <br/>- 4、8、16 仮想コアの場合は 1 TB<br/>- 24 仮想コアの場合は 2 TB<br/>- 32、40、64、80 仮想コアの場合は 4 TB |
| 最大データベース サイズ | 現在利用可能なインスタンスのサイズまで (仮想コア数に応じて最大 2 TB から 8 TB)。 | 現在利用可能なインスタンスのサイズまで (仮想コア数に応じて最大 1 TB から 4 TB)。 |
| 最大 tempDB サイズ | 24 GB/仮想コア (96 から 1,920 GB) と現在利用可能なインスタンスのストレージ サイズに制限されています。<br/>tempdb 領域を増やすには、仮想コアを追加します。<br/> ログ ファイルは 120 GB に制限されています。| 現在利用可能なインスタンスのストレージ サイズまで。 |
| インスタンスごとの最大データベース数 | インスタンスのストレージ サイズの制限に達していない限り、100 個。 | インスタンスのストレージ サイズの制限に達していない限り、100 個。 |
| インスタンスごとの最大データベース ファイル数 | インスタンスのストレージ サイズまたは [Azure Premium ディスクの記憶域割り当ての領域](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)の上限に達していない限り、最大で 280 個。 | インスタンスのストレージ サイズの上限に達していない限り、データベースごとに 32,767 ファイル。 |
| データ ファイルの最大サイズ | 現在利用可能なインスタンスのストレージ サイズ (最大 2 TB から 8 TB) と [Azure Premium ディスクの記憶域割り当ての領域](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)に制限されています。 | 現在利用可能なインスタンスのストレージ サイズ (最大 1 TB から 4 TB) に制限されています。 |
| 最大ログ ファイル サイズ | 2 TB と現在利用可能なインスタンスのストレージ サイズに制限されています。 | 2 TB と現在利用可能なインスタンスのストレージ サイズに制限されています。 |
| データ/ログの IOPS (概算) | インスタンス* あたり最大で 30 から 40 K IOPS、ファイルあたり 500 から 7500<br/>\*[IOPS を増やすには、ファイル サイズを大きくします](#file-io-characteristics-in-general-purpose-tier)| 10 K から 200 K (2500 IOPS/仮想コア)<br/>IO パフォーマンスを向上させるには、仮想コアを追加します。 |
| ログ書き込みのスループット制限 (インスタンスあたり) | 仮想コアあたり 3 MB/秒<br/>最大 22 MB/秒 | 仮想コアあたり 4 MB/秒<br/>最大 48 MB/秒 |
| データ スループット (概算) | ファイルあたり 100 ～ 250 MB/秒<br/>\*[IO パフォーマンスを向上させるには、ファイル サイズを増やします](#file-io-characteristics-in-general-purpose-tier) | 制限なし。 |
| ストレージ IO 待機時間 (概算) | 5 ～ 10 ms | 1 ～ 2 ms |
| インメモリ OLTP | サポートされていません | 利用可能、[サイズは仮想コアの数に依存](#in-memory-oltp-available-space) |
| 最大セッション数 | 30000 | 30000 |
| 最大同時実行ワーカー (要求) 数 | Gen4:210 * 仮想コアの数 + 800<br>Gen5:105 * 仮想コアの数 + 800 | Gen4:210 * 仮想コア数 + 800<br>Gen5:105 * 仮想コア数 + 800 |
| [読み取り専用レプリカ](../database/read-scale-out.md) | 0 | 1 (価格に含まれます) |
| コンピューティングの分離 | Gen5:<br/>\- 80 仮想コアについてサポートされます<br/>\- 他のサイズではサポートされません<br/><br/>Gen4 は非推奨のためサポートされていません|Gen5:<br/>\- 60、64、80 仮想コアについてサポートされます<br/>\- 他のサイズではサポートされません<br/><br/>Gen4 は非推奨のためサポートされていません|


追加の考慮事項: 

- **現在利用可能なインスタンスのストレージ サイズ**は、予約インスタンスのサイズと使用されているストレージ スペースの差です。
- ユーザー データベースとシステム データベースのデータ ファイルおよびログ ファイルのサイズはどちらも、最大ストレージ サイズの制限と比較されるインスタンス ストレージ サイズに含まれます。 データベースによって使用される合計領域を確認するには、[sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) システム ビューを使用します。 エラー ログは保持されず、サイズには含まれません。 バックアップは、ストレージ サイズに含まれません。
- General Purpose レベルではスループットと IOPS も、SQL Managed Instance によって明示的に制限されない[ファイル サイズ](#file-io-characteristics-in-general-purpose-tier)に依存します。
  [自動フェールオーバー グループ](../database/auto-failover-group-configure.md)を利用すれば、異なる Azure リージョンで別の読み取り可能レプリカを作成できます
- 最大インスタンス IOPS は、ワークロードにおけるファイルのレイアウトおよび分散に依存します。 たとえば、それぞれ最大 5K IOPS を持つ 1 TB ファイルを 7 個と、それぞれ 500 IOPS を持つ小さいファイル (128 GB 未満) を 7 個作成したとすると、ワークロードですべてのファイルを使用できる場合、インスタンスあたり 38500 IOPS (7 x 5000 + 7 x 500) を取得できます。 一部の IOPS は、自動バックアップにも使用されることに注意してください。

詳細については、[こちらの記事の SQL Managed Instance プールでのリソース制限に関する説明](instance-pools-overview.md#resource-limitations)を参照してください。

### <a name="file-io-characteristics-in-general-purpose-tier"></a>General Purpose サービス レベルでのファイル IO 特性

General Purpose サービス レベルでは、すべてのデータベース ファイルで、ファイルのサイズに依存する専用の IOPS とスループットが取得されます。 データ ファイルが大きいほど、IOPS とスループットも大きくなります。 次の表に、データベース ファイルの IO 特性を示します。

| ファイル サイズ | >=0 および <=128 GiB | >128 および <=256 GiB | >256 および <= 512 GiB | >0.5 および <=1 TiB    | >1 および <=2 TiB    | >2 および <=4 TiB | >4 および <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| ファイルあたりの IOPS       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12,500   |
| ファイルあたりのスループット | 100 MiB/秒 | 125 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒 | 480 MiB/s | 

何らかのデータベース ファイルに対する IO 待機時間が長いことに気付いた場合、または IOPS/スループットが上限に達している場合は、[ファイルのサイズを大きくする](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)ことで、パフォーマンスを改善できる場合があります。

最大ログ書き込みスループットにはインスタンス レベルの制限もあるので (22 MB/秒)、インスタンスのスループット制限に達したため、ログ ファイルに対する最大ファイル スループットに達することができない場合があります。

## <a name="supported-regions"></a>サポートされているリージョン

SQL Managed Instance は、[サポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)でのみ作成できます。 現在サポートされていないリージョンで SQL Managed Instance を作成するには、[Azure portal でサポート リクエストを送信](../database/quota-increase-request.md)できます。

## <a name="supported-subscription-types"></a>サポートされているサブスクリプションの種類

SQL Managed Instance では、現在、次の種類のサブスクリプションでのデプロイだけがサポートされています。

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [クラウド サービス プロバイダー (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio サブスクライバー向けの毎月の Azure クレジット付きサブスクリプション](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>リージョンのリソース制限

> [!Note]
> サブスクリプションの利用可能なリージョンに関する最新情報については、まず、[リージョンの選択](https://aka.ms/sqlcapacity)に関するページを確認してください。

サポートされているサブスクリプションの種類には、リージョンごとのリソース数の制限を組み入れることができます。 SQL Managed Instance には、サブスクリプションの種類に応じて、(特別な[サポート リクエストを Azure portal で](../database/quota-increase-request.md)作成することによって、オンデマンドで増加する可能性がある) Azure リージョンごとに 2 つの既定の制限があります。

- **サブネットの制限**: SQL Managed Instance のインスタンスが単一リージョンにデプロイされているサブネットの最大数。
- **仮想コア ユニットの制限**: 単一リージョン内のすべてのインスタンスを超えてデプロイできる仮想コア ユニットの最大数。 1 つの GP 仮想コアでは仮想コア ユニットを 1 つ使用し、1 つの BC 仮想コアでは仮想コア ユニットを 4 つ使用します。 インスタンスの合計数は、仮想コア ユニットの制限内である限り、制限されません。

> [!Note]
> これらの制限は既定の設定であり、技術的な制限ではありません。 現在のリージョンでさらに多くのインスタンスが必要な場合、[Azure portal でサポート リクエスト](../database/quota-increase-request.md)を特別に作成して、これらの制限をオンデマンドで引き上げることができます。 サポート リクエストを送信せずに、代わりに、別の Azure リージョンに SQL Managed Instance の新しいインスタンスを作成することも可能です。

次の表は、サポートされている種類のサブスクリプションを対象に、**既定のリージョン別制限**についてまとめたものです (既定の制限は、下に説明がある、サポート リクエストを利用して拡張できます)。

|サブスクリプションの種類| SQL Managed Instance のサブネットの最大数 | 最大仮想コア ユニット数* |
| :---| :--- | :--- |
|従量課金制|3|320|
|CSP |8 (リージョンによっては 15**)|960 (リージョンによっては 1440**)|
|開発テスト用の従量課金制プラン|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (リージョンによっては 15**)|960 (リージョンによっては 1440**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional および MSDN Platforms|2|32|

\* デプロイの計画では、Business Critical (BC) サービス レベルの場合、仮想コアの容量が General Purpose (GP) サービス レベルより 4 倍多い必要があることを考慮してください。 次に例を示します。1 つの GP 仮想コア = 1 つの仮想コア ユニット、1 つの BC 仮想コア = 4 つの仮想コア ユニットとなります。 既定の制限に対する消費量分析を簡素化するために、SQL Managed Instance がデプロイされているリージョン内のすべてのサブネットの仮想コア ユニットを集計して、その結果をサブスクリプションの種類のインスタンス ユニットの制限と比較します。 「**最大仮想コア ユニット数**」の制限は、リージョン内の各サブスクリプションに適用されます。 個々のサブネットあたりの制限はありませんが、複数のサブネット全体のデプロイされたすべての仮想コアの合計は、「**最大仮想コア ユニット数**」以下である必要があります。

\*\* 次のリージョンには、より大きなサブネットと仮想コアの制限があります。オーストラリア東部、米国東部、米国東部 2、北ヨーロッパ、米国中南部、東南アジア、英国南部、西ヨーロッパ、米国西部 2。

> [!IMPORTANT]
> 仮想コアとサブネットの制限が 0 になっている場合、サブスクリプションの種類に対して既定のリージョン制限が設定されていないことを意味します。 また、必要な仮想コアとサブネットの値を指定する際と同じ手順に従って、特定のリージョンでサブスクリプションのアクセスを取得するためにクォータの引き上げ要求を使用することもできます。

## <a name="request-a-quota-increase"></a>クォータの増加を要求する

現在のリージョンでより多くのインスタンスが必要な場合、Azure portal を使用してクォータを拡張するためのサポート リクエストを送信します。 詳細については、「[Azure SQL Database のクォータの増加を要求する](../database/quota-increase-request.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- SQL Managed Instance の詳細については、「[SQL Managed Instance とは何か](sql-managed-instance-paas-overview.md)」を参照してください。
- 料金情報については、[SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。
- 最初の SQL Managed Instance を作成する方法については、[クイックスタート ガイド](instance-create-quickstart.md)を参照してください。
