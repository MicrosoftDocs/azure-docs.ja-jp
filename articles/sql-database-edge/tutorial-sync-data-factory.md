---
title: Azure Data Factory を使用して Azure SQL Database Edge からデータを同期する | Microsoft Docs
description: Azure SQL Database Edge と Azure Blob Storage の間でデータを同期する方法について説明します。
keywords: sql database edge,sql database edge からデータを同期する, sql database edge data factory
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851691"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>チュートリアル:Azure Data Factory を使用して SQL Database Edge から Azure Blob Storage にデータを同期する

このチュートリアルでは、Azure Data Factory を使用し、Azure SQL Database Edge インスタンス内のテーブルから Azure Blob Storage にデータを増分同期します。

## <a name="before-you-begin"></a>開始する前に

Azure SQL Database Edge デプロイでデータベースまたはテーブルをまだ作成していない場合、次のいずれかの方法で 1 つ作成してください。

* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) または [Azure Data Studio](/sql/azure-data-studio/download/) を使用して SQL Database Edge に接続します。 SQL スクリプトを実行して、データベースとテーブルを作成します。
* SQL Database Edge モジュールに直接接続し、[SQLCMD](/sql/tools/sqlcmd-utility/) を使用して SQL データベースとテーブルを作成します。 詳細については、[sqlcmd を使用したデータベース エンジンへの接続](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/)に関するページを参照してください。
* SQLPackage.exe を使用して、DAC パッケージ ファイルを SQL Database Edge コンテナーにデプロイします。 このプロセスは、モジュールの必要なプロパティの構成の一環として SqlPackage ファイル URI を指定することで自動化できます。 また、SqlPackage.exe クライアント ツールを直接使用して DAC パッケージを SQL Database Edge にデプロイすることもできます。

    SqlPackage.exe をダウンロードする方法については、「[sqlpackage をダウンロードしてインストールする](/sql/tools/sqlpackage-download/)」を参照してください。 SqlPackage.exe 用のサンプル コマンドの一部を次に示します。 詳細については、SqlPackage.exe のドキュメントを参照してください。

    **DAC パッケージを作成する**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **DAC パッケージを適用する**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>基準値レベルを格納および更新するための SQL テーブルとプロシージャを作成する

基準値テーブルは、データが Azure Storage と同期された最終タイムスタンプを格納するために使用されます。 Transact-SQL (T-SQL) ストアド プロシージャは、同期のたびに基準値テーブルを更新するために使用されます。

SQL Database Edge インスタンスで次のコマンドを実行します。

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Data Factory パイプラインを作成する

このセクションでは、Azure SQL Database Edge 内のテーブルから Azure Blob Storage にデータを同期するための Azure Data Factory パイプラインを作成します。

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Data Factory UI を使用してデータ ファクトリを作成する

[このチュートリアル](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)の手順に従って、データ ファクトリを作成します。

### <a name="create-a-data-factory-pipeline"></a>Data Factory パイプラインを作成する

