---
title: SQL Server から SQL Managed Instance:パフォーマンス分析
description: SQL Server データベースを Azure SQL Managed Instance に移行するときにパフォーマンス ベースラインを作成して比較する方法について学習します。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: 28eedd3a22a0a5adc6e9a37c9e0f0fa0214a4e47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564441"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>移行のパフォーマンス: SQL Server から SQL Managed Instance の場合のパフォーマンス分析
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

SQL Managed Instance でのワークロードのパフォーマンスを、SQL Server で実行される元のワークロードのものと比較するためのパフォーマンス ベースラインを作成します。 

## <a name="create-a-baseline"></a>ベースラインを作成する

移行後のパフォーマンスは同等かそれ以上であることが理想的です。そのため、ソースのベースライン パフォーマンス値を測定して記録してから、それらをターゲット環境と比較することが重要です。 パフォーマンス ベースラインは、ソースの平均ワークロードを定義する一連のパラメーターです。 

ビジネス ワークロードを表し、それらにとって重要な一連のクエリを選択します。 これらのクエリの最小/平均/最大期間と CPU 使用率だけでなく、平均/最大 CPU 使用率、平均/最大ディスク IO 待機時間、スループット、IOPS、ページの平均/最大予測保持期間、tempdb の平均最大サイズなど、ソース サーバーのパフォーマンス メトリックを測定して文書化します。 

次のリソースは、パフォーマンス ベースラインの定義に役立ちます。 

   - [CPU 使用率を監視](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)します
   - [メモリ使用率を監視](/sql/relational-databases/performance-monitor/monitor-memory-usage) し、バッファー プール、プラン キャッシュ、列ストア プール、 [インメモリ OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage) などのさまざまなコンポーネントで使用されるメモリの量を明らかにします。さらに、ページの予測保持期間メモリ パフォーマンス カウンターの平均値とピーク値を調べる必要があります。 
   -  [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)  ビューまたは [パフォーマンス カウンター](/sql/relational-databases/performance-monitor/monitor-disk-usage)を使って、ソース SQL Server インスタンスでのディスク IO 使用率を監視します。 
   - 動的管理ビュー (SQL Server 2016 以降から移行する場合はクエリ ストア) を調べることで、ワークロードとクエリのパフォーマンスを監視します。 ワークロード内の最も重要なクエリの平均期間と CPU 使用率を特定します。 

移行前に、ソース SQL Server のすべてのパフォーマンスの問題に対処する必要があります。 既知の問題が新しいシステムに移行されると、予期しない結果が発生し、パフォーマンスの比較が無効になる可能性があります。 


## <a name="compare-performance"></a>パフォーマンスを比較する 

ベースラインを定義した後、ターゲット SQL Managed Instance で類似したワークロードのパフォーマンスを比較します。 正確性を確保するために、SQL Managed Instance 環境が可能な限り SQL Server 環境に匹敵していることが重要です。 

パフォーマンスを完全に一致させられない可能性がある、SQL Managed Instance インフラストラクチャの違いがあります。 クエリには、予想よりも高速に実行されるものもあれば、低速であるものもあります。 この比較の目的は、マネージド インスタンスでのワークロードのパフォーマンスが SQL Server でのパフォーマンスと (平均で) 一致することを確認し、重要なクエリの中にパフォーマンスが元のパフォーマンスに匹敵しないものがあるかどうかを明らかにすることです。 

パフォーマンスの比較では、次のような結果になる可能性があります。 

- マネージド インスタンスでのワークロードのパフォーマンスが、ソース SQL Server でのワークロードのパフォーマンスと同等かそれより優れています。 この場合、移行が成功したことを正常に確認しました。 

- ワークロードのパフォーマンス パラメーターとクエリの多くは予期したとおりに実行されていますが、一部の例外によりパフォーマンスが低下しています。 この場合は、相違点とその重要度を特定します。 重要なクエリでパフォーマンスが低下したものがある場合は、基になる SQL プランが変更されたか、クエリでリソースが制限に達しているかを調べます。 重要なクエリ (たとえば、互換性レベル、レガシ カーディナリティ推定機能の変更) に対して、直接またはプラン ガイドを使用して、ヒントをいくつか適用することでこれを軽減できます。 確実に、統計とインデックスが最新であり、両方の環境で同等であるようにします。 

