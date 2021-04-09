---
title: Azure Synapse Analytics にデータを読み込む
description: Azure Data Factory を使用して Azure Synapse Analytics にデータをコピーします
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/29/2020
ms.openlocfilehash: 0ebbbcd7430a86ee431f67f7b6174b45d60056e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593715"
---
# <a name="load-data-into-azure-synapse-analytics-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Synapse Analytics へのデータの読み込み

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) は、クラウドベースのスケールアウト データベースであり、リレーショナルか非リレーショナルかを問わず、大量のデータを処理できます。 Azure Synapse Analytics は、企業のデータ ウェアハウスのワークロード向けに最適化された超並列処理 (MPP) アーキテクチャを基盤としています。 ストレージとコンピューティングを別々にスケールできる柔軟性によって、クラウドの弾力性を提供します。

Azure Synapse Analytics は、Azure Data Factory を使用する場合にさらに使いやすくなっています。 Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 このサービスを使用して、既存のシステムのデータで Azure Synapse Analytics を設定し、分析ソリューションを構築する際の時間を節約できます。

Azure Data Factory には、Azure Synapse Analytics にデータを読み込む際に次の利点があります。

* **簡単なセットアップ**: 直感的なウィザードが示す 5 つの手順に従うだけです。スクリプトは必要ありません。
* **豊富なデータ ストアのサポート**:オンプレミスとクラウドベースのデータ ストアの豊富なセットに対するサポートが組み込まれています。 詳しい一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
* **セキュリティとコンプライアンスへの準拠**:データは HTTPS または ExpressRoute 経由で転送されます。 グローバル サービスの存在により、データが地理的な境界を越えることはありません。
* **PolyBase の使用による比類のないパフォーマンス**:PolyBase は、Azure Synapse Analytics にデータを移動するための最も効率的な方法です。 ステージング BLOB の機能を使用して、Azure Blob Storage と Data Lake Store を含むすべての種類のデータ ストアからデータを高速で読み込むことができます。 (PolyBase では Azure Blob Storage と Azure Data Lake Store が既定でサポートされます。)詳しくは、[コピー アクティビティのパフォーマンス](copy-activity-performance.md)に関する記事をご覧ください。

この記事では、Data Factory のデータのコピー ツールを使用して "_Azure SQL Database から Azure Synapse Analytics にデータを読み込む_" 方法を示します。 その他の種類のデータ ストアからデータをコピーする場合も、同様の手順で実行できます。

> [!NOTE]
> 詳しくは、[Azure Data Factory を使用して Azure Synapse Analytics をコピー先またはコピー元としてデータをコピーする](connector-azure-sql-data-warehouse.md)ことに関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Azure Synapse Analytics:SQL データベースからコピーされたデータは、データ ウェアハウスに格納されます。 Azure Synapse Analytics がない場合は、[Azure Synapse Analytics インスタンスの作成](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md)の手順を参照してください。
* Azure SQL Database:このチュートリアルでは、Azure SQL Database 内に Adventure Works LT サンプル データセットからのデータをコピーします。 SQL Database 内にこのサンプル データベースを作成するには、[Azure SQL Database でのサンプル データベースの作成](../azure-sql/database/single-database-create-quickstart.md)に関する記事の手順に従います。
* Azure ストレージ アカウント:Azure Storage は、一括コピー操作の _ステージング_ BLOB として使用されます。 Azure ストレージ アカウントがない場合は、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」の手順をご覧ください。

## <a name="create-a-data-factory"></a>Data Factory の作成

1. 左側のメニューで、 **[リソースの作成]**  >  **[データ + 分析]**  >  **[Data Factory]** の順に選択します。

2. **[新しいデータ ファクトリ]** ページで、次の項目の値を指定します。

    * **Name**:名前に「*LoadSQLDWDemo*」を入力します。 データ ファクトリの名前はグローバルに一意にする必要があります。 "データ ファクトリ名 'LoadSQLDWDemo' は利用できません" エラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、_**yourname**_**ADFTutorialDataFactory** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。  
    * **バージョン**: **[V2]** を選択します。
    * **[場所]** :データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 このようなデータ ストアには、Azure Data Lake Store、Azure Storage、Azure SQL Database などがあります。

3. **［作成］** を選択します
4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="[作成と監視] タイルが含まれた Azure Data Factory のホーム ページ。":::

   **[作成と監視]** タイルを選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="load-data-into-azure-synapse-analytics"></a>Azure Synapse Analytics にデータを読み込む

1. **[Get started]\(開始\)** ページで、 **[データのコピー]** タイルを選択してデータのコピー ツールを起動します。

