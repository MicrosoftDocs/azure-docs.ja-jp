---
title: Azure SQL Data Warehouse でのワークロードの重要度の管理と監視 |Microsoft Docs
description: 要求レベルの重要度を管理および監視する方法について説明します。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 30afe1805748012b0a137c865c799580f79d31d8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588641"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのワークロードの重要度の管理と監視

DMV とカタログ ビューを使用して、Azure SQL Data Warehouse での要求レベルの重要度を管理および監視します。

## <a name="monitor-importance"></a>重要度を監視する

重要度の監視は [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 動的管理ビューの新しい重要度の列を使用して行います。
以下の監視クエリはクエリの送信時刻と開始時刻を表示します。 送信時刻と開始時刻を重要度と一緒に確認すれば、重要度がスケジュールに及ぼす影響を理解できます。

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

クエリがスケジュールされる方法をさらに詳しく調べるには、カタログ ビューを使用します。

## <a name="manage-importance-with-catalog-views"></a>カタログ ビューで重要度を管理する

カタログ ビュー sys.workload_management_workload_classifiers には、Azure SQL Data Warehouse インスタンスの分類子に関する情報が含まれています。 リソース クラスにマップされるシステム定義分類子を除外するには、次のコードを実行します。

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

カタログ ビュー [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest) には、分類子の作成で使用されるパラメーターに関する情報が含まれています。  次のクエリでは、ExecutiveReports という値を持つ ```membername``` パラメーターに対して ExecReportsClassifier が作成されたことを示しています。

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![クエリの結果](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

誤分類のトラブルシューティングを簡単にするため、ワークロード分類子を作成するときは、リソース クラス ロールのマッピングを削除することをお勧めします。 次のコードは、既存のリソース クラス ロールのメンバーシップを返します。 対応するリソース クラスから返された ```membername``` ごとに、sp_droprolemember を実行します。
ワークロード分類子を削除する前に存在を確認する例を次に示します。

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>次の手順
- 分類の詳細については、[ワークロード分類](sql-data-warehouse-workload-classification.md)に関するページを参照してください。
- 重要度の詳細については、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)に関するページを参照してください

> [!div class="nextstepaction"]
> [ワークロードの重要度の構成に移動](sql-data-warehouse-how-to-configure-workload-importance.md)
