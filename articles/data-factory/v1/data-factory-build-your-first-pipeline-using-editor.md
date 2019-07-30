---
title: 初めてのデータ ファクトリの作成 (Azure Portal) | Microsoft Docs
description: このチュートリアルでは、Azure Portal で Data Factory エディターを使用して、サンプルの Azure Data Factory パイプラインを作成します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: d5b14e9e-e358-45be-943c-5297435d402d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 2a7e2f9e5018bdad2a1ed2c6edcb727a2ffdcddd
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839110"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>チュートリアル:Azure portal を使用した初めてのデータ ファクトリの作成
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Azure Resource Manager テンプレート](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> この記事は、一般公開されている Azure Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory を使用してデータ ファクトリを作成する方法のクイック スタート](../quickstart-create-data-factory-dot-net.md)に関するページを参照してください。

> [!WARNING]
> ADF v1 パイプラインの作成とデプロイのための Azure Portal の JSON エディターは、2019 年 7 月 31 日に無効になります。 2019 年 7 月 31 日以降は、引き続き [ADF v1 Powershell コマンドレット](https://docs.microsoft.com/powershell/module/az.datafactory/?view=azps-2.4.0&viewFallbackFrom=azps-2.3.2)、[ADF v1 .Net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.datafactories.models?view=azure-dotnet)、[ADF v1 REST API](https://docs.microsoft.com/rest/api/datafactory/) を使用して、ADF v1 パイプラインの作成とデプロイを行うことができます。

この記事では、[Azure Portal](https://portal.azure.com/) を使用して最初のデータ ファクトリを作成する方法について説明します。 その他のツールや SDK を使用してチュートリアルを行うには、ドロップダウン リストでいずれかのオプションを選択します。 

このチュートリアルのパイプラインには、1 つのアクティビティ (Azure HDInsight Hive アクティビティ) が含まれます。 このアクティビティは、入力データを変換して出力データを生成する Hive スクリプトを HDInsight クラスターで実行します。 このパイプラインは、指定した開始時刻と終了時刻の間で、月 1 回実行されるようスケジュールされています。 

> [!NOTE]
> このチュートリアルのデータ パイプラインでは、入力データを変換して出力データを生成します。 Data Factory を使用してデータをコピーする方法のチュートリアルについては、[Azure Blob Storage から Azure SQL Database にコピーする方法のチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するページを参照してください。
> 
> 1 つのパイプラインには複数のアクティビティを含めることができます。 また、1 つのアクティビティの出力データセットを別のアクティビティの入力データセットとして指定することで、2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) ことができます。 詳細については、「[Data Factory のスケジュール設定と実行](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)」を参照してください。

## <a name="prerequisites"></a>前提条件
[チュートリアルの概要](data-factory-build-your-first-pipeline.md)を読んで、「前提条件」セクションの手順に従ってください。

この記事では、Data Factory サービスの概念については説明しません。 サービスの詳細については、「[Azure Data Factory の概要](data-factory-introduction.md)」を参照してください。  

## <a name="create-a-data-factory"></a>Data Factory を作成する。
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 パイプラインには、1 つまたは複数のアクティビティを含めることができます。 1 つの例として、コピー元データ ストアからコピー先データ ストアにデータをコピーするコピー アクティビティがあります。 別の例として、Hive スクリプトを実行し、入力データを変換して出力データを生成する HDInsight Hive アクティビティが挙げられます。 

データ ファクトリを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[新規]**  >  **[データ + 分析]**  >  **[データ ファクトリ]** を選択します。

   ![[作成] ブレード](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

1. **[新しいデータ ファクトリ]** ブレードで、 **[名前]** に「**GetStartedDF**」と入力します。

   ![[新しいデータ ファクトリ] ブレード](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > データ ファクトリの名前はグローバルに一意にする必要があります。 "データ ファクトリ名 GetStartedDF は利用できません" というエラーが発生した場合は、データ ファクトリの名前を変更します。 たとえば、yournameGetStartedDF を使用し、もう一度データ ファクトリを作成します。 名前付け規則の詳細については、[Data Factory の名前付け規則](data-factory-naming-rules.md)に関するページを参照してください。
   >
   > データ ファクトリの名前は今後、DNS 名として登録され、一般ユーザーに表示される可能性があります。
   >
   >
1. **[サブスクリプション]** で、データ ファクトリを作成する Azure サブスクリプションを選択します。

1. 既存のリソース グループを選択するか、新しいリソース グループを作成します。 このチュートリアルでは、**ADFGetStartedRG** という名前のリソース グループを作成します。

1. **[場所]** で、データ ファクトリの場所を選択します。 Data Factory サービスによってサポートされているリージョンのみ、ドロップダウン リストに表示されます。

1. **[ダッシュボードにピン留めする]** チェック ボックスをオンにします。

1. **作成** を選択します。

   > [!IMPORTANT]
   > Data Factory インスタンスを作成するには、サブスクリプション/リソース グループ レベルで [Data Factory の共同作業者](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ロールのメンバーである必要があります。
   >
   >
1. ダッシュボードに、 **[Deploying Data Factory]\(Data Factory をデプロイしています\)** というステータスを示した次のタイルが表示されます。    

   ![[Deploying Data Factory]\(Data Factory をデプロイしています\) ステータス](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

1. データ ファクトリが作成されると、データ ファクトリの内容を表示する **[データ ファクトリ]** ページが表示されます。     

    ![Data Factory ブレード](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

データ ファクトリにパイプラインを作成する前に、まず、データ ファクトリ エンティティをいくつか作成する必要があります。 最初に、データ ストアやコンピューティングを自分のデータ ストアにリンクするリンクされたサービスを作成します。 次に、リンクされたデータ ストア内のデータを表す入力データセットと出力データセットを定義します。 最後に、これらのデータセットを使用するアクティビティを含むパイプラインを作成します。

## <a name="create-linked-services"></a>リンクされたサービスを作成します
この手順では、Azure Storage アカウントとオンデマンド HDInsight クラスターをデータ ファクトリにリンクします。 ストレージ アカウントには、このサンプルのパイプラインの入力データと出力データが保持されます。 HDInsight のリンクされたサービスは、このサンプルのパイプラインのアクティビティに指定された Hive スクリプトを実行するために使用されます。 自分のシナリオで使用する[データ ストア](data-factory-data-movement-activities.md)/[コンピューティング サービス](data-factory-compute-linked-services.md)を識別します。 次に、リンクされたサービスを作成して、それらのサービスをデータ ファクトリにリンクします。  

### <a name="create-a-storage-linked-service"></a>ストレージのリンクされたサービスを作成するには
この手順では、ストレージ アカウントをデータ ファクトリにリンクします。 このチュートリアルでは、同じストレージ アカウントを使用して、入力/出力データと HQL スクリプト ファイルを格納します。

1. **GetStartedDF** の **[データ ファクトリ]** ブレードで、 **[作成およびデプロイ]** を選択します。 Data Factory エディターが起動します。

   ![[作成とデプロイ] タイル](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

1. **[新しいデータ ストア]** 、 **[Azure Storage]** の順に選択します。

   ![[新しいデータ ストア] ブレード](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

1. Storage のリンクされたサービスを作成するための JSON スクリプトがエディターに表示されます。

   ![Storage のリンクされたサービス](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. **accountname** をストレージ アカウントの名前に置き換えます。 **accountkey** をストレージ アカウントのアクセス キーに置き換えます。 ストレージ アクセス キーを取得する方法については、「[ストレージ アカウントの管理](../../storage/common/storage-account-manage.md#access-keys)」のストレージ アクセス キーを表示、コピー、および再生成する方法を参照してください。

1. コマンド バーの **[デプロイ]** を選択して、リンクされたサービスをデプロイします。

    ![デプロイ ボタン](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   リンクされたサービスが正常にデプロイされると、[Draft-1] ウィンドウが消えます。 **AzureStorageLinkedService** が左側のツリー ビューに表示されます。

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>HDInsight のリンクされたサービスを作成する
この手順では、オンデマンド HDInsight クラスターをデータ ファクトリにリンクします。 HDInsight クラスターは、実行時に自動的に作成されます。 処理が終わり、アイドル状態が一定時間続くと、クラスターは削除されます。

1. Data Factory エディターで、 **[詳細]**  >  **[新規計算]**  >  **[On-demand HDInsight cluster]\(オンデマンド HDInsight クラスター\)** を選択します。

    ![New compute](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

1. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。 この JSON スニペットは、HDInsight クラスターをオンデマンドで作成するために使用されるプロパティを記述します。

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    次の表に、このスニペットで使用される JSON プロパティの説明を示します。

   | プロパティ | 説明 |
   |:--- |:--- |
   | clusterSize |HDInsight クラスターのサイズを指定します。 |
   | timeToLive | 削除されるまでの HDInsight クラスターのアイドル時間を指定します。 |
   | linkedServiceName | HDInsight によって生成されるログを保存するために使用されるストレージ アカウントを指定します。 |

    以下の点に注意してください。

     a. データ ファクトリは、JSON プロパティで Linux ベースの HDInsight クラスターを自動的に作成します。 詳細については、[オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)に関するセクションを参照してください。

     b. オンデマンド HDInsight クラスターの代わりに、独自の HDInsight クラスターを使用できます。 詳細については、[HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)に関するセクションを参照してください。

     c. HDInsight クラスターは、JSON プロパティ (**linkedServiceName**) で指定した BLOB ストレージに既定のコンテナーを作成します。 クラスターを削除しても、HDInsight はこのコンテナーを削除しません。 この動作は仕様です。 オンデマンド HDInsight のリンクされたサービスでは、既存のライブ クラスター (**timeToLive**) がある場合を除き、スライスが処理されるたびに HDInsight クラスターが作成されます。 クラスターは、処理が終了すると自動的に削除されます。

     処理されるスライスが多いほど、BLOB ストレージ内のコンテナーも増えます。 ジョブのトラブルシューティングのためにコンテナーが必要ない場合、コンテナーを削除してストレージ コストを削減できます。 これらのコンテナーの名前は、"adf**データ ファクトリ名**-**リンクされたサービス名**-日時スタンプ" というパターンに従います。 BLOB ストレージ内のコンテナーを削除するには、[Azure Storage Explorer](https://storageexplorer.com/) などのツールを使用します。

     詳細については、[オンデマンド HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)に関するセクションを参照してください。

1. コマンド バーの **[デプロイ]** を選択して、リンクされたサービスをデプロイします。

    ![デプロイ オプション](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

1. **AzureStorageLinkedService** と **HDInsightOnDemandLinkedService** が両方とも左側のツリー ビューに表示されていることを確認します。

    ![リンクされたサービスを表示しているツリー ビュー](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>データセットを作成する
この手順では、Hive 処理の入力データと出力データを表すデータセットを作成します。 これらのデータセットは、このチュートリアルで前に作成した AzureStorageLinkedService を参照します。 このリンクされたサービスは、ストレージ アカウントを指します。 データセットは、入力データと出力データを保持するストレージのコンテナー、フォルダー、ファイル名を指定します。   

### <a name="create-the-input-dataset"></a>入力データセットを作成する
1. Data Factory エディターで、 **[詳細]**  >  **[新しいデータセット]**  >  **[Azure Blob Storage]** を選択します。

    ![新しいデータセット](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

1. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。 この JSON スニペットでは、パイプラインのアクティビティの入力データを表す **AzureBlobInput** というデータセットを作成します。 さらに、**adfgetstarted** という BLOB コンテナーと **inputdata** というフォルダーに入力データが配置されるように指定します。

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
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
    次の表に、このスニペットで使用される JSON プロパティの説明を示します。

   | プロパティ | 入れ子先 | 説明 |
   |:--- |:--- |:--- |
   | type | properties |データは BLOB ストレージに存在するため、type プロパティを **AzureBlob** に設定しています。 |
   | linkedServiceName | format |前に作成した AzureStorageLinkedService を参照します。 |
   | folderPath | typeProperties | BLOB コンテナーと、入力 BLOB を格納するフォルダーを指定します。 | 
   | fileName | typeProperties |このプロパティは省略可能です。 このプロパティを省略した場合は、folderPath のすべてのファイルが取得されます。 このチュートリアルでは、input.log ファイルのみが処理されます。 |
   | type | format |ログ ファイルはテキスト形式です。そのため、**TextFormat** を使用します。 |
   | columnDelimiter | format |ログ ファイル内の列はコンマ (`,`) で区切られています。 |
   | frequency/interval | availability |frequency を **Month** に設定し、interval を **1** に設定しています。そのため、入力スライスは 1 か月ごとになります。 |
   | external | properties | このパイプラインによって入力データが生成されない場合は、このプロパティを **true** に設定します。 このチュートリアルでは、input.log ファイルはこのパイプラインで生成されないため、プロパティを **true** に設定します。 |

    これらの JSON プロパティの詳細については、[Azure BLOB コネクタ](data-factory-azure-blob-connector.md#dataset-properties)に関する記事を参照してください。

1. コマンド バーの **[デプロイ]** を選択して、新しく作成したデータセットをデプロイします。 データセットが左側のツリー ビューに表示されます。

### <a name="create-the-output-dataset"></a>出力データセットを作成する
次に、BLOB ストレージに格納される出力データを表す出力データセットを作成します。

1. Data Factory エディターで、 **[詳細]**  >  **[新しいデータセット]**  >  **[Azure Blob Storage]** を選択します。

1. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。 この JSON スニペットでは、**AzureBlobOutput** というデータセットを作成して、Hive スクリプトによって生成されるデータの構造を指定しています。 さらに、**adfgetstarted** という BLOB コンテナーと **partitioneddata** というフォルダーに結果が保存されるように指定します。 **availability** セクションでは、出力データセットが毎月生成されることを指定します。

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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
    これらのプロパティの説明については、「入力データセットを作成する」セクションを参照してください。 Data Factory サービスによってデータセットが生成されるため、出力データセットの external プロパティは設定しません。

1. コマンド バーの **[デプロイ]** を選択して、新しく作成したデータセットをデプロイします。

1. データセットが正常に作成されたことを確認します。

    ![リンクされたサービスを表示しているツリー ビュー](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>パイプラインを作成する。
この手順では、HDInsight Hive アクティビティを含む最初のパイプラインを作成します。 入力スライスは 1 か月ごと (frequency: Month、interval: 1) に使用可能です。 出力スライスは 1 か月ごとに生成されます。 アクティビティの scheduler プロパティも "1 か月ごと" に設定します。 出力データセットとアクティビティの scheduler の設定は一致している必要があります。 現在、スケジュールは出力データセットによって開始されるため、アクティビティが出力を生成しない場合でも、出力データセットを作成する必要があります。 アクティビティが入力を受け取らない場合は、入力データセットの作成を省略できます。 次の JSON スニペットで使用されているプロパティについては、このセクションの最後で説明します。

1. Data Factory エディターで、 **[詳細]**  >  **[新しいパイプライン]** を選択します。

    ![[新しいパイプライン] オプション](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

1. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。

   > [!IMPORTANT]
   > **storageaccountname** は、JSON スニペットでストレージ アカウントの名前に置き換えます。
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
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

    Hive スクリプト ファイル **partitionweblogs.hql** は、**AzureStorageLinkedService** という名前の scriptLinkedService で指定されたストレージ アカウントに保存されます。 これは、**adfgetstarted** コンテナーの **script** フォルダーにあります。

    **defines** セクションは、Hive 構成値として Hive スクリプトに渡される実行時設定を指定するために使用されます。 その例として、${hiveconf:inputtable} や ${hiveconf:partitionedtable} があります。

    パイプラインの **start** および **end** プロパティでは、パイプラインのアクティブな期間を指定します。

    アクティビティ JSON では、**linkedServiceName**:**HDInsightOnDemandLinkedService** で指定された計算で Hive スクリプトが実行されるように指定します。

   > [!NOTE]
   > 例で使用されている JSON プロパティの詳細については、[Data Factory のパイプラインとアクティビティ](data-factory-create-pipelines.md)に関するページの「パイプライン JSON」を参照してください。
   >
   >
1. 次のことを確認します。

   a. BLOB ストレージの **adfgetstarted** コンテナーの **inputdata** フォルダーに **input.log** ファイルが存在すること。

   b. BLOB ストレージの **adfgetstarted** コンテナーの **script** フォルダーに **partitionweblogs.hql** ファイルが存在すること。 これらのファイルがない場合は、[チュートリアルの概要](data-factory-build-your-first-pipeline.md)に関するページの「前提条件」の手順を実行してください。

   c. パイプライン JSON で実際のストレージ アカウントの名前を **storageaccountname** に指定済みであること。

1. コマンド バーの **[デプロイ]** を選択して、パイプラインをデプロイします。 **start** と **end** が過去の日時に設定され、**isPaused** が **false** に設定されているため、パイプライン (パイプラインのアクティビティ) はデプロイするとすぐに実行されます。

1. ツリー ビューにパイプラインが表示されることを確認します。

    ![パイプラインを表示しているツリー ビュー](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>パイプラインを監視する
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>[ダイアグラム] ビューを使用してパイプラインを監視する
1. **[データ ファクトリ]** ブレードで、 **[ダイアグラム]** を選択します。

    ![Diagram tile](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

1. **[ダイアグラム]** ビューに、パイプラインの概要と、このチュートリアルで使用するデータセットが表示されます。

    ![[ダイアグラム] ビュー](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

1. パイプラインのすべてのアクティビティを表示するために、ダイアグラム内のパイプラインを右クリックし、 **[パイプラインを開く]** を選択します。

    ![パイプラインを開くメニュー](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

1. パイプラインに **[Hive アクティビティ]** が表示されることを確認します。

    ![Open pipeline view](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    前のビューに戻るには、上部にあるメニューの **[データ ファクトリ]** を選択します。

1. **[ダイアグラム]** ビューで、**AzureBlobInput** データセットをダブルクリックします。 スライスの状態が **[準備完了]** であることを確認します。 スライスの状態が **[準備完了]** と表示されるまでに数分かかる場合があります。 しばらく待っても [準備完了] と表示されない場合は、入力ファイル (**input.log**) が適切なコンテナー (**adfgetstarted**) とフォルダー (**inputdata**) に配置されていることを確認してください。

   ![準備完了状態の入力スライス](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

1. **[AzureBlobInput]** ブレードを閉じます。

1. **[ダイアグラム]** ビューで、**AzureBlobOutput** データセットをダブルクリックします。 現在処理中のスライスが表示されます。

   ![実行中のデータセット処理](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

1. 処理が完了すると、スライスの状態が **[準備完了]** と表示されます。

   ![準備完了状態のデータセット](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > オンデマンド HDInsight クラスターの作成には通常約 20 分かかります。 そのため、パイプラインによるスライスの処理に約 30 分かかると想定してください。
   >
   >

1. スライスが **[準備完了]** 状態になったら、BLOB ストレージの **adfgetstarted** コンテナーの **partitioneddata** フォルダーで出力データを調べます。  

   ![出力データ](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

1. スライスを選択すると、 **[データ スライス]** ブレードに詳細が表示されます。

    ![データ スライス情報](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

1. **[アクティビティの実行]** 一覧でアクティビティの実行を選択すると、実行の詳細が表示されます (このシナリオでは Hive アクティビティ)。情報は **[アクティビティの実行の詳細]** ブレードに表示されます。   

    ![[アクティビティの実行の詳細] ウィンドウ](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   ログ ファイルで、実行された Hive クエリとステータス情報を確認できます。 これらのログは、すべての問題のトラブルシューティングに役立ちます。
   詳細については、[Azure Portal のブレードを使用したパイプラインの監視と管理](data-factory-monitor-manage-pipelines.md)に関するページを参照してください。

> [!IMPORTANT]
> 入力ファイルは、スライスが正常に処理された時点で削除されます。 そのためスライスを取得したり、このチュートリアルをもう一度行ったりする場合は、**adfgetstarted** コンテナーの **inputdata** フォルダーに入力ファイル (**input.log**) をアップロードしてください。
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>監視と管理アプリを使用してパイプラインを監視する
パイプラインは、監視と管理アプリを使用して監視することもできます。 このアプリケーションの使用法の詳細については、[監視と管理アプリを使用した Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関する記事を参照してください。

1. データ ファクトリのホーム ページの **[監視と管理]** タイルを選択します。

    ![Monitor & Manage tile](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

1. 監視と管理アプリで、パイプラインの開始時刻と終了時刻に一致するように、 **[開始時刻]** と **[終了時刻]** を変更します。 **[適用]** を選択します。

    ![監視と管理アプリ](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

1. **[Activity Windows]\(アクティビティ ウィンドウ\)** 一覧でアクティビティ ウィンドウを選択し、情報を確認します。

    ![[アクティビティ ウィンドウ] 一覧](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>まとめ
このチュートリアルでは、HDInsight Hadoop クラスター上で Hive スクリプトを実行してデータを処理するために、データ ファクトリを作成しました。 以下の操作を実行するために、Azure Portal で Data Factory エディターを使用しました。  

* データ ファクトリを作成します。
* 2 つのリンクされたサービスを作成する。
   * 入力/出力ファイルを保持する BLOB ストレージをデータ ファクトリにリンクするための、Storage のリンクされたサービス。
   * オンデマンド HDInsight Hadoop クラスターをデータ ファクトリにリンクするための、HDInsight オンデマンドのリンクされたサービス。 Data Factory は、入力データを処理し、出力データを生成するために、HDInsight Hadoop クラスターをジャストインタイムで作成します。
* パイプラインの HDInsight Hive アクティビティ向けの入出力データを記述する 2 つのデータセットを作成する。
* HDInsight Hive アクティビティを持つパイプラインを作成する。

## <a name="next-steps"></a>次の手順
この記事では、オンデマンド HDInsight クラスターで Hive スクリプトを実行する変換アクティビティ (HDInsight アクティビティ) を含むパイプラインを作成しました。 コピー アクティビティを使用して BLOB ストレージから SQL データベースにデータをコピーする方法については、[Blob Storage から SQL Database にデータをコピーする方法のチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

## <a name="see-also"></a>関連項目
| トピック | 説明 |
|:--- |:--- |
| [パイプライン](data-factory-create-pipelines.md) |この記事は、Data Factory のパイプラインとアクティビティの概要、およびそれらを利用して実際のシナリオやビジネスのためにエンド ツー エンドのデータ主導ワークフローを作成する方法について理解するのに役立ちます。 |
| [データセット](data-factory-create-datasets.md) |この記事は、Data Factory のデータセットについて理解するのに役立ちます。 |
| [スケジュールと実行](data-factory-scheduling-and-execution.md) |この記事では、Data Factory アプリケーション モデルのスケジュール設定と実行の側面について説明します。 |
| [監視と管理アプリを使用したパイプラインの監視と管理](data-factory-monitor-manage-app.md) |この記事では、監視と管理アプリを使用してパイプラインを監視、管理、デバッグする方法について説明します。 |