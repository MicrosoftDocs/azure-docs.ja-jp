---
title: 長期のバックアップ リテンション期間
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database と Azure SQL Managed Instance が、長期リテンション ポリシーにより最大で 10 年間、データベースの完全バックアップの格納をサポートする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: shkale
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: b1ba3f98f39511bcebf94502ed749d7cba1fc71b
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184051"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>長期リテンション - Azure SQL Database と Azure SQL Managed Instance

多くのアプリケーションで、規制、コンプライアンス、またはその他のビジネス上の目的で、Azure SQL Database と Azure SQL Managed Instance の[自動バックアップ](automated-backups-overview.md)によって提供される 7 ～ 35 日間を超えて、データベースのバックアップを保持する必要があります。 長期保有 (LTR) 機能を使用すると、指定された SQL Database と SQL Managed Instance の完全バックアップを、最大で 10 年間、[冗長性を設定](automated-backups-overview.md#backup-storage-redundancy)して Azure Blob Storage に格納することができます。 その後、LTR バックアップを新しいデータベースとして復元できます。

長期リテンションは Azure SQL Database に対して有効にすることができ、Azure SQL Managed Instance に対しては限定パブリック プレビュー段階にあります。 この記事では、長期リテンションの概念の概要を説明します。 長期リテンションを構成するには、[Azure SQL Database LTR の構成](long-term-backup-retention-configure.md)と [Azure SQL Managed Instance LTR の構成](../managed-instance/long-term-backup-retention-configure.md)に関するページを参照してください。 

> [!NOTE]
> SQL エージェント ジョブを使用すれば、LTR の代替手段として、[コピーのみのデータベース バックアップ](/sql/relational-databases/backup-restore/copy-only-backups-sql-server)を 35 日間以上スケジュールすることができます。

> [!IMPORTANT]
> マネージド インスタンスでの長期リテンションは現在、Azure パブリック リージョンでのみパブリック プレビューで利用できます。 


## <a name="how-long-term-retention-works"></a>長期リテンションのしくみ
     
長期的なバックアップ保有期間 (LTR) は、ポイントインタイム リストア (PITR) を有効にするために[自動的に作成された](automated-backups-overview.md)完全なデータベース バックアップを利用します。 LTR ポリシーが構成されている場合、これらのバックアップは長期保存用に異なるストレージ BLOB にコピーされます。 コピーは、データベースのワークロードのパフォーマンスに影響しないバック グラウンド ジョブです。 SQL Database では、各データベースの LTR ポリシーで LTR バックアップを作成する頻度も指定できます。

LTR を有効にするために、ポリシーの定義には、毎週のバックアップ リテンション期間 (W)、毎月のバックアップ リテンション期間 (M)、毎年のバックアップ リテンション期間 (Y)、年度の通算週 (WeekOfYear) の 4 つのパラメーターの組み合わせを使用できます。 W を指定すると、毎週 1 つのバックアップが長期ストレージにコピーされます。 M を指定すると、毎月の最初のバックアップが長期ストレージにコピーされます。 Y を指定すると、WeekOfYear によって指定された週に 1 つのバックアップが長期ストレージにコピーされます。 指定した WeekOfYear がポリシーの構成時点で既に過去である場合、初回の LTR バックアップは次の年に作成されます。 各バックアップは、LTR バックアップの作成時に構成されるポリシー パラメーターに従って、長期ストレージに保持されます。

> [!NOTE]
> LTR ポリシーへの変更内容は、今後のバックアップにのみ適用されます。 たとえば、毎週のバックアップ リテンション期間 (W)、毎月のバックアップ リテンション期間 (M)、または毎年のバックアップ リテンション期間 (Y) が変更された場合、新しいリテンション期間の設定は新しいバックアップにのみ適用されます。 既存のバックアップのリテンション期間は変更されません。 リテンション期間の期限が切れる前に古い LTR バックアップを削除したい場合は、[バックアップを手動で削除](./long-term-backup-retention-configure.md#delete-ltr-backups)する必要があります。
> 

LTR ポリシーの例:

-  W=0、M=0、Y=5、WeekOfYear=3

   毎年、第 3 週の完全バックアップが 5 年間保持されます。
   
- W=0、M=3、Y=0

   毎月、最初の完全バックアップが 3 か月間保持されます。

- W=12、M=0、Y=0

   毎週、完全バックアップが 12 週間保持されます。

- W=6、M=12、Y=10、WeekOfYear=16

   毎週、完全バックアップが 6 週間保持されます。 ただし、各月の最初の完全バックアップについては、12 か月間保持されます。 また、各年の第 16 週に作成された完全バックアップについては、10 年間保持されます。 

以下の表は、次のポリシーの長期バックアップの周期と有効期限を示しています。

W=12 weeks (84 日)、M=12 months (365 日)、Y=10 years (3650 日)、WeekOfYear=15 (4 月 15 日の週)

   ![LTR の例](./media/long-term-retention-overview/ltr-example.png)


このポリシーを変更して、W = 0 (毎週のバックアップなし) を設定すると、上記の表のバックアップ コピーの周期は、強調表示された日付のように変更されます。 それに応じて、これらのバックアップの保持に必要なストレージ容量は減ります。 

> [!IMPORTANT]
> 個々の LTR バックアップのタイミングは、Azure によって制御されます。 手動で LTR バックアップを作成またはバックアップの作成タイミングを制御することはできません。 LTR ポリシーを構成した後、最初の LTR バックアップが利用可能なバックアップの一覧に表示されるまで、最大 7 日かかる場合があります。  


## <a name="geo-replication-and-long-term-backup-retention"></a>geo レプリケーションと長期のバックアップ リテンション期間

ビジネス継続性のソリューションとしてアクティブ geo レプリケーションやフェールオーバー グループを使用している場合、最終的なフェールオーバーを準備して、セカンダリ データベースまたはインスタンス上に同じ LTR ポリシーを構成する必要があります。 バックアップがセカンダリから生成されないときに、LTR ストレージのコストが増加することはありません。 セカンダリがプライマリになった場合にのみ、バックアップが作成されます。 フェールオーバーがトリガーされ、プライマリがセカンダリ リージョンに移動するときに、LTR バックアップの中断されない生成が保証されます。 

> [!NOTE]
> 元のプライマリ データベースをフェールオーバーの原因となる停止から回復させると、これが新しいセカンダリになります。 したがって、バックアップの作成は再開することなく、既存の LTR ポリシーは、もう一度プライマリになるまで反映されることはありません。 


## <a name="configure-long-term-backup-retention"></a>長期のバックアップ リテンション期間の構成

Azure SQL Database には Azure portal を使用し、Azure SQL Managed Instance には PowerShell を使用して、長期のバックアップ リテンション期間を構成できます。 LTR ストレージからデータベースを復元するために、特定のバックアップを、そのタイムスタンプに基づいて選択することができます。 データベースは、元のデータベースと同じサブスクリプションの既存のサーバーまたはマネージド インスタンスに復元できます。

Azure portal または PowerShell を使用して、長期のリテンション期間を構成したり、SQL Database のバックアップからデータベースを復元したりする方法については、「[Azure SQL Database の長期的なバックアップ保有期間を管理する](long-term-backup-retention-configure.md)」を参照してください。

PowerShell を使用して、長期のリテンション期間を構成したり、SQL Managed Instance のバックアップからデータベースを復元したりする方法については、[Azure SQL Managed Instance の長期的なバックアップ保有期間の管理](../managed-instance/long-term-backup-retention-configure.md)に関するページを参照してください。

LTR ストレージからデータベースを復元するために、特定のバックアップを、そのタイムスタンプに基づいて選択することができます。 データベースは、元のデータベースと同じサブスクリプションの既存のサーバーに復元できます。 Azure portal または PowerShell を使用して LTR バックアップからデータベースを復元する方法については、「[Azure SQL Database の長期的なバックアップ保有期間を管理する](long-term-backup-retention-configure.md)」を参照してください。 

## <a name="next-steps"></a>次のステップ

データベース バックアップは、データの不慮の破損または削除から保護するため、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 

- その他の SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関する記事を参照してください。
- サービスによって生成された自動バックアップについては、[自動バックアップ](../database/automated-backups-overview.md)に関する記事を参照してください。
