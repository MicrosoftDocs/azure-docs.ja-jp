---
title: 制御テーブルを使用してデータベースから増分コピーを行う
description: Azure Data Factory でソリューション テンプレートを使用して、データベースから新規行または更新行のみを増分コピーする方法について説明します。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: b3b5679b254a07b275cc7fd1295ba4ca5b405fbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100362014"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>データベースから制御テーブルを使用して差分コピーを行う

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、高基準値を格納する外部制御テーブルを使用してデータベース テーブルから Azure に新規行や更新行を増分読み込みする際に使用できるテンプレートについて説明します。

このテンプレートでは、新規行や更新行を識別するため、ソース データベースのスキーマにタイムスタンプ列か増分キーが含まれている必要があります。

>[!NOTE]
> 新規行や更新行を識別するタイムスタンプ列がソース データベースに含まれているものの、差分コピーで使用する外部制御テーブルを作成したくない場合は、代わりに [Azure Data Factory データ コピー ツール](copy-data-tool.md)を使用してパイプラインを取得できます。 このツールは、トリガーでスケジュール設定される時間を変数として使用して、ソース データベースから新規行を読み取ります。

## <a name="about-this-solution-template"></a>このソリューション テンプレートについて

このテンプレートは、まず古い基準値を取得し、その値を現在の基準値と比較します。 その後、2 つの基準値の比較に基づいて、変更箇所のみをソース データベースからコピーします。 最後に、次に差分データを読み込むときのために、新しい高基準値を外部制御テーブルに格納します。

このテンプレートには、4 つのアクティビティが含まれています。
- **Lookup** が、外部制御テーブルに格納されている古い高基準値を取得します。
- 別の **Lookup** アクティビティが、ソース データベースから現在の高基準値を取得します。
- **Copy** が、変更箇所のみをソース データベースからコピー先ストアにコピーします。 ソース データベースの変更箇所を識別するクエリは、SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “last high-watermark” and TIMESTAMP_Column <= “current high-watermark” のようになります。
- **SqlServerStoredProcedure** が、次回の差分コピーのために現在の高基準値を外部制御テーブルに書き込みます。

このテンプレートでは、次のパラメーターを定義します。
- *Data_Source_Table_Name* は、データの読み込み元になるソース データベース内のテーブルです。
- *Data_Source_WaterMarkColumn* は、新規行や更新行を識別するために使用されるソース テーブル内の列の名前です。 通常、この列の型は *datetime* や *INT* などです。
- *Data_Destination_Container* は、コピー先ストア内でデータがコピーされる場所のルート パスです。
- *Data_Destination_Directory* は、コピー先ストア内でデータがコピーされる場所のルートの下のディレクトリ パスです。
- *Data_Destination_Table_Name* は、コピー先ストア内でデータがコピーされる場所です (データのコピー先として "Azure Synapse Analytics" が選択されている場合に適用されます)。
- *Data_Destination_Folder_Path* は、コピー先ストア内でデータがコピーされる場所です (データのコピー先として "ファイル システム" または "Azure Data Lake Storage Gen1" が選択されている場合に適用されます)。
- *Control_Table_Table_Name* は、高基準値を格納する外部制御テーブルです。
- *Control_Table_Column_Name* は、高基準値を格納する外部制御テーブル内の列です。

## <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. 読み込むソース テーブルを調べて、新規行や更新行を識別するために使用できる高基準値列を定義します。 この列の型には *datetime* や *INT* などがあります。 この列の値は、新規行が追加されると増加します。 次のサンプル ソース テーブル (data_source_table) では、高基準値列として *LastModifytime* 列を使用できます。

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. SQL Server か Azure SQL Database に、差分データの読み込みに使用される高基準値を格納する制御テーブルを作成します。 次の例では、制御テーブルの名前は *watermarktable* です。 このテーブルにある *WatermarkValue* が高基準値を格納する列で、その型は *datetime* です。

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 制御テーブルを作成するために使用したのと同じ SQL Server か Azure SQL Database インスタンスに、ストアド プロシージャを作成します。 このストアド プロシージャは、次回差分データを読み込むのに必要な新しい高基準値を外部制御テーブルに書き込むために使用されます。

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. **[データベースからの差分コピー]** テンプレートに移動します。 データのコピー元となるソース データベースへの **新しい** 接続を作成します。

    ![ソース テーブルへの新しい接続の作成](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. データのコピー先となるコピー先データ ストアへの **新しい** 接続を作成します。

    ![宛先テーブルへの新しい接続の作成](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 手順 2 と 3 で作成した外部制御テーブルとストアド プロシージャへの **新しい** 接続を作成します。

    ![制御テーブル データ ストアへの新しい接続の作成](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. **[このテンプレートを使用]** を選択します。
    
8. 次の例に示すように、使用可能なパイプラインが表示されます。
  
    ![パイプラインのレビュー](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. **[ストアド プロシージャ]** を選択します。 **[ストアド プロシージャ名]** に **[dbo].[update_watermark]** を選択します。 **[Import parameter (インポート パラメーター)]** を選択し、 **[動的なコンテンツの追加]** を選択します。  

    ![ストアド プロシージャ アクティビティの設定](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** という内容を書き込んで、 **[完了]** を選択します。  

    ![ストアド プロシージャのパラメーターの内容を書き込む](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. **[デバッグ]** を選択し、 **[パラメーター]** で入力し、 **[完了]** を選択します。

    ![**デバッグ** の選択](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 次の例に示すような結果が表示されます。

    ![結果を確認する](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. ソース テーブル内に新しい行を作成できます。 新規行を作成する SQL 言語の例を次に示します。

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. パイプラインをもう一度実行するには、 **[デバッグ]** を選択し、 **[パラメーター]** に入力して、 **[完了]** を選択します。

    宛先に新規行のみがコピーされたことがわかります。

15. (省略可能:)データの宛先として Azure Synapse Analytics を選択した場合、ステージング用に Azure BLOB ストレージへの接続も指定する必要があります。これは Azure Synapse Analytics の Polybase に必要です。 テンプレートによって、コンテナーのパスが生成されます。 パイプラインの実行後、コンテナーが BLOB ストレージに作成されているかどうかを確認します。
    
    ![PolyBase の構成](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>次のステップ

- [Azure Data Factory で制御テーブルを使用してデータベースから一括コピーを行う](solution-template-bulk-copy-with-control-table.md)
- [Azure Data Factory を使用して複数のコンテナーからファイルをコピーする](solution-template-copy-files-multiple-containers.md)