- ほとんどのクエリは、ソース SQL Server インスタンスよりマネージド インスタンスでの方が実行速度が遅くなります。 この場合は、IO、メモリ、インスタンス ログ レート制限のような [リソース制限への到達](../../managed-instance/resource-limits.md#service-tier-characteristics)など、違いの根本原因を明らかにしてみます。 違いを引き起こすリソースの制限がない場合は、データベースの互換性レベルを変更してみるか、レガシ カーディナリティ推定などのデータベース設定を変更して、テストを再実行します。 マネージド インスタンスまたはクエリ ストア ビューによって提供されるレコメンデーションを確認し、パフォーマンスが低下したクエリを特定します。 

SQL Managed Instance には、既定で有効になる組み込みの自動プラン修正機能があります。 この機能を使用すると、過去に正常に機能していたクエリのパフォーマンスが今後は低下しないことが保証されます。 この機能が有効になっていない場合は、SQL Managed Instance でパフォーマンス ベースラインを学習できるように、古い設定でワークロードを実行します。 その後、この機能を有効にし、新しい設定でもう一度ワークロードを実行します。 

ニーズに合ったワークロードのパフォーマンスが得られるまで、テストのパラメーターを変更するか、上位のサービス レベルにアップグレードして、最適な構成に近付けます。 

## <a name="monitor-performance"></a>パフォーマンスの監視 

SQL Managed Instance には、監視とトラブルシューティングのための高度なツールが用意されており、それらを使用してインスタンスのパフォーマンスを監視する必要があります。 監視する主なメトリックをいくつか以下に示します。 

- プロビジョニングした仮想コアの数がワークロードに適したものであるかどうかを判断するための、インスタンスの CPU 使用率。 
- [追加のメモリ](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444)が必要かどうかを判断するための、マネージド インスタンスのページの予測保持期間。
-  ストレージ IO の問題を特定する INSTANCE_LOG_GOVERNOR や PAGEIOLATCH などの統計。特に、IO パフォーマンスを向上させるためにファイルの事前割り当てが必要になる可能性がある General Purpose レベルで当てはまります。 


## <a name="considerations"></a>考慮事項  

パフォーマンスを比較する場合は、次のことを考慮してください。 

- ソースとターゲットの間で設定は一致します。 2 つの環境の間で、インスタンス、データベース、および tempdb のさまざまな設定が等しいことを確認します。 構成、互換性レベル、暗号化設定、トレース フラグなどの違いはすべてパフォーマンスにずれを生じさせる可能性があります。 

- ストレージは[ベスト プラクティス](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)に従って構成されます。 たとえば、General Purpose では、パフォーマンスを向上させるためにファイルのサイズを事前に割り当てる必要がある場合があります。 

- マネージド インスタンスと SQL Server の間でパフォーマンスの違いを引き起こす可能性のある[重要な環境の違い](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)があります。 パフォーマンスの問題の要因になる可能性のある環境に関するリスクを特定します。 

- SQL Managed Instance ではクエリ ストアと自動チューニングを有効にする必要があります。これは、ワークロードのパフォーマンスを測定し、潜在的なパフォーマンスの問題を自動的に軽減するのに役立つためです。 



## <a name="next-steps"></a>次のステップ

新しい Azure SQL Managed Instance 環境を最適化する方法の詳細については、次のリソースを参照してください。 

- [Azure SQL Managed Instance でのワークロードのパフォーマンスが SQL Server と異なる理由を特定する方法](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [SQL Managed Instance と SQL Server の間でパフォーマンスの差が生じる主な原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [ストレージ パフォーマンスのベスト プラクティスおよび Azure SQL Managed Instance (General Purpose) に関する考慮事項](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Azure SQL Managed Instance のリアルタイムのパフォーマンス監視 (これはアーカイブされていますが、これは目的のターゲットですか?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)