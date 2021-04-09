---
title: 専用 SQL プール (以前の SQL DW) を Gen2 に移行する
description: 既存の専用 SQL プール (以前の SQL DW) を Gen2 に移行するための手順およびリージョン別の移行スケジュール。
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0ce07ff3ca5fbcc9776792129d3bfb4ef54efe7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120123"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>専用 SQL プール (以前の SQL DW) を Gen2 にアップグレードする

Microsoft では、専用 SQL プール (以前の SQL DW) の実行に関するエントリレベルのコストを削減することを支援しています。  要求の厳しいクエリを処理できる低いコンピューティング層を専用 SQL プール (以前の SQL DW) に使用できるようになりました。 「[Lower compute tier support for Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/)」の告知の全文をお読みください。 この新しいオファリングは、下記の表に記載されたリージョンで利用することができます。 サポートされているリージョンの場合、次のいずれかの方法で既存の Gen1 専用 SQL プール (以前の SQL DW) を Gen2 にアップグレードできます。

- **自動アップグレード プロセス:** 自動アップグレードは、リージョンでサービスが使用できるようになってもすぐには開始されません。  特定のリージョンで自動アップグレードが開始されると、選択したメンテナンス スケジュール中に個々のデータ ウェアハウスのアップグレードが実行されます。
- [**Gen2 へのセルフアップグレード:**](#self-upgrade-to-gen2) Gen2 へのセルフアップグレードを実行することで、アップグレードのタイミングを制御することができます。 利用するリージョンがまだサポート対象でない場合は、サポートされているリージョンで復元ポイントから直接 Gen2 インスタンスに復元することができます。

## <a name="automated-schedule-and-region-availability-table"></a>自動スケジュールと利用可能なリージョンの表

次の表では、下位 Gen2 コンピューティング レベルがいつ利用可能になるか、および自動アップグレードがいつ開始されるかを、リージョン別にまとめています。 日付は変更されることがあります。 お客様のリージョンがいつ利用可能になるかを再度ご確認ください。

\* は、リージョンに対して特定のスケジュールが現時点で利用不可であることを示します。

| **リージョン** | **下位の Gen2 が利用可能** | **自動アップグレードの開始** |
|:--- |:--- |:--- |
| カナダ東部 |2020 年 6 月 1 日 |2020 年 7 月 1 日 |
| 中国東部 |\* |\* |
| 中国北部 |\* |\* |
| ドイツ中部 |\* |\* |
| ドイツ中西部 |利用可能 |2020 年 5 月 1 日 |
| インド西部 |利用可能 |2020 年 5 月 1 日  |

## <a name="automatic-upgrade-process"></a>自動アップグレード プロセス

Microsoft では、上記の利用可能表に基づいて、Gen1 インスタンスの自動アップグレードのスケジュールを設定します。 専用 SQL プール (以前の SQL DW) が予期せず使用できなくなるのを回避するために、自動アップグレードは、お客様のメンテナンス スケジュール中にスケジュールされます。 Gen2 への自動アップグレードが実行されるリージョンでは、新しい Gen1 インスタンスを作成する機能が無効になります。 自動アップグレードが完了した後、Gen1 は非推奨となります。 スケジュールの詳細については、「[メンテナンス スケジュールを表示する](maintenance-scheduling.md#view-a-maintenance-schedule)」を参照してください。

アップグレード プロセスでは、専用 SQL プール (以前の SQL DW) を再起動する際に接続が短時間 (約 5 分) 切断されます。  専用 SQL プール (以前の SQL DW) を再起動すると、完全に使用できるようになります。 ただし、アップグレード プロセスによりバックグラウンドでデータ ファイルがアップグレードされている間にパフォーマンスの低下が発生する場合があります。 パフォーマンス低下の合計時間は、ご使用のデータ ファイルのサイズによって異なります。

また、再起動後に、より大きい SLO およびリソース クラスを使用して、すべてのプライマリ列ストア テーブルに対して [Alter Index rebuild](sql-data-warehouse-tables-index.md) を実行することで、データ ファイルのアップグレード プロセスを高速化できます。

> [!NOTE]
> Alter Index rebuild はオフライン操作であり、再構築が完了するまでテーブルは使用できなくなります。

## <a name="self-upgrade-to-gen2"></a>Gen2 へのセルフアップグレード

既存の Gen1 専用 SQL プール (以前の SQL DW) で次の手順に従うことにより、セルフアップグレードを選択できます。 セルフアップグレードを選択した場合は、お客様のリージョンで自動アップグレード プロセスが開始される前にセルフアップグレードを完了する必要があります。 そうすることで、自動アップグレードが原因で競合が発生するリスクを回避できます。

セルフアップグレードを実行する場合、2 つのオプションがあります。  現在の専用 SQL プール (以前の SQL DW) をインプレースでアップグレードするか、Gen1 専用 SQL プール (以前の SQL DW) を Gen2 インスタンスに復元することができます。

- [インプレース アップグレード](upgrade-to-latest-generation.md) - このオプションを選択すると、既存の Gen1 専用 SQL プール (以前の SQL DW) が Gen2 にアップグレードされます。 アップグレード プロセスでは、専用 SQL プール (以前の SQL DW) を再起動する際に接続が短時間 (約 5 分) 切断されます。  再起動したら、完全に使用できるようになります。 アップグレード中に問題が発生した場合は、[サポート リクエスト](sql-data-warehouse-get-started-create-support-ticket.md)を作成し、考えられる原因として "Gen2 アップグレード" と記載してください。
- [復元ポイントからのアップグレード](sql-data-warehouse-restore-points.md) - 現在の Gen1 専用 SQL プール (以前の SQL DW) に対してユーザー定義の復元ポイントを作成し、Gen2 インスタンスに直接復元します。 既存の Gen1 専用 SQL プール (以前の SQL DW) はそのまま維持されます。 復元が完了すると、Gen2 専用 SQL プール (以前の SQL DW) は完全に使用できるようになります。  復元された Gen2 インスタンスに対してテストおよび検証プロセスをすべて実行した後、元の Gen1 インスタンスは削除してかまいません。

  - 手順 1:Azure portal から、[ユーザー定義の復元ポイントを作成](sql-data-warehouse-restore-active-paused-dw.md)します。
  - 手順 2:ユーザー定義の復元ポイントから復元する場合、"パフォーマンス レベル" は優先する Gen2 レベルに設定します。

アップグレード プロセスがバックグラウンドでデータ ファイルをアップグレードしている間にパフォーマンスの低下の期間が発生する場合があります。 パフォーマンス低下の合計時間は、ご使用のデータ ファイルのサイズによって異なります。

データ移行のバックグラウンド プロセスの時間を短縮するために、大規模な SLO とリソース クラスにあるクエリ対象のすべてのプライマリ列ストア テーブルに対して [Alter Index rebuild](sql-data-warehouse-tables-index.md) を実行して、データ移動を即時に強制実行できます。

> [!NOTE]
> Alter Index rebuild はオフライン操作であり、再構築が完了するまでテーブルは使用できなくなります。

専用 SQL プール (以前の SQL DW) で問題が発生した場合は、[サポート リクエスト](sql-data-warehouse-get-started-create-support-ticket.md)を作成し、考えられる原因を "Gen2 アップグレード" としてください。

詳細については、[Gen2 へのアップグレード](upgrade-to-latest-generation.md)に関するページを参照してください。

## <a name="migration-frequently-asked-questions"></a>移行についてよく寄せられる質問

**Q:Gen2 のコストは Gen1 と同じですか?**

- A:はい。

**Q:アップグレードは自動スクリプトにどのように影響しますか?**

- A:サービス レベル目標を参照する自動スクリプトは、Gen2 の同等のものに対応するように変更する必要があります。  詳細については、[こちら](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal)を参照してください。

**Q:通常、セルフアップグレードはどのくらいの時間がかかりますか?**

- A:インプレース アップグレードを行うことも、復元ポイントからアップグレードを行うこともできます。

  - インプレース アップグレードを実行すると、専用 SQL プール (以前の SQL DW) が一時的に一時停止され、再開されます。  専用 SQL プール (以前の SQL DW) がオンラインの間は、バックグラウンド プロセスが続行されます。  
  - 復元ポイントを介してアップグレードする場合、アップグレードが完全な復元プロセスを経るため、より長い時間がかかります。

**Q:自動アップグレードはどのくらいの時間がかかりますか?**

- A:アップグレードの実際のダウンタイムは、サービスの一時停止と再開にかかる時間のみで、5 分から 10 分です。 短時間のダウンタイムの後、バックグラウンド プロセスによってストレージの移行が行われます。 バックグラウンド プロセスの時間の長さは、ご使用の専用 SQL プール (以前の SQL DW) のサイズによって異なります。

**Q:この自動アップグレードはいつ実行されますか?**

- A:メンテナンス スケジュール中です。 選択したメンテナンス スケジュールを活用することで、ビジネスの中断を最小限に抑えることができます。

**Q:バックグラウンドのアップグレード プロセスが停止していると思われる場合はどうすればよいでしょうか?**

- A:列ストア テーブルのインデックスの再作成を開始してください。 この操作中、テーブルのインデックスの再作成はオフラインとなるので注意してください。

**Q:Gen2 に、Gen1 で使用していたサービス レベル目標がない場合はどのようにしますか?**

- A:Gen1 で DW600 または DW1200 を実行している場合、それぞれ DW500c または DW1000c を使用することをお勧めします。これは、Gen2 が、Gen1 よりも多くのメモリ、リソース、およびより高いパフォーマンスを提供するためです。

**Q:geo バックアップは無効にできますか?**

- A:いいえ。 geo バックアップは、あるリージョンが使用不可になった場合に専用 SQL プール (以前の SQL DW) の可用性を維持するためのエンタープライズ機能です。 さらに懸案事項がある場合は、[サポート リクエスト](sql-data-warehouse-get-started-create-support-ticket.md)をオープンしてください。

**Q:Gen1 と Gen2 の間に T-SQL 構文の違いはありますか?**

- A:Gen1 と Gen2 の T-SQL 言語構文に変更はありません。

**Q:Gen2 はメンテナンス期間をサポートしていますか?**

- A:はい。

**Q:私のリージョンがアップグレードされた後に新しい Gen1 インスタンスを作成できますか?**

- A:いいえ。 リージョンがアップグレードされると、Gen1 の新しいインスタンスの作成は無効になります。

## <a name="next-steps"></a>次のステップ

- [アップグレードの手順](upgrade-to-latest-generation.md)
- [メンテナンス期間](maintenance-scheduling.md)
- [リソース正常性の監視](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [移行開始前の確認](upgrade-to-latest-generation.md#before-you-begin)
- [インプレース アップグレードと復元ポイントからのアップグレード](upgrade-to-latest-generation.md)
- [ユーザー定義の復元ポイントの作成](sql-data-warehouse-restore-points.md)
- [Gen2 に復元する方法](sql-data-warehouse-restore-active-paused-dw.md)
- [Azure Synapse Analytics サポート リクエストを開く](./sql-data-warehouse-get-started-create-support-ticket.md)