---
title: チュートリアル:データを移動するパイプラインを Azure PowerShell で作成する | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell を使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ce9566618870419e22dc9c420e77ef2ed6cd3c81
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836546"
---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>チュートリアル:データを移動する Data Factory パイプラインを Azure PowerShell で作成する
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [コピー ウィザード](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager テンプレート](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[コピー アクティビティのチュートリアル](../quickstart-create-data-factory-powershell.md)に関するページを参照してください。 

この記事では、PowerShell を使用して Azure Blob Storage から Azure SQL データベースにデータをコピーするパイプラインを備えたデータ ファクトリを作成します。 Azure Data Factory の使用経験がない場合は、このチュートリアルを実行する前に、「[Azure Data Factory の概要](data-factory-introduction.md)」を参照してください。   

このチュートリアルでは、1 つのアクティビティ (コピー アクティビティ) が含まれたパイプラインを作成します。 コピー アクティビティは、サポートされているデータ ストアからサポートされているシンク データ ストアにデータをコピーします。 ソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するセクションを参照してください。 このアクティビティは、安全で信頼性の高いスケーラブルな方法によってさまざまなデータ ストア間でデータをコピーできる、グローバルに利用可能なサービスによって動作します。 コピー アクティビティの詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。

1 つのパイプラインには複数のアクティビティを含めることができます。 また、1 つのアクティビティの出力データセットを別のアクティビティの入力データセットとして指定することで、2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) ことができます。 詳細については、「[パイプライン内の複数アクティビティ](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)」を参照してください。

