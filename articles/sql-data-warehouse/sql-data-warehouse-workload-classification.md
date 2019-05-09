---
title: SQL Data Warehouse の分類 | Microsoft Docs
description: Azure SQL Data Warehouse で分類を使用してクエリのコンカレンシー、重要度、コンピューティング リソースを管理するためのガイダンスです。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 888a64de29178834fc47199a033eb6bc62858e57
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617752"
---
# <a name="sql-data-warehouse-workload-classification-preview"></a>SQL Data Warehouse のワークロード分類 (プレビュー)

この記事では、受信要求にリソース クラスと重要度を割り当てる SQL Data Warehouse のワークロード分類プロセスについて説明します。

> [!Note]
> ワークロード分類は、SQL Data Warehouse Gen2 でプレビューできます。 ワークロード管理の分類および重要度のプレビューは、公開日が 2019 年 4 月 9 日以降のビルド用です。  この日付より前のビルドは、ワークロード管理のテストに使用しないでください。  お使いのビルドがワークロード管理に対応しているかどうかを調べるには、SQL Data Warehouse インスタンスに接続された状態で「select @@version」を実行します。

## <a name="classification"></a>分類

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

ワークロード管理の分類では、[リソース クラス](resource-classes-for-workload-management.md#what-are-resource-classes)と[重要度](sql-data-warehouse-workload-importance.md)を割り当てて、ワークロード ポリシーを要求に適用することができます。

データ ウェアハウスのワークロードを分類するにはさまざまな方法がありますが、最も簡単で一般的な分類は読み込みとクエリです。 データを読み込むには、INSERT、UPDATE、DELETE ステートメントを使用します。  データをクエリするには、SELECT を使用します。 データ ウェアハウス ソリューションには、多くの場合、より高いリソース クラスにはより多くのリソースを割り当てるなど、読み込みアクティビティに関するワークロード ポリシーがあります。 クエリには、読み込みアクティビティに比べて重要度を低くするなど、異なるワークロード ポリシーを適用できます。

読み込みとクエリのワークロードを下位分類することもできます。 下位分類を使用すると、ワークロードをより細かく制御できます。 たとえば、クエリ ワークロードがキューブの更新、ダッシュボードのクエリ、アドホック クエリで構成されているとします。 これらのクエリ ワークロードをそれぞれ異なるリソース クラスや重要度の設定で分類できます。 読み込みも、下位分類の利点を受けることができます。 大規模な変換を大規模なリソース クラスに割り当てることができます。 より高い重要度を使用して、気象データやソーシャル データのフィードよりも先に主要な販売データを読み込むことができます。

リソースを必要としないか、実行に影響を及ぼす重要度を必要としない一部のステートメントは分類されません。  DBCC コマンド、BEGIN、COMMIT、および ROLLBACK TRANSACTION ステートメントは分類されません。

## <a name="classification-process"></a>分類プロセス

現在、SQL Data Warehouse での分類は、[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) を使用して、対応するリソース クラスが割り当てられたロールにユーザーを割り当てることで実現されます。 1 つのリソース クラスへのログインを超えて要求を特徴付けることは、この機能によって制限されます。 より高度な分類方法として、[CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql) 構文を利用できるようになりました。  SQL Data Warehouse ユーザーは、この構文を使用して要求に重要度とリソース クラスを割り当てることができます。  

> [!NOTE]
> 分類は要求単位で評価されます。 1 つのセッション内の複数の要求を別々に分類できます。

## <a name="classification-precedence"></a>分類の優先順位

分類プロセスの一環として、割り当てるリソース クラスを決定するための優先順位が用意されています。 データベース ユーザーに基づく分類は、ロール メンバーシップよりも優先されます。 たとえば、UserA データベース ユーザーを mediumrc リソース クラスにマップする分類子を作成します。 次に、(UserA がそのメンバーである) RoleA データベース ロールを largerc リソース クラスにマップします。 このデータベース ユーザーを mediumrc リソース クラスにマップする分類子は、RoleA データベース ロールを largerc リソース クラスにマップする分類子よりも優先されます。

ユーザーが複数のロールのメンバーであり、各ロールに異なるリソース クラスが割り当てられている場合、またはユーザーが複数の分類子に一致する場合、このユーザーには最も高いリソース クラスが割り当てられます。  この動作は、既存のリソース クラス割り当て動作と整合します。

## <a name="system-classifiers"></a>システム分類子

ワークロード分類には、システム ワークロード分類子が用意されています。 システム分類子は、既存のリソース クラス ロールのメンバーシップを通常の重要度でリソース クラスのリソース割り当てにマップします。 システム分類子は削除できません。 システム分類子を表示するには、次のクエリを実行します。

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>リソース クラス割り当てと分類子の混在

自動的に作成されるシステム分類子を利用すると、ワークロード分類に簡単に移行できます。 リソース クラス ロールのマッピングと分類の優先順位を併用すると、重要度を使用して新しい分類子を作成し始めるときに誤分類が起きやすくなります。

次のシナリオで考えてみましょう。

• 既存のデータ ウェアハウスで、データベース ユーザー DBAUser に largerc リソース クラス ロールが割り当てられています。 このリソース クラス割り当ては、sp_addrolemember を使用して行われました。
• ここで、データ ウェアハウスのワークロード管理が更新されました。
• 新しい分類構文をテストするため、(DBAUser がそのメンバーである) データベース ロール DBARole を mediumrc と高い重要度にマッピングする分類子を作成しました。
• DBAUser がログインしてクエリを実行すると、クエリは largerc に割り当てられます。 これは、ロールのメンバーシップよりもユーザーが優先されるためです。

誤分類のトラブルシューティングを簡単にするため、ワークロード分類子を作成するときは、リソース クラス ロールのマッピングを削除することをお勧めします。  次のコードは、既存のリソース クラス ロールのメンバーシップを返します。  対応するリソース クラスから返されたメンバー名ごとに、[sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) を実行します。

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>次の手順

SQL Data Warehouse のワークロード分類と重要度の詳細については、[ワークロード分類子の作成](quickstart-create-a-workload-classifier-tsql.md)と [SQL Data Warehouse の重要度](sql-data-warehouse-workload-importance.md)に関する記事をご覧ください。 クエリと割り当てられている重要度を確認するには、「[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)」を参照してください。
