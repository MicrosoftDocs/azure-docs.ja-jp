---
title: 初めてのデータ ファクトリの作成 (PowerShell) | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell を使用して、サンプルの Azure Data Factory パイプラインを作成します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: 22ec1236-ea86-4eb7-b903-0e79a58b90c7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0f18fc8a18cbcf5d85b68ed40790eda36d653812
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836589"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>チュートリアル:Azure PowerShell を使用した初めての Azure データ ファクトリの作成
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager テンプレート](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Azure Data Factory を使用してデータ ファクトリを作成する方法のクイック スタート](../quickstart-create-data-factory-powershell.md)に関するページを参照してください。

この記事では、Azure PowerShell を使用して最初の Azure データ ファクトリを作成します。 その他のツールや SDK を使用してチュートリアルを行うには、ドロップダウン リストでいずれかのオプションを選択します。

このチュートリアルのパイプラインには、1 つのアクティビティ (**HDInsight Hive アクティビティ**) が含まれます。 このアクティビティは、入力データを変換して出力データを生成する Hive スクリプトを Azure HDInsight クラスターで実行します。 このパイプラインは、指定した開始時刻と終了時刻の間で、月 1 回実行されるようスケジュールされています。 

> [!NOTE]
> このチュートリアルのデータ パイプラインでは、入力データを変換して出力データを生成します。 データをソース データ ストアからターゲット データ ストアにコピーするのではありません。 Azure Data Factory を使用してデータをコピーする方法のチュートリアルについては、[Blob Storage から SQL Database にデータをコピーする方法のチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。
> 
> 1 つのパイプラインには複数のアクティビティを含めることができます。 また、1 つのアクティビティの出力データセットを別のアクティビティの入力データセットとして指定することで、2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) ことができます。 詳細については、[Data Factory のスケジュール設定と実行](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* 「 [チュートリアルの概要](data-factory-build-your-first-pipeline.md) 」に目を通し、 **前提条件** の手順を完了する必要があります。
* 「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview) 」に記載されている手順に従って、コンピューターに Azure PowerShell の最新バージョンをインストールします。
* (省略可能) この記事では、すべての Data Factory コマンドレットを取り上げているわけではありません。 Data Factory コマンドレットに関する包括的なドキュメントについては、「 [Data Factory コマンドレット リファレンス](/powershell/module/az.datafactory) 」を参照してください。

## <a name="create-data-factory"></a>データ ファクトリの作成
この手順では、Azure PowerShell を使用して、 **FirstDataFactoryPSH**という名前の Azure データ ファクトリを作成します。 データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインには、1 つまたは複数のアクティビティを含めることができます。 たとえば、コピー元からコピー先のデータ ストアにデータをコピーするコピー アクティビティや、Hive スクリプトを実行して入力データを変換する HDInsight Hive アクティビティなどを含めることができます。 それでは、この手順でデータ ファクトリの作成から始めましょう。

1. Azure PowerShell を起動し、次のコマンドを実行します。 Azure PowerShell は、このチュートリアルが終わるまで開いたままにしておいてください。 Azure PowerShell を閉じて再度開いた場合は、これらのコマンドをもう一度実行する必要があります。
   * 次のコマンドを実行して、Azure ポータルへのサインインに使用するユーザー名とパスワードを入力します。
     ```PowerShell
     Connect-AzAccount
     ```    
   * 次のコマンドを実行して、このアカウントのすべてのサブスクリプションを表示します。
     ```PowerShell
     Get-AzSubscription  
     ```
   * 次のコマンドを実行して、使用するサブスクリプションを選択します。 このサブスクリプションは、Azure ポータルで使用したものと同じである必要があります。
     ```PowerShell
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```     
2. 次のコマンドを実行して、 **ADFTutorialResourceGroup** という名前の Azure リソース グループを作成します。
    
    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    このチュートリアルの一部の手順は、ADFTutorialResourceGroup という名前のリソース グループを使用することを前提としています。 異なるリソース グループを使用する場合は、このチュートリアルで ADFTutorialResourceGroup の代わりにそのリソース グループを使用する必要があります。
