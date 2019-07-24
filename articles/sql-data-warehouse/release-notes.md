---
title: Azure SQL Data Warehouse リリース ノート | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
ms.openlocfilehash: e77556ac0d6f64797906c0f3b4181f147b1668e2
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448424"
---
# <a name="azure-sql-data-warehouse-release-notes-and-documentation-updates"></a>Azure SQL Data Warehouse リリース ノートとドキュメントの更新

Azure SQL Data Warehouse (SQL DW) は、超並列処理 (MPP) を利用して、ペタバイト単位のデータに対して複雑なクエリを短時間で実行するクラウドベースのエンタープライズ データ ウェアハウスです。 ビッグ データ ソリューションの主要コンポーネントとして SQL Data Warehouse を使用してください。 簡単な PolyBase T-SQL クエリで SQL Data Warehouse にビッグ データをインポートしてから、MPP の機能を利用して高パフォーマンスの分析を実行します。 統合と分析を進めると、企業が洞察時に信頼できる 1 つの真実がデータ ウェアハウスに構築されます。

最新バージョンの Azure SQL Data Warehouse で利用できる新しい機能と機能強化について詳しくは、以下のリンクをクリックしてください。 示されているメンテナンス スケジュールの間に、これらのサービスの更新プログラムを受け取ることができます。

- [2019 年 3 月](./release-notes-10-0-10106-0.md#march-2019)
- [2019 年 1 月](./release-notes-10-0-10106-0.md#january-2019)
- [2018 年 12 月](./release-notes-10-0-10106-0.md#december-2018)
- [2018 年 10 月](./release-notes-10-0-10106-0.md#october-2018)
- [2018 年 9 月](./release-notes-september-2018.md)
- [2018 年 8 月](./release-notes-august-2018.md)
- [2018 年 7 月](./release-notes-july-2018.md)
- [2018 年 6 月](./release-notes-june-2018.md)
- [2018 年 5 月](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>お使いのデータ ウェアハウスに適用されたコード バージョンの確認

お使いのデータ ウェアハウスに適用されているリリースを確認するには。 SSMS 経由でお使いのデータ ウェアハウスに接続して、次の構文を実行すると、SQL Data Warehouse の現在のバージョンが戻ります。

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

出力例:![SQL Data Warehouse のバージョン](./media/release-notes/sql_data_warehouse_version.png)

示された日付を使用して、お使いの Azure SQL Data Warehouse に適用されているリリースを確認してください。 


## <a name="next-steps"></a>次の手順
- メンテナンス スケジュールを表示する方法について[詳しく知る](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule)。 
- メンテナンス スケジュールを変更する方法について[詳しく知る](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule)。
- Azure Monitor を使用してアラートを作成、表示、管理する方法について[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric)。
- ログ アラート ルール用の Webhook アクションについて[詳しく知る](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。
- Azure Service Health の詳細については、[さらに理解する](https://docs.microsoft.com/azure/service-health/service-health-overview)