> [!NOTE]
> この記事では、すべての Data Factory コマンドレットを取り上げているわけではありません。 これらのコマンドレットに関する包括的なドキュメントについては、[Data Factory コマンドレット リファレンス](/powershell/module/az.datafactory)を参照してください。
> 
> このチュートリアルのデータ パイプラインでは、ソース データ ストアからターゲット データ ストアにデータをコピーします。 Azure Data Factory を使用してデータを変換する方法のチュートリアルについては、[Hadoop クラスターを使用してデータを変換するパイプラインを作成する方法のチュートリアル](data-factory-build-your-first-pipeline.md)を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- [チュートリアルの前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関する記事に記載されている前提条件を満たしてください。
- **Azure PowerShell**をインストールします。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページに記載されている手順に従います。

## <a name="steps"></a>手順
このチュートリアルの一部として実行する手順を次に示します。

1. Azure **データ ファクトリ**を作成します。 この手順では、ADFTutorialDataFactoryPSH という名前のデータ ファクトリを作成します。 
1. **リンクされたサービス**をデータ ファクトリに作成します。 この手順では、2 つのリンクされたサービスを作成します (タイプ: Azure Storage、Azure SQL Database)。 
    
    AzureStorageLinkedService は、Azure ストレージ アカウントをデータ ファクトリにリンクします。 [前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部として、コンテナーを作成し、データをこのストレージ アカウントにアップロードしました。   

    AzureSqlLinkedService は、Azure SQL データベースをデータ ファクトリにリンクします。 Blob Storage からコピーされたデータは、このデータベースに格納されます。 [前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部として、このデータベースに SQL テーブルを作成しました。   
1. 入力および出力**データセット**をデータ ファクトリに作成します。  
    
    Azure Storage のリンクされたサービスは、Data Factory サービスが実行時に Azure ストレージ アカウントへの接続に使用する接続文字列を指定します。 また、入力 BLOB データセットは、コンテナーと、入力データが含まれているフォルダーを指定します。  

    同様に、Azure SQL Database のリンクされたサービスは、Data Factory サービスが実行時に Azure SQL データベースへの接続に使用する接続文字列を指定します。 出力 SQL テーブル データセットは、BLOB ストレージのデータのコピー先となるデータベース内のテーブルを指定します。
1. データ ファクトリに**パイプライン**を作成します。 この手順では、コピー アクティビティのあるパイプラインを作成します。   
    
    コピー アクティビティでは、Azure Blob Storage の BLOB から Azure SQL データベースのテーブルにデータをコピーします。 パイプラインでコピー アクティビティを使用して、任意のサポートされているソースから任意のサポートされているターゲットにデータをコピーできます。 サポートされているデータ ストアの一覧については、[データ移動アクティビティ](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関する記事を参照してください。 
1. パイプラインを監視します。 この手順では、PowerShell を使用して、入力および出力データセットのスライスを**監視**します。

## <a name="create-a-data-factory"></a>Data Factory を作成する。
> [!IMPORTANT]
> [チュートリアルの前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を完了します (まだ完了していない場合)。   

データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインには、1 つまたは複数のアクティビティを含めることができます。 たとえば、コピー元からコピー先のデータ ストアにデータをコピーするコピー アクティビティや、Hive スクリプトを実行し、入力データを変換して出力データを生成する HDInsight Hive アクティビティなどを含めることができます。 それでは、この手順でデータ ファクトリの作成から始めましょう。

1. **PowerShell**を起動します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。

    次のコマンドを実行して、Azure Portal へのサインインに使用するユーザー名とパスワードを入力します。

    ```powershell
    Connect-AzAccount
    ```   
   
    次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。

    ```powershell
    Get-AzSubscription
    ```

    次のコマンドを実行して、使用するサブスクリプションを選択します。 **&lt;NameOfAzureSubscription**&gt; を自分の Azure サブスクリプションの名前で置き換えます。

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```
1. 次のコマンドを実行して、 **ADFTutorialResourceGroup** という名前の Azure リソース グループを作成します。

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    
    このチュートリアルの一部の手順は、 **ADFTutorialResourceGroup**という名前のリソース グループを使用することを前提としています。 異なるリソース グループを使用する場合は、このチュートリアルで ADFTutorialResourceGroup の代わりにそのリソース グループを使用する必要があります。
1. **New-AzDataFactory** コマンドレットを実行し、**ADFTutorialDataFactoryPSH** という名前のデータ ファクトリを作成します。  

    ```powershell
    $df=New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    この名前は既に取得されている可能性があります。 そのため、プレフィックスまたはサフィックスを追加してデータ ファクトリの名前を一意にしたうえで (たとえば、ADFTutorialDataFactoryPSH05152017)、再度コマンドを実行します。  

以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 次のエラーが表示される場合は、名前を変更します (yournameADFTutorialDataFactoryPSH など)。 このチュートリアルの手順の実行中に、この名前を ADFTutorialFactoryPSH の代わりに使用します。 Data Factory アーティファクトについては、[Data Factory - 名前付け規則](data-factory-naming-rules.md)に関するトピックを参照してください。

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Data Factory インスタンスを作成するには、Azure サブスクリプションの共同作成者または管理者である必要があります。
* データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。
* 次のエラーが表示される場合があります。"**This subscription is not registered to use namespace Microsoft.DataFactory (このサブスクリプションは、名前空間 Microsoft.DataFactory を使用するように登録されていません)** " 次のいずれかを実行し、もう一度発行してみてください。

  * Azure PowerShell で次のコマンドを実行して、Data Factory プロバイダーを登録します。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Data Factory プロバイダーが登録されたことを確認するには、次のコマンドを実行します。

    ```powershell
    Get-AzResourceProvider
    ```
  * Azure サブスクリプションを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 Data Factory のブレードに移動するか、Azure Portal でデータ ファクトリを作成します。 この操作によって、プロバイダーが自動的に登録されます。

## <a name="create-linked-services"></a>リンクされたサービスを作成します
データ ストアおよびコンピューティング サービスをデータ ファクトリにリンクするには、リンクされたサービスをデータ ファクトリに作成します。 このチュートリアルでは、Azure HDInsight、Azure Data Lake Analytics などのコンピューティング サービスを使用しません。 ここでは、Azure Storage (ソース) と Azure SQL Database (ターゲット) の 2 種類のデータ ストアを使用します。 

したがって、AzureStorageLinkedService と AzureSqlLinkedService という名前の 2 つのリンクされたサービスを作成します (タイプ: AzureStorage、AzureSqlDatabase)。  

AzureStorageLinkedService は、Azure ストレージ アカウントをデータ ファクトリにリンクします。 このストレージ アカウントは、[前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部としてコンテナーを作成し、データをアップロードしたストレージ アカウントです。   

AzureSqlLinkedService は、Azure SQL データベースをデータ ファクトリにリンクします。 Blob Storage からコピーされたデータは、このデータベースに格納されます。 [前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)の一部として、このデータベースに emp テーブルを作成しました。 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Azure ストレージ アカウント用にリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。

1. 以下の内容を記述した **AzureStorageLinkedService.json** という名前の JSON ファイルを **C:\ADFGetStartedPSH** フォルダー内に作成します。(ADFGetStartedPSH フォルダーがない場合は作成します)。

    > [!IMPORTANT]
    > ファイルを保存する前に、&lt;accountname&gt; と &lt;accountkey&gt; を Azure ストレージ アカウントの名前とキーに置き換えます。 

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ``` 
1. **Azure PowerShell** で **ADFGetStartedPSH** フォルダーに切り替えます。
1. **New-AzDataFactoryLinkedService** コマンドレットを実行して、リンクされたサービスを作成します。**AzureStorageLinkedService** を作成します。 このコマンドレットと、このチュートリアルで使用する他の Data Factory コマンドレットでは、**ResourceGroupName** パラメーターと **DataFactoryName** パラメーターの値を渡す必要があります。 または、コマンドレットを実行するたびに ResourceGroupName と DataFactoryName を入力することなく、New-AzDataFactory コマンドレットから返された DataFactory オブジェクトを渡すことができます。 

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    出力例を次に示します。

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    このリンクされたサービスを作成する別の方法として、DataFactory オブジェクトを指定する代わりに、リソース グループ名とデータ ファクトリ名を指定します。  

    ```powershell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Azure SQL データベース用にリンクされたサービスを作成する
この手順では、Azure SQL データベースをデータ ファクトリにリンクします。

1. 以下の内容を記述した AzureSqlLinkedService.json という名前の JSON ファイルを C:\ADFGetStartedPSH フォルダー内に作成します。

    > [!IMPORTANT]
    > &lt;servername&gt;、&lt;databasename&gt;、&lt;username@servername&gt;、&lt;password&gt; を、Azure SQL のサーバー名、データベース名、ユーザー アカウント、パスワードに置き換えます。
    
    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
1. 次のコマンドを実行して、リンクされたサービスを作成します。

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```
    
    出力例を次に示します。

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   SQL データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定がオンになっていることを確認します。 この設定を確認してオンにするには、次の手順を実行します。

    1. [Azure ポータル](https://portal.azure.com)
    1. 左側にある **[その他のサービス]** をクリックし、 **[データベース]** カテゴリの **[SQL servers]\(SQL サーバー\)** をクリックします。
    1. SQL サーバーの一覧で、目的のサーバーを選択します。
    1. SQL サーバーのブレードで、 **[ファイアウォール設定の表示]** リンクをクリックします。
    1. **[ファイアウォールの設定]** ブレードの **[Azure サービスへのアクセスを許可する]** で **[オン]** をクリックします。
    1. ツール バーの **[保存]** をクリックします。 

## <a name="create-datasets"></a>データセットを作成する
前の手順では、Azure ストレージ アカウントと Azure SQL データベースをデータ ファクトリにリンクするためのリンクされたサービスを作成しました。 この手順では、InputDataset と OutputDataset という名前の 2 つのデータセットを定義します。これらはそれぞれ、AzureStorageLinkedService と AzureSqlLinkedService が参照するデータ ストアに格納されている入力データと出力データを表します。

Azure Storage のリンクされたサービスは、Data Factory サービスが実行時に Azure ストレージ アカウントへの接続に使用する接続文字列を指定します。 また、入力 BLOB データセット (InputDataset) は、コンテナーと、入力データが含まれているフォルダーを指定します。  

同様に、Azure SQL Database のリンクされたサービスは、Data Factory サービスが実行時に Azure SQL データベースへの接続に使用する接続文字列を指定します。 出力 SQL テーブル データセット (OututDataset) は、BLOB ストレージのデータのコピー先となるデータベース内のテーブルを指定します。 

### <a name="create-an-input-dataset"></a>入力データセットの作成
この手順では、BLOB ファイル (emp.txt) を参照する InputDataset という名前のデータセットを作成します。このファイルは、リンクされたサービス AzureStorageLinkedService が表す Azure Storage 内の BLOB コンテナー (adftutorial) のルート フォルダーにあります。 fileName の値を指定しなかった場合やこれをスキップした場合、入力フォルダー内のすべての BLOB のデータがターゲットにコピーされます。 このチュートリアルでは、fileName の値を指定します。  

1. 以下の内容を記述した **InputDataset.json** という名前の JSON ファイルを **C:\ADFGetStartedPSH** フォルダー内に作成します。

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
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
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
1. 次のコマンドを実行して、Data Factory データセットを作成します。

    ```powershell  
    New-AzDataFactoryDataset $df -File .\InputDataset.json
    ```
    出力例を次に示します。

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>出力データセットの作成
手順のこの部分では、 **OutputDataset**という名前の出力データセットを作成します。 このデータセットは、 **AzureSqlLinkedService**で表される Azure SQL データベース内の SQL テーブルをポイントします。 

1. 以下の内容を記述した **OutputDataset.json** という名前の JSON ファイルを **C:\ADFGetStartedPSH** フォルダー内に作成します。

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
            "linkedServiceName": "AzureSqlLinkedService",
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
1. 次のコマンドを実行して、データ ファクトリ データセットを作成します。

    ```powershell   
    New-AzDataFactoryDataset $df -File .\OutputDataset.json
    ```

    出力例を次に示します。

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>パイプラインを作成する。
この手順では、**InputDataset** を入力、**OutputDataset** を出力として使用する**コピー アクティビティ**を備えたパイプラインを作成します。

現在、スケジュールは出力データセットによって開始されます。 このチュートリアルでは、1 時間ごとにスライスを生成するように出力データセットを構成します。 パイプラインの開始時刻と終了時刻の差は 1 日 (24 時間) です。 したがって、24 個の出力データセットのスライスがパイプラインによって生成されます。 


1. 以下の内容を記述した **ADFTutorialPipeline.json** という名前の JSON ファイルを **C:\ADFGetStartedPSH** フォルダー内に作成します。

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
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```
    以下の点に注意してください。
   
   - activities セクションに、**type** が **Copy** に設定されたアクティビティが 1 つだけあります。 コピー アクティビティの詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。 Data Factory ソリューションでは、[データ変換アクティビティ](data-factory-data-transformation-activities.md)を使用することもできます。
   - アクティビティの入力を **InputDataset** に設定し、出力を **OutputDataset** に設定します。 
   - **typeProperties** セクションでは、ソースの種類として **BlobSource** が指定され、シンクの種類として **SqlSink** が指定されています。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの完全な一覧については、[サポートされるデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するセクションを参照してください。 サポートされているデータ ストアをソースおよびシンクとして使用する方法については、表内のリンクをクリックしてください。  
     
     **start** プロパティの値を現在の日付に置き換え、**end** プロパティの値を翌日の日付に置き換えます。 日付の部分のみを指定し、時刻の部分をスキップすることもできます。 たとえば、"2016-02-03" と "2016-02-03T00:00:00Z" は同じです。
     
     start と end の日時は、いずれも [ISO 形式](https://en.wikipedia.org/wiki/ISO_8601)である必要があります。 例: 2016-10-14T16:32:41Z。 **end** の時刻は省略可能ですが、このチュートリアルでは使用します。 
     
     **end** プロパティの値を指定しない場合、"**start + 48 時間**" として計算されます。 パイプラインを無期限に実行する場合は、**9999-09-09** を **end** プロパティの値として指定します。
     
     前の例では、各データ スライスが 1 時間ごとに生成されるため、データ スライスは 24 個になります。

     パイプライン定義内の JSON プロパティの説明については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 コピー アクティビティ定義内の JSON プロパティの説明については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。 BlobSource でサポートされる JSON プロパティの説明については、[Azure BLOB コネクタ](data-factory-azure-blob-connector.md)に関する記事を参照してください。 SqlSink でサポートされる JSON プロパティの説明については、[Azure SQL Database コネクタ](data-factory-azure-sql-connector.md)に関する記事を参照してください。
1. 次のコマンドを実行して、データ ファクトリ テーブルを作成します。

    ```powershell   
    New-AzDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    出力例を次に示します。 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**お疲れさまでした。** Azure Blob Storage から Azure SQL データベースにデータをコピーするパイプラインを備えた Azure データ ファクトリが正常に作成されました。 

## <a name="monitor-the-pipeline"></a>パイプラインの監視
この手順では、Azure PowerShell を使用して、Azure データ ファクトリの状況を監視します。

1. &lt;DataFactoryName&gt; をデータ ファクトリの名前で置き換え、**Get-AzDataFactory** を実行してその出力を $df 変数に割り当てます。

    ```powershell  
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    例:
    ```powershell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```
    
    次に、$df の内容を表示して次の出力を確認します。 
    
    ```
    PS C:\ADFGetStartedPSH> $df
    
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
1. **Get-AzDataFactorySlice** を実行し、**OutputDataset** のすべてのスライスの詳細を表示します。これは、パイプラインの出力データセットです。  

    ```powershell   
    Get-AzDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   この設定は、JSON パイプラインの **Start** の値と一致します。 現在の日付の 12 AM から次の日の 12 AM までの 1 時間ごとに 1 つずつ、合計 24 個のスライスが表示されます。

   出力の 3 つのサンプルのスライスを次に示します。 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0   

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
1. **Get-AzDataFactoryRun** を実行して、**特定の**スライスに関するアクティビティの実行の詳細を取得します。 前のコマンドの出力から日付/時刻値をコピーして、StartDateTime パラメーターの値を指定します。 

    ```powershell  
    Get-AzDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   出力例を次に示します。 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

Data Factory コマンドレットに関する包括的なドキュメントについては、[Data Factory コマンドレットのリファレンス](/powershell/module/az.datafactory)を参照してください。

## <a name="summary"></a>まとめ
このチュートリアルでは、Azure Data Factory を作成し、Azure BLOB から Azure SQL データベースにデータをコピーしました。 また、PowerShell を使用して、データ ファクトリ、リンクされたサービス、データセット、パイプラインを作成しました。 以下は、このチュートリアルで実行した手順の概要です。  

1. Azure **データ ファクトリ**を作成しました。
1. 次の **リンクされたサービス**を作成しました。

   a. 入力データを保持する Azure ストレージ アカウントをリンクするための、**Azure Storage** のリンクされたサービス。     
   b. 出力データを保持する Azure SQL データベースをリンクするための、**Azure SQL** のリンクされたサービス。
1. パイプラインの入力データと出力データを記述する **データセット** を作成しました。
1. ソースとして **BlobSource**、シンクとして **SqlSink** を持つ**コピー アクティビティ**がある**パイプライン**を作成しました。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Blob Storage をコピー操作のソース データ ストア、Azure SQL データベースをターゲット データ ストアとして使用しました。 次の表は、コピー アクティビティによってソースおよびターゲットとしてサポートされているデータ ストアの一覧です。 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

データ ストアにデータをコピーしたり、データ ストアからデータをコピーしたりする方法を確認するには、表のデータ ストアのリンクをクリックしてください。 

