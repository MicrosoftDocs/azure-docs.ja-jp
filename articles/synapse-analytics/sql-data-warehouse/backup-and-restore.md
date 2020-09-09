---
title: バックアップと復元 - スナップショット、Geo 冗長
description: Azure Synapse Analytics の SQL プールにおけるバックアップと復元の動作について説明します。 データ ウェアハウスをプライマリ リージョンの復元ポイントに復元するにはバックアップを使用します。 異なる地理的リージョンに復元するには、Geo 冗長バックアップを使用します。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: d4a08035b03c104555c39311bfb812218cca44b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482549"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Azure Synapse の SQL プールにおけるバックアップと復元

Azure Synapse の SQL プールにおけるバックアップと復元の使用方法について説明します。 データ ウェアハウスをプライマリ リージョンの以前の状態に復旧またはコピーするには、SQL プールの復元ポイントを使用します。 異なる地理的リージョンに復元するには、データ ウェアハウスの geo 冗長バックアップを使用します。

## <a name="what-is-a-data-warehouse-snapshot"></a>データ ウェアハウスのスナップショットとは

"*データ ウェアハウスのスナップショット*" では、データ ウェアハウスの以前の状態を復旧またはコピーするために利用できる復元ポイントが作成されます。  SQL プールは分散システムなので、データ ウェアハウスのスナップショットは Azure Storage に配置されている多くのファイルで構成されます。 スナップショットでは、データ ウェアハウスに格納されたデータの増分の変更がキャプチャされます。

"*データ ウェアハウスの復元*" とは、既存または削除済みのデータ ウェアハウスの復元ポイントから作成された新しいデータ ウェアハウスのことです。 偶発的な破損や削除が起きた後にデータを再作成するデータウェアハウスの復元は、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 データ ウェアハウスは、テストまたは開発用にデータ ウェアハウスのコピーを作成する強力なメカニズムでもあります。  SQL プールの復元速度は、データベースのサイズと、ソースとターゲットのデータ ウェアハウスの場所によって異なる場合があります。

## <a name="automatic-restore-points"></a>自動復元ポイント

スナップショットは、復元ポイントを作成する組み込み機能です。 この機能を有効にする必要はありません。 ただし、復元ポイントを作成するには、SQL プールがアクティブ状態である必要があります。 SQL プールを頻繁に一時停止すると自動復元ポイントが作成されない可能性があるため、SQL プールを一時停止する前にユーザー定義の復元ポイントを作成するようにしてください。 サービスは自動復元ポイントを使用して復旧の SLA を維持するので、現時点では、ユーザーはこれらの復元ポイントを削除できません。

データ ウェアハウスのスナップショットは 1 日を通して取得され、7 日間利用できる復元ポイントが作成されます。 この保持期間は変更できません。 SQL プールでは、8 時間の RPO (回復ポイントの目標) がサポートされています。 プライマリ リージョンのデータ ウェアハウスを、過去 7 日間に作成されたいずれかのスナップショットから復元することができます。

最新のスナップショットが開始された時間を表示するには、オンラインの SQL プールで次のクエリを実行します。

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>ユーザー定義の復元ポイント

