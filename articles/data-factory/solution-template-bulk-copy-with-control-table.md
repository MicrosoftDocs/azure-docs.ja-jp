---
title: 制御テーブルを使用してデータベースから一括コピーを行う
description: Azure Data Factory で、ソース テーブルのパーティションの一覧を格納する外部制御テーブルを使用して、データベースからデータを一括コピーするソリューション テンプレートの使用方法について説明します。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: eed7a304bdd57846cd038cc9bf9a67e8150ca505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392461"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>制御テーブルを使用してデータベースから一括コピーを行う

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Oracle サーバー、Netezza、Teradata、または SQL Server 内のデータ ウェアハウスから Azure Synapse Analytics にデータをコピーするには、複数のテーブルから膨大な量のデータを読み込む必要があります。 通常は、複数のスレッドを並列して使用して単一のテーブルから行が読み込まれるように、各テーブルでデータをパーティション化する必要があります。 この記事では、これらのシナリオで使用するテンプレートについて説明します。

 >注: データのボリュームが比較的小さい少数のテーブルからデータを Azure Synapse Analytics にコピーする場合、[Azure Data Factory のデータ コピー ツール](copy-data-tool.md)を使用するのがより効率的です。 この記事で説明するテンプレートは、そのシナリオで必要とするものより大きくなっています。

## <a name="about-this-solution-template"></a>このソリューション テンプレートについて

このテンプレートでは、外部制御テーブルからコピーするソース データベース パーティションの一覧が取得されます。 次に、ソース データベース内の各パーティションが反復処理され、宛先にデータがコピーされます。

このテンプレートには、3 つのアクティビティが含まれています。
- **ルックアップ** では、外部制御テーブルからデータベース パーティションの一覧が確実に取得されます。
- **ForEach** では、ルックアップ アクティビティからパーティションの一覧が取得され、各パーティションがコピー アクティビティに対して反復処理されます。
- **コピー** では各パーティションがソース データベース ストアから宛先ストアにコピーされます。

このテンプレートでは、次のパラメーターを定義します。
- *Control_Table_Name* は外部制御テーブルであり、ソース データベースのパーティション一覧が格納されています。
- *Control_Table_Schema_PartitionID* は、各パーティション ID を格納する外部制御テーブル内の列名です。 ソース データベース内の各パーティションのパーティション ID が一意であることを確認してください。
- *Control_Table_Schema_SourceTableName* は、ソース データベースからのそれぞれのテーブル名を格納する外部制御テーブルです。
- *Control_Table_Schema_FilterQuery* は、ソース データベース内の各パーティションからデータを取得するためのフィルター クエリを格納する外部制御テーブル内の列名です。 たとえば、ご利用のデータが年単位でパーティション化されている場合、各行に格納されるクエリは、‘select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999''' のようになる可能性があります。
- *Data_Destination_Folder_Path* は、データが宛先ストアにコピーされる先のパスです (選択した宛先が "ファイル システム" または "Azure Data Lake Storage Gen1" の場合に適用されます)。 
- *Data_Destination_Container* は、データが宛先ストアにコピーされる先のルート フォルダーのパスです。 
- *Data_Destination_Directory* は、データが宛先ストアにコピーされる先のルート以下のディレクトリ パスです。 

宛先ストアのパスを定義する最後の 3 つのパラメーターは、選択した宛先がファイルベースのストレージである場合にのみ表示されます。 宛先ストアとして "Azure Synapse Analytics" を選択した場合、これらのパラメーターは不要です。 ただし、Azure Synapse Analytics 内のテーブル名とスキーマは、ソース データベース内のものと同じである必要があります。

## <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. 一括コピーのためにソース データベースのパーティションの一覧を格納するには、SQL Server または Azure SQL Database 内に制御テーブルを作成します。 次の例では、ソース データベース内に 5 つのパーティションがあります。 3 つのパーティションは *datasource_table* 用で、2 つのテーブルは *project_table* 用です。 ソース データベースからのテーブル *datasource_table* 内のデータをパーティション分割するには、列 *LastModifytime* が使用されます。 最初のパーティションを読み取るために使用されるクエリは 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999''' です。 同様のクエリを使用して、他のパーティションからもデータを読み取ることができます。

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. **[データベースからの一括コピー]** テンプレートに移動します。 手順 1 で作成した外部制御テーブルへの **新しい** 接続を作成します。

    ![制御テーブルへの新しい接続の作成](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. データのコピー元であるソース データベースへの **新しい** 接続を作成します。

    ![ソース データベースへの新しい接続の作成](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. データのコピー先である宛先データ ストアへの **新しい** 接続を作成します。

    ![宛先ストアへの新しい接続の作成](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. **[このテンプレートを使用]** を選択します。

6. 次の例に示すように、パイプラインが表示されます。

    ![パイプラインのレビュー](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. **[デバッグ]** を選択し、 **[パラメーター]** で入力し、 **[完了]** を選択します。

    ![**[デバッグ]** をクリックします。](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 次の例のような結果が表示されます。

    ![結果を確認する](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (省略可能) データの宛先として "Azure Synapse Analytics" を選択した場合、Azure Synapse Analytics Polybase に必要であれば、ステージングのために Azure Blob Storage への接続を入力する必要があります。 テンプレートを使うと、Blob ストレージのコンテナー パスが自動的に生成されます。 パイプラインの実行後は、コンテナーが作成されているかどうかを確認します。
    
    ![Polybase 設定](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>次のステップ

- [Azure Data Factory の概要](introduction.md)
