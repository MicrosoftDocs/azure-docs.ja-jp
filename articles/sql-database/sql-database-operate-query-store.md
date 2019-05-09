---
title: Azure SQL Database のクエリ ストアの動作
description: Azure SQL Database でクエリ ストアがどのように動作するかについて説明します
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 3ceb8569d952f2947870ce7314f869623b2d87f9
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562948"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Azure SQL Database のクエリ ストアの動作

Azure のクエリ ストアは、すべてのクエリに関する詳細な履歴情報を継続的に収集して提示する、フル マネージドのデータベース機能です。 クエリ ストアは、航空機のフライト データ レコーダーに似ていると考えることができます。この機能によって、クラウドとオンプレミスのユーザーの両方が、クエリ パフォーマンスのトラブルシューティングを大幅に簡素化できます。 この記事では、Azure でのクエリ ストアの動作の特定の側面について説明します。 この事前収集されたクエリ データを使用してパフォーマンスの問題をすばやく診断して解決することで、業務に向ける時間を増やすことができます。 

クエリ ストアは、2015 年 11 月以降、Azure SQL Database で [グローバルに使用できる](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) ようになっています。 クエリ ストアは、 [SQL Database Advisor やパフォーマンス ダッシュボード](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)などのパフォーマンス分析とチューニング機能の基盤です。 この記事の発行時点で、クエリ ストアは、Azure の 200,000 台を超えるユーザー データベースで実行され、クエリに関連する情報を、数か月にわたって中断することなく収集し続けています。

> [!IMPORTANT]
> Microsoft では、すべての Azure SQL Database (既存と新規) でクエリ ストアをアクティブ化するプロセスを続けています。 

## <a name="optimal-query-store-configuration"></a>クエリ ストアの最適構成

このセクションでは、クエリ ストアと、 [SQL Database Advisor やパフォーマンス ダッシュボード](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)などの依存機能の信頼できる動作を保証するように考えられた、最適構成の既定値について説明します。 既定の構成は、データ収集が継続的に実施される (OFF/READ_ONLY 状態の時間が最小限になる) ように最適化されています。

| 構成 | 説明 | 既定値 | Comment (コメント) |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |クエリ ストアがユーザーのデータベース内で使用するデータ領域の制限を指定します。 |100 |新しいデータベースに適用 |
| INTERVAL_LENGTH_MINUTES |クエリ プランで収集されたランタイム統計が集計されて保存される間隔を定義します。 すべてのアクティブなクエリ プランには、この構成で定義された期間の行が最大で 1 行含まれます。 |60 |新しいデータベースに適用 |
| STALE_QUERY_THRESHOLD_DAYS |保存されたランタイム統計と非アクティブなクエリのリテンション期間を制御する、時間に基づくクリーンアップ ポリシー |30 |新しいデータベースと前の既定値 (367) を持つデータベースに適用 |
| SIZE_BASED_CLEANUP_MODE |クエリ ストアのデータ サイズが制限値に近づいたときに、データの自動クリーンアップが発生するかどうかを指定します |AUTO |すべてのデータベースに適用 |
| QUERY_CAPTURE_MODE |すべてのクエリを追跡するか、クエリのサブセットのみを追跡するかを指定します |AUTO |すべてのデータベースに適用 |
| FLUSH_INTERVAL_SECONDS |キャプチャされたランタイム統計がディスクにフラッシュされる前に、メモリ内に保持される最大期間を指定します |900 |新しいデータベースに適用 |
|  | | | |

> [!IMPORTANT]
> 上記の既定値は、すべての Azure SQL Database でのクエリ ストアのアクティブ化の最終段階で自動的に適用されます (上記の重要な注意事項を参照してください)。 アクティブ化された後、ユーザーによって設定された構成値は、主要なワークロードまたはクエリ ストアの信頼できる動作に悪影響を与えない限り、Azure SQL Database によって変更されることはありません。

カスタム設定を維持する場合は、 [ALTER DATABASE とクエリ ストア オプション](https://msdn.microsoft.com/library/bb522682.aspx) を使用して、構成を前の状態に戻します。 「 [クエリ ストアを使用する際の推奨事項](https://msdn.microsoft.com/library/mt604821.aspx) 」で、よく選ばれている最適構成のパラメーターを確認してください。

## <a name="next-steps"></a>次の手順

[SQL Database Performance Insight](sql-database-performance.md)

## <a name="additional-resources"></a>その他のリソース

詳細については、次の記事を参照してください。

- [データベースのためのフライト データ レコーダー](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [クエリ ストアを使用したパフォーマンスの監視](https://msdn.microsoft.com/library/dn817826.aspx)
- [クエリ ストアの使用シナリオ](https://msdn.microsoft.com/library/mt614796.aspx)
