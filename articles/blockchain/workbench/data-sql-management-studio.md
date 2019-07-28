---
title: SQL Server Management Studio で Azure Blockchain Workbench データを使用します
description: SQL Server Management Studio 内から Azure Blockchain ワークベンチの SQL データベースに接続する方法を説明します。
services: azure-blockchain
keywords: ''
author: pataltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: db43dd04da601650b3592dc74cdd804fae394c00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510916"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>SQL Server Management Studio で Azure Blockchain Workbench データを使用します

Microsoft SQL Server Management Studio では、Azure Blockchain ワークベンチの SQL データベースに対してクエリを短時間で作成およびテストする機能が提供されます。 このセクションでは、SQL Server Management Studio 内から Azure Blockchain ワークベンチの SQL データベースに接続する方法を、段階ごとに説明します。

## <a name="prerequisites"></a>前提条件

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)をダウンロードします。

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>SQL Server Management Studio を Azure Blockchain Workbench データに接続します

1. SQL Server Management Studio を開き、**接続**を選択します。
2. **データベース エンジン**を選択します。

    ![データベース エンジン](./media/data-sql-management-studio/database-engine.png)

3. **[サーバーに接続]** ダイアログで、サーバー名とお使いのデータベース資格情報を入力します。

    Azure Blockchain Workbench のデプロイプロセスによって作成された資格情報を使用している場合、ユーザー名は **dbadmin** となり、パスワードはデプロイ時に指定したパスワードです。

    ![SQL 資格情報を入力します](./media/data-sql-management-studio/sql-creds.png)

   1. SQL Server Management Studio では、Azure Blockchain Workbench データベース内のデータベース、データベース ビュー、およびストアド プロシージャの一覧が表示されます。

      ![データベースの一覧](./media/data-sql-management-studio/db-list.png)

5. データベース ビューのいずれかに関連付けられているデータを表示するには、以下の手順を使用して、自動的に選択ステートメントを生成できます。
6. オブジェクト エクスプローラーのデータベースビューのいずれかを右クリックします。
7. **[スクリプトビュー]** を選択します。
8. **[SELECT]** を選択します。
9. **新しいクエリ エディター ウィンドウ**を選択します。
10. **新しいクエリ**を選択して、新しいクエリを作成することができますです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデータベース ビュー](database-views.md)