2. **[プロパティ]** ページで、 **[タスク名]** フィールドに「**CopyFromSQLToSQLDW**」と指定し、 **[次へ]** を選択します。

    ![[プロパティ] ページ](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. **[ソース データ ストア]** ページで、次の手順を実行します。
    >[!TIP]
    >ソース データ ストアの認証の種類として、このチュートリアルでは "*SQL 認証*" を使用しますが、サポートされている他の認証方法 ("*サービス プリンシパル*"、"*マネージド ID*") を必要に応じて選ぶこともできます。 詳細については、[この記事](./connector-azure-sql-database.md#linked-service-properties)の対応するセクションを参照してください。
    >さらに、データ ストアのシークレットを安全に格納するために、Azure Key Vault の使用をお勧めします。 詳細については、[この記事](./store-credentials-in-key-vault.md)を参照してください。

    a. **[+ 新しい接続の作成]** をクリックします。

    b. ギャラリーから **[Azure SQL Database]** を選択し、 **[続行]** を選択します。 検索ボックスに「SQL」と入力して、コネクタをフィルター処理できます。

    ![Azure SQL DB の選択](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. **[New Linked Service]\(新しいリンクされたサービス\)** ページで、ドロップダウン リストからご自身のサーバー名と DB を選択し、ユーザー名とパスワードを指定します。 **[テスト接続]** をクリックして設定を検証し、 **[作成]** を選択します。

    ![Azure SQL DB の構成](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. 新しく作成したリンクされたサービスをソースとして選択し、 **[次へ]** をクリックします。

4. **[Select tables from which to copy the data or use a custom query]\(データのコピー元またはカスタム クエリの使用元となるテーブルの選択\)** ページで、「**SalesLT**」と入力してテーブルをフィルター処理します。 **[(すべて選択)]** ボックスを選択してコピーにすべてのテーブルを使用し、 **[次へ]** を選択します。

    ![ソース テーブルの選択](./media/load-azure-sql-data-warehouse/select-source-tables.png)

5. **[フィルターの適用]** ページで、設定を指定するか、 **[次へ]** を選択します。

6. **[配布先データ ストア]** ページで、次の手順を実行します。
    >[!TIP]
    >配布先データ ストアの認証の種類として、このチュートリアルでは "*SQL 認証*&quot; を使用しますが、サポートされている他の認証方法 (&quot;*サービス プリンシパル*&quot;、&quot;*マネージド ID*") を必要に応じて選ぶこともできます。 詳細については、[この記事](./connector-azure-sql-data-warehouse.md#linked-service-properties)の対応するセクションを参照してください。
    >さらに、データ ストアのシークレットを安全に格納するために、Azure Key Vault の使用をお勧めします。 詳細については、[この記事](./store-credentials-in-key-vault.md)を参照してください。

    a. **[+ 新しい接続の作成]** をクリックして、接続を追加します

    b. ギャラリーから **[Azure Synapse Analytics]** を選択し、 **[続行]** を選択します。

    ![Azure Synapse Analytics を選択](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. **[New Linked Service]\(新しいリンクされたサービス\)** ページで、ドロップダウン リストからご自身のサーバー名と DB を選択し、ユーザー名とパスワードを指定します。 **[テスト接続]** をクリックして設定を検証し、 **[作成]** を選択します。

    ![Azure Synapse Analytics を構成](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. 新しく作成したリンクされたサービスをシンクとして選択し、 **[次へ]** をクリックします。

7. **[テーブル マッピング]** ページで、コンテンツを確認し、 **[次へ]** を選択します。 インテリジェント テーブル マッピングが表示されます。 ソース テーブルは、テーブル名に基づくコピー先テーブルにマップされます。 コピー先にソース テーブルが存在しない場合、Azure Data Factory によって同名のコピー先テーブルが既定で作成されます。 既存のコピー先テーブルにソース テーブルをマップすることもできます。

   ![[テーブル マッピング] ページ](./media/load-azure-sql-data-warehouse/table-mapping.png)

8. **[列マッピング]** ページで、内容を確認し、 **[次へ]** を選択します。 インテリジェント テーブル マッピングは、列名に基づきます。 Data Factory でテーブルを自動的に作成する場合、ソースとコピー先のストア間に互換性がないとデータ型の変換が行われます。 コピー元とコピー先の列の間でサポートされていないデータ型変換がある場合、対応するテーブルの横にエラー メッセージが表示されます。

    ![[列マッピング] ページ](./media/load-azure-sql-data-warehouse/schema-mapping.png)

9. **[設定]** ページで、次の手順を完了します。

    a. **[Staging settings]\(ステージングの設定\)** セクションで、 **[+ 新規]** をクリックしてステージング ストレージを新規作成します。 このストレージは、PolyBase を使用して Azure Synapse Analytics に読み込む前に、データをステージングするために使用されます。 コピーの完了後、Azure Blob Storage 内の暫定データは自動的にクリーンアップされます。

    b. **[New Linked Service]\(新しいリンクされたサービス\)** ページで、ストレージ アカウントを選択し、 **[作成]** を選択してリンクされたサービスをデプロイします。

    c. **[使用型の既定]** オプションを選択解除し、 **[次へ]** を選択します。

    ![PolyBase の構成](./media/load-azure-sql-data-warehouse/configure-polybase.png)

10. **[サマリー]** ページで設定を確認し、 **[次へ]** を選択します。

    ![概要ページ](./media/load-azure-sql-data-warehouse/summary-page.png)

11. **[Deployment]\(デプロイ\)** ページで **[監視]** を選択してパイプライン (タスク) を監視します。 
 
12. 左側の **[監視]** タブが自動的に選択されたことがわかります。 パイプラインの実行が正常に完了したら、 **[パイプライン名]** 列の下の **[CopyFromSQLToSQLDW]** リンクを選択してアクティビティの実行の詳細を表示するか、またはパイプラインを再実行します。

    [![パイプラインの実行を監視する](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)

13. パイプラインの実行ビューに戻るには、上部の **[すべてのパイプラインの実行]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。

    ![アクティビティの実行を監視する](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

14. 各コピー アクティビティの実行の詳細を監視するには、アクティビティの実行ビューの **[アクティビティ名]** の下の **[詳細]** リンク (眼鏡のアイコン) を選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視できます。
    ![最初のアクティビティの実行の詳細の監視](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![2 回目のアクティビティの実行の詳細の監視](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>次のステップ

次の資料に進んで、Azure Synapse Analytics のサポートを確認します。

> [!div class="nextstepaction"]
>[Azure Synapse Analytics コネクタ](connector-azure-sql-data-warehouse.md)