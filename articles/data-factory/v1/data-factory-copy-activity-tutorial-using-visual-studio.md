---
title: チュートリアル:コピー アクティビティがあるパイプラインを Visual Studio で作成する | Microsoft Docs
description: このチュートリアルでは、Visual Studio を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.custom: vs-azure
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 934effe585b85075a80eede4236258d4a428b9ce
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836566"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>チュートリアル:コピー アクティビティがあるパイプラインを Visual Studio で作成する
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [コピー ウィザード](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager テンプレート](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[コピー アクティビティのチュートリアル](../quickstart-create-data-factory-dot-net.md)に関するページを参照してください。 

この記事では、Microsoft Visual Studio を使用して Azure Blob Storage から Azure SQL データベースにデータをコピーするパイプラインを備えたデータ ファクトリを作成します。 Azure Data Factory の使用経験がない場合は、このチュートリアルを実行する前に、「[Azure Data Factory の概要](data-factory-introduction.md)」を参照してください。   

このチュートリアルでは、1 つのアクティビティ (コピー アクティビティ) が含まれたパイプラインを作成します。 コピー アクティビティは、サポートされているデータ ストアからサポートされているシンク データ ストアにデータをコピーします。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するセクションを参照してください。 このアクティビティは、安全で信頼性の高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。 コピー アクティビティの詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。

1 つのパイプラインには複数のアクティビティを含めることができます。 また、1 つのアクティビティの出力データセットを別のアクティビティの入力データセットとして指定することで、2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) ことができます。 詳細については、「[パイプライン内の複数アクティビティ](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)」を参照してください。