3. **New-AzDataFactory** コマンドレットを実行して、**FirstDataFactoryPSH** という名前のデータ ファクトリを作成します。

    ```PowerShell
    New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
   以下の点に注意してください。

* Azure Data Factory の名前はグローバルに一意にする必要があります。 " **Data factory 名 "FirstDataFactoryPSH" は利用できません**" というエラーが発生した場合は、名前を変更します (yournameFirstDataFactoryPSH など)。 このチュートリアルの手順の実行中に、この名前を ADFTutorialFactoryPSH の代わりに使用します。 Data Factory アーティファクトの名前付け規則については、 [Data Factory - 名前付け規則](data-factory-naming-rules.md) に関するトピックを参照してください。
* Data Factory インスタンスを作成するには、Azure サブスクリプションの共同作成者または管理者である必要があります。
* データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。
* エラー "**This subscription is not registered to use namespace Microsoft.DataFactory (このサブスクリプションは、名前空間 Microsoft.DataFactory を使用するように登録されていません)** " が表示された場合は、以下のいずれかの操作を行ってから、もう一度発行してみます。

  * Azure PowerShell で次のコマンドを実行して、Data Factory プロバイダーを登録します。

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Data Factory プロバイダーが登録されたことを確認するには、次のコマンドを実行します。

    ```PowerShell
    Get-AzResourceProvider
    ```
  * Azure サブスクリプションを使用して [Azure ポータル](https://portal.azure.com) にログインし、[Data Factory] ブレードに移動するか、Azure ポータルでデータ ファクトリを作成します。 この操作によって、プロバイダーが自動的に登録されます。

パイプラインを作成する前に、まず、Data Factory エンティティをいくつか作成する必要があります。 最初に、データ ストアやコンピューティングをデータ ストアにリンクするリンクされたサービスを作成し、リンクされたデータ ストア内の入力/出力データを表す入力データセットと出力データセットを定義した後、これらのデータセットを使用するアクティビティを含むパイプラインを作成します。

## <a name="create-linked-services"></a>リンクされたサービスを作成します
この手順では、Azure ストレージ アカウントとオンデマンド Azure HDInsight クラスターをデータ ファクトリにリンクします。 Azure ストレージ アカウントには、このサンプルのパイプラインの入力データと出力データが保持されます。 HDInsight のリンクされたサービスは、このサンプルのパイプラインのアクティビティに指定された Hive スクリプトを実行するために使用されます。 自分のシナリオで使用するデータ ストアやコンピューティング サービスを特定し、リンクされたサービスを作成して、それらのサービスをデータ ファクトリにリンクします。

### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。 同じ Azure ストレージ アカウントを使用して、入力/出力データと HQL スクリプト ファイルを格納します。

1. 次の内容で C:\ADFGetStarted フォルダーに StorageLinkedService.json という名前の JSON ファイルを作成します。 ADFGetStarted フォルダーがない場合は作成します。

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
    **accountname** は Azure ストレージ アカウントの名前に、**accountkey** は Azure ストレージ アカウントのアクセス キーに置き換えます。 ストレージ アクセス キーを取得する方法については、「[ストレージ アカウントの管理](../../storage/common/storage-account-manage.md#access-keys)」のストレージ アクセス キーを表示、コピー、再生成する方法に関する情報を参照してください。
2. Azure PowerShell で ADFGetStarted フォルダーに切り替えます。
3. **New-AzDataFactoryLinkedService** コマンドレットを使用して、リンクされたサービスを作成できます。 このコマンドレットと、このチュートリアルで使用する他の Data Factory コマンドレットでは、*ResourceGroupName* パラメーターと *DataFactoryName* パラメーターの値を渡す必要があります。 または、**Get-AzDataFactory** を使用して **DataFactory** オブジェクトを取得すると、コマンドレットを実行するたびに *ResourceGroupName* と *DataFactoryName* を入力しなくてもオブジェクトを渡すことができます。 **Get-AzDataFactory** コマンドレットの出力を **$df** 変数に割り当てるには、次のコマンドを実行します。

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. これで、**New-AzDataFactoryLinkedService** コマンドレットを実行して、リンクされた **StorageLinkedService** サービスを作成できます。

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    **Get-AzDataFactory** コマンドレットを実行せず出力を **$df** 変数に割り当てていない場合は、*ResourceGroupName* と *DataFactoryName* パラメーターの値を次のように指定する必要があります。

    ```PowerShell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    チュートリアルの途中で Azure PowerShell を閉じた場合、次に Azure PowerShell を起動したときに、 **Get-AzDataFactory** コマンドレットを実行してチュートリアルを完了する必要があります。

### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight のリンクされたサービスを作成する
この手順では、オンデマンド HDInsight クラスターをデータ ファクトリにリンクします。 HDInsight クラスターは、実行時に自動的に作成されます。また、処理が終わり、アイドル状態が一定時間続くと削除されます。 オンデマンド HDInsight クラスターの代わりに、独自の HDInsight クラスターを使用できます。 コピー アクティビティでサポートされているすべてのソースとシンクについては、 [Compute Linked Services](data-factory-compute-linked-services.md) に関するセクションを参照してください。

1. **C:\ADFGetStarted** フォルダーに、次の内容を記述した **HDInsightOnDemandLinkedService**.json という名前の JSON ファイルを作成します。

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    次の表に、このスニペットで使用される JSON プロパティの説明を示します。

   | プロパティ | 説明 |
   |:--- |:--- |
   | clusterSize |HDInsight クラスターのサイズを指定します。 |
   | timeToLive |削除されるまでの HDInsight クラスターのアイドル時間を指定します。 |
   | linkedServiceName |HDInsight によって生成されるログを保存するために使用されるストレージ アカウントを指定します。 |

    以下の点に注意してください。

   * Data Factory は、JSON で **Linux ベース** の HDInsight クラスターを自動的に作成します。 詳細については、 [オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) に関するセクションを参照してください。
   * オンデマンド HDInsight クラスターの代わりに、 **独自の HDInsight クラスター** を使用できます。 詳細については、 [HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) に関するセクションを参照してください。
   * HDInsight クラスターは、JSON (**linkedServiceName**) で指定した BLOB ストレージに**既定のコンテナー**を作成します。 クラスターを削除しても、HDInsight はこのコンテナーを削除しません。 この動作は仕様です。 オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスが処理されるたびに HDInsight クラスターが作成されます。 クラスターは、処理が終了すると自動的に削除されます。

       処理されるスライスが多いほど、Azure BLOB ストレージ内のコンテナーも増えます。 ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、"adf**データ ファクトリ名**-**リンクされたサービス名**-日時スタンプ" というパターンに従います。 Azure BLOB ストレージ内のコンテナーを削除するには、 [Microsoft ストレージ エクスプローラー](https://storageexplorer.com/) などのツールを使用します。

     詳細については、 [オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) に関するセクションを参照してください。
2. **New-AzDataFactoryLinkedService** コマンドレットを実行して、HDInsightOnDemandLinkedService という名前のリンクされたサービスを作成します。
    
    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>データセットを作成する
この手順では、Hive 処理の入力データと出力データを表すデータセットを作成します。 これらのデータセットは、このチュートリアルで前に作成した **StorageLinkedService** を参照します。 このリンクされたサービスは Azure ストレージ アカウントを指し、データセットは入力データと出力データを保持するストレージのコンテナー、フォルダー、ファイル名を指定します。

### <a name="create-input-dataset"></a>入力データセットの作成
1. **C:\ADFGetStarted** フォルダーに、次の内容を記述した **InputTable.json** という名前の JSON ファイルを作成します。

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    この JSON では、**AzureBlobInput** という名前のデータセットを定義します。これは、パイプラインのアクティビティの入力データを表します。 さらに、**adfgetstarted** という BLOB コンテナーと **inputdata** というフォルダーに入力データが配置されるように指定します。

    次の表に、このスニペットで使用される JSON プロパティの説明を示します。

   | プロパティ | 説明 |
   |:--- |:--- |
   | type |データは Azure Blob Storage に存在するため、type プロパティを AzureBlob に設定しています。 |
   | linkedServiceName |前に作成した StorageLinkedService を参照します。 |
   | fileName |このプロパティは省略可能です。 このプロパティを省略した場合は、folderPath のすべてのファイルが取得されます。 このチュートリアルでは、input.log のみが処理されます。 |
   | type |ログ ファイルはテキスト形式です。そのため、TextFormat を使用します。 |
   | columnDelimiter |ログ ファイル内の列はコンマ (,) で区切られています。 |
   | frequency/interval |frequency を Month に設定し、interval を 1 に設定しています。そのため、入力スライスは 1 か月ごとになります。 |
   | external |Data Factory サービスによって入力データが生成されない場合は、このプロパティを true に設定します。 |
2. 次のコマンドを Azure PowerShell で実行して、Data Factory データセットを作成します。

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>出力データセットの作成
次に、Azure BLOB ストレージに格納される出力データを表す出力データセットを作成します。

1. **C:\ADFGetStarted** フォルダーに、次の内容を記述した **OutputTable.json** という名前の JSON ファイルを作成します。

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    この JSON では、**AzureBlobOutput** という名前のデータセットを定義します。これは、パイプラインのアクティビティの出力データを表します。 さらに、**adfgetstarted** という BLOB コンテナーと **partitioneddata** というフォルダーに結果が保存されるように指定します。 **availability** セクションでは、出力データセットが 1 か月ごとに生成されることを指定します。
2. 次のコマンドを Azure PowerShell で実行して、Data Factory データセットを作成します。

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>パイプラインの作成
この手順では、 **HDInsightHive** アクティビティを含む最初のパイプラインを作成します。 入力スライスは 1 か月ごと (frequency:Month、interval:1) に使用可能であり、出力スライスは 1 か月ごとに生成されるため、アクティビティの scheduler プロパティも 1 か月ごとに設定します。 出力データセットとアクティビティの scheduler の設定は一致している必要があります。 現在、スケジュールは出力データセットによって開始されるため、アクティビティが出力を生成しない場合でも、出力データセットを作成する必要があります。 アクティビティが入力を受け取らない場合は、入力データセットの作成を省略できます。 次の JSON で使用されているプロパティについては、このセクションの最後で説明します。

1. 以下の内容を記述した MyFirstPipelinePSH.json という名前の JSON ファイルを C:\ADFGetStarted フォルダー内に作成します。

   > [!IMPORTANT]
   > **storageaccountname** は、JSON でのストレージ アカウントの名前に置き換えます。
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```
    この JSON スニペットでは、Hive を使用して HDInsight クラスターのデータを処理する 1 つのアクティビティで構成されるパイプラインを作成します。

    Hive スクリプト ファイル **partitionweblogs.hql** は、(**StorageLinkedService** という scriptLinkedService によって指定された) Azure ストレージ アカウントと **adfgetstarted** コンテナーの **script** フォルダーに格納されます。

    **defines** セクションは、Hive 構成値 (例: ${hiveconf:inputtable}、${hiveconf:partitionedtable}) として Hive スクリプトに渡される実行時設定を指定するために使用されます。

    パイプラインの **start** および **end** プロパティでは、パイプラインのアクティブな期間を指定します。

    アクティビティ JSON では、**linkedServiceName** に指定されたコンピューティング **HDInsightOnDemandLinkedService** で Hive スクリプトが実行されるように指定します。

   > [!NOTE]
   > 例で使用されている JSON プロパティの詳細については、「[Azure Data Factory のパイプラインとアクティビティ](data-factory-create-pipelines.md)」の「パイプライン JSON」を参照してください。

2. Azure Blob Storage の **adfgetstarted/inputdata** フォルダーに **input.log** ファイルがあることを確認し、次のコマンドを実行してパイプラインをデプロイします。 **start** と **end** が過去の日時に設定され、**isPaused** が false に設定されているため、パイプライン (パイプラインのアクティビティ) はデプロイするとすぐに実行されます。

    ```PowerShell
    New-AzDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. これで、Azure PowerShell を使用して最初のパイプラインを作成できました。

## <a name="monitor-pipeline"></a>パイプラインを監視する
この手順では、Azure PowerShell を使用して、Azure データ ファクトリの状況を監視します。

1. **Get-AzDataFactory** を実行し、出力を **$df** 変数に割り当てます。

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. **Get-AzDataFactorySlice** を実行し、**EmpSQLTable** のすべてのスライスの詳細を表示します。これは、パイプラインの出力テーブルです。

    ```PowerShell
    Get-AzDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    ここで指定した StartDateTime がパイプライン JSON で指定されている開始時刻と同じであることに注意してください。 出力例を次に示します。

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. **Get-AzDataFactoryRun** を実行して、特定のスライスに関するアクティビティの実行の詳細を取得します。

    ```PowerShell
    Get-AzDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    出力例を次に示します。 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    スライスが**準備完了**状態または**失敗**状態になるまで、このコマンドレットを実行し続けることができます。 スライスが準備完了状態になったら、Blob Storage の **adfgetstarted** コンテナーの **partitioneddata** フォルダーで出力データを調べます。  オンデマンド HDInsight クラスターの作成には、通常、しばらく時間がかかります。

    ![output data](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> オンデマンド HDInsight クラスターの作成には通常しばらく時間がかかります (約 20 分)。 そのため、パイプラインによるスライスの処理に **約 30 分** かかると想定してください。
>
> 入力ファイルは、スライスが正常に処理された時点で削除されます。 そのためスライスを取得したり、このチュートリアルをもう一度行ったりする場合は、adfgetstarted コンテナーの inputdata フォルダーに入力ファイル (input.log) をアップロードしてください。
>
>

## <a name="summary"></a>まとめ
このチュートリアルでは、HDInsight Hadoop クラスター上で Hive スクリプトを実行してデータを処理するために、Azure データ ファクトリを作成しました。 以下の手順を実行するために、Azure ポータルで Data Factory エディターを使用しました。

1. Azure **データ ファクトリ**を作成しました。
2. 次の 2 つの **リンクされたサービス**を作成しました。
   1. **Azure Storage** のリンクされたサービス。
   2. **Azure HDInsight** オンデマンドのリンクされたサービス。 Azure Data Factory は、入力データを処理し、出力データを生成するために、HDInsight Hadoop クラスターをジャストインタイムで作成します。
3. パイプラインの HDInsight Hive アクティビティ向けの入出力データを記述する 2 つの **データセット**を作成しました。
4. **HDInsight Hive** アクティビティを持つ**パイプライン**を作成しました。

## <a name="next-steps"></a>次の手順
この記事では、オンデマンド Azure HDInsight クラスターで Hive スクリプトを実行する変換アクティビティ (HDInsight アクティビティ) を含むパイプラインを作成しました。 コピー アクティビティを使用して Azure BLOB から Azure SQL にデータをコピーする方法については、[Azure BLOB から Azure SQL にデータをコピーする方法のチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

## <a name="see-also"></a>関連項目

| トピック | 説明 |
|:--- |:--- |
| [Data Factory コマンドレット リファレンス](/powershell/module/az.datafactory) |Data Factory コマンドレットに関する包括的なドキュメントです。 |
| [パイプライン](data-factory-create-pipelines.md) |この記事では、Azure Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して実際のシナリオやビジネスのためにエンド ツー エンドのデータ主導ワークフローを作成する方法を説明します。 |
| [データセット](data-factory-create-datasets.md) |この記事では、Azure Data Factory のデータセットについて説明します。 |
| [スケジュールと実行](data-factory-scheduling-and-execution.md) |この記事では、Azure Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
| [監視アプリを使用したパイプラインの監視と管理に関する記事](data-factory-monitor-manage-app.md) |この記事では、監視と管理アプリを使用してパイプラインを監視、管理、デバッグする方法について説明します。 |
