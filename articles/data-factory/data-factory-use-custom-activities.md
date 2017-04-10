---
title: "Azure Data Factory パイプラインでカスタム アクティビティを使用する"
description: "カスタム アクティビティを作成して Azure Data Factory パイプラインで使用する方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: b4b0a8139b69a31e4af40e1f8231d4d7772fee0b
ms.lasthandoff: 03/18/2017


---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Azure Data Factory パイプラインでカスタム アクティビティを使用する

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive アクティビティ](data-factory-hive-activity.md) 
> * [Pig アクティビティ](data-factory-pig-activity.md)
> * [MapReduce アクティビティ](data-factory-map-reduce.md)
> * [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
> * [Spark アクティビティ](data-factory-spark.md)
> * [Machine Learning バッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新リソース アクティビティ](data-factory-azure-ml-update-resource-activity.md)
> * [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)
> * [.NET カスタム アクティビティ](data-factory-use-custom-activities.md)


Azure Data Factory パイプラインでは、2 種類のアクティビティを使用できます。

- [サポートされるソース データ ストアとシンク データ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の間でデータを移動する[データ移動アクティビティ](data-factory-data-movement-activities.md)。
- Azure HDInsight、Azure Batch、Azure Machine Learning などのコンピューティング サービスを使用してデータを変換する[データ変換アクティビティ](data-factory-data-transformation-activities.md)。 

Data Factory でサポートされていないデータ ストアとの間でデータを移動するには、独自のデータ移動ロジックで**カスタム アクティビティ**を作成し、パイプラインでそのアクティビティを使用します。 同じように、Data Factory でサポートされていない方法でデータを変換/処理するには、独自のデータ変換ロジックでカスタム アクティビティを作成し、パイプラインでそのアクティビティを使用します。 

仮想マシンの **Azure Batch** プールまたは **Azure HDInsight** クラスターで実行するカスタム アクティビティを構成することができます。 Azure Batch を使用する場合は、既存の Azure Batch プールのみを使用できます。 一方、HDInsight を使用する場合は、既存の HDInsight クラスター、またはランタイムにオンデマンドで自動的に作成されるクラスターを使用できます。  

以下のチュートリアルでは、カスタム .NET アクティビティを作成し、パイプラインでそのカスタム アクティビティを使用する手順について説明します。 このチュートリアルでは、**Azure Batch** のリンクされたサービスを使用します。 代わりに Azure HDInsight のリンクされたサービスを使用するには、型が **HDInsight** (独自の HDInsight クラスター) または **HDInsightOnDemand** (Data Factory によってオンデマンドで作成される HDInsight クラスター) のリンクされたサービスを作成します。 次に、HDInsight のリンクされたサービスを使用するようにカスタム アクティビティを構成します。 Azure HDInsight を使用してカスタム アクティビティを実行する方法の詳細については、「 [Azure HDInsight のリンクされたサービスの使用](#use-hdinsight-compute-service) 」セクションを参照してください。

> [!NOTE]
> カスタム アクティビティからデータ管理ゲートウェイを使用してオンプレミスのデータ ソースにアクセスすることはできません。 現時点では、[データ管理ゲートウェイ](data-factory-data-management-gateway.md)でサポートされるのは Data Factory でのコピー アクティビティとストアド プロシージャ アクティビティのみです。   

## <a name="walkthrough"></a>チュートリアル
### <a name="prerequisites"></a>前提条件
* Visual Studio 2012/2013/2015
* [Azure .NET SDK][azure-developer-center] のダウンロードとインストール

### <a name="azure-batch-prerequisites"></a>Azure Batch の前提条件
このチュートリアルでは、コンピューティング リソースとして Azure Batch を使用して、カスタム .NET アクティビティを実行します。 **Azure Batch** は、大規模な並列コンピューティングやハイ パフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行するためのプラットフォーム サービスです。 多くのコンピューティング処理を要する作業を管理された**仮想マシンの集合**で実行するようにスケジュール設定し、ジョブのニーズに合わせてコンピューティング リソースを自動的に拡大/縮小できます。 Azure Batch サービスの詳細については、[Azure Batch の基礎][batch-technical-overview]に関するページをご覧ください。

このチュートリアルでは、VM のプールと Azure Batch アカウントを作成します。 手順は次のようになります。

1. **Azure ポータル** を使用して、 [Azure Batch アカウント](http://portal.azure.com)を作成します。 手順については、「[Azure Batch アカウントの作成と管理][batch-create-account]」の記事を参照してください。
2. Azure Batch のアカウント名とアカウント キー、URI、プール名をメモしておきます。 Azure Batch のリンクされたサービスを作成するためにこれらが必要になります。
    1. Azure Batch アカウントのホーム ページに、**https://myaccount.westus.batch.azure.com** の形式で **URL** が表示されます。 この例では、**myaccount** が Azure Batch のアカウント名です。 リンクされたサービスの定義で使用する URI は、URL からアカウント名を除いたものです。 たとえば、**https://westus.batch.azure.com** です。
    2. 左側のメニューの **[キー]** をクリックして、**プライマリ アクセス キー**をコピーします。
    3. 既存のプールを使用するには、メニューの **[プール]** をクリックして、プールの **ID** をメモしておきます。 既存のプールがない場合は、次の手順に進みます。     
2. **Azure Batch プール**を作成します。

   1. [Azure Portal](https://portal.azure.com) で、左側のメニューの **[参照]** をクリックし、**[Batch アカウント]** をクリックします。
   2. Azure Batch アカウントを選択して、 **[Batch アカウント]** ブレードを開きます。
   3. **[プール]** タイルをクリックします。
   4. **[プール]** ブレードで、ツールバーの [追加] ボタンをクリックしてプールを追加します。
      1. プールの ID を入力します (**プール ID**)。 **プールの ID**は、Data Factory ソリューションを作成するときに必要になります。
      2. オペレーティング システム ファミリ設定には、 **Windows Server 2012 R2** を指定します。
      3. **ノード価格レベル**を選択します。
      4. **ターゲットの専用数**の設定値として、「**2**」と入力します。
      5. **ノードごとの最大タスク**の設定値として、「**2**」と入力します。
   5. **[OK]** をクリックすると、プールが作成されます。
   6. プールの **ID** をメモしておきます。 



### <a name="high-level-steps"></a>手順の概要
このチュートリアルの一環として実行する 2 つの大まかな手順を次に示します。 

1. シンプルなデータ変換/処理ロジックが含まれまるカスタム アクティビティを作成します。
2. カスタム アクティビティを使用するパイプラインを備えた Azure Data Factory を作成します。

## <a name="create-a-custom-activity"></a>カスタム アクティビティの作成
.NET カスタム アクティビティを作成するには、その **IDotNetActivity** インターフェイスを実装したクラスを含む **.NET クラス ライブラリ**プロジェクトを作成します。 このインターフェイスには、 [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) という 1 つのメソッドのみが含まれ、そのシグネチャは次のとおりです。

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


このメソッドには次の 4 つのパラメーターがあります。

- **linkedServices**。 このプロパティは、このアクティビティの入力/出力データセットで参照される、データ ストアのリンクされたサービスの列挙可能なリストです。   
- **datasets**。 このプロパティは、このアクティビティの入力/出力データセットの列挙可能なリストです。 このパラメーターを使用すると、入力と出力のデータセットに定義されている場所とスキーマを取得できます。
- **activity**。 このプロパティは、現在のアクティビティを表します。 これを使用して、カスタム アクティビティに関連付けられた拡張プロパティにアクセスできます。 詳細については、「[拡張プロパティへのアクセス](#access-extended-properties)」を参照してください。
- **logger**。 このオブジェクトを使用すると、パイプラインのユーザー ログに表示されるデバッグ コメントを作成できます。

メソッドから、今後、カスタム アクティビティの連結に使用できるディクショナリが返されます。 この機能はまだ実装されていないので、メソッドから空のディクショナリが返されます。  

### <a name="procedure"></a>手順
1. **.NET クラス ライブラリ** プロジェクトを作成します。
   <ol type="a">
     <li><b>Visual Studio 2015</b>、<b>Visual Studio 2013</b>、または <b>Visual Studio 2012</b> を起動します。</li>
     <li><b>[ファイル]</b> をクリックし、<b>[新規作成]</b> をポイントして、<b>[プロジェクト]</b> をクリックします。</li>
     <li><b>[テンプレート]</b> を展開し、<b>[Visual C#]</b> を選択します。 このチュートリアルでは C# を使用しますが、カスタム アクティビティの開発には、どの .NET 言語でも使用できます。</li>
     <li>右側にあるプロジェクトの種類の一覧から <b>[クラス ライブラリ]</b> を選択します。</li>
     <li><b>[プロジェクト名]</b> に「<b>MyDotNetActivity</b>」と入力します。</li>
     <li><b>[場所]</b> は <b>[C:\ADFGetStarted]</b> を選択します。</li>
     <li><b>[OK]</b> をクリックしてプロジェクトを作成します。</li>
   </ol>
2. **[ツール]** をクリックし、**[NuGet パッケージ マネージャー]** をポイントして、**[パッケージ マネージャー コンソール]** をクリックします。
手順 3. パッケージ マネージャー コンソールで、次のコマンドを実行して **Microsoft.Azure.Management.DataFactories**をインポートします。

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. **Azure Storage** NuGet パッケージをプロジェクトにインポートします。

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory サービス起動ツールには、WindowsAzure.Storage の 4.3 バージョンが必要です。 カスタム アクティビティ プロジェクトで Azure Storage アセンブリの新しいバージョンへの参照を追加した場合、アクティビティの実行時にエラーが表示されます。 このエラーを解決する方法については、「[Appdomain の分離](#appdomain-isolation)」セクションを参照してください。 
5. 次の **using** ステートメントをプロジェクト内のソース ファイルに追加します。

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. **名前空間**の名前を **MyDotNetActivityNS** に変更します。

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. 次のコード スニペットに示すように、クラス名を **MyDotNetActivity** に変更し、**IDotNetActivity** インターフェイスから派生させます。

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. **IDotNetActivity** インターフェイスの **Execute** メソッドを **MyDotNetActivity** クラスに実装 (追加) し、次のサンプル コードをメソッドにコピーします。

    次のサンプルでは、データ スライスに関連付けられている各 BLOB で検索語句 ("Microsoft") の出現回数をカウントします。

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    
    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];
    
        // to log information, use the logger object
        // log all extended properties            
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }
    
        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;
        
        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset 
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);
    
        // get the first Azure Storate linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;
    
        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;
    
        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.
    
        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);
    
            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
               // with the data slice. definition of the method is shown in the next step.
    
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
        } while (continuationToken != null);
    
        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path    
        logger.Write("Writing blob to the folder: {0}", folderPath);
    
        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);
    
        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.  
    
        return new Dictionary<string, string>();
    }
    ```
9. 以下のヘルパー メソッドを追加します。 

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    
    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset    
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the folder path found in the type properties
        return blobDataset.FolderPath;
    }
    
    /// <summary>
    /// Gets the fileName value from the input/output dataset.   
    /// </summary>
    
    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }
    
        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>
    
    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    GetFolderPath メソッドは、データセットが指すフォルダーのパスを返し、GetFileName メソッドは、データセットが指す BLOB/ファイルの名前を返します。 {Year}、{Month}、{Day} などの変数を使用して folderPath を定義した場合、このメソッドは文字列を実行時の値で置き換えずにそのまま返します。 SliceStart、SliceEnd などにアクセスする方法の詳細については、「 [拡張プロパティへのアクセス](#access-extended-properties) 」をご覧ください。    

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Calculate メソッドは、入力ファイル (フォルダー内の BLOB) 内のキーワード Microsoft のインスタンス数を計算します。 検索語句 ("Microsoft") は、コードにハード コーディングされています。
10. プロジェクトをコンパイルします。 メニューの **[ビルド]** をクリックし、**[ソリューションのビルド]** をクリックします。

    > [!IMPORTANT]
    > プロジェクトのターゲット フレームワークとして .NET Framework バージョン 4.5.2 を設定してください。プロジェクトを右クリックし、**[プロパティ]** をクリックしてターゲット フレームワークを設定します。 Data Factory では、4.5.2 より後の .NET Framework のバージョンに対してコンパイルされたカスタム アクティビティはサポートされません。

11. **Windows エクスプローラー**を起動し、ビルドの種類に応じて、**bin\debug** フォルダーまたは **bin\release** フォルダーに移動します。
12. <project folder>\bin\Debug フォルダー内のすべてのバイナリを含む zip ファイル、**MyDotNetActivity.zip** を作成します。 エラー発生時の問題の原因となったソース コードの行番号など、追加情報を取得するために、**MyDotNetActivity.pdb** ファイルを含めます。 

    > [!IMPORTANT]
    > カスタム アクティビティの zip ファイル内のファイルは、いずれもサブフォルダーがない **最上位レベル** に置く必要があります。

    ![バイナリ出力ファイル](./media/data-factory-use-custom-activities/Binaries.png)
14. **customactivitycontainer** という名前の BLOB コンテナーがまだ存在していない場合は、作成します。
15. AzureStorageLinkedService によって参照される**汎用** Azure Blob Storage (ホット/クール BLOB ストレージでない) の customactivitycontainer に MyDotNetActivity.zip を BLOB としてアップロードします。  

> [!NOTE]
> この .NET アクティビティ プロジェクトを Visual Studio で Data Factory プロジェクトを含むソリューションに追加し、Data Factory アプリケーション プロジェクトから .NET アクティビティ プロジェクトへの参照を追加する場合は、最後の 2 つの手順 (手動での zip ファイルの作成と汎用 Azure Blob Storage へのアップロード) を実行する必要はありません。 Data Factory エンティティを Visual Studio を使用して発行すると、これらの手順は発行プロセスによって自動的に実行されます。 Visual Studio を使用した Data Factory エンティティの作成と発行の詳細については、記事「[Visual Studio を使用した初めてのパイプラインの作成](data-factory-build-your-first-pipeline-using-vs.md)」と記事「[チュートリアル: Azure BLOB から Azure SQL にデータをコピーする](data-factory-copy-activity-tutorial-using-visual-studio.md)」を参照してください。  


## <a name="create-a-data-factory"></a>Data Factory を作成する。 
カスタム アクティビティを作成し、バイナリと zip ファイルを**汎用**の Azure ストレージ アカウントの BLOB コンテナーにアップロードしました。 ここでは、カスタム アクティビティを使用したパイプラインで Azure Data Factory を作成します。

カスタム アクティビティの入力データセットは、Blob Storage の adftutorial コンテナーの customactivityinput フォルダー内にある BLOB (ファイル) を表します。 カスタム アクティビティの出力データセットは、Blob Storage の adftutorial コンテナーの customactivityoutput フォルダー内にある出力 BLOB を表します。

以下の内容を記述した **file.txt** ファイルを作成し、**adftutorial** コンテナーの **customactivityinput** フォルダーにアップロードします。 adftutorial コンテナーを作成します (まだ存在しない場合)。 

```
test custom activity Microsoft test custom activity Microsoft
```

入力フォルダーに複数のファイルが含まれている場合でも、入力フォルダーは Azure Data Factory の 1 つのスライスに対応します。 パイプラインで各スライスが処理されるときに、カスタム アクティビティでは、そのスライスの入力フォルダー内にあるすべての BLOB が反復処理されます。

adftutorial\customactivityoutput folder フォルダーには、1 つ以上の行 (入力フォルダーの BLOB と同じ数) が含まれた 1 つの出力ファイルが作成されます。

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


このセクションで実行する手順を次に示します。

1. **Data Factory**を作成します。
2. カスタム アクティビティが実行される VM の Azure Batch プールの**リンクされたサービス**と、入力/出力 BLOB を保持する Azure Storage を作成します。
3. カスタム アクティビティの入力と出力を示す入力および出力の**データセット**を作成します。
4. カスタム アクティビティを使用する**パイプライン**を作成します。

> [!NOTE]
> まだ行っていない場合は、 **file.txt** を作成し、BLOB コンテナーにアップロードします。 手順については、前のセクションを参照してください。   

### <a name="step-1-create-the-data-factory"></a>手順 1: Data Factory を作成する
1. Azure Portal へのログイン後、次の手順を実行します。
   1. 左側のメニューの **[新規]** をクリックします。
   2. **[新規]** ブレードの **[データ + 分析]** をクリックします。
   3. **[データ分析]** ブレードの **[Data Factory]** をクリックします。
2. **[新しい Data Factory]** ブレードで、[名前] フィールドに「**CustomActivityFactory**」と入力します。 Azure Data Factory の名前はグローバルに一意にする必要があります。 **""CustomActivityFactory" という名前の Data Factory は使用できません"** というエラーが発生した場合は、Data Factory の名前を変更して (**yournameCustomActivityFactory** など) 作成し直してください。
3. **[リソース グループ名]**をクリックし、既存のリソース グループを選択するか、リソース グループを作成します。
4. 作成する Data Factory に適した**サブスクリプション**と**リージョン**を使用していることを確認します。
5. **[新しい Data Factory]** ブレードで **[作成]** をクリックします。
6. 作成した Data Factory は、Azure ポータルの **ダッシュボード** に表示されます。
7. Data Factory が正常に作成されると、Data Factory の内容を示す [Data Factory] ブレードが表示されます。

### <a name="step-2-create-linked-services"></a>手順 2. リンク サービスを作成する
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。 この手順では、Azure Storage アカウントと Azure Batch アカウントを Data Factory にリンクします。

#### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
1. **[CustomActivityFactory]** の **[Data Factory]** ブレードで、**[作成およびデプロイ]** をクリックします。 Data Factory エディターが起動します。
2. コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure Storage]** を選択します。 Azure Storage のリンクされたサービスを作成するための JSON スクリプトがエディターに表示されます。

3. **accountname** は Azure ストレージ アカウントの名前に、**accountkey** は Azure ストレージ アカウントのアクセス キーに置き換えます。 ストレージ アクセス キーを取得する方法については、「 [ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md#manage-your-storage-account)」を参照してください。

4. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

#### <a name="create-azure-batch-linked-service"></a>Azure Batch のリンクされたサービスの作成
1. Data Factory エディターで、コマンド バーの **[新規計算]** をクリックし、メニューの **[Azure Batch]** を選択します。
2. JSON スクリプトを次のように変更します。

   1. **accountName** プロパティに Azure Batch アカウント名を指定します。 **[Azure Batch アカウント] ブレード**の **URL** は、次の形式です: **accountname**.region.batch.azure.com。 JSON の **batchUri** プロパティでは、URL から **"accountname." を削除**し、 **accountName** JSON プロパティには **accountname** を使用する必要があります。
   2. **accessKey** プロパティに Azure Batch アカウント キーを指定します。
   3. **poolName** プロパティに、前提条件の一部として作成したプールの名前を指定します。 プールの名前の代わりにプールの ID を指定することもできます。
   4. **batchUri** プロパティに Azure Batch の URI を指定します。 例: https://westus.batch.azure.com。  
   5. **AzureStorageLinkedService** for the **linkedServiceName** プロパティに Azure Batch アカウント名を指定します。

        ```JSON
        {
         "name": "AzureBatchLinkedService",
         "properties": {
           "type": "AzureBatch",
           "typeProperties": {
             "accountName": "myazurebatchaccount",
             "batchUri": "https://westus.batch.azure.com",
             "accessKey": "<yourbatchaccountkey>",
             "poolName": "myazurebatchpool",
             "linkedServiceName": "AzureStorageLinkedService"
           }
         }
        }
        ```

       **poolName** プロパティでは、プール名の代わりにプール ID を指定することもできます。

      > [!IMPORTANT]
      > Data Factory サービスでは、HDInsight の場合と同様、Azure Batch のオンデマンド オプションはサポートされません。 Azure データ ファクトリでは、独自の Azure Batch プールのみ使用できます。   
    

### <a name="step-3-create-datasets"></a>手順 3: データセットを作成する
この手順では、入力データと出力データを表すデータセットを作成します。

#### <a name="create-input-dataset"></a>入力データセットの作成
1. Data Factory **エディター**のツール バーで **[新しいデータセット]** ボタンをクリックし、ドロップダウン メニューの **[Azure BLOB ストレージ]** をクリックします。
2. 右側のウィンドウの JSON を、次の JSON スニペットに置き換えます。

    ```JSON
    {
     "name": "InputDataset",
     "properties": {
         "type": "AzureBlob",
         "linkedServiceName": "AzureStorageLinkedService",
         "typeProperties": {
             "folderPath": "adftutorial/customactivityinput/",
             "format": {
                 "type": "TextFormat"
             }
         },
         "availability": {
             "frequency": "Hour",
             "interval": 1
         },
         "external": true,
         "policy": {}
     }
    }
    ```

   このチュートリアルでは、開始時刻が 2016-11-16T00:00:00Z、終了時刻が 2016-11-16T05:00:00Z のパイプラインを後ほど作成します。 データを毎時生成するようにスケジュールされているので、5 つの入力/出力スライスがあります (**00**:00:00 -> **05**:00:00)。

   入力データセットの **frequency** と **interval** はそれぞれ **Hour** と **1** に設定されています。つまり、1 時間に 1 つの入力スライスを利用できます。 このサンプルでは、intputfolder の同じファイル (file.txt) です。

   次に各スライスの開始時刻を示します。これは上記の JSON スニペットの SliceStart システム変数で表されます。
3. ツール バーの **[デプロイ]** をクリックし、**InputDataset** を作成してデプロイします。 エディターのタイトル バーに **"テーブルが正常に作成されました"** というメッセージが表示されていることを確認します。

#### <a name="create-an-output-dataset"></a>出力データセットの作成
1. **Data Factory エディター**で、**[新しいデータセット]** をクリックし、コマンド バーの **[Azure BLOB ストレージ]** をクリックします。
2. 右側のウィンドウの JSON スクリプトを、次の JSON スクリプトに置き換えます。

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     出力場所は **adftutorial/customactivityoutput/** で、出力ファイル名は yyyy-MM-dd-HH.txt です。この yyyy-MM-dd-HH は、スライスが生成された年、月、日、時間です。 詳細については、「[開発者用リファレンス][adf-developer-reference]」のページをご覧ください。

    各入力スライスの出力 BLOB/ファイルが生成されます。 次に、各スライスの出力ファイルの命名方法を示します。 すべての出力ファイルは、**adftutorial\customactivityoutput** という 1 つのフォルダーに生成されます。

   | スライス | 開始時刻 | 出力ファイル |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    入力フォルダー内のすべてのファイルは、前述の開始時刻であるスライスの一部です。 このスライスを処理すると、カスタム アクティビティは各ファイルをスキャンし、出力ファイルに、検索語句 (“Microsoft”) の出現回数が記述された 1 行を生成します。 inputfolder に 3 つのファイルがある場合、1 時間ごとの各スライスの出力ファイル (2016-11-16-00.txt、2016-11-16:01:00:00.txt など) に 3 つの行が含まれます。
3. **OutputDataset** をデプロイするには、コマンド バーの **[デプロイ]** をクリックします。

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>カスタム アクティビティを使用するパイプラインの作成と実行
1. Data Factory エディターで、コマンド バーの **[新しいパイプライン]** をクリックします。 このコマンドが表示されない場合は、**[...](省略記号)** をクリックすると表示されます。
2. 右側のウィンドウの JSON を、次の JSON スクリプトに置き換えます。

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    以下の点に注意してください。

   * **Concurrency** を **2** に設定し、Azure Batch プール内の 2 つの VM で 2 つのスライスが並列処理されるようにします。
   * activities セクションには、 **DotNetActivity**型のアクティビティが 1 つあります。
   * **AssemblyName** を DLL の名前 (**MyDotnetActivity.dll**) に設定します。
   * **EntryPoint** を **MyDotNetActivityNS.MyDotNetActivity** に設定します。
   * **PackageLinkedService** は **AzureStorageLinkedService** に設定されます。これは、カスタム アクティビティの zip ファイルを含む Blob Storage を示します。 入力/出力ファイルとカスタム アクティビティ zip ファイルに異なる Azure Storage アカウントを使用している場合は、Azure Storage の別のリンクされたサービスを作成します。 この記事では、同じ Azure Storage アカウントを使用している前提で説明します。
   * **PackageFile** を **customactivitycontainer/MyDotNetActivity.zip** に設定します。 形式は containerforthezip/nameofthezip.zip です。
   * カスタム アクティビティは入力として **InputDataset**、出力として **OutputDataset** を使用します。
   * カスタム アクティビティの linkedServiceName プロパティは、**AzureBatchLinkedService** を示します。これによって、Azure Batch VM でカスタム アクティビティが実行する必要がある Azure Data Factory がわかります。
   * **isPaused** プロパティは、既定で **false** に設定されています。 この例では、スライスが過去に開始されているので、パイプラインは即時に実行されます。 このプロパティを true に設定すると、パイプラインを一時停止できます。また false に設定し直すと再開されます。
   * **start** 時刻と **end** 時刻は **5** 時間離れており、スライスは 1 時間ごとに生成されるので、パイプラインによって 5 つのスライスが生成されます。
3. パイプラインをデプロイするには、コマンド バーの **[デプロイ]** をクリックします。

### <a name="monitor-the-pipeline"></a>パイプラインの監視
1. Azure ポータルの [Data Factory] ブレードで、 **[ダイアグラム]**をクリックします。

    ![[ダイアグラム] タイル](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. [ダイアグラム] ビューで、OutputDataset をクリックします。

    ![[ダイアグラム] ビュー](./media/data-factory-use-custom-activities/diagram.png)
3. 5つの出力スライスが準備完了状態であることが、表示されます。 準備完了状態にない場合、まだ生成されていません。 

   ![出力スライス](./media/data-factory-use-custom-activities/OutputSlices.png)
4. **adftutorial** コンテナー内の BLOB ストレージに出力ファイルが生成されていることを確認します。

   ![カスタム アクティビティの出力][image-data-factory-ouput-from-custom-activity]
5. 出力ファイルを開くと、以下のような出力結果が表示されるはずです。

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. [Azure ポータル][azure-preview-portal]または Azure PowerShell コマンドレットを使用して、データ ファクトリ、パイプライン、データセットを監視します。 カスタム アクティビティのコード内の **ActivityLogger** からのメッセージは、ポータルからダウンロードできるログ (user-0.log など) で確認することも、コマンドレットを使用して確認することもできます。

   ![カスタム アクティビティのダウンロード ログ][image-data-factory-download-logs-from-custom-activity]

データセットやパイプラインを監視するための詳細な手順については、「 [パイプラインの監視と管理](data-factory-monitor-manage-pipelines.md) 」を参照してください。      

### <a name="data-factory-and-batch-integration"></a>Data Factory と Batch の統合
Data Factory サービスによって、Azure Batch に **adf-poolname: job-xxx** という名前のジョブが作成されます。 左側のメニューで **[ジョブ]** をクリックします。 

![Azure Data Factory - Batch ジョブ](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

スライスのアクティビティの実行ごとに、1 つのタスクが作成されます。 5 個のスライスを処理する準備ができた場合、このジョブに 5 個のタスクが作成されています。 Batch プール内に複数のコンピューティング ノードがある場合は、2 つ以上のスライスを並列で実行できます。 コンピューティング ノードごとの最大タスクが 1 より大きな値に設定されている場合、同じコンピューティング ノードで複数のスライスを実行することもできます。

![Azure Data Factory - Batch ジョブのタスク](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

次の図は、Azure Data Factory と Batch のタスク間の関係を示しています。

![Data Factory と Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>エラーをトラブルシューティングする
トラブルシューティングには、いくつかの基本的な技術があります。

1. 次のエラーが表示された場合は、汎用の Azure Blob Storage を使用する代わりにホット/クール BLOB ストレージを使用することができます。 zip ファイルを**汎用の Azure ストレージ アカウント**にアップロードします。 
 
    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ``` 
2. 次のエラーが表示された場合は、CS ファイル内のクラスの名前が、パイプライン JSON の **EntryPoint** プロパティに指定した名前と一致していることを確認します。 チュートリアルでは、クラスの名前は MyDotNetActivity であり、JSON の EntryPoint は MyDotNetActivityNS.**MyDotNetActivity**です。

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   名前が一致する場合は、zip ファイルの **ルート フォルダー** にすべてのバイナリが含まれていることを確認します。 つまり、zip ファイルを開くと、サブ フォルダーではなく、ルート フォルダーにすべてのファイルが表示されます。   
3. 入力スライスが **[準備完了]**に設定されていない場合、入力フォルダー構造が正しく、入力フォルダーに **file.txt** が存在することを確認します。
3. 問題のトラブルシューティングに役立つ情報をログに記録するには、カスタム アクティビティの **Execute** メソッドで、**IActivityLogger** オブジェクトを使用します。 ログに記録されたメッセージは、ユーザー ログ ファイル (user-0.log、user-1.log、user-2.log などの名前が付けられた 1 つ以上のファイル) に表示されます。

   **[OutputDataset]** ブレードで、スライスをクリックすると、そのスライスの **[データ スライス]** ブレードが表示されます。 そのスライスの **アクティビティの実行** が表示されます。 このスライスには 1 回のアクティビティの実行が表示されます。 コマンド バーの [実行] をクリックする場合、同じスライスの別のアクティビティの実行を開始できます。

   アクティビティの実行をクリックすると、**[アクティビティの実行の詳細]** ブレードにログ ファイルの一覧が表示されます。 user_0.log ファイルにログに記録されたメッセージが表示されます。 パイプライン/アクティビティ JSON で再試行回数が 3 に設定されているので、エラーが発生した場合、3 つのアクティビティの実行が表示されます。 アクティビティの実行をクリックすると、ログ ファイルが表示されます。このファイルを確認して、エラーのトラブルシューティングを行うことができます。

   ログ ファイルの一覧で、 **user-0.log**をクリックします。 右側のパネルには、 **IActivityLogger.Write** メソッドの使用結果が表示されます。 一部のメッセージが表示されない場合は、user_1.log、user_2.log のような名前のログ ファイルがほかにもないか確認します。それ以外の場合、コードは、最後にログにメッセージが記録された後に、失敗した可能性があります。

   さらに、**system-0.log** では、システム エラー メッセージと例外を確認できます。
4. エラーの詳細にエラー発生時の**コール スタック**などの情報が含まれるようにするには、zip ファイルに **PDB** ファイルを含めます。
5. カスタム アクティビティの zip ファイル内のファイルは、いずれもサブフォルダーがない **最上位レベル** に置く必要があります。
6. **assemblyName** (MyDotNetActivity.dll)、**entryPoint** (MyDotNetActivityNS.MyDotNetActivity)、**packageFile** (customactivitycontainer/MyDotNetActivity.zip)、**packageLinkedService** (zip ファイルを含む**汎用**の Azure Blob Storage を示す必要があります) が正しい値に設定されていることを確認します。
7. エラーを修正し、スライスを再処理する場合は、**[OutputDataset]** ブレードのスライスを右クリックし、**[実行]** をクリックします。
8. 次のエラーが表示される場合は、バージョン 4.3.0 より新しい Azure Storage パッケージを使用しています。 Data Factory サービス起動ツールには、WindowsAzure.Storage の 4.3 バージョンが必要です。 Azure Storage アセンブリの新しいバージョンを使用する必要がある場合の対処法については、「[Appdomain の分離](#appdomain-isolation)」セクションを参照してください。 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    Azure Storage パッケージの 4.3.0 バージョンを使用できる場合は、バージョン 4.3.0 より新しい Azure Storage パッケージへの既存の参照を削除します。 次に、NuGet パッケージ マネージャー コンソールから、次のコマンドを実行します。 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    プロジェクトをビルドします。 bin\Debug フォルダーからバージョン 4.3.0 より新しい Azure.Storage アセンブリを削除します。 バイナリと PDB ファイルを含む zip ファイルを作成します。 BLOB コンテナー (customactivitycontainer) 内で元の zip ファイルをこの zip ファイルで置き換えます。 失敗したスライスを再実行します (スライスを右クリックし、[実行] をクリックします)。   
8. このカスタム アクティビティでは、パッケージ内の **app.config** ファイルは使用されません。 そのためこの構成ファイルから接続文字列を読み取るようにコードを記述した場合、実行時に正しく機能しません。 Azure Batch を使用するときは、すべてのシークレットを **Azure KeyVault** に保持し、証明書ベースのサービス プリンシパルを使用して **keyvault** を保護したうえで、Azure Batch プールに証明書を配布するのがベスト プラクティスです。 こうすることで .NET カスタム アクティビティが実行時に KeyVault 内のシークレットにアクセスすることができます。 これは一般的な手法であり、接続文字列に限らず、あらゆる種類のシークレットに応用できます。

   ベスト プラクティスではありませんが、同じことをもっと簡単に行うこともできます。接続文字列設定を使用して **Azure SQL のリンクされたサービス**を作成し、そのリンクされたサービスを使用するデータセットを作成して、カスタム .NET アクティビティにダミーの入力データセットとして連結します。 リンクされたサービスの接続文字列にカスタム アクティビティのコード内からアクセスすれば、実行時に適切に機能します。  

## <a name="update-custom-activity"></a>カスタム アクティビティを更新する
カスタム アクティビティのコードを更新する場合は、カスタム アクティビティを構築し、新しいバイナリを含む zip ファイルを BLOB ストレージにアップロードします。

## <a name="appdomain-isolation"></a>Appdomain の分離
Data Factory ランチャーによって使用されるアセンブリのバージョン (WindowsAzure.Storage v4.3.0、Newtonsoft.Json v6.0.x など) に制限されないカスタム アクティビティを作成する方法については、[クロス AppDomain のサンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample)を参照してください。

## <a name="access-extended-properties"></a>拡張プロパティへのアクセス
次のサンプルに示すようにアクティビティ JSON の拡張プロパティを宣言できます。

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```


この例では、2 つの拡張プロパティ (**SliceStart** と **DataFactoryName**) があります。 SliceStart の値は、SliceStart システム変数に基づいています。 サポートされているシステム変数の一覧については、 [システム変数](data-factory-scheduling-and-execution.md#data-factory-functions-and-system-variables) に関するページをご覧ください。 DataFactoryName の値は、CustomActivityFactory にハードコーディングされます。

**Execute** メソッドでこれらの拡張プロパティにアクセスするには、次のようなコードを使用します。

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties                               
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch の自動スケール
**自動スケール** 機能で、Azure Batch プールを作成することもできます。 たとえば、専用 VM 数が 0 の Azure Batch プールと、保留中のタスクの数に基づく自動スケールの数式を作成できます。

保留中のタスクにつき一度に 1 つの VM (例: 保留中のタスクが 5 つ -> 5 つの VM):

```
pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
$TargetDedicated = max(pendingTaskSampleVector);
```

保留中のタスクの数に関係なく、最大で一度に 1 つの VM:

```
pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
$TargetDedicated = (max(pendingTaskSampleVector)>0)?1:0;
```

詳細については、「 [Azure Batch プール内のコンピューティング ノードの自動スケール](../batch/batch-automatic-scaling.md) 」をご覧ください。

プールで既定の [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)を使用する場合、Batch サービスがカスタム アクティビティを実行する前に VM を準備するのに 15 ～ 30 分かかることがあります。  プールが異なる 　autoScaleEvaluationInterval を使用する場合、Batch サービスは autoScaleEvaluationInterval + 10 分を要することがあります。

## <a name="use-hdinsight-compute-service"></a>HDInsight コンピューティング サービスを使用する
このチュートリアルでは、Azure Batch コンピューティングを使用して、カスタム アクティビティを実行しました。 独自の HDInsight クラスターを使用するか、Data Factory によってオンデマンド HDInsight クラスターを作成してから、HDInsight クラスターでカスタム アクティビティを実行することもできます。 ここでは、HDInsight クラスターを使用するための手順の概要を示します。  

1. Azure HDInsight のリンクされたサービスを作成します。   
2. パイプライン JSON 内の **AzureBatchLinkedService** の代わりに HDInsight のリンクされたサービスを使用します。

チュートリアルでこれをテストする場合は、Azure HDInsight サービスを使用してシナリオをテストできるように、パイプラインの**開始**時刻と**終了**時刻を変更できます。

#### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight のリンクされたサービスを作成する
Azure Data Factory サービスはオンデマンド クラスターの作成をサポートしており、このクラスターを使用して入力データの処理と出力データの生成を行います。 また、独自のクラスターを使って同じ処理を行うことも可能です。 オンデマンド HDInsight クラスターを使用する場合は、スライスごとにクラスターが作成されます。 一方、独自の HDInsight クラスターを使用する場合、そのクラスターはすぐにスライスを処理できる状態にあります。 そのため、オンデマンド クラスターを使用すると、独自のクラスターを使用するよりデータの出力が遅いと感じる場合があります。

> [!NOTE]
> 実行時に、.NET アクティビティのインスタンスは HDInsight クラスターの 1 つの worker ノードでのみ実行されます。複数のノードで実行されるように拡張することはできません。 .NET アクティビティの複数のインスタンスは、HDInsight クラスターの別々のノードで並列に実行できます。
>
>

##### <a name="to-use-an-on-demand-hdinsight-cluster"></a>オンデマンド HDInsight クラスターを使用するには
1. **Azure Batch アカウント**で、左側のメニューの **[作成とデプロイ]** をクリックします。
2. Data Factory エディターで、コマンド バーの **[新規計算]** をクリックし、メニューの **[オンデマンド HDInsight クラスター]** を選択します。
3. JSON スクリプトを次のように変更します。

   1. **clusterSize** プロパティには、HDInsight クラスターのサイズを指定します。
   2. **timeToLive** プロパティには、顧客が削除されるまでの間にアイドル状態でいられる時間を指定します。
   3. **version** プロパティには、使用する HDInsight のバージョンを指定します。 このプロパティを除外した場合は、最新バージョンが使用されます。  
   4. **linkedServiceName** には、「**AzureStorageLinkedService**」と指定します。

        ```JSON
        {
           "name": "HDInsightOnDemandLinkedService",
           "properties": {
               "type": "HDInsightOnDemand",
               "typeProperties": {
                   "clusterSize": 4,
                   "timeToLive": "00:05:00",
                   "osType": "Windows",
                   "linkedServiceName": "AzureStorageLinkedService",
               }
           }
        }
        ```
4. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

##### <a name="to-use-your-own-hdinsight-cluster"></a>独自の HDInsight クラスターを使用するには
1. **Azure Batch アカウント**で、左側のメニューの **[作成とデプロイ]** をクリックします。
2. **Data Factory エディター**で、コマンド バーの **[新規計算]** をクリックし、メニューの **[HDInsight クラスター]** を選択します。
3. JSON スクリプトを次のように変更します。

   1. **clusterUri** プロパティには、HDInsight の URL を入力します。 たとえば、https://<clustername>.azurehdinsight.net/ などです。     
   2. **UserName** プロパティには、HDInsight クラスターにアクセスできるユーザー名を入力します。
   3. **Password** プロパティには、ユーザーのパスワードを入力します。
   4. **LinkedServiceName** プロパティには、「**AzureStorageLinkedService**」と入力します。
4. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

詳細については、「 [計算のリンクされたサービス](data-factory-compute-linked-services.md) 」を参照してください。

**パイプライン JSON**で、HDInsight (オンデマンドまたは独自) のリンクされたサービスを使用します。

```JSON
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "HDInsightOnDemandLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00Z",
    "end": "2016-11-16T05:00:00Z",
    "isPaused": false
  }
}
```

## <a name="examples"></a>例
| サンプル | カスタム アクティビティの動作 |
| --- | --- |
| [HTTP データ ダウンローダー](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Data Factory のカスタム C# アクティビティを使用して、HTTP エンドポイントから Azure Blob Storage にデータをダウンロードします。 |
| [Twitter センチメント分析のサンプル](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Azure ML モデルを呼び出し、センチメント分析、スコア付け、予測などを行います。 |
| [R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |既に R がインストールされている HDInsight クラスターで RScript.exe を実行し、R スクリプトを呼び出します。 |
| [クロス AppDomain .NET アクティビティ](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Data Factory 起動ツールによって使用されているアセンブリ バージョンとは別のバージョンを使用します。 |


[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

