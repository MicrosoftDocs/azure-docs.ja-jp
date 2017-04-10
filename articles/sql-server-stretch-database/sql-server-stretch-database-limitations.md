---
title: "Stretch Database の制限事項 | Microsoft Docs"
description: "Stretch Database の制限事項について説明します。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 0a865872-7d42-4873-99b9-cbae45691e54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: anvang
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c1599dfc6aee8adf7ec8714c392a7d8d4427d78


---
# <a name="limitations-for-stretch-database"></a>Stretch Database の制限事項
Stretch が有効なテーブルの制限事項と、テーブルの Stretch の有効化を現在妨げている制限事項について説明します。

## <a name="a-namecaveatsa-limitations-for-stretch-enabled-tables"></a><a name="Caveats"></a> Stretch が有効なテーブルの制限事項
Stretch が有効なテーブルの制限事項を次に示します。

### <a name="constraints"></a>制約
* 移行されたデータが含まれる Azure テーブルの UNIQUE 制約と PRIMARY KEY 制約では一意性が強制されません。

### <a name="dml-operations"></a>DML 操作
* Stretch が有効なテーブル、または Stretch が有効なテーブルが含まれるビューでは、移行されている行、または移行する資格がある行は、更新も削除できません。
* リンクされているサーバーの Stretch が有効なテーブルに行を挿入することはできません。

### <a name="indexes"></a>インデックス
* Stretch が有効なテーブルを含むビューのインデックスを作成することはできません。
* SQL Server インデックスのフィルターは、リモート テーブルには反映されません。

## <a name="a-namelimitationsa-limitations-that-currently-prevent-you-from-enabling-stretch-for-a-table"></a><a name="Limitations"></a> テーブルの Stretch の有効化を現在妨げている制限事項
テーブルの Stretch の有効化を現在妨げている制限事項を次に示します。

### <a name="table-properties"></a>テーブルのプロパティ
* 1,023 よりも多くの列または 998 よりも多くのインデックスが含まれるテーブル
* FILESTREAM データが格納されている FileTable またはテーブル
* レプリケートされたテーブル、または変更追跡あるいは Change Data Capture が現在使用されているテーブル
* メモリ最適化テーブル

### <a name="data-types"></a>データの種類
* text、ntext、および image
* timestamp
* sql\_variant
* XML
* CLR データ型。geometry、geography、hierarchyid、CLR ユーザー定義型など

### <a name="column-types"></a>列の型
* COLUMN\_SET
* 計算列

### <a name="constraints"></a>制約
* 既定の制約と CHECK 制約
* テーブルを参照する外部キー制約。 親子関係 \(Order と Order\_Detail など\) では、子テーブル \(Order\_Detail\) については Stretch を有効にできますが、親テーブル \(Order\) に対して有効にすることはできません。

### <a name="indexes"></a>インデックス
* フル テキスト インデックス
* XML インデックス
* 空間インデックス
* テーブルを参照するインデックス付きビュー

## <a name="see-also"></a>関連項目
[Stretch Database Advisor を実行して Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)

[データベースの Stretch Database を有効にする](sql-server-stretch-database-enable-database.md)

[テーブルの Stretch Database を有効にする](sql-server-stretch-database-enable-table.md)




<!--HONumber=Nov16_HO3-->


