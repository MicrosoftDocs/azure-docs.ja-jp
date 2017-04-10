---
title: "Azure SQL Data Warehouse でのデータベースの管理 | Microsoft Docs"
description: "SQL Data Warehouse のデータベース管理の概要について説明します。 ここでは管理ツール、DWU およびパフォーマンスのスケールアウト、クエリ パフォーマンスのトラブルシューティング、適切なセキュリティ ポリシーの設定、データの破損や地域的障害からのデータベースの復旧について見ていきます。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 0056c458cc002cbdb1b8dabefe0d11a8abd61179
ms.lasthandoff: 03/29/2017


---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのデータベースの管理
SQL Data Warehouse を利用すると、データベース管理の自動化を進めることができます。 たとえば、パフォーマンスのスケーリングについては、調整後、最適なコンピューティング リソースの料金をお支払いいただけば、あとは SQL Data Warehouse がパフォーマンスの拡張/縮小をすべて行います。

ワークロードを監視してパフォーマンス ニーズを特定したり、実行時間の長いクエリのトラブルシューティングを行ったりしたいとお考えの方が多いと思います。 また、セキュリティ タスクをいくつか実行して、ユーザーやログインのアクセス許可を管理したい方もいらっしゃるかもしれません。

この概要では、SQL Data Warehouse の管理に関する次の項目について説明します。

* 管理ツール
* コンピューティングのスケーリング
* 一時停止と再開
* パフォーマンスのベスト プラクティス
* クエリの監視
* セキュリティ
* バックアップと復元

## <a name="management-tools"></a>管理ツール
SQL Data Warehouse では、さまざまなツールを使用してデータベースを管理することができます。 データベースを管理するにつれて、実行するタスクのタイプに応じたお好みのツールがはっきりしてくることでしょう。

### <a name="azure-portal"></a>Azure ポータル
[Azure Portal][Azure portal] は、データベースの作成、更新、削除、およびデータベース リソースの監視を行うことができる Web ベースの管理ポータルです。 このツールは、Azure を使い始めたばかりの場合や、管理対象のデータ ウェアハウス データベースの数が少ない場合、またはすばやく作業を行う必要がある場合に便利です。

Azure Portal の使用については、[SQL Data Warehouse の作成 (Azure Portal)][Create a SQL Data Warehouse (Azure portal)] に関する記事をご覧ください。

### <a name="sql-server-data-tools-in-visual-studio"></a>Visual Studio での SQL Server Data Tools
Visual Studio の [SQL Server Data Tools][SQL Server Data Tools] (SSDT) を使用すると、データベースへの接続、管理、および開発を行うことができます。 Visual Studio やその他の統合開発環境 (IDE) を使い慣れているアプリケーション開発者には、Visual Studio の SSDT の使用をお勧めします。

SSDT には SQL Server Object Explorer が含まれており、これを使用して SQL Data Warehouse データベースに対する視覚化、接続、およびスクリプト実行を行うことができます。 SQL Data Warehouse にすばやく接続するには、Azure クラシック ポータルにデータベース詳細が表示されているときに、コマンド バーで [ **Visual Studio で開く** ] ボタンをクリックするだけです。  

Visual Studio の SSDT を使用する手順については、[Visual Studio を使用した Azure SQL Data Warehouse に対するクエリ][Query Azure SQL Data Warehouse with Visual Studio]に関する記事をご覧ください。

### <a name="command-line-tools"></a>コマンド ライン ツール
コマンド ライン ツールは、ワークロードの自動化に最適なツールです。  PowerShell と sqlcmd は、プロセスの自動化に最適です。  多数の論理サーバーを管理したり、運用環境でリソースの変更をデプロイしたりする場合は、タスクをスクリプト化した後、自動化する必要があるため、これらのツールを使用することをお勧めします。

