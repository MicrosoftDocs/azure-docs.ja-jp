---
title: "XTP インメモリ ストレージの監視 | Microsoft Docs"
description: "XTP インメモリ ストレージの使用量と容量を推定し、監視します。また、容量不足エラー 41823 を解決します。"
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jodebrui
ms.openlocfilehash: e953b60493c5a7c7a7ad74533471bd321d42abef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-in-memory-oltp-storage"></a>インメモリ OLTP ストレージの監視
[インメモリ OLTP](sql-database-in-memory.md) を使用している場合、メモリ最適化テーブルおよびテーブル変数内のデータは、インメモリ OLTP ストレージに格納されています。 Premium サービス レベルには、それぞれインメモリ OLTP ストレージの最大サイズがあります。詳しくは、[単一データベースのリソース制限に関する記事](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels)および[エラスティック プールのリソース制限に関する記事](sql-database-resource-limits.md#elastic-pool-change-storage-size)をご覧ください。 この上限を超過すると、挿入操作や更新操作が (エラー 41823 で) 失敗することがあります。 その場合は、データを削除してメモリを解放するか、データベースのパフォーマンス階層をアップグレードする必要があります。

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>データがインメモリ ストレージの上限に収まるかどうかを判断する
さまざまな Premium サービス階層のストレージの上限を確認します。 [単一データベースのリソース制限に関する記事](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels)および[エラスティック プールのリソース制限に関する記事](sql-database-resource-limits.md#elastic-pool-change-storage-size)をご覧ください。

メモリ最適化テーブルのメモリ必要量の推定は、Azure SQL Database で SQL Server の要件を推定する場合と同じように行います。 少し時間をとって、 [MSDN](https://msdn.microsoft.com/library/dn282389.aspx)でメモリ最適化テーブルのメモリ必要量の推定について確認してください。

テーブル行とテーブル変数行、およびインデックスは、最大ユーザー データ サイズにカウントされるので注意してください。 また、テーブル全体とそのインデックスの新しいバージョンを作成するには、ALTER TABLE に十分な領域が必要になります。

## <a name="monitoring-and-alerting"></a>監視とアラート
[Azure Portal](https://portal.azure.com/) で、インメモリ ストレージの使用量をパフォーマンス階層のストレージ上限に対するパーセンテージとして監視できます。 

1. [データベース] ブレードの [リソース使用率] ボックスで [編集] をクリックします。
2. メトリック `In-Memory OLTP Storage percentage` を選択します。
3. アラートを追加するには、[リソース使用率] チェック ボックスをオンにして [メトリック] ブレードを開き、[アラートの追加] をクリックします。

または、次のクエリを使用して、インメモリ ストレージの使用率を表示します。

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>メモリ不足状況の修正 - エラー 41823
メモリが不足すると、INSERT、UPDATE、および CREATE 操作が失敗し、エラー メッセージ 41823 が表示されます。

エラー メッセージ 41823 は、メモリ最適化テーブルとテーブル変数が最大サイズを超えたことを示しています。

このエラーを解決するには、次のいずれかを実行します。

* 従来のディスク ベース テーブルにデータをオフロードするなどして、メモリ最適化テーブルからデータを削除します。
* メモリ最適化テーブルにデータを残す必要がある場合は、十分なインメモリ ストレージがあるサービス階層にアップグレードします。

## <a name="next-steps"></a>次のステップ
管理のガイダンスについては、「[動的管理ビューを使用した Azure SQL Database の監視](sql-database-monitoring-with-dmvs.md)」をご覧ください。
