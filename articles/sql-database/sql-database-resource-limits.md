---
title: "Azure SQL Database のリソース制限 | Microsoft Docs"
description: "このページでは、Azure SQL Database に対するいくつかの一般的なリソース制限について説明します。"
services: sql-database
documentationcenter: na
author: janeng
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 61eac09668b14a98a42b1907a54577d80eb933a6
ms.lasthandoff: 03/29/2017


---
# <a name="azure-sql-database-resource-limits"></a>Azure SQL Database のリソース制限
## <a name="overview"></a>Overview
Azure SQL Database では、**リソース ガバナンス**と**制限の適用**という 2 つの異なるメカニズムを使用して、データベースで使用できるリソースを管理します。 このトピックでは、リソース管理のこれら 2 つの主な領域について説明します。

## <a name="resource-governance"></a>リソース ガバナンス
Basic、Standard、および Premium サービス レベルの設計目標の 1 つは、Azure SQL Database の動作を、データベースが他のデータベースから分離された専用のコンピューターで稼働しているかのようにすることです。 リソース ガバナンスは、この動作をエミュレートします。 集計されたリソース使用率が、データベースに割り当てられた利用可能な CPU、メモリ、ログ I/O、データ I/O のリソースの最大値に達すると、リソース管理により、実行中のクエリがキューに配置されます。キューに配置されたクエリには、リソースが解放されるたびに、順次リソースが割り当てられていきます。

専用のコンピューターの場合と同様、利用可能なリソースをすべて使用すると、現在実行中のクエリの実行時間が長くなり、コマンドがクライアントでタイムアウトする可能性があります。 積極的な再試行ロジックを使用するアプリケーションやデータベースに対して高い頻度でクエリを実行するアプリケーションでは、同時要求の制限に達したときに新しいクエリを実行しようとするとエラー メッセージが表示される場合があります。

### <a name="recommendations"></a>推奨事項:
データベースの最大使用率に近づいてきたら、リソース使用率とクエリの平均応答時間を監視します。 クエリの待機時間が長くなる場合、一般的には 3 つのオプションがあります。

1. タイムアウトの発生や、要求が溜まる事態を予防するため、データベースへの着信要求の数を減らします。
2. データベースにより高いパフォーマンス レベルを割り当てます。
3. クエリを最適化して、各クエリのリソース使用率を引き下げます。 詳細については、Azure SQL Database パフォーマンス ガイダンスのクエリのチューニングとヒント設定に関するセクションを参照してください。

## <a name="enforcement-of-limits"></a>制限の適用
制限に達すると、新しい要求を拒否することによって CPU、メモリ、ログ I/O、およびデータ I/O 以外のリソースが適用されます。 データベースが構成された最大サイズに達すると、データ サイズを増加させる挿入操作と更新操作が実行に失敗するようになります (選択操作と削除操作は引き続き正常に機能します)。 達した制限に応じて、クライアントは[エラー メッセージ](sql-database-develop-error-messages.md)を受信します。

たとえば、SQL Database への接続数と処理可能な同時要求の数が制限されます。 SQL Database では、接続プールをサポートするために、データベースへの接続数が同時要求の数を上回ることを許可します。 利用可能な接続数はアプリケーションで簡単に制御できるのに対し、並列要求の数の見積もりや制御は往々にして困難です。 特に、負荷のピーク時に、アプリケーションから送信される要求が多すぎる場合や、データベースがそのリソースの制限に達し、クエリの実行時間が長くなることが原因でワーカー スレッドが蓄積され始める場合に、エラーが発生することがあります。

## <a name="service-tiers-and-performance-levels"></a>サービス プランとパフォーマンス レベル
単一データベースとエラスティック プールの両方に、サービス レベルとパフォーマンス レベルがあります。

### <a name="single-databases"></a>Single Database
単一のデータベースの場合、データベースの制限はデータベース サービス階層とパフォーマンス レベルによって決まります。 次の表では、パフォーマンス レベルごとの Basic、Standard、Premium の各データベースの特性について説明します。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!IMPORTANT]
> P11 と P15 のパフォーマンス レベルを使用しているお客様は、追加料金なしで最大 4 TB の付属のストレージを使用できます。 この 4 TB のオプションは現在、米国東部 2、米国西部、西ヨーロッパ、東南アジア、東日本、オーストラリア東部、カナダ中部、カナダ東部の各リージョンで、パプリック プレビューの段階にあります。
>

### <a name="elastic-pools"></a>エラスティック プール
[エラスティック プール](sql-database-elastic-pool.md) は、プール内のデータベース全体のリソースを共有します。 次の表では、Basic、Standard、および Premium のエラスティック プールの特性について説明します。

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

前の表に示されている各リソースの詳細な定義いついては、「 [サービス レベルの機能と制限](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)」の説明を参照してください。 サービス階層の概要については、 [Azure SQL Database のサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)に関するページを参照してください。

## <a name="other-sql-database-limits"></a>その他の SQL Database の制限
| 領域 | 制限 | Description |
| --- | --- | --- |
| サブスクリプションあたりの自動エクスポートを使用するデータベース |10 |自動エクスポートを使用すると、カスタム スケジュールを作成して、SQL Database をバックアップできます。 この機能のプレビューは、2017 年 3 月 1 日に終了します。  |
| サーバーあたりのデータベース数 |最大 5000 |V12 サーバーでは、サーバーあたり最大 5000 個のデータベースが許可されています。 |
| サーバーあたりの DTU |45000 |スタンドアロン データベースとエラスティック プールをプロビジョニングする場合、V12 サーバーではサーバーあたり 45,000 DTU を使用できます。 サーバーごとに許可されるスタンドアロン データベースとプールの合計数は、サーバーの DTU の数に制限されます。  

> [!IMPORTANT]
> Azure SQL Database の自動エクスポート機能は現在プレビュー段階であり、2017 年 3 月 1 日に廃止されます。 2016 年 12 月 1 日から、SQL データベースの自動エクスポートを構成することはできなくなります。 既存の自動エクスポート ジョブは、引き続き 2017 年 3 月 1 日まで実行されます。 2016 年 12 月 1 日以降は、[長期のバックアップ リテンション期間](sql-database-long-term-retention.md)または [Azure Automation](../automation/automation-intro.md) を使用してください。PowerShell を使用して、選択したスケジュールに従って定期的に SQL データベースをアーカイブできます。 サンプル スクリプトは [GitHub からダウンロード](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)できます。
>


## <a name="resources"></a>リソース
[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)

[Azure SQL Database のサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)

[SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md)