> [!NOTE] 
> このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Hadoop クラスターを使用してデータを変換するパイプラインを作成する方法のチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 「 [チュートリアルの概要](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 」に目を通し、 **前提条件** の手順を完了する必要があります。       
2. Data Factory インスタンスを作成するには、サブスクリプション/リソース グループ レベルで [Data Factory の共同作業者](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ロールのメンバーである必要があります。
3. コンピューターに以下がインストールされている必要があります。 
   * Visual Studio 2013 または Visual Studio 2015
   * Azure SDK for Visual Studio 2013 または Visual Studio 2015 をダウンロードします。 [Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)に移動し、 **.NET** セクションの **[VS 2013]** または **[VS 2015]** をクリックします。
   * Visual Studio 用の最新の Azure Data Factory プラグイン ([VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) または [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)) をダウンロードします。 次の手順を実行して、プラグインを更新することもできます。メニューで **[ツール]**  ->  **[拡張機能と更新プログラム]**  ->  **[オンライン]**  ->  **[Visual Studio ギャラリー]**  ->  **[Microsoft Azure Data Factory Tools for Visual Studio]**  ->  **[更新]** の順にクリックします。

## <a name="steps"></a>手順
このチュートリアルの一部として実行する手順を次に示します。

1. **リンクされたサービス**をデータ ファクトリに作成します。 この手順では、2 つのリンクされたサービスを作成します (タイプ: Azure Storage、Azure SQL Database)。 
    
    AzureStorageLinkedService は、Azure ストレージ アカウントをデータ ファクトリにリンクします。 [前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部として、コンテナーを作成し、データをこのストレージ アカウントにアップロードしました。   

    AzureSqlLinkedService は、Azure SQL データベースをデータ ファクトリにリンクします。 Blob Storage からコピーされたデータは、このデータベースに格納されます。 [前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部として、このデータベースに SQL テーブルを作成しました。     
2. 入力および出力**データセット**をデータ ファクトリに作成します。  
    
    Azure Storage のリンクされたサービスは、Data Factory サービスが実行時に Azure ストレージ アカウントへの接続に使用する接続文字列を指定します。 また、入力 BLOB データセットは、コンテナーと、入力データが含まれているフォルダーを指定します。  

    同様に、Azure SQL Database のリンクされたサービスは、Data Factory サービスが実行時に Azure SQL データベースへの接続に使用する接続文字列を指定します。 出力 SQL テーブル データセットは、BLOB ストレージのデータのコピー先となるデータベース内のテーブルを指定します。
3. データ ファクトリに**パイプライン**を作成します。 この手順では、コピー アクティビティのあるパイプラインを作成します。   
    
    コピー アクティビティでは、Azure Blob Storage の BLOB から Azure SQL データベースのテーブルにデータをコピーします。 パイプラインでコピー アクティビティを使用して、任意のサポートされているソースから任意のサポートされているターゲットにデータをコピーできます。 サポートされているデータ ストアの一覧については、[データ移動アクティビティ](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関する記事を参照してください。 
4. Data Factory のエンティティ (リンクされたサービス、データセット、テーブル、およびパイプライン) をデプロイするときに Azure **データ ファクトリ**を作成します。 

## <a name="create-visual-studio-project"></a>Visual Studio プロジェクトの作成
1. **Visual Studio 2015** を起動します。 **[ファイル]** をクリックし、 **[新規作成]** をポイントして、 **[プロジェクト]** をクリックします。 **[新しいプロジェクト]** ダイアログ ボックスが表示されます。  
2. **[新しいプロジェクト]** ダイアログ ボックスで、 **[DataFactory]** テンプレートを選択し、 **[Empty Data Factory Project (空の Data Factory プロジェクト)]** をクリックします。  
   
    ![[新しいプロジェクト] ダイアログ ボックス](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. プロジェクトの名前、ソリューションの場所、およびソリューションの名前を指定し、 **[OK]** をクリックします。
   
    ![ソリューション エクスプローラー](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このチュートリアルでは、Azure HDInsight、Azure Data Lake Analytics などのコンピューティング サービスを使用しません。 ここでは、Azure Storage (ソース) と Azure SQL Database (ターゲット) の 2 種類のデータ ストアを使用します。 

したがって、2 つのリンクされたサービスを作成します (タイプ: AzureStorage、AzureSqlDatabase)。  

Azure Storage のリンクされたサービスでは、Azure ストレージ アカウントをデータ ファクトリにリンクします。 このストレージ アカウントは、[前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部としてコンテナーを作成し、データをアップロードしたストレージ アカウントです。   

Azure SQL のリンクされたサービスは、Azure SQL データベースをデータ ファクトリにリンクします。 Blob Storage からコピーされたデータは、このデータベースに格納されます。 [前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部として、このデータベースに emp テーブルを作成しました。

リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。 コピー アクティビティでサポートされているすべてのソースとシンクについては、 [サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に関する記事を参照してください。 Data Factory でサポートされているコンピューティング サービスの一覧については、「 [コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md) 」を参照してください。 このチュートリアルでは、コンピューティング サービスは使用しません。 

### <a name="create-the-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成します。
1. **ソリューション エクスプローラー**の **[リンクされたサービス]** を右クリックして、 **[追加]** をポイントし、 **[新しい項目]** をクリックします。      
2. **[新しい項目の追加]** ダイアログ ボックスで、一覧から **[Azure Storage Linked Service (Azure Storage のリンクされたサービス)]** を選択し、 **[追加]** をクリックします。 
   
    ![新規のリンクされたサービス](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. `<accountname>` と `<accountkey>`* を Azure ストレージ アカウントの名前とキーで置き換えます。 
   
    ![Azure Storage のリンクされたサービス](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. **AzureStorageLinkedService1.json** ファイルを保存します。

    リンクされたサービスの定義の中の JSON プロパティの詳細については、[Azure Blob Storage コネクタ](data-factory-azure-blob-connector.md#linked-service-properties)に関する記事を参照してください。

### <a name="create-the-azure-sql-linked-service"></a>Azure SQL のリンクされたサービスを作成します。
1. **ソリューション エクスプローラー**で **[リンクされたサービス]** ノードをもう一度右クリックして **[追加]** をポイントし、 **[新しい項目]** をクリックします。 
2. このとき、 **[Azure SQL Linked Service (Azure SQL のリンクされたサービス)]** を選択し、 **[追加]** をクリックします。 
3. **AzureSqlLinkedService1.json** ファイルで、`<servername>`、`<databasename>`、`<username@servername>`、`<password>` を Azure SQL のサーバー名、データベース名、ユーザー アカウント、パスワードに置き換えます。    
4. **AzureSqlLinkedService1.json** ファイルを保存します。 
    
    これらの JSON プロパティの詳細については、[Azure SQL Database コネクタ](data-factory-azure-sql-connector.md#linked-service-properties)に関する記事を参照してください。


## <a name="create-datasets"></a>データセットを作成する
前の手順では、Azure ストレージ アカウントと Azure SQL データベースをデータ ファクトリにリンクするためのリンクされたサービスを作成しました。 この手順では、InputDataset と OutputDataset という名前の 2 つのデータセットを定義します。これらはそれぞれ、AzureStorageLinkedService1 と AzureSqlLinkedService1 が参照するデータ ストアに格納されている入力データと出力データを表します。

Azure Storage のリンクされたサービスは、Data Factory サービスが実行時に Azure ストレージ アカウントへの接続に使用する接続文字列を指定します。 また、入力 BLOB データセット (InputDataset) は、コンテナーと、入力データが含まれているフォルダーを指定します。  

同様に、Azure SQL Database のリンクされたサービスは、Data Factory サービスが実行時に Azure SQL データベースへの接続に使用する接続文字列を指定します。 出力 SQL テーブル データセット (OututDataset) は、BLOB ストレージのデータのコピー先となるデータベース内のテーブルを指定します。 

### <a name="create-input-dataset"></a>入力データセットの作成
この手順では、BLOB ファイル (emp.txt) をポイントする InputDataset という名前のデータセットを作成します。このファイルは、リンクされたサービス AzureStorageLinkedService1 が表す Azure Storage 内の BLOB コンテナー (adftutorial) のルート フォルダーにあります。 fileName の値を指定しなかった場合やこれをスキップした場合、入力フォルダー内のすべての BLOB のデータがターゲットにコピーされます。 このチュートリアルでは、fileName の値を指定します。 

ここでは、用語として "データセット" ではなく "テーブル" を使用します。 テーブルは四角形のデータセットであり、現時点でサポートされている唯一の種類のデータセットです。 

1. **ソリューション エクスプローラー**の **[テーブル]** を右クリックして **[追加]** をポイントし、 **[新しい項目]** をクリックします。
2. **[新しい項目の追加]** ダイアログ ボックスで、 **[Azure BLOB]** をクリックし、 **[追加]** をクリックします。   
3. JSON テキストを次のテキストで置き換え、 **AzureBlobLocation1.json** ファイルを保存します。 

   ```json   
   {
    "name": "InputDataset",
    "properties": {
      "structure": [
        {
          "name": "FirstName",
          "type": "String"
        },
        {
          "name": "LastName",
          "type": "String"
        }
      ],
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
   }
   ``` 
    次の表に、このスニペットで使用される JSON プロパティの説明を示します。

    | プロパティ | 説明 |
    |:--- |:--- |
    | type | データは Azure Blob Storage に存在するため、type プロパティを **AzureBlob** に設定しています。 |
    | linkedServiceName | 前に作成した **AzureStorageLinkedService** を参照します。 |
    | folderPath | BLOB **コンテナー**と、入力 BLOB を格納する**フォルダー**を指定します。 このチュートリアルでは、adftutorial は BLOB コンテナーで、フォルダーはルート フォルダーです。 | 
    | fileName | このプロパティは省略可能です。 このプロパティを省略した場合は、folderPath のすべてのファイルが取得されます。 このチュートリアルでは fileName に **emp.txt** が指定されているため、このファイルのみが処理のために取得されます。 |
    | format -> type |入力ファイルはテキスト形式のため、**TextFormat** を使用します。 |
    | columnDelimiter | 入力ファイル内の列は**コンマ (`,`)** で区切られています。 |
    | frequency/interval | frequency を **Hour** に設定し、interval を **1** に設定しています。つまり、**1 時間ごと**に入力スライスが取得されます。 言い換えると、Data Factory サービスは、指定された BLOB コンテナー (**adftutorial**) のルート フォルダーにある入力データを 1 時間ごとに検索します。 パイプラインの開始時刻の前または終了時刻の後ではなく、開始時刻と終了時刻の間のデータが検索されます。  |
    | external | このパイプラインによってデータが生成されない場合は、このプロパティを **true** に設定します。 このチュートリアルの入力データは emp.txt ファイルに存在し、このパイプラインで生成されるわけではないため、このプロパティを true に設定します。 |

    これらの JSON プロパティの詳細については、[Azure BLOB コネクタ](data-factory-azure-blob-connector.md#dataset-properties)に関する記事を参照してください。   

### <a name="create-output-dataset"></a>出力データセットの作成
この手順では、 **OutputDataset**という名前の出力データセットを作成します。 このデータセットは、 **AzureSqlLinkedService1**で表される Azure SQL データベース内の SQL テーブルをポイントします。 

1. **ソリューション エクスプローラー**の **[テーブル]** をもう一度右クリックし、 **[追加]** をポイントし、 **[新しい項目]** をクリックします。
2. **[新しい項目の追加]** ダイアログ ボックスで、 **[Azure SQL]** を選択し、 **[追加]** をクリックします。 
3. JSON テキストを次の JSON テキストで置き換え、 **AzureSqlTableLocation1.json** ファイルを保存します。

   ```json
    {
     "name": "OutputDataset",
     "properties": {
       "structure": [
         {
           "name": "FirstName",
           "type": "String"
         },
         {
           "name": "LastName",
           "type": "String"
         }
       ],
       "type": "AzureSqlTable",
       "linkedServiceName": "AzureSqlLinkedService1",
       "typeProperties": {
         "tableName": "emp"
       },
       "availability": {
         "frequency": "Hour",
         "interval": 1
       }
     }
    }
    ```
    次の表に、このスニペットで使用される JSON プロパティの説明を示します。

    | プロパティ | 説明 |
    |:--- |:--- |
    | type | type プロパティを **AzureSqlTable** に設定します。これは、データを Azure SQL データベースのテーブルにコピーするためです。 |
    | linkedServiceName | 前に作成した **AzureSqlLinkedService** を参照します。 |
    | tableName | データのコピー先となる**テーブル**を指定します。 | 
    | frequency/interval | frequency は **Hour**、interval は **1** に、それぞれ設定されています。これは、出力スライスがパイプラインの開始時刻から終了時刻までの間 **1 時間ごと**に生成されることを表します (出力スライスは、開始時刻の前および終了時刻の後には生成されません)。  |

    データベース内の emp テーブルには、**ID**、**FirstName**、**LastName** の 3 つの列があります。 ID は ID 列であるため、ここで指定する必要があるのは **FirstName** と **LastName** のみです。

    これらの JSON プロパティの詳細については、[Azure SQL コネクタ](data-factory-azure-sql-connector.md#dataset-properties)に関する記事を参照してください。

## <a name="create-pipeline"></a>パイプラインの作成
この手順では、**InputDataset** を入力、**OutputDataset** を出力として使用する**コピー アクティビティ**を備えたパイプラインを作成します。

現在、スケジュールは出力データセットによって開始されます。 このチュートリアルでは、1 時間ごとにスライスを生成するように出力データセットを構成します。 パイプラインの開始時刻と終了時刻の差は 1 日 (24 時間) です。 したがって、24 個の出力データセットのスライスがパイプラインによって生成されます。 

1. **ソリューション エクスプローラー**の **[パイプライン]** を右クリックして **[追加]** をポイントし、 **[新しい項目]** をクリックします。  
2. **[新しい項目の追加]** ダイアログ ボックスで **[データ パイプラインのコピー]** を選択し、 **[追加]** をクリックします。 
3. JSON テキストを次の JSON テキストで置き換え、 **CopyActivity1.json** ファイルを保存します。

   ```json   
    {
     "name": "ADFTutorialPipeline",
     "properties": {
       "description": "Copy data from a blob to Azure SQL table",
       "activities": [
         {
           "name": "CopyFromBlobToSQL",
           "type": "Copy",
           "inputs": [
             {
               "name": "InputDataset"
             }
           ],
           "outputs": [
             {
               "name": "OutputDataset"
             }
           ],
           "typeProperties": {
             "source": {
               "type": "BlobSource"
             },
             "sink": {
               "type": "SqlSink",
               "writeBatchSize": 10000,
               "writeBatchTimeout": "60:00:00"
             }
           },
           "Policy": {
             "concurrency": 1,
             "executionPriorityOrder": "NewestFirst",
             "style": "StartOfInterval",
             "retry": 0,
             "timeout": "01:00:00"
           }
         }
       ],
       "start": "2017-05-11T00:00:00Z",
       "end": "2017-05-12T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
   - activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。 コピー アクティビティの詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。 Data Factory ソリューションでは、[データ変換アクティビティ](data-factory-data-transformation-activities.md)を使用することもできます。
   - アクティビティの入力を **InputDataset** に設定し、出力を **OutputDataset** に設定します。 
   - **typeProperties** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの完全な一覧については、[サポートされるデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するセクションを参照してください。 サポートされているデータ ストアをソースおよびシンクとして使用する方法については、表内のリンクをクリックしてください。  
     
     **start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。 日付の部分のみを指定し、時刻の部分をスキップすることもできます。 たとえば、"2016-02-03" と "2016-02-03T00:00:00Z" は同じです。
     
     start と end の日時は、いずれも [ISO 形式](https://en.wikipedia.org/wiki/ISO_8601)である必要があります。 例: 2016-10-14T16:32:41Z。 **end** の時刻は省略可能ですが、このチュートリアルでは使用します。 
     
     **end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティの値として指定します。
     
     前の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。

     パイプライン定義内の JSON プロパティの説明については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 コピー アクティビティ定義内の JSON プロパティの説明については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。 BlobSource でサポートされる JSON プロパティの説明については、[Azure BLOB コネクタ](data-factory-azure-blob-connector.md)に関する記事を参照してください。 SqlSink でサポートされる JSON プロパティの説明については、[Azure SQL Database コネクタ](data-factory-azure-sql-connector.md)に関する記事を参照してください。

## <a name="publishdeploy-data-factory-entities"></a>Data Factory エンティティの発行/デプロイ
この手順では、先ほど作成した Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) を発行します。 また、これらのエンティティを保持するために、新しいデータ ファクトリの名前を指定して作成します。  

1. ソリューション エクスプローラーでプロジェクトを右クリックし、 **[発行]** をクリックします。 
2. **[Microsoft アカウントへのサインイン]** ダイアログ ボックスが表示されたら、Azure サブスクリプションを所有するアカウントの資格情報を入力し、 **[サインイン]** をクリックします。
3. 次のダイアログ ボックスが表示されます。
   
   ![[発行] ダイアログ ボックス](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. データ ファクトリの構成ページで、次の手順を行います。 
   
   1. **[Data Factory の新規作成]** オプションを選択します。
   2. **[名前]** に「**VSTutorialFactory**」と入力します。  
      
      > [!IMPORTANT]
      > Azure Data Factory の名前はグローバルに一意にする必要があります。 発行時にデータ ファクトリの名前に関するエラーが発生した場合は、そのデータ ファクトリの名前を変更して (yournameVSTutorialFactory など)、発行し直してください。 Data Factory アーティファクトの名前付け規則については、 [Data Factory - 名前付け規則](data-factory-naming-rules.md) に関するトピックを参照してください。        
      > 
      > 
   3. **[サブスクリプション]** フィールドで Azure サブスクリプションを選択します。
      
      > [!IMPORTANT]
      > サブスクリプションが表示されない場合は、サブスクリプションの管理者または共同管理者のアカウントを使用してログインしたことを確認します。  
      > 
      > 
   4. 作成するデータ ファクトリの **リソース グループ** を選択します。 
   5. データ ファクトリの **リージョン** を選択します。 Data Factory サービスによってサポートされているリージョンのみ、ドロップダウン リストに表示されます。
   6. **[次へ]** をクリックし、 **[項目の発行]** ページに切り替えます。
      
       ![Configure data factory page](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. **[項目の発行]** ページで、すべての Data Factory エンティティが選択されていることを確認し、 **[次へ]** をクリックして **[概要]** ページに切り替えます。
   
   ![Publish items page](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. 概要を確認してから **[次へ]** をクリックし、デプロイ プロセスを開始して **[デプロイ ステータス]** を表示します。
   
   ![Publish summary page](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. **[デプロイ ステータス]** ページに、デプロイメント プロセスのステータスが表示されます。 デプロイメントが完了したら、[完了] をクリックします。
 
   ![Deployment status page](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

以下の点に注意してください。 

* エラー "This subscription is not registered to use namespace Microsoft.DataFactory (このサブスクリプションは、名前空間 Microsoft.DataFactory を使用するように登録されていません)" が表示された場合は、以下のいずれかの操作を行ってから、もう一度発行してみます。 
  
  * Azure PowerShell で次のコマンドを実行して、Data Factory プロバイダーを登録します。 

    ```powershell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Data Factory プロバイダーが登録されたことを確認するには、次のコマンドを実行します。 
    
    ```powershell
    Get-AzResourceProvider
    ```
  * Azure サブスクリプションを使用して [Azure ポータル](https://portal.azure.com) にログインし、[Data Factory] ブレードに移動するか、Azure ポータルでデータ ファクトリを作成します。 この操作によって、プロバイダーが自動的に登録されます。
* データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。

> [!IMPORTANT]
> Data Factory インスタンスを作成するには、Azure サブスクリプションの管理者または共同管理者である必要があります。

## <a name="monitor-pipeline"></a>パイプラインを監視する
データ ファクトリのホーム ページに移動します。

1. [Azure Portal](https://portal.azure.com) にログインします。
2. 左側のメニューの **[その他のサービス]** をクリックし、 **[データ ファクトリ]** をクリックします。

    ![Browse data factories](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. データ ファクトリの名前の入力を開始します。

    ![データ ファクトリの名前](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. 結果の一覧で目的のデータ ファクトリをクリックし、データ ファクトリのホーム ページを表示します。

    ![データ ファクトリのホーム ページ](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. [データセットとパイプラインの監視](data-factory-monitor-manage-pipelines.md)に関するセクションの手順に従って、このチュートリアルで作成したパイプラインとデータセットを監視します。 現時点では、Visual Studio は Data Factory パイプラインの監視をサポートしていません。 

## <a name="summary"></a>まとめ
このチュートリアルでは、Azure Data Factory を作成し、Azure BLOB から Azure SQL データベースにデータをコピーしました。 また、Visual Studio を使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成しました。 以下は、このチュートリアルで実行した手順の概要です。  

1. Azure **データ ファクトリ**を作成しました。
2. 次の **リンクされたサービス**を作成しました。
   1. 入力データを保持する Azure ストレージ アカウントをリンクするための、 **Azure Storage** のリンクされたサービス。     
   2. 出力データを保持する Azure SQL データベースをリンクするための、 **Azure SQL** のリンクされたサービス。 
3. パイプラインの入力データと出力データを記述する **データセット**を作成しました。
4. ソースとして **BlobSource**、シンクとして **SqlSink** を持つ**コピー アクティビティ**がある**パイプライン**を作成しました。 

HDInsight Hive アクティビティを使用して Azure HDInsight クラスターを使用してデータを変換する方法については、[Hadoop クラスターを使用してデータを変換する初めてのパイプラインを作成する方法のチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。

2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) には、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。 詳細については、[Data Factory でのスケジュールと実行](data-factory-scheduling-and-execution.md)に関するページを参照してください。 

## <a name="view-all-data-factories-in-server-explorer"></a>サーバー エクスプローラーを使用したすべてのデータ ファクトリの表示
このセクションでは、Visual Studio のサーバー エクスプローラーを使用して、Azure サブスクリプション内のすべてのデータ ファクトリを表示し、既存のデータ ファクトリに基づいて Visual Studio プロジェクトを作成する方法について説明します。 

1. **Visual Studio** のメニューで **[ビュー]** をクリックし、 **[サーバー エクスプローラー]** をクリックします。
2. [サーバー エクスプローラー] ウィンドウで、 **[Azure]** 、 **[Data Factory]** の順に展開します。 **[Visual Studio にサインイン]** が表示されたら、Azure サブスクリプションに関連付けられている**アカウント**を入力し、 **[続行]** をクリックします。 **パスワード**を入力し、 **[サインイン]** をクリックします。 Visual Studio は、サブスクリプション内のすべての Azure データ ファクトリに関する情報を取得しようとします。 **[Data Factory Task List (Data Factory タスク リスト)]** ウィンドウで、この操作のステータスを確認します。

    ![[サーバー エクスプローラー]](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>既存のデータ ファクトリの Visual Studio プロジェクトの作成

- サーバー エクスプローラーでデータ ファクトリを右クリックし、 **[Export Data Factory to New Project]\(データ ファクトリを新しいプロジェクトにエクスポートする\)** を選択して、既存のデータ ファクトリに基づいて Visual Studio プロジェクトを作成します。

    ![Data Factory の Visual Studio プロジェクトへのエクスポート](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Visual Studio の Data Factory ツールを更新する
Visual Studio の Azure Data Factory ツールを更新するには、次の手順に従います。

1. メニューで **[ツール]** をクリックし、 **[拡張機能と更新プログラム]** を選択します。 
2. 左ウィンドウで **[更新]** を選択し、 **[Visual Studio ギャラリー]** を選択します。
3. **[Azure Data Factory tools for Visual Studio]** を選択して、 **[更新]** をクリックします。 このエントリが表示されない場合は、ツールは既に最新バージョンです。 

## <a name="use-configuration-files"></a>構成ファイルを使用する
リンクされたサービス/テーブル/パイプラインには、Visual Studio から構成ファイルを使用して環境ごとに異なるプロパティを構成できます。

Azure Storage のリンクされたサービスに関して次のような JSON 定義があるとします。 Data Factory エンティティのデプロイ先となる環境 (開発/テスト/運用) ごとに異なる値を **connectionString** の accountname と accountkey に指定するには、 環境ごとに個別の構成ファイルを使用します。

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>構成ファイルを追加する
環境ごとに次の手順を実行して構成ファイルを追加します。   

1. Visual Studio ソリューションで Data Factory プロジェクトを右クリックし、 **[追加]** をポイントして、 **[新しい項目]** をクリックします。
2. 左側にあるインストールされたテンプレートの一覧で **[構成]** を選択し、 **[構成ファイル]** を選択して、構成ファイルの**名前**を入力し、 **[追加]** をクリックします。

    ![Add configuration file](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. 次の形式で構成パラメーターとその値を追加します。

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    この例で構成しているのは、Azure Storage のリンクされたサービスと Azure SQL のリンクされたサービスの connectionString プロパティです。 名前を指定するための構文が [JsonPath](https://goessner.net/articles/JsonPath/)であることに注目してください。   

    ここで、JSON のプロパティが、次のコードのように値の配列になっているとします。  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    次の構成ファイルのように、プロパティを構成します (0 から始まるインデックスを使用)。

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>スペースが含まれているプロパティ名
プロパティ名にスペースが含まれている場合は、次の例 (Database server name) のように、角かっこを使用します。

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>構成を指定してソリューションをデプロイする
Azure Data Factory のエンティティを VS で発行するときに、その発行操作に使用する構成を指定できます。

構成ファイルを使用して Azure Data Factory プロジェクトのエンティティを発行するには、次の手順を実行します。   

1. Data Factory プロジェクトを右クリックして **[発行]** をクリックし、 **[Publish Items (項目の発行)]** ダイアログ ボックスを表示します。
2. **[Configure data factory (データ ファクトリの構成)]** ページで、既存のデータ ファクトリを選択するか、データ ファクトリを作成するために値を指定して、 **[次へ]** をクリックします。   
3. **[Publish Items (項目の発行)]** ページのドロップダウン リストに、 **[Select Deployment Config (デプロイ構成の選択)]** フィールドで使用できる構成が表示されます。

    ![Select config file](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. 使用する**構成ファイル**を選択し、 **[次へ]** をクリックします。
5. JSON ファイルの名前が **[概要]** ページに表示されていることを確認し、 **[次へ]** をクリックします。
6. デプロイ操作が終了したら、 **[完了]** をクリックします。

実際にデプロイすると、JSON ファイルに指定されているプロパティの値が構成ファイルの値を使用して設定された後、Azure Data Factory サービスにエンティティがデプロイされます。   

## <a name="use-azure-key-vault"></a>Azure Key Vault の使用
接続文字列などの機密データをコード リポジトリにコミットすることは、お勧めしません。また、多くの場合、セキュリティ ポリシーに違反します。 機密情報を Azure Key Vault に格納し、Data Factory エンティティの発行中に使用する方法について学習するには、GitHub の [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) サンプルを参照してください。 Visual Studio の Secure Publish 拡張機能を使用すると、機密情報を Key Vault に格納することができ、リンクされたサービスやデプロイ構成ではそれらへの参照だけが指定されます。 これらの参照は、Data Factory エンティティを Azure に発行するときに解決されます。 これらのファイルは、機密情報を公開せずに、ソース リポジトリにコミットできます。


## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Blob Storage をコピー操作のソース データ ストア、Azure SQL データベースをターゲット データ ストアとして使用しました。 次の表は、コピー アクティビティによってソースおよびターゲットとしてサポートされているデータ ストアの一覧です。 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

データ ストアにデータをコピーしたり、データ ストアからデータをコピーしたりする方法を確認するには、表のデータ ストアのリンクをクリックしてください。
