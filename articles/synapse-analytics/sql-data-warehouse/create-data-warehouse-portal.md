---
title: 'クイックスタート: 専用 SQL プール (以前の SQL DW) を作成してクエリを実行する (Azure portal)'
description: Azure portal を使用して専用 SQL プール (以前の SQL DW) を作成してクエリを実行します
services: synapse-analytics
author: pimorano
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/28/2019
ms.author: pimorano
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: dd9d66683d5f2349807915651c50bf9eedd285c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602232"
---
# <a name="quickstart-create-and-query-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-the-azure-portal"></a>クイックスタート: Azure portal から Azure Synapse Analytics の専用 SQL プール (以前の SQL DW) を作成し、クエリを実行する

Azure Synapse Analytics の専用 SQL プール (以前の SQL DW) を Azure portal から短時間で作成し、クエリを実行します。

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

   > [!NOTE]
   > Azure Synapse の専用 SQL プール (以前の SQL DW) を作成すると、課金対象のサービスが新たに生じることがあります。 詳細については、「[Azure Synapse Analytics の価格](https://azure.microsoft.com/pricing/details/synapse-analytics/)」を参照してください。

2. 最新バージョンの [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) をダウンロードしてインストールします。 注: SSMS は、Windows ベースのプラットフォームでのみ使用できます。[サポートされているプラットフォームの詳細な一覧](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15&preserve-view=true#supported-operating-systems-ssms-185t)を参照してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-sql-pool"></a>SQL プールを作成する

データ ウェアハウスは、Azure Synapse Analytics の専用 SQL プール (以前の SQL DW) を使用して作成されます。 専用 SQL プール (以前の SQL DW) は、定義された一連の[コンピューティング リソース](memory-concurrency-limits.md)を使用して作成されます。 データベースは、[Azure リソース グループ](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)内と [論理 SQL サーバー](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に作成されます。

以下の手順で **AdventureWorksDW** サンプル データを含む専用 SQL プール (以前の SQL DW) を作成します。

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。

   ![Azure portal でリソースを作成する](./media/create-data-warehouse-portal/create-a-resource.png)

2. 検索バーに、「専用 SQL プール」と入力し、[専用 SQL プール (以前の SQL DW)] を選択します。 表示されたページで **[作成]** を選択します。

   ![空のデータ ウェアハウスを作成する](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. **[基本]** で、ご利用のサブスクリプション、リソース グループ、専用 SQL プール (以前の SQL DW) 名、サーバー名を指定します。

   | 設定 | 推奨値 | 説明 |
   | :------ | :-------------- | :---------- |
   | **サブスクリプション** | 該当するサブスクリプション | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   | **リソース グループ** | myResourceGroup | 有効なリソース グループ名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関するページを参照してください。 |
   | **SQL プール名** | グローバルに一意の名前 (例: *mySampleDataWarehouse*) | 有効なデータベース名については、「[Database Identifiers (データベース識別子)](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。  |
   | **サーバー** | グローバルに一意の名前 | 既存のサーバーを選択するか、新しいサーバー名を付けて **[新規作成]** を選択します。 有効なサーバー名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)に関するページを参照してください。 |

   ![データ ウェアハウスの基本情報を作成する](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. **[パフォーマンス レベル]** で **[パフォーマンス レベルの選択]** を選択し、スライダーを使いながら必要に応じて構成を変更します。

   ![データ ウェアハウスのパフォーマンス レベルを変更する](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   パフォーマンス レベルの詳細については、[Azure Synapse Analytics でのコンピューティングの管理](sql-data-warehouse-manage-compute-overview.md)に関するページを参照してください。

5. **[既存のデータの使用]** で **[追加設定]** を選択し、 **[サンプル]** を選択して、AdventureWorksDW が同じサンプル データベースとして作成されるようにします。

    ![[既存のデータの使用] を選択します](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png)

6. Azure Synapse Analytics フォームの [基本] タブの入力が完了したら、 **[確認および作成]** 、 **[作成]** の順に選択して、SQL プールを作成します。 プロビジョニングには数分かかります。

   ![[確認と作成] を選択する](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![[作成] の選択](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. デプロイ プロセスを監視するために、ツール バーの **[通知]** を選択します。

   ![スクリーンショットは、デプロイ中の [通知] を示しています。](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則を作成する

Azure Synapse サービスでは、サーバーレベルでファイアウォールが作成されます。 このファイアウォールにより、外部のアプリケーションとツールはサーバーやサーバー上のすべてのデータベースに接続できなくなります。 接続できるようにするには、特定の IP アドレスに接続を許可するファイアウォール規則を追加します。 次の手順に従って、クライアントの IP アドレスに対する[サーバーレベルのファイアウォール規則](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を作成します。

> [!NOTE]
> Azure Synapse の通信は、ポート 1433 で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、サーバーに接続することはできません。

1. デプロイが完了したら、左側のメニューから **[すべてのサービス]** を選択します。 **[データベース]** を選択し、 **[Azure Synapse Analytics]** の横にある星印を選択して、Azure Synapse Analytics をお気に入りに追加します。

2. 左側のメニューから **[Azure Synapse Analytics]** を選択し、 **[Azure Synapse Analytics]** ページの **[mySampleDataWarehouse]** を選択します。 データベースの概要ページが開き、完全修飾サーバー名 (**sqlpoolservername.database.windows.net** など) と、さらに構成するためのオプションが表示されます。

3. この完全修飾サーバー名は、このクイック スタートや他のクイック スタートでお使いのサーバーとそのデータベースへの接続に使用する場合にコピーします。 サーバー設定を開くには、サーバー名を選択します。

   ![サーバー名を検索する](./media/create-data-warehouse-portal/find-server-name.png)

4. **[ファイアウォール設定の表示]** を選択します。

   ![サーバー設定](./media/create-data-warehouse-portal/server-settings.png)

5. サーバーの **[ファイアウォール設定]** ページが開きます。

   ![サーバーのファイアウォール規則](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. 現在の IP アドレスを新しいファイアウォール規則に追加するために、ツール バーの **[クライアント IP の追加]** を選択します。 ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

7. **[保存]** を選択します。 サーバーでポート 1433 を開いている現在の IP アドレスに対して、サーバーレベルのファイアウォール規則が作成されます。

8. **[OK]** を選択し、 **[ファイアウォール設定]** ページを閉じます。

これで、この IP アドレスを使って、サーバーとその SQL プールに接続できるようになりました。 接続するには、SQL Server Management Studio または他の適当なツールを使います。 接続するときは、前に作成した ServerAdmin アカウントを使います。

> [!IMPORTANT]
> 既定では、すべての Azure サービスで、SQL Database ファイアウォール経由のアクセスが有効になります。 このページの **[オフ]** を選択し、 **[保存]** を選択して、すべての Azure サービスに対してファイアウォールを無効にします。

## <a name="get-the-fully-qualified-server-name"></a>完全修飾サーバー名を取得する

Azure portal で、サーバーの完全修飾サーバー名を取得します。 後でサーバーに接続するときに、完全修飾名を使います。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. 左側のメニューから **[Azure Synapse Analytics]** を選択し、 **[Azure Synapse Analytics]** ページで目的のものを選択します。

3. そのデータベースの Azure Portal ページの **[基本]** ウィンドウで、**サーバー名** を見つけてコピーします。 この例の完全修飾名は sqlpoolservername.database.windows.net です。

    ![接続情報](./media/create-data-warehouse-portal/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>サーバー管理者としてサーバーに接続する

このセクションでは、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) を使って、サーバーへの接続を確立します。

1. SQL Server Management Studio を開きます。

2. **[サーバーへの接続]** ダイアログ ボックスで、次の情報を入力します。

   | 設定 | 推奨値 | 説明 |
   | :------ | :-------------- | :---------- |
   | サーバーの種類 | データベース エンジン | この値は必須です |
   | サーバー名 | 完全修飾サーバー名 | 例: **sqlpoolservername.database.windows.net** |
   | 認証 | SQL Server 認証 | このチュートリアルで構成した認証の種類は "SQL 認証" のみです。 |
   | ログイン | サーバー管理者アカウント | サーバーの作成時に指定したアカウントです。 |
   | Password | サーバー管理者アカウントのパスワード | サーバーの作成時に指定したパスワードです。 |
   ||||

   ![[サーバーに接続]](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. **[接続]** を選択します。 SSMS で [オブジェクト エクスプローラー] ウィンドウが開きます。

4. オブジェクト エクスプローラーで、 **[データベース]** を展開します。 **mySampleDatabase** を展開して、新しいデータベースのオブジェクトを表示します。

   ![データベース オブジェクト](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>クエリを実行する

[制限されたリソース クラス](resource-classes-for-workload-management.md)を使用するため、サーバー管理者としてログに記録されている間は、大規模なクエリを実行しないことをお勧めします。 代わりに、[チュートリアルで説明](./load-data-wideworldimportersdw.md#create-a-user-for-loading-data)されているように、[ワークロードの分離](./quickstart-configure-workload-isolation-tsql.md)を構成します。

Azure Synapse Analytics では、クエリ言語として T-SQL が使用されています。 クエリ ウィンドウを開いて T-SQL クエリを実行するには、次の手順を実行します。

1. **mySampleDataWarehouse** を右クリックして、 **[新しいクエリ]** を選びます。 新しいクエリ ウィンドウが開きます。

2. クエリ ウィンドウで、次のコマンドを入力してデータベースの一覧を表示します。

    ```sql
    SELECT * FROM sys.databases
    ```

3. **[Execute]\(実行\)** を選択します。 クエリ結果には、**master** と **mySampleDataWarehouse** という 2 つのデータベースが表示されます。

   ![データベースのクエリを実行する](./media/create-data-warehouse-portal/query-databases.png)

4. 何かのデータを確認する場合、次のコマンドを使用すると、家庭に 3 人の子供がいる Adams という名前のユーザー数が表示されます。 結果として 6 人のユーザーが一覧表示されます。

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![クエリ dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

データ ウェアハウス ユニットと専用 SQL プール (以前の SQL DW) に格納されているデータに対して課金されます。 これらのコンピューティングとストレージのリソースは別々に請求されます。

- データをストレージに保持しておく場合は、専用 SQL プール (以前の SQL DW) を使わない間、コンピューティング リソースを一時停止できます。 コンピューティングを一時停止すると、データ ストレージに対してのみ課金されます。 データを使用する準備ができたら、コンピューティングを再開できます。

- それ以上課金されないようにする場合は、専用 SQL プール (以前の SQL DW) を削除できます。

不要になったリソースをクリーンアップするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、専用 SQL プール (以前の SQL DW) を選択します。

   ![リソースをクリーンアップする](./media/create-data-warehouse-portal/clean-up-resources.png)

2. コンピューティング リソースを一時停止するには、 **[一時停止]** ボタンを選択します。 専用 SQL プール (以前の SQL DW) が一時停止すると、ボタンの表示が **[再開]** になります。 コンピューティングを再開するには、 **[再開]** をクリックします。

3. コンピューティング リソースやストレージに課金されないように専用 SQL プール (以前の SQL DW) を削除するには、 **[削除]** を選択します。

4. 作成したサーバーを削除するには、前の画像の **sqlpoolservername.database.windows.net** を選択して、 **[削除]** を選択します。 サーバーを削除すると、サーバーに割り当てられているすべてのデータベースが削除されるので、削除には注意してください。

5. リソース グループを削除するには、**myResourceGroup** を選択して、 **[リソース グループの削除]** を選択します。

クラウドの支出を最適化して節約しますか?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>次のステップ

専用 SQL プール (以前の SQL DW) へのデータの読み込みの詳細については、[専用 SQL プールへのデータの読み込み](load-data-from-azure-blob-storage-using-copy.md)に関する記事に進んでください。