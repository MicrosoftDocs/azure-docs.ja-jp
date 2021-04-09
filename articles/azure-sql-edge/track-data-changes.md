---
title: Azure SQL Edge でデータ変更を追跡する
description: Azure SQL Edge での変更の追跡と変更データ キャプチャについて説明します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dddaad3e171c757b353deb81ffcb77cfbe706340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108264"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Azure SQL Edge でデータ変更を追跡する

Azure SQL Edge では、データベースのデータに対する変更を追跡する 2 つの SQL Server 機能である[変更の追跡](/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking)と[変更データ キャプチャ](/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture)がサポートされています。 これらの機能では、データベース内のユーザー テーブルに対して行われたデータ変更言語の変更 (挿入操作、更新操作、および削除操作) をアプリケーションで特定できます。 変更データ キャプチャと変更の追跡は、同じデータベースで有効にすることができます。 特に注意が必要な点はありません。

データベースで変更されたデータをクエリする機能は、一部のアプリケーションの効率を高めるための重要な要件です。 一般に、データ変更を確認するには、アプリケーション開発者がトリガー、timestamp 列、および追加のテーブルを組み合わせて使用することで、カスタムの追跡方法をアプリケーションに実装する必要があります。 通常、このようなアプリケーションを作成するには実装に非常に手間がかかり、スキーマの更新も必要になり、多くの場合、パフォーマンスのオーバーヘッドが増加します。

IoT ソリューションのケースにおいて、データをエッジからクラウドまたはデータセンターに定期的に移動する必要がある場合は、変更の追跡が非常に役立ちます。 ユーザーは、最後の同期からの変更についてのみ迅速かつ効果的にクエリを実行し、それらの変更をクラウドまたはデータセンター ターゲットにアップロードすることができます。 詳細については、「[変更データ キャプチャまたは変更の追跡を使用する利点](/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking)」を参照してください。 

これらの 2 つの機能は同じではありません。 詳細については、「[変更データ キャプチャと変更の追跡の機能の違い](/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)」を参照してください。

## <a name="change-data-capture"></a>変更データ キャプチャ

この機能の動作の詳細については、「[変更データ キャプチャについて](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)」を参照してください。

この機能を有効または無効にする方法については、「[変更データ キャプチャの有効化と無効化](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)」を参照してください。

この機能を管理および監視する方法については、「[変更データ キャプチャの管理と監視](/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)」を参照してください。

変更されたデータを照会および処理する方法については、[変更データの処理](/sql/relational-databases/track-changes/work-with-change-data-sql-server)に関する記事を参照してください。

> [!NOTE]
> CLR に依存する変更データ キャプチャ関数は Azure SQL Edge でサポートされていません。

## <a name="change-tracking"></a>変更の追跡

この機能の動作の詳細については、「[変更の追跡について](/sql/relational-databases/track-changes/about-change-tracking-sql-server)」を参照してください。

この機能を有効または無効にする方法については、「[変更の追跡の有効化と無効化](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)」を参照してください。

この機能を運用、監視、および管理するには、「[変更の追跡の管理](/sql/relational-databases/track-changes/manage-change-tracking-sql-server)」を参照してください。

変更されたデータを照会および処理する方法については、[変更データの処理](/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)に関する記事を参照してください。

## <a name="temporal-tables"></a>テンポラル テーブル

Azure SQL Edge では、SQL Server のテンポラル テーブル機能もサポートされています。 この機能 (*システムによってバージョン管理されたテンポラル テーブル* とも呼ばれます) には、任意の時点でテーブルに格納されているデータに関する情報を提供するためのサポートが組み込まれています。 この機能では、その時点で正しいデータのみに関する情報が提供されるだけではありません。

システム バージョン管理されたテンポラル テーブルは、データ変更の履歴を完全に保持し、特定の時点の分析を簡単に実行できるよう設計されたユーザー テーブルの一種です。 各行の有効期間はシステム (つまりデータベース エンジン) によって管理されているため、この種類のテンポラル テーブルは、システム バージョン管理されたテンポラル テーブルと呼ばれます。

すべてのテンポラル テーブルには、それぞれに `datetime2` データ型が明示的に定義されている 2 つの列があります。 これらの列は、"*期間*" 列と呼ばれます。 行が変更されるたびに各行の有効期間を記録するために、これら期間列はシステムからのみ使用されます。

テンポラル テーブルには、これらの期間列に加え、ミラー化されたスキーマを使用する別のテーブルへの参照も含まれています。 システムでは、このテーブルを使用して、テンポラル テーブルの行が更新または削除されるたびに、行の以前のバージョンを自動的に保存します。 現在 (実際) の行バージョンを保存するメインのテーブルが "*現行*" テーブルまたはテンポラル テーブルと簡単に呼ばれているのに対し、この追加のテーブルは、"*履歴*" テーブルと呼ばれています。 テンポラル テーブルの作成時に、ユーザーは既存の履歴テーブルを指定できます (スキーマに準拠している必要があります)。そうしない場合は、システムによって既定の履歴テーブルが作成されます。

詳細については、「[テンポラル テーブル](/sql/relational-databases/tables/temporal-tables)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Edge でのデータ ストリーミング](stream-data.md)
- [Azure SQL Edge での ONNX を使用した機械学習と AI](onnx-overview.md)
- [Azure SQL Edge へのレプリケーションを構成する](configure-replication.md)
- [Azure SQL Edge でのデータベースのバックアップと復元](backup-restore.md)
