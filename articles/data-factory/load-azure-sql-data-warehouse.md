---
title: Azure SQL Data Warehouse へのデータの読み込み
description: Azure Data Factory を使用して Azure SQL Data Warehouse にデータをコピーします
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/22/2018
ms.openlocfilehash: 732d5d170ee647dc0dfdbf4d09a12617c8c9bcce
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931511"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure SQL Data Warehouse へのデータの読み込み

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) は、クラウドベースのスケールアウト データベースであり、リレーショナルか非リレーショナルかを問わず、大量のデータを処理できます。 SQL Data Warehouse は、企業のデータ ウェアハウスのワークロード向けに最適化された超並列処理 (MPP) アーキテクチャを基盤としています。 ストレージとコンピューティングを別々にスケールできる柔軟性によって、クラウドの弾力性を提供します。

Azure SQL Data Warehouse は、Azure Data Factory を使用する場合にさらに使いやすくなっています。 Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 このサービスを使用して、既存のシステムのデータで SQL Data Warehouse を設定し、分析ソリューションを構築する際の時間を節約できます。

Azure Data Factory には、Azure SQL Data Warehouse にデータを読み込む際に次の利点があります。

* **簡単なセットアップ**: 直感的なウィザードが示す 5 つの手順に従うだけです。スクリプトは必要ありません。
* **豊富なデータ ストアのサポート**:オンプレミスとクラウドベースのデータ ストアの豊富なセットに対するサポートが組み込まれています。 詳しい一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
* **セキュリティとコンプライアンスへの準拠**:データは HTTPS または ExpressRoute 経由で転送されます。 グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **PolyBase の使用による比類のないパフォーマンス**:PolyBase は、Azure SQL Data Warehouse にデータを移動するための最も効率的な方法です。 ステージング BLOB の機能を使用して、Azure Blob Storage と Data Lake Store を含むすべての種類のデータ ストアからデータを高速で読み込むことができます。 (PolyBase では Azure Blob Storage と Azure Data Lake Store が既定でサポートされます。)詳しくは、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事をご覧ください。

この記事では、Data Factory のデータのコピー ツールを使用して "_Azure SQL Database から Azure SQL Data Warehouse にデータを読み込む_" 方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

