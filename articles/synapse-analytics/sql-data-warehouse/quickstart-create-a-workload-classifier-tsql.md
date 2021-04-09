---
title: クイック スタート:ワークロード分類子を作成する - T-SQL
description: T-SQL を使用して重要度の高いワークロード分類子を作成します。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e757c8047bf6d634ab6d7cbc8963087c0eccc46a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677370"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>クイック スタート:T-SQL を使用してワークロード分類子を作成する

このクイック スタートでは、組織の CEO に対して重要度の高いワークロード分類子をすばやく作成します。 このワークロード分類子を使用すると、CEO のクエリを、キュー内の重要度の低いその他のクエリよりも優先することができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

> [!NOTE]
> Azure Synapse Analytics に専用 SQL プール インスタンスを作成すると、新しい課金対象サービスが発生する可能性があります。  詳細については、「[Azure Synapse Analytics の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」を参照してください。
>
>

## <a name="prerequisites"></a>前提条件

このクイックスタートは、Azure Synapse Analytics で専用 SQL プールが既にプロビジョニングされていて、CONTROL DATABASE 権限があることを前提としています。 作成する必要がある場合は、[ポータルでの作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前の専用 SQL プールを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-login-for-theceo"></a>TheCEO のログインを作成する

"TheCEO" に対して [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を使用して、`master` データベースで SQL Server 認証ログインを作成します。

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>ユーザーの作成

mySampleDataWarehouse で "TheCEO" という [ユーザーを作成します](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>ワークロード分類子を作成する

"TheCEO" に対して重要度の高い[ワークロード分類子](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)を作成します。

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>既存の分類子を表示する

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

データ ウェアハウス ユニットと専用 SQL プールに格納されているデータに対して課金されます。 これらのコンピューティングとストレージのリソースは別々に請求されます。

- データをストレージに保持しておく場合は、専用 SQL プールを使わない間、コンピューティング リソースを一時停止できます。 コンピューティングを一時停止すると、データ ストレージに対してのみ課金されます。 データを使用する準備ができたら、コンピューティングを再開します。
- それ以上課金されないようにする場合は、専用 SQL プールを削除できます。

以下の手順に従ってリソースをクリーンアップします。

1. [Azure portal](https://portal.azure.com) にサインインし、専用 SQL プールを選択します。

    ![リソースをクリーンアップする](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. コンピューティング リソースを一時停止するには、 **[一時停止]** ボタンを選択します。 専用 SQL プールが一時停止すると、ボタンの表示が **[開始]** になります。  コンピューティング リソースを再開するには、 **[開始]** を選択します。

3. コンピューティング リソースやストレージに課金されないように専用 SQL プールを削除するには、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- これで、ワークロード分類子が作成されました。 TheCEO としていくつかのクエリを実行して、それらがどのように実行されるかを確認します。 クエリと割り当てられている重要度を確認するには、「[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。
- 専用 SQL プールのワークロード管理の詳細については、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)と[ワークロードの分類](sql-data-warehouse-workload-classification.md)に関するページを参照してください。
- [ワークロードの重要度の構成](sql-data-warehouse-how-to-configure-workload-importance.md)と [Workload Management の管理と監視](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)に関するハウツー記事を参照してください。
