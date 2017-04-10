---
title: "コールド データを Azure にアーカイブする - Stretch Database | Microsoft Docs"
description: "Stretch Database を使用して、透過的かつ安全にコールド データを Microsoft Azure クラウドに移行する方法について説明します。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: c360dc10-a02b-446f-91a0-278358f7a297
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: cfa45dbb2d6e7579ed151c0213d394581cb73c4d
ms.openlocfilehash: 0c4355eb424f169238f274241747acff4532d589
ms.lasthandoff: 01/24/2017


---
# <a name="introduction-to-stretch-database"></a>Stretch Database の概要
Stretch Database は、透過的かつ安全にコールド データを Microsoft Azure クラウドに移行します。

すぐに Stretch Database を開始する場合は、「 [[Stretch Database を有効にする] ウィザードを実行する方法の概要](sql-server-stretch-database-wizard.md)」を参照してください。

## <a name="what-are-the-benefits-of-stretch-database"></a>Stretch Database の利点
Stretch Database には次の利点があります。

### <a name="provides-cost-effective-availability-for-cold-data"></a>コールド データ用に、コスト効果の高い可用性を提供
SQL Server Stretch Database を使用して、ウォーム トランザクション データとコールド トランザクション データを SQL Server から Microsoft Azure に動的に拡張します。 通常のコールド データ ストレージとは異なり、データは常にオンラインであり、クエリに使用できます。 顧客の注文履歴のように大きなテーブルでも、高いコストをかけずに、データ リテンション期間のタイムラインを長くすることができます。 スケーリングのコストが高いオンプレミス ストレージよりも、低コストの Azure をお勧めします。 価格レベルを選択し、Azure ポータルで設定を構成することで、コストを管理します。 必要に応じてスケールアップまたはスケールダウンします。 詳細については、 [SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) に関するページを参照してください。

### <a name="doesnt-require-changes-to-queries-or-applications"></a>クエリまたはアプリケーションの変更を必要としない
オンプレミスであるか、クラウドに拡張されているかにかかわらず、SQL Server データにシームレスにアクセスできます。  管理者がデータを格納する場所を決定するポリシーを設定すると、SQL Server はバックグラウンドでデータ移行を処理します。 テーブル全体が常にオンラインであり、クエリ可能です。 また、Stretch Database は既存のクエリやアプリケーションを変更する必要がありません。データの場所は、アプリケーションに対して完全に透過的です。

### <a name="streamlines-on-premises-data-maintenance"></a>オンプレミス データのメンテナンスを合理化
オンプレミスのメンテナンスと、データ用のストレージを削減します。 オンプレミス データのバックアップはより高速に実行され、保守期間内に完了します。 データのクラウド部分のバックアップは自動的に実行されます。 オンプレミス ストレージのニーズは大幅に軽減されます。 Azure Storage は、オンプレミスの SSD に追加するよりも 80% 低いコストになる可能性があります。

### <a name="keeps-your-data-secure-even-during-migration"></a>移行中もデータの保護を維持
重要度の高いアプリケーションでも、クラウドに安全に拡張できるので安心です。 SQL Server の Always Encrypted 機能によって、移動中のデータも暗号化できます。 また、行レベルのセキュリティ (RLS) と他の高度な SQL Server セキュリティ機能も Stretch Database と連携してデータを保護できます。

## <a name="what-does-stretch-database-do"></a>Stretch Database の機能
SQL Server インスタンス、データベース、少なくとも&1; つのテーブルで Stretch Database を有効にすると、コールド データの Azure への移行が自動的に開始されます。

* 別個のテーブルにコールド データを格納する場合、テーブル全体を移行できます。
* テーブルにホット データとコールド データの両方が含まれている場合、移行する行を選択するフィルター関数を指定できます。

**既存のクエリとクライアント アプリを変更する必要はありません。** データの移行中も、ローカル データとリモート データの両方に対して、継続してシームレスにアクセスできます。 リモート クエリの場合は短時間の遅延がありますが、コールド データにクエリするときにのみ、このような遅延が発生します。

**データの損失が発生しません** 。 また、移行中に接続の問題が発生した場合でも、その問題に対応する再試行ロジックがあります。 動的管理ビューには、移行の状態が表示されます。

**データ移行を一時停止することができます** 。

![Stretch Database の概要][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>Stretch Database が適しているかどうか
次のような意見がある場合、Stretch Database で要件を満たし、問題を解決できる可能性があります。

| 意思決定者の場合 | データベース管理者の場合 |
| --- | --- |
| トランザクション データを長期間保存する必要がある。 |テーブルのサイズが大きくなり、管理が困難になっている。 |
| ときにはコールド データにクエリする必要がある。 |ユーザーはコールド データへのアクセスを希望しているが、ほとんど使用することはない。 |
| 古いアプリケーションを含め、更新する予定がないアプリケーションがある。 |次々と記憶域を購入し、追加し続ける必要がある。 |
| 記憶域にかかるコストを削減する方法を見つけたい。 |SLA 内でこのように大きなテーブルをバックアップまたは復元することができない。 |

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>Stretch Database の候補となるデータベースとテーブルの種類
Stretch Database は、大量のコールド データ (通常は少数のテーブルに格納されている) があるトランザクション データベースを対象としています。 たとえば、10 億行を超えるテーブルなどがあります。

SQL Server 2016 のテンポラル テーブルを使用している場合、Stretch Database を使用して、関連する履歴テーブルのすべてまたは一部をコスト効率のよい Azure の記憶域に移行します。 詳細については、「 [システム バージョン管理されたテンポラル テーブルの履歴データの保有期間管理](https://msdn.microsoft.com/library/mt637341.aspx)」を参照してください。

SQL Server 2016 Upgrade Advisor の 1 機能である Stretch Database Advisor を使用して、Stretch Database のデータベースとテーブルを特定します。 詳細については、「 [Stretch Database Advisor を実行して Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)」を参照してください。 潜在的なブロックの問題の詳細については、「 [Stretch Database のセキュリティ構成の制限とブロック問題](sql-server-stretch-database-limitations.md)」を参照してください。

## <a name="test-drive-stretch-database"></a>Stretch Database を試用する
**AdventureWorks サンプル データベースで Stretch Database を試用することができます。** AdventureWorks サンプル データベースを入手するには、[こちら](https://www.microsoft.com/download/details.aspx?id=49502)から、少なくともデータベース ファイル、サンプル ファイル、スクリプト ファイルをダウンロードします。 サンプル データベースを SQL Server 2016 のインスタンスに復元した後に、サンプル ファイルを解凍し、Stretch DB フォルダーの Stretch DB Samples ファイルを開きます。 このファイルのスクリプトを実行して、Stretch Database を有効にする前後でデータに使用される領域を確認します。同時に、データ移行の進行状況を追跡することで、データの移行中も移行後も、継続して既存のデータの照会と新しいデータの挿入ができていることを確認します。

## <a name="next-step"></a>次のステップ
**Stretch Database の候補となるデータベースとテーブルを特定します。** SQL Server 2016 Upgrade Advisor をダウンロードし、Stretch Database Advisor を実行して、Stretch Database の候補となるデータベースとテーブルを特定します。 Stretch Database Advisor はブロック問題も特定します。 詳細については、「 [Stretch Database Advisor を実行して Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)」を参照してください。

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

