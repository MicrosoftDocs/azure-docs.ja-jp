---
title: クイック スタート:ワークロード分類子の作成 - T-SQL | Microsoft Docs
description: T-SQL を使用して重要度の高いワークロード分類子を作成します。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: f400989fdbdede4f4a07ee13c5a606d51529150c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588703"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>クイック スタート:T-SQL を使用してワークロード分類子を作成する

このクイック スタートでは、組織の CEO に対して重要度の高いワークロード分類子をすばやく作成します。 このワークロード分類子を使用すると、CEO のクエリを、キュー内の重要度の低いその他のクエリよりも優先することができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

> [!NOTE]
> SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。  詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」を参照してください。
>
>

## <a name="prerequisites"></a>前提条件

このクイック スタートは、SQL Data Warehouse が既に用意されていて、CONTROL データベース権限を持っていることを前提としています。 作成する必要がある場合は、[ポータルでの作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前のデータ ウェアハウスを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-login-for-theceo"></a>TheCEO のログインを作成する

"TheCEO" に対して [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) を使用して、`master` データベースで SQL Server 認証ログインを作成します。

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>ユーザーの作成

mySampleDataWarehouse で "TheCEO" という [ユーザーを作成します](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest)

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>ワークロード分類子を作成する

"TheCEO" に対して重要度の高い[ワークロード分類子](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)を作成します。

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

## <a name="clean-up-resources"></a>リソースのクリーンアップ

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

データ ウェアハウス ユニットと、データ ウェアハウスに格納されているデータに対して課金されます。 これらのコンピューティングとストレージのリソースは別々に請求されます。

- データをストレージに保持しておく場合は、データ ウェアハウスを使わない間、コンピューティング リソースを一時停止できます。 コンピューティングを一時停止すると、データ ストレージに対してのみ課金されます。 データを使用する準備ができたら、コンピューティングを再開します。
- それ以上課金されないようにする場合は、データ ウェアハウスを削除できます。

以下の手順に従ってリソースをクリーンアップします。

1. [Azure portal](https://portal.azure.com) にサインインし、データ ウェアハウスを選択します。

    ![リソースのクリーンアップ](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. コンピューティング リソースを一時停止するには、 **[一時停止]** ボタンを選択します。 データ ウェアハウスが一時停止すると、ボタンの表示が **[開始]** になります。  コンピューティング リソースを再開するには、 **[開始]** を選択します。

3. コンピューティング リソースやストレージに課金されないようにデータ ウェアハウスを削除するには、 **[削除]** を選択します。

4. 作成した SQL Server を削除するには、前の画像の **mynewserver-20180430.database.windows.net** を選択して、 **[削除]** を選択します。  サーバーを削除すると、サーバーに割り当てられているすべてのデータベースが削除されるので、削除には注意してください。

5. リソース グループを削除するには、**myResourceGroup** を選択して、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

- これで、ワークロード分類子が作成されました。 TheCEO としていくつかのクエリを実行して、それらがどのように実行されるかを確認します。 クエリと割り当てられている重要度を確認するには、「[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)」を参照してください。
- Azure SQL Data Warehouse ワークロードの管理の詳細については、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)と、[ワークロードの分類](sql-data-warehouse-workload-classification.md)に関するページを参照してください。
- [ワークロードの重要度を構成する](sql-data-warehouse-how-to-configure-workload-importance.md)方法に関する記事と、[Workload Management を管理および監視する](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)方法を参照してください。
