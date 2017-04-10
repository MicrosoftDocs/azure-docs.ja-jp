---
title: "Data Factory と Batch を使用して大規模なデータセットを処理する | Microsoft Docs"
description: "Azure Batch の並列処理機能を使用して、Azure Data Factory パイプラインで膨大な量のデータを処理する方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 9702f179a65754be88646987f868385b02a9f2d7
ms.lasthandoff: 03/29/2017


---
# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>Data Factory と Batch を使用して大規模なデータセットを処理する
この記事では、大規模なデータセットの移動と処理をスケジュールに沿って自動的に行う、サンプル ソリューションのアーキテクチャについて説明します。 また、Azure Data Factory と Azure Batch を使用してソリューションを実装する、エンドツーエンドのチュートリアルも提供します。

この記事には、サンプル ソリューション全体のチュートリアルが含まれるため、通常の記事よりも長くなっています。 Batch と Data Factory のご使用が初めての方は、Batch と Data Factory のサービスについて知り、これらがどのように連携するかを学ぶことができます。 これらのサービスについてはすでにご存じで、ソリューションを設計している方は、この記事の[アーキテクチャ セクション](#architecture-of-sample-solution)をご覧ください。プロトタイプやソリューションの開発を行っている方は、[チュートリアル](#implementation-of-sample-solution)の手順を追った説明も参考にしてください。 内容に関するご意見や、ご利用法についてお聞かせください。

最初に、クラウドの大規模なデータセットの処理に、Data Factory と Batch のサービスがどのように役立つかについて説明します。      

## <a name="why-azure-batch"></a>Azure Batch を選ぶ理由
Azure Batch を使用すると、大規模な並列コンピューティングやハイ パフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行できます。 Azure Batch は、多くのコンピューティング処理を要する作業を管理された仮想マシンの集合で実行するようにスケジュール設定するためのプラットフォーム サービスです。ジョブのニーズに合わせてコンピューティング リソースを自動的に拡大/縮小できます。

Batch サービスでは、複数のアプリケーションを並列で大規模に実行するための Azure コンピューティング リソースを定義します。 オンデマンドのジョブやスケジュールされたジョブを実行することができ、HPC クラスター、個々の仮想マシン、仮想ネットワークや、複雑なジョブとタスクのスケジュール インフラストラクチャを手動で作成、構成、管理する必要がありません。

Azure Batch のご使用が初めての方は、次の記事を参照してください。この記事で説明されるソリューションのアーキテクチャ/実装の理解に役立ちます。   

* [Azure Batch の基礎](../batch/batch-technical-overview.md)
* [Batch 機能の概要](../batch/batch-api-basics.md)

(省略可能) Azure Batch の詳細については、 [Azure Batch のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/batch/)に関する記事を参照してください。

## <a name="why-azure-data-factory"></a>Azure Data Factory を選ぶ理由
Data Factory は、データの移動や変換を調整し自動化するクラウドベースのデータ統合サービスです。 Data Factory サービスを利用すれば、オンプレミスやクラウド データ ストアから一元的なデータ ストア (例: Azure Blob Storage) にデータを移動する、管理されたデータ パイプラインを作成したり、Azure HDInsight や Azure Machine Learning などのサービスを使用して、データの処理/変換を行うことができます。 また、データ パイプラインがスケジュールに沿って (毎時、毎日、毎週など) 実行するように設定し、データ パイプラインの一元的な監視と管理を通じて、問題の特定と対処を行うことができます。

Azure Data Factory のご使用が初めての方は、次の記事を参照してください。この記事で説明されるソリューションのアーキテクチャ/実装の理解に役立ちます。   

* [Azure Data Factory の概要](data-factory-introduction.md)
* [最初のデータ パイプラインの作成](data-factory-build-your-first-pipeline.md)   

(省略可能) Azure Data Factory の詳細については、「 [Azure Data Factory のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/data-factory/)」を参照してください。

## <a name="data-factory-and-batch-together"></a>Data Factory と Batch の連携
Data Factory には、ソース データ ストアからコピー先のデータ ストアにデータのコピー/移動を行うコピー アクティビティや、Azure で Hadoop クラスター (HDInsight) を使用してデータを処理する Hive アクティビティなどの、組み込みのアクティビティが含まれています。 サポートされている変換アクティビティの一覧については、 [データの変換アクティビティ](data-factory-data-transformation-activities.md) に関する記事を参照してください。

また、独自のロジックでデータの移動や処理を行うカスタム .NET アクティビティを作成して、これらのアクティビティを Azure HDInsight クラスターや、仮想マシンの Azure Batch プールで実行することができます。 Azure Batch を使用すると、入力した式に基づいて、プールを自動スケール(ワークロードに基づき仮想マシンの追加と削除を行う) に設定できます。     

## <a name="architecture-of-sample-solution"></a>サンプル ソリューションのアーキテクチャ
この記事で説明されるアーキテクチャはシンプルなソリューションに向けたものですが、金融サービスによるリスク モデリング、画像処理とレンダリング、およびゲノムの分析などの複雑なシナリオに関係します。

このダイアグラムでは、1) Data Factory がデータの移動と処理を調整する方法、および 2) Azure Batch が並列的にデータを処理する方法を示します。 簡単に参照できるように、図をダウンロードして印刷 (11 x 17 インチ または A3 サイズ): [HPC and data orchestration using Azure Batch and Data Factory (Azure Batch と Data Factory を使用した HPC とデータのオーケストレーション)](http://go.microsoft.com/fwlink/?LinkId=717686)。

[![大規模なデータ処理のダイアグラム](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

処理の基本的な手順を次のリストに示します。 ソリューションには、エンド ツー エンド ソリューションをビルドするためのコードと説明が含まれています。

1. **コンピューティング ノード (VM) のプールで Azure Batch を構成**します。 各ノードのノード数とサイズを指定することができます。
2. **Azure Data Factory インスタンスを作成** します。
3. Data Factory パイプラインに**カスタム .NET アクティビティを作成**します。 このアクティビティは、Azure Batch プールで実行されるユーザー コードです。
4. **Azure ストレージに大量の入力データを BLOB として格納**します。 データは、論理スライス (通常は時間) に分割されます。
5. **並行して処理されるデータを Data Factory が 2 次拠点にコピー**します。
6. **Data Factory は、Batch によって割り当てられたプールを使用してカスタム アクティビティを実行**します。 Data Factory は、アクティビティを同時に実行できます。 各アクティビティは、データのスライスを処理します。 結果は Azure Storage に格納されます。
7. アプリによる配布、または他のツールによる追加処理のために、**Data Factory は最終的な結果を 3 次拠点に移動**させます。

## <a name="implementation-of-sample-solution"></a>サンプル ソリューションの実装
ここではサンプル ソリューションを意図的にシンプルにして、データセットの処理に Data Factory と Batch を一緒に使用する方法を示しています。 ソリューションでは、タイム シリーズで編成された入力ファイルで、検索語句 ("Microsoft") の出現数をカウントします。 出力ファイルにその数を出力します。

**時間**: Azure、Data Factory、および Batch の基礎を熟知しており、下記の前提条件をすべて満たしている場合、このソリューションを完了するための推定所要時間は 1 ～ 2 時間です。

### <a name="prerequisites"></a>前提条件
#### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションがない場合は、無料試用版のアカウントを数分で作成することができます。 「 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。

#### <a name="azure-storage-account"></a>Azure ストレージ アカウント
このチュートリアルでは、データの格納に Azure ストレージ アカウントを使用します。 Azure ストレージ アカウントがない場合は、「 [ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」を参照してください。 サンプル ソリューションでは、Blob Storage を使用します。

#### <a name="azure-batch-account"></a>Azure Batch アカウント
[Azure Portal](http://manage.windowsazure.com/) を使用して、Azure Batch アカウントを作成します。 「 [Azure Batch アカウントの作成と管理](../batch/batch-account-create-portal.md)」を参照してください。 Azure Batch のアカウント名とアカウント キーをメモしておきます。 また、 [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) コマンドレットを使用して、Azure Batch アカウントを作成することもできます。 このコマンドレットの使用に関する詳細な手順については、「 [Azure Batch PowerShell コマンドレットの概要](../batch/batch-powershell-cmdlets-get-started.md) 」を参照してください。

サンプル ソリューションでは、(Azure Data Factory パイプラインを通じて間接的に) Azure Batch を使用して、仮想マシンの管理コレクションであるコンピューティング ノードのプールで、同じ方法でデータを処理します。

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>仮想マシン (VM) の Azure Batch プール
2 個以上のコンピューティング ノードで **Azure Batch プール** を作成します。

1. [Azure Portal](https://portal.azure.com) で、左側のメニューの **[参照]** をクリックし、**[Batch アカウント]** をクリックします。
2. Azure Batch アカウントを選択して、 **[Batch アカウント]** ブレードを開きます。
3. **[プール]** タイルをクリックします。
4. **[プール]** ブレードで、ツールバーの [追加] ボタンをクリックしてプールを追加します。
   1. プールの ID を入力します (**プール ID**)。 **プールの ID**は、Data Factory ソリューションを作成するときに必要になります。
   2. オペレーティング システム ファミリ設定には、 **Windows Server 2012 R2** を指定します。
   3. **ノード価格レベル**を選択します。
   4. **ターゲットの専用数**の設定値として、「**2**」と入力します。
   5. **ノードごとの最大タスク**の設定値として、「**2**」と入力します。
   6. **[OK]** をクリックすると、プールが作成されます。

#### <a name="azure-storage-explorer"></a>Azure ストレージ エクスプローラー
[Azure Storage エクスプローラー 6 (ツール)](https://azurestorageexplorer.codeplex.com/) または [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (ClumsyLeaf ソフトウェアから)。 これらのツールは、クラウドでホストされるアプリケーションのログを含む、Azure Storage プロジェクト内のデータを調べたり、変更したりするために使用します。

1. プライベートなアクセス (匿名アクセスなし) で **mycontainer** という名前のコンテナーを作成します
2. **CloudXplorer**を使用している場合、次の構造でフォルダーおよびサブフォルダーを作成します。

   ![](./media/data-factory-data-processing-using-batch/image3.png)

   **Inputfolder** と **outputfolder** は、**mycontainer** のトップ レベル フォルダーにあり、**inputfolder** には日付タイム スタンプ (YYYY-MM-DD-HH) を含むサブフォルダーがあります。

   **Azure Storage エクスプローラ**を使用している場合は、次の手順で、以下の名前を持つファイルをアップロードする必要があります。inputfolder/2015-11-16-00/file.txt、inputfolder/2015-11-16-01/file.txt など。 この手順では、フォルダーが自動的に作成されます。
3. テキスト ファイル **file.txt** を、キーワード **Microsoft** を含めた内容でコンピューターに作成します。 例: “test custom activity Microsoft test custom activity Microsoft”。
4. ファイルを Azure Blob Storage 内の次の入力フォルダーにアップロードします。

   ![](./media/data-factory-data-processing-using-batch/image4.png)

   **Azure Storage エクスプローラー**を使用している場合、ファイル **file.txt** を **mycontainer** にアップロードします。 ツールバーで **[コピー]** をクリックして、BLOB のコピーを作成します。 **[BLOB のコピー]** ダイアログ ボックスで、**[宛先 BLOB 名]** を「**inputfolder/2015-11-16-00/file.txt**」に変更します。 この手順を繰り返して、inputfolder/2015-11-16-01/file.txt、inputfolder/2015-11-16-02/file.txt、inputfolder/2015-11-16-03/file.txt、inputfolder/2015-11-16-04/file.txt などを作成します。 この操作では、フォルダーが自動的に作成されます。
5. **customactivitycontainer**という名前の別のコンテナーを作成します。 カスタム アクティビティの zip ファイルを、このコンテナーにアップロードします。

#### <a name="visual-studio"></a>Visual Studio
Microsoft Visual Studio 2012 以降をインストールして、Data Factory ソリューションで使用するカスタム Batch アクティビティを作成します。

### <a name="high-level-steps-to-create-the-solution"></a>ソリューションを作成する手順の概要
1. データ処理ロジックが含まれるカスタム アクティビティを作成します。
2. カスタム アクティビティを使用する Azure Data Factory を作成します。

### <a name="create-the-custom-activity"></a>カスタム アクティビティの作成
Data Factory のカスタム アクティビティは、このサンプル ソリューションの核となる機能です。 サンプル ソリューションでは、Azure Batch を使用して、カスタム アクティビティを実行します。 カスタム アクティビティを開発し、Azure Data Factory パイプラインで使用する方法の基本情報は、「 [Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md) 」を参照してください。

Azure Data Factory パイプラインで使用できる .NET カスタム アクティビティを作成するには、**IDotNetActivity** インターフェイスを実装したクラスを含む **.NET Class Library** プロジェクトを作成する必要があります。 このインターフェイスには、 **Execute**という 1 つのメソッドのみが含まれます。 このメソッドのシグネチャを次に示します。

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

このメソッドには、理解しておく必要がある重要な要素がいくつかあります。

* このメソッドには次の 4 つのパラメーターがあります。

  1. **linkedServices**。 入力/出力データ ソース (例: Azure Blob Storage) を Data Factory にリンクする、リンクされたサービスの列挙可能な一覧です。 このサンプルでは、入力と出力の両方に使用される Azure Storage 型のリンクされたサービスは 1 つのみです。
  2. **datasets**。 データセットの列挙可能な一覧です。 このパラメーターを使用すると、入力と出力のデータセットに定義されている場所とスキーマを取得できます。
  3. **activity**。 このパラメーターは、現在のコンピューティング エンティティ (この場合は Azure Batch サービス) を表します。
  4. **logger**。 logger を使用すると、パイプラインの "User" ログとして表示されるデバッグ コメントを作成できます。
* メソッドから、今後、カスタム アクティビティの連結に使用できるディクショナリが返されます。 この機能はまだ実装されていないので、メソッドから空のディクショナリが返されます。

#### <a name="procedure-create-the-custom-activity"></a>手順: カスタム アクティビティの作成
1. Visual Studio で .NET クラス ライブラリ プロジェクトを作成します。

   1. **Visual Studio 2012**/**2013/2015** を起動します。
   2. **[ファイル]** をクリックし、**[新規作成]** をポイントして、**[プロジェクト]** をクリックします。
   3. **[テンプレート]** を展開し、**[Visual C#]\#** を選択します。このチュートリアルでは C\# を使用しますが、カスタム アクティビティの開発には、どの .NET 言語でも使用できます。
   4. 右側にあるプロジェクトの種類の一覧から **[クラス ライブラリ]** を選択します。
   5. **MyDotNetActivity** for the **MyDotNetActivity**に関する記事を参照してください。
   6. **[場所]** で [**C:\\ADF**] を選択します。 **ADF** フォルダーがない場合には、フォルダーを作成します。
   7. **[OK]** をクリックしてプロジェクトを作成します。
2. **[ツール]** をクリックし、**[NuGet パッケージ マネージャー]** をポイントして、**[パッケージ マネージャー コンソール]** をクリックします。
3. **[パッケージ マネージャー コンソール]** で、次のコマンドを実行して **Microsoft.Azure.Management.DataFactories** をインポートします。

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. **Azure Storage** NuGet パッケージをプロジェクトにインポートします。 このパッケージは、このサンプルで Blob Storage API を使用するために必要です。

    ```powershell
    Install-Package Azure.Storage
    ```
5. 次の **using** ディレクティブをプロジェクト内のソース ファイルに追加します。

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
7. クラス名を **MyDotNetActivity** に変更し、次に示すように **IDotNetActivity** インターフェイスから派生させます。

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. **IDotNetActivity** インターフェイスの **Execute** メソッドを **MyDotNetActivity** クラスに実装 (追加) し、次のサンプル コードをメソッドにコピーします。 このメソッドで使用されるロジックのための説明については、「 [メソッドの実行](#execute-method) 」セクションを参照してください。

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
    
       // declare types for input and output data stores
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // using First method instead of Single since we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
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
           // with the data slice.
           //
           // definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
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
9. 次のヘルパー メソッドをクラスに追加します。 これらのメソッドは、 **Execute** メソッドによって呼び出されます。 最も重要な点は、 **Calculate** メソッドは、各 BLOB で反復処理されるコードを分離することです。

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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
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
    **GetFolderPath** メソッドは、データセットが指すフォルダーのパスを返し、**GetFileName** メソッドは、データセットが指す BLOB/ファイルの名前を返します。

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    **Calculate** メソッドは、入力ファイル (フォルダー内の BLOB) 内のキーワード **Microsoft** のインスタンス数を計算します。 検索語句 ("Microsoft") は、コードにハード コーディングされています。

1. プロジェクトをコンパイルします。 メニューの **[ビルド]** をクリックし、**[ソリューションのビルド]** をクリックします。
2. **Windows エクスプローラー**を起動し、ビルドの種類に応じて、**bin\\debug** フォルダーまたは **bin\\release** フォルダーに移動します。
3. **\\bin\\Debug** フォルダー内のすべてのバイナリを含む zip ファイル、**MyDotNetActivity.zip** を作成します。 エラー発生時の問題の原因となったソース コードの行番号など、追加情報を取得するために、MyDotNetActivity.**pdb** ファイルを含めることもできます。

   ![](./media/data-factory-data-processing-using-batch/image5.png)
4. **MyDotNetActivity.zip** を Azure Blob Storage 内の **customactivitycontainer** に BLOB としてアップロードします。この BLOB コンテナーは、**ADFTutorialDataFactory** 内のリンクされたサービス **StorageLinkedService** が使用します。 BLOB コンテナー **customactivitycontainer** が既に存在していなければ、作成します。

#### <a name="execute-method"></a>Execute メソッド
ここでは、Execute メソッドのコードの詳細と注意事項について説明します。

1. 入力コレクションを反復処理するメンバーは、 [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) 名前空間にあります。 BLOB コレクションの反復処理では、 **BlobContinuationToken** クラスを使用する必要があります。 基本的に、既存のループのメカニズムとして、トークンに do-while ループを使用する必要があります。 詳細については、「 [.NET から BLOB ストレージを使用する方法](../storage/storage-dotnet-how-to-use-blobs.md)」を参照してください。 基本的なループを次に示します。

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   詳細については、 [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) メソッドのドキュメントを参照してください。
2. 論理的には、BLOB セットを操作するコードを do-while ループ内に配置します。 **Execute** メソッドの場合、do-while ループは BLOB の一覧を **Calculate** というメソッドに渡します。 Calculate メソッドは、セグメント内のすべての BLOB を反復処理した結果である **output** という文字列変数を返します。

   また、**Calculate** メソッドに渡された BLOB 内の検索語句 (**Microsoft**) の出現回数を返します。

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. **Calculate** メソッドの処理が完了すると、新しい BLOB に出力されます。 そのため、処理対象の BLOB セットごとに、結果を新しい BLOB に出力できます。 新しい BLOB に出力するには、まず出力データセットを検索します。

    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. また、このコードは、 **GetFolderPath** というヘルパー メソッドも呼び出して、フォルダー パス (ストレージ コンテナー名) を取得します。

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   **GetFolderPath** は、FolderPath というプロパティがある AzureBlobDataSet に DataSet オブジェクトをキャストします。

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. コードで、ファイル名 (BLOB 名) を取得する **GetFileName** メソッドを呼び出します。 このコードは、フォルダー パスを取得する上記のコードと似ています。

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. URI オブジェクトを作成することで、ファイル名が書き込まれます。 URI コンストラクターは **BlobEndpoint** プロパティを使用して、コンテナー名を返します。 フォルダー パスとファイル名が追加され、出力 BLOB URI が構築されます。  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. ファイル名が出力されるので、 **Calculate** メソッドから出力文字列を新しい BLOB に出力できるようになります。

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Data Factory の作成
「 [カスタム アクティビティの作成](#create-the-custom-activity) 」セクションでは、カスタム アクティビティを作成し、バイナリと PDB ファイルを含む zip ファイルを Azure Blob コンテナーにアップロードしました。 ここでは、**カスタム アクティビティ**を使用した**パイプライン**で Azure **Data Factory** を作成します。

カスタム アクティビティの入力データセットは、Blob Storage の入力フォルダー (mycontainer\\inputfolder) 内にある BLOB (ファイル) を表します。 アクティビティの出力データセットは、Blob Storage の出力フォルダー (mycontainer\\outputfolder) 内にある出力 BLOB を表します。

次のように、入力フォルダーに 1 つ以上のファイルをドロップします。

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

たとえば、次の内容を含む 1 つのファイル (file.txt) を各フォルダーにドロップします。

```
test custom activity Microsoft test custom activity Microsoft
```

入力フォルダーに複数のファイルが含まれている場合でも、各入力フォルダーは Azure Data Factory のスライスに相当します。 パイプラインで各スライスが処理されるときに、カスタム アクティビティでは、そのスライスの入力フォルダー内にあるすべての BLOB が反復処理されます。

同じ内容の 5 つの出力ファイルが表示されます。 たとえば、2015-11-16-00 フォルダーのファイルを処理して出力されるファイルには、次の内容が含まれます。

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

同じ内容の複数のファイル (file.txt、file2.txt、file3.txt) を入力フォルダーにドロップした場合、出力ファイルに次のコンテンツが表示されます。 各フォルダー (2015-11-16-00 など) は、フォルダーに複数の入力ファイルがある場合でも、このサンプルのスライスに対応します。

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

出力ファイルには 3 行あり、スライス (2015-11-16-00) に関連付けられているフォルダー内の入力ファイル (BLOB) ごとに 1 行あります。

アクティビティの実行ごとに、1 つのタスクが作成されます。 このサンプルでは、パイプラインにアクティビティが 1 つだけあります。 スライスがパイプラインによって処理されると、カスタム アクティビティはスライスを処理する Azure Batch 上で実行されます。 スライスが 5 個あるため (スライスごとに複数の BLOB またはファイルを持つことができます)、Azure Batch でタスクが 5 個作成されます。 タスクが Batch 上で実行されると、タスクは実際に実行されているカスタム アクティビティです。

次のチュートリアルでは、追加情報を示します。

#### <a name="step-1-create-the-data-factory"></a>手順 1: Data Factory を作成する
1. [Azure Portal](https://portal.azure.com/) にログインしたら、次の手順を実行します。

   1. 左側のメニューの **[新規]** をクリックします。
   2. **[新規]** ブレードの **[データ + 分析]** をクリックします。
   3. **[データ分析]** ブレードの **[Data Factory]** をクリックします。
2. **[新しい Data Factory]** ブレードで、[名前] フィールドに「**CustomActivityFactory**」と入力します。 Azure Data Factory の名前はグローバルに一意にする必要があります。 **""CustomActivityFactory" という名前の Data Factory は使用できません"** というエラーが発生した場合は、Data Factory の名前を変更して (**yournameCustomActivityFactory** など) 作成し直してください。
3. **[リソース グループ名]**をクリックし、既存のリソース グループを選択するか、リソース グループを作成します。
4. 作成する Data Factory に適したサブスクリプションとリージョンを使用していることを確認します。
5. **[新しい Data Factory]** ブレードで **[作成]** をクリックします。
6. 作成した Data Factory は、Azure ポータルの **ダッシュボード** に表示されます。
7. データ ファクトリが正常に作成されると、データ ファクトリの内容を表示するデータ ファクトリ ページが表示されます。

   ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>手順 2. リンク サービスを作成する
リンクされたサービスは、データ ストアまたはコンピューティング サービスを Azure Data Factory にリンクします。 この手順では、**Azure Storage** アカウントと **Azure Batch** アカウントを Data Factory にリンクします。

#### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
1. **[CustomActivityFactory]** の **[Data Factory]** ブレードで、**[作成およびデプロイ]** をクリックします。 Data Factory エディターが起動します。
2. コマンド バーの **[新しいデータ ストア]** をクリックし、**[Azure Storage]** を選択します。 Azure Storage のリンクされたサービスを作成するための JSON スクリプトがエディターに表示されます。

   ![](./media/data-factory-data-processing-using-batch/image7.png)

3. **accountname** は Azure ストレージ アカウントの名前に、**accountkey** は Azure ストレージ アカウントのアクセス キーに置き換えます。 ストレージ アクセス キーを取得する方法については、「 [ストレージ アクセス キーの表示、コピーおよび再生成](../storage/storage-create-storage-account.md#manage-your-storage-account)」を参照してください。

4. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

   ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>Azure Batch のリンクされたサービスの作成
この手順では、Data Factory のカスタム アクティビティの実行に使用される、**Azure Batch** アカウント用にリンクされたサービスを作成します。

1. コマンド バーの **[新しい計算]** をクリックし、**[Azure Batch]** を選択します。 Azure Batch のリンク サービスを作成するための JSON スクリプトが、エディターに表示されます。
2. JSON スクリプト内では:

   1. **account name** を Azure Batch アカウントの名前に置き換えます。
   2. **access key** を Azure Batch アカウントのアクセス キーに置き換えます。
   3. **poolName** プロパティにプールの ID を入力します**。** このプロパティでは、プール名またはプール ID のいずれかを指定できます。
   4. **batchUri** JSON プロパティにバッチ URI を入力します。

      > [!IMPORTANT]
      > **[Azure Batch アカウント] ブレード**の **URL** は、次の形式です: \<accountname\>.\<region\>.batch.azure.com。 JSON の **batchUri** プロパティでは、URL から **"accountname." を削除** する必要があります。 例: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![](./media/data-factory-data-processing-using-batch/image9.png)

      **poolName** プロパティでは、プール名の代わりにプール ID を指定することもできます。

      > [!NOTE]
      > Data Factory サービスでは、HDInsight の場合と同様、Azure Batch のオンデマンド オプションはサポートされません。 Azure データ ファクトリでは、独自の Azure Batch プールのみ使用できます。
      >
      >
   5. **linkedServiceName** プロパティに、**StorageLinkedService** を指定します。 このリンクされたサービスは、前の手順で作成しています。 このストレージは、ファイルおよびログのステージング領域として使用されます。
3. コマンド バーの **[デプロイ]** をクリックして、リンク サービスをデプロイします。

#### <a name="step-3-create-datasets"></a>手順 3: データセットを作成する
この手順では、入力データと出力データを表すデータセットを作成します。

#### <a name="create-input-dataset"></a>入力データセットの作成
1. Data Factory **エディター**のツール バーで **[新しいデータセット]** ボタンをクリックし、ドロップダウン メニューの **[Azure BLOB ストレージ]** をクリックします。
2. 右側のウィンドウの JSON を、次の JSON スニペットに置き換えます。

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
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

    このチュートリアルでは、開始時刻が 2015-11-16T00:00:00Z、終了時刻が 2015-11-16T05:00:00Z のパイプラインを後ほど作成します。 データを**毎時**生成するようにスケジュールされているので、5 つの入力/出力スライスがあります (**00**:00:00 -\> **05**:00:00)。

    入力データセットの **frequency** と **interval** はそれぞれ **Hour** と **1** に設定されています。つまり、1 時間に 1 つの入力スライスを利用できます。

    次に各スライスの開始時刻を示します。これは上記の JSON スニペットの **SliceStart** システム変数で表されます。

    | **スライス** | **開始時刻**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    **folderPath** は、スライス開始時間 (**SliceStart**) の年、月、日、時間の部分を使用して計算されます。 そのため、入力フォルダーをスライスにマップする方法を示します。

    | **スライス** | **開始時刻**          | **入力フォルダー**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. ツール バーの **[デプロイ]** をクリックし、**InputDataset** テーブルを作成してデプロイします。

#### <a name="create-output-dataset"></a>出力データセットの作成
この手順では、出力データを表す AzureBlob 型の別のデータセットを作成します。

1. Data Factory **エディター**のツール バーで **[新しいデータセット]** ボタンをクリックし、ドロップダウン メニューの **[Azure BLOB ストレージ]** をクリックします。
2. 右側のウィンドウの JSON を、次の JSON スニペットに置き換えます。

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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

    各入力スライスの出力 BLOB/ファイルが生成されます。 次に、各スライスの出力ファイルの命名方法を示します。 すべての出力ファイルは、**mycontainer\\outputfolder** という 1 つの出力フォルダーに生成されます。

    | **スライス** | **開始時刻**          | **出力ファイル**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    入力フォルダー (例: 2015-11-16-00) 内のすべてのファイルは、開始時刻が 2015-11-16-00 であるスライスの一部です。 このスライスを処理すると、カスタム アクティビティは各ファイルをスキャンし、出力ファイルに、検索語句 (“Microsoft”) の出現回数が記述された 1 行を生成します。 2015-11-16-00 フォルダーに 3 つのファイルがある場合は、出力ファイル (2015-11-16-00.txt) に 3 行が出力されます。

1. ツール バーの **[デプロイ]** をクリックし、**OutputDataset** を作成してデプロイします。

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>手順 4: カスタム アクティビティを使用して、パイプラインを作成して実行する
この手順では、以前に作成したカスタム アクティビティである、1 つのアクティビティでパイプラインを作成します。

> [!IMPORTANT]
> BLOB コンテナー内の入力フォルダーに **file.txt** をまだアップロードしていない場合は、パイプラインを作成する前に行ってください。 **isPaused** プロパティがパイプラインの JSON で false に設定されているため、パイプラインは **start** の日を過ぎると、すぐに実行されます。
>
>

1. Data Factory エディターで、コマンド バーの **[新しいパイプライン]** をクリックします。 このコマンドが表示されない場合は、**[...](省略記号)** をクリックすると表示されます。
2. 右側のウィンドウの JSON を、次の JSON スクリプトに置き換えます。

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
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
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   次の点に注意してください。

   * パイプラインには、 **DotNetActivity**型のアクティビティが 1 つだけあります。
   * **AssemblyName** を DLL の名前 (**MyDotNetActivity.dll**) に設定します。
   * **EntryPoint** を **MyDotNetActivityNS.MyDotNetActivity** に設定します。 基本的に、コード内の \<namespace\>.\<classname\> です。
   * **PackageLinkedService** は **StorageLinkedService** に設定されます。これは、カスタム アクティビティの zip ファイルを含む Blob Storage を示します。 入力/出力ファイルとカスタム アクティビティ zip ファイルに別の Azure Storage アカウントを使用している場合、Azure Storage のリンクされたサービスを別に作成する必要があります。 この記事では、同じ Azure Storage アカウントを使用している前提で説明します。
   * **PackageFile** を **customactivitycontainer/MyDotNetActivity.zip** に設定します。 形式は \<containerforthezip\>/\<nameofthezip.zip\> です。
   * カスタム アクティビティは入力として **InputDataset**、出力として **OutputDataset** を使用します。
   * カスタム アクティビティの **linkedServiceName** プロパティは、**AzureBatchLinkedService** を示します。これによって、Azure Batch でカスタム アクティビティが実行する必要がある Azure Data Factory がわかります。
   * **concurrency** の設定は重要です。 既定値を使用する場合は、Azure Batch プールにコンピューティング ノードが複数ある場合でも、スライスは 1 つずつ処理されます。 そのため、Azure Batch の並行処理機能を利用することはありません。 **concurrency** をより大きな値に設定した場合、2 とすると、つまり 2 つのスライス (Azure Batch 内の 2 つのタスクに対応する) が、同時に処理できます。この場合、Azure Batch プール内の VM が両方利用されます。 そのため、concurrency プロパティを適切に設定します。
   * 既定では、1 つのみのタスク (スライス) が、VM 上で任意のポイントで実行されます。 これは、既定では、**VM ごとの最大タスク**が、Azure Batch プールに対して 1 に設定されているためです。 前提条件の一部として、このプロパティを 2 に設定したプールを作成しているため、2 つの Data Factory のスライスは、同時に VM 上で実行される場合があります。

    -   **isPaused** プロパティは、既定で false に設定されています。 この例では、スライスが過去に開始されているので、パイプラインは即時に実行されます。 このプロパティを true に設定すると、パイプラインを一時停止できます。また false に設定し直すと再開されます。

    -   **start** 時刻と **end** 時刻は 5 時間離れており、スライスは 1 時間ごとに生成されるので、パイプラインによって 5 つのスライスが生成されます。

1. コマンド バーの **[デプロイ]** をクリックして、パイプラインをデプロイします。

#### <a name="step-5-test-the-pipeline"></a>手順 5: パイプラインをテストする
この手順では、ファイルを入力フォルダーにドロップして、パイプラインをテストします。 入力フォルダーごとに 1 つのファイルを使用して、パイプラインをテストしましょう。

1. Azure ポータルの [Data Factory] ブレードで、 **[ダイアグラム]**をクリックします。

   ![](./media/data-factory-data-processing-using-batch/image10.png)
2. ダイアグラム ビューで、 **[InputDataset]**という入力データセットをダブルクリックします。

   ![](./media/data-factory-data-processing-using-batch/image11.png)
3. 5 個のスライスがすべて準備完了の **[InputDataset]** ブレードが表示されます。 各スライスの **[スライス開始時間]** と **[スライス終了時間]** に注意してください。

   ![](./media/data-factory-data-processing-using-batch/image12.png)
4. **[ダイアグラム]** ビューで、**OutputDataset** をクリックします。
5. スライスが生成されると、準備完了状態の出力スライスが 5 個表示されます。

   ![](./media/data-factory-data-processing-using-batch/image13.png)
6. Azure Portal を使用して、**スライス**に関連付けられている**タスク**を表示し、各スライスが実行された VM を確認します。 詳細については、「 [Data Factory と Batch の統合](#data-factory-and-batch-integration) 」をご覧ください。
7. Azure Blob Storage の **mycontainer** の **outputfolder** に、出力ファイルが表示されます。

   ![](./media/data-factory-data-processing-using-batch/image15.png)

   出力ファイルが 5 個 (入力スライスごとに 1 個) 表示されます。 出力ファイルごとに、次の出力のようなコンテンツがあります。

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   次のダイアグラムでは、Data Factory のスライスを Azure Batch のタスクにマップする方法を示します。 この例では、スライスごとに 1 つだけを実行します。

   ![](./media/data-factory-data-processing-using-batch/image16.png)
8. フォルダー内の複数のファイルを試してみましょう。 フォルダー **2015-11-06-01** にある file.txt と同じ内容で、　**file2.txt**、**file3.txt**、**file4.txt**、および **file5.txt** のファイルを作成します。
9. 出力フォルダーで、**2015-11-16-01.txt** という出力ファイルを**削除**します。
10. **[OutputDataset]** ブレードで、**[スライス開始時間]** を **11/16/2015 01:00:00 AM** に設定したスライスを右クリックして、**[実行]** をクリックし、スライスを再実行または再度処理します。 スライスには、1 個のファイルではなく、5 個のファイルがあります。

    ![](./media/data-factory-data-processing-using-batch/image17.png)
11. スライスを実行して、その状態が **[準備完了]** になると、Blob Storage の **mycontainer** の **outputfolder** で、このスライスの出力ファイル (**2015-11-16-01.txt**) の内容を確認します。 スライスのファイルごとに 1 行あります。

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> 5 つの入力ファイルを試す前に、出力ファイル 2015-11-16-01.txt を削除しなかった場合は、以前に実行したスライスから 1 行と、現在実行しているスライスから 5 行表示されます。 既定では、コンテンツが既に存在している場合、出力ファイルに追加されます。
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory と Batch の統合
Data Factory サービスによって、Azure Batch に **adf-poolname:job-xxx**という名前のジョブが作成されます。

![Azure Data Factory - Batch ジョブ](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

スライスのアクティビティの実行ごとに、1 つのタスクがジョブに作成されます。 10 個のスライスを処理する準備ができたとき、ジョブには 10 個のタスクが作成されています。 プール内に複数のコンピューティング ノードがある場合、複数のスライスを並列して実行することができます。 コンピューティング ノードごとの最大タスクが 1 より大きな値に設定されている場合、同じコンピューティング ノードで複数のスライスを実行することもできます。

この例では、スライスが 5 個あるため、Azure Batch には 5 個のタスクがあります。 Azure Data Factory のパイプライン JSON の **concurrency** を **5** に設定し、**2** つの VM が含まれた Azure Batch プールの **[Maximum tasks per VM (VM ごとの最大タスク数)]** を **2** に設定すると、タスクが高速で実行されるようになります (タスクの開始時刻と終了時刻を確認してください)。

ポータルを使用して、 **スライス** に関連付けられている Batch ジョブとそのタスクを表示し、各スライスが実行された VM を確認します。

![Azure Data Factory - Batch ジョブのタスク](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>パイプラインのデバッグ
デバッグには、いくつかの基本的な技術があります。

1. 入力スライスが **[準備完了]**に設定されていない場合、入力フォルダー構造が正しく、入力フォルダーに file.txt が存在することを確認します。

   ![](./media/data-factory-data-processing-using-batch/image3.png)
2. 問題のトラブルシューティングに役立つ情報をログに記録するには、カスタム アクティビティの **Execute** メソッドで、**IActivityLogger** オブジェクトを使用します。 ログに記録されたメッセージは user\_0.log ファイルに表示されます。

   **[OutputDataset]** ブレードで、スライスをクリックすると、そのスライスの **[データ スライス]** ブレードが表示されます。 そのスライスの **アクティビティの実行** が表示されます。 このスライスには 1 回のアクティビティの実行が表示されます。 コマンド バーの **[実行]** をクリックする場合、同じスライスの別のアクティビティの実行を開始できます。

   アクティビティの実行をクリックすると、**[アクティビティの実行の詳細]** ブレードにログ ファイルの一覧が表示されます。 **user\_0.log** ファイルにログに記録されたメッセージが表示されます。 パイプライン/アクティビティ JSON で再試行回数が 3 に設定されているので、エラーが発生した場合、3 つのアクティビティの実行が表示されます。 アクティビティの実行をクリックすると、ログ ファイルが表示されます。このファイルを確認して、エラーのトラブルシューティングを行うことができます。

   ![](./media/data-factory-data-processing-using-batch/image18.png)

   ログ ファイルの一覧で、 **user-0.log**をクリックします。 右側のパネルには、 **IActivityLogger.Write** メソッドの使用結果が表示されます。

   ![](./media/data-factory-data-processing-using-batch/image19.png)

   system-0.log では、システム エラー メッセージと例外を確認できます。

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. エラーの詳細にエラー発生時の**コール スタック**などの情報が含まれるようにするには、zip ファイルに **PDB** ファイルを含めます。
4. カスタム アクティビティの zip ファイルのすべてのファイルは、サブフォルダーがない **トップ レベル** に置く必要があります。

   ![](./media/data-factory-data-processing-using-batch/image20.png)
5. **assemblyName** (MyDotNetActivity.dll)、**entryPoint** (MyDotNetActivityNS.MyDotNetActivity)、**packageFile** (customactivitycontainer/MyDotNetActivity.zip)、**packageLinkedService** (zip ファイルを含む Azure Blob Storage を示す必要があります) が正しい値に設定されていることを確認します。
6. エラーを修正し、スライスを再処理する場合は、**[OutputDataset]** ブレードのスライスを右クリックし、**[実行]** をクリックします。

   ![](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > **adfjobs** という名前の Azure Blob Storage に、**コンテナー**が表示されます。 このコンテナーは自動的に削除されませんが、ソリューションのテストを完了した後に、安全に削除することができます。 同様に、Data Factory ソリューションでは、**adf-\<pool ID/name\>:job-0000000001** という名前の Azure Batch **ジョブ**を作成します。 必要な場合は、ソリューションをテストした後、このジョブを削除することができます。
   >
   >
7. このカスタム アクティビティでは、パッケージ内の **app.config** ファイルは使用されません。 そのためこの構成ファイルから接続文字列を読み取るようにコードを記述した場合、実行時に正しく機能しません。 Azure Batch を使用するときは、すべてのシークレットを **Azure KeyVault** に保持し、証明書ベースのサービス プリンシパルを使用して keyvault を保護したうえで、Azure Batch プールに証明書を配布するのがベスト プラクティスです。 こうすることで .NET カスタム アクティビティが実行時に KeyVault 内のシークレットにアクセスすることができます。 このソリューションは一般的なものであり、接続文字列に限らず、あらゆる種類のシークレットに応用できます。

    ベスト プラクティスではありませんが、同じことをもっと簡単に行うこともできます。接続文字列設定を使用して **Azure SQL のリンクされたサービス**を作成し、そのリンクされたサービスを使用するデータセットを作成して、カスタム .NET アクティビティにダミーの入力データセットとして連結します。 リンクされたサービスの接続文字列にカスタム アクティビティのコード内からアクセスすれば、実行時に適切に機能します。  

#### <a name="extend-the-sample"></a>サンプルの拡張
Azure Data Factory および Azure Batch の機能の詳細については、このサンプルを拡張することができます。 たとえば、異なる時間範囲でスライスを処理するには、次の手順を実行します。

1. **inputfolder**に2015-11-16-05、2015-11-16-06、201-11-16-07、2011-11-16-08、2015-11-16-09 のサブフォルダーを追加し、これらのフォルダーに入力ファイルを配置します。 パイプラインの終了時刻を `2015-11-16T05:00:00Z` から `2015-11-16T10:00:00Z` に変更します。 **[ダイアグラム] ビュー**で、**[InputDataset]** をダブルクリックして、入力スライスが準備完了であることを確認します。 **[OuptutDataset]** をダブルクリックして、出力スライスの状態を表示します。 準備完了の状態である場合、出力ファイルの outputfolder を確認します。
2. 特に Azure Batch 上で発生する処理のように、どのようにソリューションのパフォーマンスに影響するかを理解するには、**concurrency** の設定を増加または減少させます。 (**concurrency** の設定の詳細については、「手順 4: パイプラインを作成して実行する」を参照してください。)
3. **[VM ごとの最大タスク]** を高くまたは低くして、プールを作成します。 新しく作成したプールを使用するには、Data Factory ソリューションで、Azure Batch のリンクされたサービスを更新します。 (**VM ごとの最大タスク**の設定の詳細については、「手順 4: パイプラインを作成して実行する」を参照してください。)
4. **[自動スケール]** 機能で、Azure Batch プールを作成します。 Azure Batch プール内のコンピューティング ノードの自動スケールは、アプリケーションによって使用される処理能力を動的に調整します。 たとえば、専用 VM 数が 0 の Azure Batch プールと、保留中のタスクの数に基づく自動スケールの数式を作成できます。

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
5. サンプル ソリューションで、**Execute** メソッドは、出力データ スライスを生成するために入力データ スライスを処理する、**Calculate** メソッドを呼び出します。 入力データを処理し、Execute メソッドで呼び出される Calculate メソッドを、メソッドの呼び出しに置換する独自のメソッドを記述することができます。

### <a name="next-steps-consume-the-data"></a>次の手順: データの処理
データを処理した後、 **Microsoft Power BI** などのオンライン ツールで使用することができます。 Power BI や Azure で使用する方法を理解するために役立つリンクを次に示します。

* [Power BI でのデータセットの参照](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Power BI Desktop の概要](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Power BI でのデータの更新](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure と Power BI - 基本的な概要](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>参照
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Azure Data Factory サービスの概要](data-factory-introduction.md)
  * [Azure Data Factory を使ってみる](data-factory-build-your-first-pipeline.md)
  * [Azure Data Factory パイプラインでカスタム アクティビティを使用する](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Azure Batch の基礎](../batch/batch-technical-overview.md)
  * [Azure Batch 機能の概要](../batch/batch-api-basics.md)
  * [Azure Portal で Azure Batch アカウントを作成して管理する](../batch/batch-account-create-portal.md)
  * [Azure Batch ライブラリ .NET の概要](../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