ユーザーは、この機能を使用して、大規模な変更の前後に、スナップショットを手動でトリガーしてデータ ウェアハウスの復元ポイントを作成できます。 この機能により、復元ポイントの論理的な一貫性が保証され、ワークロードの中断やユーザー エラーが発生して迅速に復旧する必要がある場合に、追加のデータ保護が提供されます。 ユーザー定義の復元ポイントは、7 日間使用でき、自動的に削除されます。 ユーザー定義の復元ポイントの保持期間を変更することはできません。 任意の時点で **42 個のユーザー定義の復元ポイント**が保証されているため、別の復元ポイントを作成する前に[削除](https://go.microsoft.com/fwlink/?linkid=875299)する必要があります。 ユーザー定義の復元ポイントを作成するスナップショットのトリガーは、[PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) または Azure portal で行うことができます。

> [!NOTE]
> 7 日より長い復元ポイントが必要な場合は、[こちら](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)でこの機能に投票してください。 ユーザー定義の復元ポイントを作成し、新しく作成された復元ポイントを新しいデータ ウェアハウスに復元することもできます。 復元が済むと、SQL プールはオンラインになるので、無期限に一時停止してコンピューティング コストを節約することができます。 一時停止したデータベースについては、Azure Premium Storage レートでストレージに対して課金されます。 復元されたデータ ウェアハウスのアクティブなコピーが必要な場合は、わずか数分で再開できます。

### <a name="restore-point-retention"></a>復元ポイントのリテンション期間

復元ポイントのリテンション期間の詳細は次のとおりです。

1. SQL プールは、7 日間のリテンション期間に達したとき**と**、復元ポイントの合計数が少なくとも 42 個ある場合 (ユーザー定義と自動の両方を含む) に、復元ポイントを削除します
2. SQL プールが一時停止しているときはスナップショットは取得されません
3. 復元ポイントの経過時間は、復元ポイントが取得された時点 (SQL プールが一時停止された時点を含む) からのカレンダーの絶対日数によって測定されます。
4. 任意の時点で、復元ポイントが 7 日間のリテンション期間に達していない限り、SQL プールは最大 42 個のユーザー定義の復元ポイントと 42 個の自動復元ポイントを格納できることが保証されています。
5. スナップショットが取得され、SQL プールが 7 日間以上停止されてから再開した場合、復元ポイントの数が合計で 42 になるまで (ユーザー定義と自動の両方を含む)、復元ポイントが維持されます。

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>SQL プールが停止しているときのスナップショットのリテンション期間

SQL プールを削除すると、最後のスナップショットが作成され、7 日間保存されます。 SQL プールは、削除するときに作成された最後の復元ポイントに復元できます。 SQL プールが一時停止状態で削除された場合、スナップショットは作成されません。 このシナリオでは、必ず SQL プールを削除する前に、ユーザー定義の復元ポイントを作成してください。

> [!IMPORTANT]
> SQL プールをホストしているサーバーを削除すると、そのサーバーに属しているデータベースもすべて削除され、復元できなくなります。 削除されたサーバーを復元することはできません。

## <a name="geo-backups-and-disaster-recovery"></a>geo バックアップとディザスター リカバリー

[ペアのデータ センター](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)には、1 日に 1 回、geo バックアップが作成されます。 geo 復元の RPO は 24 時間です。 geo バックアップは、SQL プールがサポートされているその他の任意のリージョン内のサーバーに復元することができます。 geo バックアップにより、プライマリ リージョンの復元ポイントにアクセスできない場合でも、データ ウェアハウスを復元できます。

> [!NOTE]
> geo バックアップ用にさらに短い RPO が必要な場合は、[こちら](https://feedback.azure.com/forums/307516-sql-data-warehouse)でこの機能に投票してください。 ユーザー定義の復元ポイントを作成し、新しく作成された復元ポイントを異なるリージョンの新しいデータ ウェアハウスに復元することもできます。 復元が済むと、データ ウェアハウスはオンラインになるので、無期限に一時停止してコンピューティング コストを節約することができます。 一時停止したデータベースについては、Azure Premium Storage レートでストレージに対して課金されます。 データ ウェアハウスのアクティブなコピーが必要な場合は、わずか数分で再開できます。

## <a name="backup-and-restore-costs"></a>バックアップと復元のコスト

Azure の課金には、ストレージの明細項目とディザスター リカバリー ストレージの明細項目があることがわかります。 ストレージの料金は、プライマリ リージョンでのデータの格納と、スナップショットによってキャプチャされた増分変更のコストの合計です。 スナップショットの課金方法については、「[Understanding how Snapshots Accrue Charges](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)」 (スナップショットの料金が発生するしくみについて) を参照してください。 geo 冗長の料金には、geo バックアップを格納するコストが含まれます。  

プライマリ データ ウェアハウスと 7 日間のスナップショット変更の総コストは、TB 単位で四捨五入されます。 たとえば、データ ウェアハウスが 1.5 TB で、スナップショットが 100 GB をキャプチャする場合、Azure Premium Storage の料金で 2 TB のデータが課金されます。

地理冗長ストレージを使用している場合は、個別のストレージ料金がかかります。 geo 冗長ストレージは、標準の読み取りアクセスの地理冗長ストレージ (RA-GRS) の料金で課金されます。

Azure Synapse の価格の詳細については、[Azure Synapse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)に関するページを参照してください。 複数のリージョンにまたがる復元の場合、データのエグレスには課金されません。

## <a name="restoring-from-restore-points"></a>復元ポイントからの復元

各スナップショットでは、スナップショットの開始時刻を表す復元ポイントが作成されます。 データ ウェアハウスを復元するには、復元ポイントを選択して restore コマンドを実行します。  

復元されたデータ ウェアハウスと現在のデータ ウェアハウスの両方を保持するか、いずれか 1 つを削除することができます。 現在のデータ ウェアハウスを、復元されたデータ ウェアハウスで置換する場合は、MODIFY NAME オプションを指定して [ALTER DATABASE (SQL プール)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) を使用し、名前を変更できます。

データ ウェアハウスを復元するには、「[SQL プールの復元](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)」をご覧ください。

削除済みまたは一時停止中のデータ ウェアハウスを復元する場合は、[サポート チケットを作成](sql-data-warehouse-get-started-create-support-ticket.md)できます。

## <a name="cross-subscription-restore"></a>クロス サブスクリプションの復元

サブスクリプション間で直接復元する必要がある場合は、その機能について、[こちら](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)で投票を行ってください。 クロス サブスクリプションの復元を実行するには、別のサーバーに復元し、そのサーバーをサブスクリプション間で ['移動'](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) します。

## <a name="geo-redundant-restore"></a>geo 冗長復元

選択したパフォーマンス レベルで、SQL プールをサポートする任意のリージョンに [SQL プールを復元](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell)できます。

> [!NOTE]
> geo 冗長復元を実行するには、この機能の使用を中止しないようにする必要があります。

## <a name="next-steps"></a>次のステップ

復元ポイントの詳細については、「[ユーザー定義の復元ポイント](sql-data-warehouse-restore-points.md)」を参照してください