1. Azure Data Factory UI の **[Let's get started]\(始めましょう\) ページ**で、 **[パイプラインを作成]** を選択します。

    ![Data Factory パイプラインを作成する](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. パイプラインの **[プロパティ]** ウィンドウの **[全般]** ページで、名前に「**PeriodicSync**」と入力します。

3. 古い基準値を取得するためのルックアップ アクティビティを追加します。 **[アクティビティ]** ペインで **[全般]** を展開し、パイプライン デザイナー画面に **[ルックアップ]** アクティビティをドラッグします。 アクティビティの名前を **OldWatermark** に変更します。

    ![古い基準値のルックアップを追加する](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. **[設定]** タブに切り替えて、 **[Source Dataset]\(ソース データセット\)** の **[新規]** を選択します。 ここで、基準値テーブル内のデータを表すデータセットを作成します。 このテーブルには、前のコピー操作で使用されていた古い基準が含まれています。

5. **[新しいデータセット]** ウィンドウで **[Azure SQL Server]** を選択し、 **[続行]** を選択します。  

6. データセットの **[プロパティの設定]** ウィンドウで、 **[名前]** に「**WatermarkDataset**」と入力します。

7. **[リンクされたサービス]** で **[新規]** を選択して、次の手順を実行します。

    1. **[名前]** に「**SQLDBEdgeLinkedService**」と入力します。

    2. **[サーバー名]** に SQL Database Edge サーバーの詳細を入力します。

    3. 一覧から目的の**データベース名**を選択します。

    4. **[ユーザー名]** と **[パスワード]** を入力します。

    5. SQL Database Edge インスタンスへの接続をテストするために、 **[Test connection]\(テスト接続\)** を選択します。

    6. **作成** を選択します。

    ![リンクされたサービスを作成する](media/tutorial-sync-data-factory/create-linked-service.png)

    7. **[OK]** を選択します。

8. **[設定]** タブで、 **[編集]** を選択します。

9. **[接続]** タブで、 **[テーブル]** に **[dbo].[watermarktable]** を選択します。 テーブル内のデータをプレビューする場合、 **[データのプレビュー]** を選択します。

10. 上部のパイプライン タブを選択するか、左側のツリー ビューでパイプラインの名前を選択し、パイプライン エディターに切り替えます。 ルックアップ アクティビティのプロパティ ウィンドウで、 **[Source dataset]\(ソース データセット\)** の一覧で **[WatermarkDataset]** が選択されていることを確認します。

11. **[アクティビティ]** ペインで **[全般]** を展開し、パイプライン デザイナー画面に **[ルックアップ]** アクティビティをもう 1 つドラッグします。 プロパティ ウィンドウの **[全般]** タブで、名前を **NewWatermark** に設定します。 このルックアップ アクティビティは、ソース データを含むテーブルから新しい基準値を取得して、それをターゲットにコピーできるようにします。

12. 2 つ目のルックアップ アクティビティのプロパティ ウィンドウで、 **[設定]** タブに切り替え、 **[新規]** を選択し、新しい基準値を含むソース テーブルを指すデータセットを作成します。

13. **[新しいデータセット]** ウィンドウで、 **[SQL Database Edge instance]\(SQL Database Edge インスタンス\)** を選択し、 **[続行]** を選択します。

    1. **[プロパティの設定]** ウィンドウで、 **[名前]** に「**SourceDataset**」と入力します。 **[リンクされたサービス]** で **[SQLDBEdgeLinkedService]** を選択します。

    2. **[テーブル]** で、同期するテーブルを選択します。 このチュートリアルで後述するように、このデータセットに対するクエリを指定することもできます。 このクエリは、この手順で指定するテーブルよりも優先されます。

    3. **[OK]** を選択します。

14. 上部のパイプライン タブを選択するか、左側のツリー ビューでパイプラインの名前を選択し、パイプライン エディターに切り替えます。 ルックアップ アクティビティのプロパティ ウィンドウで、 **[Source Dataset]\(ソース データセット\)** の一覧で **[SourceDataset]** が選択されていることを確認します。

15. **[Use query]\(クエリの使用\)** で、 **[クエリ]** を選択します。 次のクエリ内のテーブル名を更新し、そのクエリを入力します。 テーブルから `timestamp` の最大値のみを選択します。 必ず **[First row only]\(先頭行のみ\)** を選択してください。

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![クエリの選択](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. **[アクティビティ]** ペインで、 **[Move & Transform]\(移動と変換\)** を展開し、 **[アクティビティ]** ペインからデザイナー画面に **[コピー]** アクティビティをドラッグします。 アクティビティの名前を **IncrementalCopy** に設定します。

17. 2 つのルックアップ アクティビティにコピー アクティビティに接続します。これには、ルックアップ アクティビティにアタッチされている緑のボタンをコピー アクティビティにドラッグします。 コピー アクティビティの境界線の色が青に変わったら、マウス ボタンを離します。

18. コピー アクティビティを選択し、そのアクティビティのプロパティが **[プロパティ]** ウィンドウに表示されることを確認します。

19. **[プロパティ]** ウィンドウで **[ソース]** タブに切り替え、次の手順を実行します。

    1. **[Source dataset]\(ソース データセット\)** ボックスで、 **[SourceDataset]** を選択します。

    2. **[クエリの使用]** で、 **[クエリ]** を選択します。

    3. **[クエリ]** ボックスに SQL クエリを入力します。 クエリの例を次に示します。

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. **[シンク]** タブの **[Sink Dataset]\(シンク データセット\)** で **[新規]** を選択します。

21. このチュートリアルでのシンク データ ストアは Azure Blob Storage データ ストアです。 **[新しいデータセット]** ウィンドウで **[Azure Blob Storage]** を選択し、 **[続行]** を選択します。

22. **[形式の選択]** ウィンドウでデータの形式を選択し、 **[続行]** を選択します。

23. **[プロパティの設定]** ウィンドウで、 **[名前]** に「**SinkDataset**」と入力します。 **[リンクされたサービス]** で **[新規]** を選択します。 次に、使用する Azure Blob Storage への接続 (リンクされたサービス) を作成します。

24. **[New Linked Service (Azure Blob storage)]\(新しいリンクされたサービス (Azure Blob Storage)\)** ウィンドウで、次の手順を実行します。

    1. **[名前]** ボックスに「**AzureStorageLinkedService**」と入力します。

    2. **[ストレージ アカウント名]** で、お使いの Azure サブスクリプションの Azure ストレージ アカウントを選択します。

    3. 接続をテストし、 **[完了]** を選択します。

25. **[プロパティの設定]** ウィンドウの **[リンクされたサービス]** で **[AzureStorageLinkedService]** が選択されていることを確認します。 **[作成]** 、 **[OK]** の順に選択します。

26. **[シンク]** タブで、 **[編集]** を選択します。

27. SinkDataset の **[接続]** タブに移動し、次の手順を実行します。

    1. **[ファイル パス]** に「*asdedatasync/incrementalcopy*」と入力します。この場合、*asdedatasync* は BLOB コンテナー名であり、*incrementalcopy* はフォルダー名です。 このコンテナーが存在しない場合は、コンテナーを作成するか、既存のコンテナーの名前を使用してください。 出力フォルダー *incrementalcopy* が存在しない場合は、Azure Data Factory によって自動的に作成されます。 **[ファイル パス]** の **[参照]** ボタンを使用して、BLOB コンテナー内のフォルダーに移動することもできます。

    2. **[ファイル パス]** の**ファイル**部分で **[動的なコンテンツの追加 [Alt+P]]** を選択し、表示されたウィンドウで「 **@CONCAT('Incremental-', pipeline().RunId, '.txt')** 」と入力します。 **[完了]** を選択します。 ファイル名は、この式によって動的に生成されます。 各パイプラインの実行には、一意の ID があります。 コピー アクティビティは、実行 ID を使用して、ファイル名を生成します。

28. 上部のパイプライン タブを選択するか、左側のツリー ビューでパイプラインの名前を選択し、パイプライン エディターに切り替えます。

29. **[アクティビティ]** ペインで **[全般]** を展開し、 **[アクティビティ]** ペインからパイプライン デザイナー画面に **[ストアド プロシージャ]** アクティビティをドラッグします。 コピー アクティビティの緑 (成功) の出力をストアド プロシージャ アクティビティに接続します。

30. パイプライン デザイナーで**ストアド プロシージャ アクティビティ**を選択し、その名前を **SPtoUpdateWatermarkActivity** に変更します。

31. **[SQL アカウント]** タブに切り替えて、 **[リンクされたサービス]** で **[QLDBEdgeLinkedService]** を選択します。

32. **[ストアド プロシージャ]** タブに切り替えて、次の手順を実行します。

    1. **[ストアド プロシージャ名]** で、 **[dbo].[usp_write_watermark]** を選択します。

    2. ストアド プロシージャのパラメーターの値を指定するには、 **[Import parameter]\(インポート パラメーター\)** を選択し、各パラメーターに次の値を入力します。

    |名前|種類|値|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|string|@{activity('OldWaterMark').output.firstRow.TableName}|

33. パイプライン設定を検証するには、ツール バーの **[検証]** を選択します。 検証エラーがないことを確認します。 **[Pipeline Validation Report]\(パイプライン検証レポート\)** ウィンドウを閉じるには、 **[>>]** を選択します。

34. **[すべて公開]** ボタンを選択して、エンティティ (リンクされたサービス、データセット、およびパイプライン) を Azure Data Factory サービスに公開します。 公開操作が成功したことを確認するメッセージが表示されるまで待ちます。

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>スケジュールに基づいてパイプラインをトリガーする

1. パイプライン ツール バーで **[トリガーの追加]** を選択し、 **[New/Edit]\(新規作成/編集\)** を選択して、 **[新規]** を選択します。

2. トリガーに **HourlySync** という名前を付けます。 **[種類]** で、 **[スケジュール]** を選択します。 **[繰り返し]** を 1 時間ごとに設定します。

3. **[OK]** を選択します。

4. **[すべて公開]** を選択します。

5. **[Trigger Now]\(今すぐトリガー\)** を選択します。

6. 左側で **[監視]** タブに切り替えます。 手動トリガーによってトリガーされたパイプラインの実行の状態を確認できます。 **[最新の情報に更新]** を選択して、一覧を更新します。

## <a name="next-steps"></a>次の手順

このチュートリアルの Azure Data Factory パイプラインでは、SQL Database Edge インスタンス上のテーブルから Azure Blob Storage 内のある場所に 1 時間ごとにデータがコピーされます。 他のシナリオでの Data Factory の使用については、これらの[チュートリアル](../data-factory/tutorial-copy-data-portal.md)を参照してください。