### <a name="dynamic-management-views"></a>動的管理ビュー
SQL Data Warehouse の管理において最も重要なのが動的管理ビュー (DMV) です。 ポータルに表示されるほぼすべての情報がDMV に依存しています。 SQL Data Warehouse の動的管理ビュー (DMV) の一覧を確認するには、[SQL Data Warehouse のシステム ビュー][SQL Data Warehouse system views]に関する記事をご覧ください。

最初に、[SQLCMD を使用した接続とクエリ][Connect and query with sqlcmd]に関する記事と、[データベースの作成 (PowerShell)][Create a database (PowerShell)] をご覧ください。

## <a name="scale-compute"></a>コンピューティングのスケーリング
SQL Data Warehouse では、CPU、メモリ、および I/O 帯域幅のコンピューティング リソースを増減させることにより、パフォーマンスの拡張と縮小をすばやく行うことができます。 パフォーマンスのスケーリングに必要なことは、SQL Data Warehouse からデータベースに割り当てられている Data Warehouse ユニット (DWU) の数を調整するだけです。 SQL Data Warehouse がすばやく変更を行い、ハードウェアやソフトウェアに対する基本的な変更をすべて処理します。

DWU のスケーリングの詳細については、「[パフォーマンスのスケーリング]」をご覧ください。

## <a name="pause-and-resume"></a>一時停止と再開
コストを節約するために、オンデマンドでコンピューティング リソースを一時停止および再開できます。 たとえば、夜間と週末にデータベースを使用しない場合、その期間にデータベースを一時停止して、日中に再開することができます。 データベースが一時停止されている間は、DWU に対して課金されません。

詳細については、「[コンピューティングの一時停止][Pause compute]」と「[コンピューティングの再開][Resume compute]」をご覧ください。

## <a name="performance-best-practices"></a>パフォーマンスのベスト プラクティス
新しいテクノロジーを初めて利用する際には、使い始めから役立つヒントやコツを知っておくことで、時間を大幅に節約できます。  ここではさまざまなトピックを通じて、ベスト プラクティスをご紹介します。

ワークロードの開発において最も重要な検討事項をまとめたものについては、[SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse Best Practices]に関する記事をご覧ください。

## <a name="query-monitoring"></a>クエリの監視
実行時間が長いクエリの特定でお困りの方はいませんか。 SQL Data Warehouse なら、動的管理ビュー(DMV) を使用して該当するクエリを特定することができます。

実行時間が長いクエリの特定については、「[DMV を利用してワークロードを監視する][Monitor your workload using DMVs]」をご覧ください。

## <a name="security"></a>セキュリティ
システムのセキュリティを維持するには、すべてのタイプの未承認のアクセスに対する警戒を強め、システムを保護していくことが重要です。 セキュリティ システムでは、承認済みの IP アドレスだけが接続できるようにファイアウォールのルールを適用することが重要です。 そのためには、ユーザーの資格情報を適切に認証する必要があります。 データベースに接続したユーザーに実行が許可されるアクションは、最小限にとどめておくべきです。 データの保護には暗号化がお勧めです。 疑わしいアクティビティが発生した場合にイベントをさかのぼれるように、監査と追跡を行うことも重要です。

セキュリティの管理については、[セキュリティの概要][Security overview]に関する記事をご覧ください。

## <a name="backup-and-restore"></a>バックアップと復元
信頼性の高いデータのバックアップは、あらゆる実稼働データベースで必要不可欠です。 SQL Data Warehouse では、一定の間隔でアクティブなデータベースを自動的にバックアップして、データを安全に保ちます。 データが破損したときや、データまたはデータベースを誤って削除したときは、これらのバックアップを使用して復旧することができます。  データのバックアップ スケジュール、保持ポリシー、データベースの復元方法については、[スナップショットからの復元][Restore from snapshot]に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
優れたデータベース設計方針を使用することにより、SQL Data Warehouse でのデータベース管理が容易になります。 詳細については、[開発の概要][Development overview]に関する記事を参照してください。

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[パフォーマンスのスケーリング]: sql-data-warehouse-manage-compute-overview.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/

