---
title: "エラスティック プールとは 複数の SQL データベースの管理 - Azure | Microsoft Docs"
description: "エラスティック プールを使用して、複数 (数百や数千) の SQL データベースを管理およびスケーリングします。 一定の価格で必要な場所にリソースを配布できます。"
keywords: "複数のデータベース, データベース リソース, データベース パフォーマンス"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 03/06/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 53c54c490e7888edef0dc83d1657adbb3db0b745
ms.lasthandoff: 03/10/2017


---

# <a name="how-elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>エラスティック プールが複数の SQL データベースの管理およびスケーリングに与えるメリット

SQL Database のエラスティック プールは、予期できない多様な使用ニーズを持つ複数のデータベースを管理しスケーリングするための、シンプルでコスト効率の高いソリューションです。 エラスティック プール内のデータベースは、単一の Azure SQL Database サーバー上にあり、設定された数のリソースを設定価格で共有します。

エラスティック プールは、[Azure Portal](sql-database-elastic-pool-manage-portal.md)、[PowerShell](sql-database-elastic-pool-manage-powershell.md)、[Transact-SQL](sql-database-elastic-pool-manage-tsql.md)、[C#](sql-database-elastic-pool-manage-csharp.md)、および REST API を使用して作成および管理できます。 エラスティック プールのリソースの単位は [eDTU](sql-database-what-is-a-dtu.md) です。


> [!NOTE]
> エラスティック プールは、現在プレビュー段階にあるインド西部を除くすべての Azure リージョンで一般公開 (GA) されています。  エラスティック プールは、このリージョンでもできるだけ早く一般公開される予定です。
>
>


## <a name="how-do-elastic-pools-help-manage-database-resources"></a>エラスティック プールは、データベース リソースの管理にどのように役立つでしょうか。

SaaS アプリケーションの一般的なパターンは、シングル テナント データベース モデルであり、顧客 (データベース) ごとにそれぞれ異なるデータベースが割り当てられます。 各顧客のメモリ、I/O、CPU のリソース要件は予測することができません。 このように需要の浮き沈みがある中で、コスト効率よくリソースを割り当てるには、どうしたらよいでしょうか。 従来、(1) ピーク時の使用量に基づくリソースの過剰プロビジョニングと、(2) ピーク時のパフォーマンスと顧客満足度を犠牲にしてコストを削減する過小プロビジョニングの&2; つの選択肢がありました。 エラスティック プールは、データベースが必要なパフォーマンス リソースを必要なときに確保できるようにすることで、この問題を解決します。 また、予測可能な予算の範囲内でシンプルなリソース割り当てメカニズムが提供されます。 エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

SQL Database では、リソースの需要に対処するデータベースの能力の相対尺度は、単一のデータベースのデータベース トランザクション ユニット (DTU) とエラスティック プール内のデータベースのエラスティック データベース トランザクション ユニット (eDTU) で表されます。 DTU と eDTU の詳細については、 [SQL Database の概要](sql-database-technical-overview.md) に関するページをご覧ください。

エラスティック プールには、設定価格に合わせて、設定された eDTU 数が与えられます。 プール内で、個々のデータベースには、設定されたパラメーターの範囲内で自動的にスケーリングを行う柔軟性が与えられます。 データベースは負荷が大きい場合、eDTU の使用量を増やして需要に対応します。 負荷が小さい場合、データベースは eDTU の使用量を減らし、負荷がなくなると eDTU を使用しません。 Single Database ではなく、プール全体に対してリソースをプロビジョニングすることで、管理タスクの簡略化を実現します。 さらに、プールにかかる予算を予測することができます。

データベースのダウンタイムなしで、既存のプールにさらに eDTU を追加できます。ただし、新たな eDTU 予約用の追加のコンピューティング リソースを提供するためにデータベースを移動する必要がある場合は例外です。 同様に、余分な eDTU が不要になった場合は、いつでも既存のプールから削除できます。

プールへのデータベースの追加、またはプールからの削除ができます。 データベースのリソース使用率が低いと予測できる場合は、プールから削除します。

## <a name="which-databases-go-in-a-pool"></a>どのデータベースをプールに移動しますか?
![エラスティック プール内で eDTU を共有する SQL データベース。][1]

エラスティック プールの候補として最適であるデータベースには通常、決まった期間のアクティビティがあり、その他の期間はアイドル状態です。 上図は、1 つのデータベース、4 つのデータベース、20 のデータベースによるエラスティック プールのアクティビティをそれぞれ示しています。 これらのデータベースは時間の経過と共にさまざまなアクティビティを行いますが、同時にアクティブにならず、eDTU を共有できるため、エラスティック プールの候補として最適です。 すべてのデータベースが、このパターンに適合するわけではありません。 リソースに常に一定の負荷があるデータベースは、リソースが個別に割り当てられる Basic、Standard、Premium サービス階層に適しています。

[エラスティック プールの価格およびパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)。

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>エラスティック プールの eDTU とストレージ制限

次の表では、Basic、Standard、および Premium のエラスティック プールの特性について説明します。

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

エラスティック プールのすべての DTU が使用されている場合は、プール内の各データベースが、同量のリソースを受け取ってクエリを処理します。  SQL Database サービスは、コンピューティング時間を均等にすることで、データベース間におけるリソース共有の公平性を実現します。 それ以外の場合、エラスティック プールのリソース共有の公平性は、データベースあたりの DTU分が&0; 以外の値に設定されているときに、リソース量に加えて各データベースに適用されることが保証されます。

## <a name="elastic-pool-properties"></a>エラスティック プールのプロパティ

次の表では、エラスティック プールとプールされたデータベースの上限について説明します。

### <a name="limits-for-elastic-pools"></a>エラスティック プールに関する制限事項
| プロパティ | Description |
|:--- |:--- |
| サービス階層 |Basic、Standard、Premium を選択できます。 サービス レベルにより、構成可能なパフォーマンス範囲とストレージ制限のほか、ビジネス継続性の選択肢が決定されます。 プール内の各データベースは、プールと同じサービス レベルになります。 "サービス レベル" は "エディション" とも呼ばれます。 |
| プールあたりの eDTU 数 |プール内のデータベースで共有できる eDTU の最大数。 プール内のデータベースで同時に使用する eDTU の合計数がこの制限を超えることはできません。 |
| プールあたりの最大ストレージ容量 (GB) |プール内のデータベースで共有できる最大ストレージ容量 (GB 単位)。 プール内のデータベースで使用するストレージの合計がこの制限を超えることはできません。 この制限は、プールごとの eDTU によって決定されます。 この制限を超えた場合、すべてのデータベースは読み取り専用になります。 プールあたりの最大ストレージ容量とは、プール内のデータ ファイルの最大ストレージ容量のことであり、ログ ファイルによって使用される領域は含みません。 |
| プールあたりのデータベースの最大数 |プールごとに許可されるデータベースの最大数。 |
| プールあたりの最大同時実行ワーカー数 |プール内のすべてのデータベースで使用可能な同時実行ワーカー (要求) の最大数。 |
| プールあたりの最大同時ログイン数 |プール内のすべてのデータベースに対する同時ログインの最大数。 |
| プールあたりの最大同時セッション数 |プール内のすべてのデータベースで使用可能なセッションの最大数。 |
|||

### <a name="limits-for-pooled-databases"></a>プールされたデータベースに関する制限事項
| プロパティ | Description |
|:--- |:--- |
| データベースあたりの最大 eDTU 数 |プール内の他のデータベースによる使用状況に基づいて使用可能な場合にプール内の任意のデータベースが使用できる eDTU の最大数。  データベースごとの最大 eDTU は、データベースに対して保証されたリソースではありません。  これは、プール内のすべてのデータベースに適用されるグローバル設定です。 データベースのピーク使用率を処理するのに十分高いデータベースあたり最大 eDTU 数を設定します。 プールでは通常、ホットとコールドのデータベース使用パターンがあり、すべてのデータベースが同時に最大に使用されることはないため、ある程度高めに上限が設定されています。 たとえば、データベースごとの最大使用量が 20 eDTU で、プールの 100 データベースの 20% のみが同時に最大で使用されるものとします。  データベースあたりの eDTU 上限が 20 eDTU に設定されている場合は、プールを 5 倍に設定し、プールあたりの eDTU 数を 400 に設定しておいても問題はありません。 |
| データベースあたりの最小 eDTU 数 |プール内の任意のデータベースで保証される eDTU の最小数。  これは、プール内のすべてのデータベースに適用されるグローバル設定です。 データベースあたりの最小 eDTU は 0 に設定でき、これが既定値です。 このプロパティは、0 とデータベースあたりの平均 eDTU 使用率の間に設定されます。 プール内のデータベースの数とデータベースごとの最小 eDTU の積がプールごとの eDTU の値を超えることはできません。  たとえば、プールに 20 のデータベースがあり、データベースあたりの最小 eDTU を 10 に設定する場合は、プールあたりの eDTU 数を少なくとも 200 eDTU にする必要があります。 |
| データベースあたりの最大ストレージ (GB) |プール内のデータベースの最大ストレージ。 プールされたデータベースはプール ストレージを共有するので、データベース ストレージは、残りのプール ストレージと、データベースあたりの最大ストレージのうち、どちらか小さい方に制限されます。 データベースあたりの最大ストレージ容量とは、データ ファイルの最大サイズのことであり、ログ ファイルによって使用される領域は含みません。 |
|||

## <a name="elastic-jobs"></a>エラスティック ジョブ
プールでは、**[エラスティック ジョブ](sql-database-elastic-jobs-overview.md)**でスクリプトを実行することで、管理タスクが簡素化されます。 エラスティック ジョブを使用すると、大量のデータベースに関連する面倒な作業のほとんどが不要になります。 まず、[エラスティック ジョブの概要](sql-database-elastic-jobs-getting-started.md)に関するページを参照してください。

複数のデータベースを操作するための他のデータベース ツールの詳細については、「[Azure SQL Database によるスケール アウト](sql-database-elastic-scale-introduction.md)」を参照してください。

## <a name="business-continuity-features-for-databases-in-a-pool"></a>プール内のデータベースのビジネス継続性機能
プールされたデータベースは、一般的に、単一データベースで使用できるのと同じ[ビジネス継続性機能](sql-database-business-continuity.md)をサポートしています。

### <a name="point-in-time-restore"></a>ポイントインタイム リストア
ポイントインタイム リストアは、自動データベース バックアップを使用して、プール内のデータベースを特定の時点に復元します。 「 [ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>地理リストア
Geo リストアは、データベースがホストされているリージョンでのインシデントのためにデータベースが利用できない場合にも既定の復旧オプションを提供します。 「 [Azure SQL Database を復元する、またはセカンダリにフェールオーバーする](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>アクティブ geo レプリケーションを選択するとき
geo リストアよりもアグレッシブな復旧要件があるアプリケーションについては、[Azure Portal](sql-database-geo-replication-portal.md)、[PowerShell](sql-database-geo-replication-powershell.md)、または [Transact-SQL](sql-database-geo-replication-transact-sql.md) を使用して、アクティブ geo レプリケーションを構成します。

## <a name="next-steps"></a>次のステップ

* エラスティック プールは、[Azure Portal](sql-database-elastic-pool-manage-portal.md)、[PowerShell](sql-database-elastic-pool-manage-powershell.md)、[Transact-SQL](sql-database-elastic-pool-manage-tsql.md)、[C#](sql-database-elastic-pool-manage-csharp.md)、および REST API を使用して作成および管理できます。
* エラスティック プールを使用するときのガイダンスについては、[エラスティック プールのガイダンス](sql-database-elastic-pool-guidance.md)を参照してください。
* ビデオについては、[Azure SQL Database のエラスティック機能に関する Microsoft Virtual Academy のビデオ コース](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)をご覧ください。

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