> [!NOTE]
> 詳しくは、「[Azure Data Factory を使用して Azure SQL Data Warehouse をコピー先またはコピー元としてデータをコピーする](connector-azure-sql-data-warehouse.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Azure SQL Data Warehouse: SQL データベースからコピーされたデータは、データ ウェアハウスに格納されます。 Azure SQL Data Warehouse がない場合は、[SQL Data Warehouse の作成](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)に関する記事の手順をご覧ください。
* Azure SQL Database:このチュートリアルでは、Adventure Works LT サンプル データを使って Azure SQL データベースからデータをコピーします。 SQL データベースを作成するには、[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事の手順に従います。 
* Azure ストレージ アカウント:Azure Storage は、一括コピー操作の_ステージング_ BLOB として使用されます。 Azure ストレージ アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」の手順をご覧ください。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のメニューで、 **[リソースの作成]**  >  **[データ + 分析]**  >  **[Data Factory]** の順に選択します。 
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、次の画像に示されているフィールドの値を指定します。
      
   ![[新しいデータ ファクトリ] ページ](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **名前**:Azure Data Factory のグローバルに一意の名前を入力します。 "データ ファクトリ名 \"LoadSQLDWDemo\" は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、 _**yourname**_ **ADFTutorialDataFactory** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
    * **バージョン**: **[V2]** を選択します。
    * **場所**:データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 このようなデータ ストアには、Azure Data Lake Store、Azure Storage、Azure SQL Database などがあります。

3. **作成** を選択します。
4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   **[作成と監視]** タイルを選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse へのデータの読み込み

1. **[Get started]\(開始\)** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを起動します。

   ![データのコピー ツールのタイル](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. **[プロパティ]** ページで、 **[タスク名]** フィールドに「**CopyFromSQLToSQLDW**」と指定し、 **[次へ]** を選択します。

    ![[プロパティ] ページ](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. **[ソース データ ストア]** ページで、次の手順を実行します。

    a. **[+ 新しい接続の作成]** をクリックします。

    ![[ソース データ ストア] ページ](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. ギャラリーから **[Azure SQL Database]** を選択し、 **[続行]** を選択します。 検索ボックスに「SQL」と入力して、コネクタをフィルター処理できます。

    ![Azure SQL DB の選択](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. **[New Linked Service]\(新しいリンクされたサービス\)** ページで、ドロップダウン リストからご自身のサーバー名と DB を選択し、ユーザー名とパスワードを指定します。 **[テスト接続]** をクリックして設定を検証し、 **[完了]** を選択します。
   
    ![Azure SQL DB の構成](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. 新しく作成したリンクされたサービスをソースとして選択し、 **[次へ]** をクリックします。

    ![ソースのリンクされたサービスの選択](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. **[Select tables from which to copy the data or use a custom query]\(データのコピー元またはカスタム クエリの使用元となるテーブルの選択\)** ページで、「**SalesLT**」と入力してテーブルをフィルター処理します。 **[(すべて選択)]** ボックスを選択してコピーにすべてのテーブルを使用し、 **[次へ]** を選択します。 

    ![ソース テーブルの選択](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. **[配布先データ ストア]** ページで、次の手順を実行します。

    a. **[+ 新しい接続の作成]** をクリックして、接続を追加します

    ![シンク データ ストア ページ](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. ギャラリーから **[Azure SQL Data Warehouse]** を選択し、 **[次へ]** を選択します。

    ![Azure SQL DW の選択](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. **[New Linked Service]\(新しいリンクされたサービス\)** ページで、ドロップダウン リストからご自身のサーバー名と DB を選択し、ユーザー名とパスワードを指定します。 **[テスト接続]** をクリックして設定を検証し、 **[完了]** を選択します。
   
    ![Azure SQL DW の構成](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. 新しく作成したリンクされたサービスをシンクとして選択し、 **[次へ]** をクリックします。

    ![シンクのリンクされたサービスの選択](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. **[テーブル マッピング]** ページで、コンテンツを確認し、 **[次へ]** を選択します。 インテリジェント テーブル マッピングが表示されます。 ソース テーブルは、テーブル名に基づくコピー先テーブルにマップされます。 コピー先にソース テーブルが存在しない場合、Azure Data Factory によって同名のコピー先テーブルが既定で作成されます。 既存のコピー先テーブルにソース テーブルをマップすることもできます。 

   > [!NOTE]
   > SQL Data Warehouse シンクに対するテーブルの自動作成は、SQL Server または Azure SQL Database がソースの場合に適用されます。 別のソース データ ストアからデータをコピーする場合は、データのコピーを実行する前にシンク Azure SQL Data Warehouse 内にスキーマを事前に作成しておく必要があります。

   ![[テーブル マッピング] ページ](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. **[スキーマ マッピング]** ページで、コンテンツを確認し、 **[次へ]** を選択します。 インテリジェント テーブル マッピングは、列名に基づきます。 Data Factory でテーブルを自動的に作成する場合、ソースとコピー先のストア間に互換性がないとデータ型の変換が行われます。 コピー元とコピー先の列の間でサポートされていないデータ型変換がある場合、対応するテーブルの横にエラー メッセージが表示されます。

    ![[スキーマ マッピング] ページ](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. **[設定]** ページで、次の手順を完了します。

    a. **[Staging settings]\(ステージングの設定\)** セクションで、 **[+ 新規]** をクリックしてステージング ストレージを新規作成します。 このストレージは、PolyBase を使用して SQL Data Warehouse に読み込む前に、データをステージングするために使用されます。 コピーの完了後、Azure Storage 内の暫定データは自動的にクリーンアップされます。 

    ![ステージングの構成](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. **[New Linked Service]\(新しいリンクされたサービス\)** ページで、ストレージ アカウントを選択して、 **[完了]** を選択します。
   
    ![Azure Storage の構成](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. **[詳細設定]** セクションで **[Use type default]\(型の既定を使用する\)** オプションをオフにして、 **[次へ]** を選択します。

    ![PolyBase の構成](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    ![概要ページ](./media/load-azure-sql-data-warehouse/summary-page.png)
1. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。

    ![[Deployment]\(デプロイ\) ページ](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列には、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクが表示されます。 

    ![パイプラインの実行を監視する](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプラインの実行ビューに戻るには、上部の **[パイプライン]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。 

    ![アクティビティの実行を監視する](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. 各コピー アクティビティの実行状況の詳細を監視するには、アクティビティ監視ビューの **[アクション]** の下の **[詳細]** リンクを選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視することができます。

    ![アクティビティの実行状況の詳細の監視](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>次の手順

次の資料に進んで、Azure SQL Data Warehouse のサポートを確認します。 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse コネクタ](connector-azure-sql-data-warehouse.md)
