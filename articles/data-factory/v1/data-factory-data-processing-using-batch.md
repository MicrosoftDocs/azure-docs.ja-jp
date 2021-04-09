---
title: Data Factory と Batch を使用して大規模なデータセットを処理する
description: Azure Batch の並列処理機能を使用して、Azure Data Factory パイプラインで膨大な量のデータを処理する方法について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 2568fc2ea133a0cdd49e0a2577ce1e580056a74c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783134"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Data Factory と Batch を使用して大規模なデータセットを処理する
> [!NOTE]
> この記事は、一般公開されている Azure Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory でのカスタム アクティビティ](../transform-data-using-dotnet-custom-activity.md)に関する記事を参照してください。

この記事では、大規模なデータセットの移動と処理をスケジュールに沿って自動的に行う、サンプル ソリューションのアーキテクチャについて説明します。 また、Data Factory と Azure Batch を使用してソリューションを実装する、エンドツーエンドのチュートリアルも提供します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

この記事には、サンプル ソリューション全体のチュートリアルが含まれるため、通常の記事よりも長くなっています。 Batch と Data Factory のご使用が初めての方は、Batch と Data Factory のサービスについて知り、これらがどのように連携するかを学ぶことができます。 サービスについてある程度知識があり、ソリューションを設計、構築している場合は、この記事のアーキテクチャのセクションに重点を置くこともできます。 プロトタイプまたはソリューションを開発している場合は、チュートリアルの詳細な手順を試すこともお勧めです。 内容に関するご意見や、ご利用法についてお聞かせください。

最初に、クラウドの大規模なデータセットの処理に、Data Factory と Batch のサービスがどのように役立つかについて説明します。     


## <a name="why-azure-batch"></a>Azure Batch を選ぶ理由
 Batch を使用して、大規模な並列コンピューティングやハイパフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行することができます。 Batch は、仮想マシン (VM) の管理されたコレクションを実行するため、大量の計算を必要とする作業をスケジュールするためのプラットフォーム サービスです。 ジョブのニーズに合わせてコンピューティング リソースを自動的にスケーリングできます。

Batch サービスでは、複数のアプリケーションを並列で大規模に実行するための Azure コンピューティング リソースを定義します。 オンデマンドまたはスケジュールされたジョブを実行できます。 HPC クラスター、個々の VM、仮想ネットワークや、複雑なジョブとタスクのスケジュール インフラストラクチャを手動で作成、構成、管理する必要がありません。

 Batch に慣れていない場合は、次の記事を参照してください。この記事で説明されるソリューションのアーキテクチャや実装の理解に役立ちます。   

* [Batch の基本](../../azure-sql/database/sql-database-paas-overview.md)
* [Batch 機能の概要](../../batch/batch-service-workflow-features.md)

必要な場合は、「[Batch のドキュメント](../../batch/index.yml)」で Batch の詳細を確認してください。

## <a name="why-azure-data-factory"></a>Azure Data Factory を選ぶ理由
Data Factory は、データの移動や変換を調整し自動化するクラウドベースのデータ統合サービスです。 Data Factory を使用すると、オンプレミスおよびクラウドのデータ ストアから中央のデータ ストアにデータを移動する、マネージド データ パイプラインを作成できます。 たとえば、Azure BLOB ストレージなどです。 Data Factory を使用すると、Azure HDInsight や Azure Machine Learning などのサービスを利用してデータを処理または変換することができます。 スケジュールされた方法 (たとえば、時間単位、日単位、週単位) でデータ パイプラインを実行するようにスケジュールを設定することもできます。 パイプラインを一目で監視および管理して、問題を特定し、行動を取ることができます。

  Data Factory に慣れていない場合は、次の記事を参照してください。この記事で説明されるソリューションのアーキテクチャや実装の理解に役立ちます。  

* [Data Factory の概要](data-factory-introduction.md)
* [最初のデータ パイプラインの作成](data-factory-build-your-first-pipeline.md)   

必要な場合は、「[the Data Factory documentation](/rest/api/datafactory/v1/data-factory-data-factory)」 (Data Factory のドキュメント) で Data Factory の詳細を確認してください。

## <a name="data-factory-and-batch-together"></a>Data Factory と Batch の連携
Data Factory には組み込みのアクティビティが含まれています。 たとえば、コピー アクティビティは、コピー元データ ストアからコピー先データ ストアにデータをコピーまたは移動するために使用されます。 Hive アクティビティは、Azure で Hadoop クラスター (HDInsight) を使用してデータを処理するために使用されます。 サポートされている変換アクティビティの一覧については、[データの変換アクティビティ](data-factory-data-transformation-activities.md)に関する記事を参照してください。

独自のロジックを使用してデータを移動または処理するカスタム .NET アクティビティを作成することもできます。 これらのアクティビティは、HDInsight クラスターまたは VM の Batch プールで実行できます。 Batch を使用すると、入力した式に基づいて、プールを自動スケール (ワークロードに基づき仮想マシンの追加または削除を行う) に設定できます。     

## <a name="architecture-of-a-sample-solution"></a>サンプル ソリューションのアーキテクチャ
  この記事で説明するアーキテクチャは、単純なソリューション向けです。 また、金融サービスによるリスク モデリング、画像処理とレンダリング、ゲノム解析などの複雑なシナリオにも関連します。

このダイアグラムは、Data Factory がデータの移動と処理を調整する方法を示しています。 また、Batch がどのようにデータを並列処理するかについても示しています。 簡単に参照できるように、このダイアグラムをダウンロードして印刷してください (11 x 17 インチまたは A3 サイズ)。 ダイアグラムにアクセスして印刷できるようにするには、「[HPC and data orchestration by using Batch and Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686)」(Batch および Data Factory を使用した HPC とデータのオーケストレーション) を参照してください。

[![大規模なデータ処理のダイアグラム](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

処理の基本的な手順を次のリストに示します。 ソリューションには、エンド ツー エンド ソリューションをビルドするためのコードと説明が含まれています。

* **コンピューティング ノード (VM) のプールで Batch を構成します。** 各ノードのノード数とサイズを指定することができます。

* BLOB ストレージ、Batch コンピューティング サービス、入出力データ、データを移動および変換するアクティビティを含むワークフロー/パイプラインを表すエンティティで構成された **Data Factory インスタンスを作成します**。

* **Data Factory パイプラインにカスタム .NET アクティビティを作成します。** このアクティビティは、Batch プールで実行されるユーザー コードです。

* **Azure Storage に大量の入力データを BLOB として格納します。** データは、論理スライス (通常は時間) に分割されます。

* **並行して処理されるデータを Data Factory が 2 次拠点にコピー** します。

* **Data Factory は、Batch によって割り当てられたプールを使用してカスタム アクティビティを実行します。** Data Factory は、アクティビティを同時に実行できます。 各アクティビティは、データのスライスを処理します。 結果はストレージに格納されます。

* アプリによる配布、または他のツールによる追加処理のために、**Data Factory は最終的な結果を 3 次拠点に移動** させます。

## <a name="implementation-of-the-sample-solution"></a>サンプル ソリューションの実装
サンプル ソリューションは意図的に単純にしています。 Data Factory と Batch を併用してデータセットを処理する方法を示すために設計されています。 このソリューションでは、時系列で編成された入力ファイル内の検索語句 "Microsoft" の出現回数をカウントします。 出力ファイルにその数を出力します。

**時間:** Azure、Data Factory、および Batch の基礎を理解し、次の前提条件をすべて満たしている場合、このソリューションの完成までにかかる時間は 1 ～ 2 時間です。

### <a name="prerequisites"></a>前提条件
#### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、すぐに無料トライアル アカウントを作成できます。 詳細については、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

#### <a name="azure-storage-account"></a>Azure ストレージ アカウント
このチュートリアルでは、ストレージ アカウントを使用してデータを保存します。 ストレージ アカウントがない場合は、[ストレージ アカウントの作成](../../storage/common/storage-account-create.md)に関するページを参照してください。 サンプル ソリューションでは、Blob Storage を使用します。

#### <a name="azure-batch-account"></a>Azure Batch アカウント
[Azure Portal](https://portal.azure.com/) を使用して Batch アカウントを作成します。 詳細については、[Batch アカウントの作成と管理](../../batch/batch-account-create-portal.md)に関するページを参照してください。 Batch のアカウント名とアカウント キーをメモしておきます。 また、[New-AzBatchAccount](/powershell/module/az.batch/new-azbatchaccount) コマンドレットを使用して Batch アカウントを作成することもできます。 このコマンドレットの使用手順については、[Batch PowerShell コマンドレットの概要](../../batch/batch-powershell-cmdlets-get-started.md)に関するページを参照してください。

サンプル ソリューションでは、(データ ファクトリ パイプラインを通じて間接的に) Batch を使用して、VM の管理コレクションであるコンピューティング ノードのプールで、同じ方法でデータを処理します。

#### <a name="azure-batch-pool-of-virtual-machines"></a>仮想マシンの Azure Batch プール
2 個以上のコンピューティング ノードで Batch プール を作成します。

1. [Azure Portal](https://portal.azure.com) で、左側のメニューの **[参照]** を選択し、 **[Batch アカウント]** を選択します。

1. Batch アカウントを選択して、 **[Batch アカウント]** ブレードを開きます。

1. **[プール]** タイルを選択します。

1. **[プール]** ブレードで、ツールバーの **[追加]** ボタンを選択してプールを追加します。

   a. プールの ID を入力します (**プール ID**)。 プールの ID をメモしておきます。 データ ファクトリ ソリューションを作成するときに必要になります。

   b. **[オペレーティング システム ファミリ]** 設定には、 **[Windows Server 2012 R2]** を指定します。

   c. **ノード価格レベル** を選択します。

   d. **[ターゲットの専用数]** の設定値として、「**2**」と入力します。

   e. **[ノードごとの最大タスク]** の設定値として、「**2**」と入力します。

   f. **[OK]** を選択してプールを作成します。

#### <a name="azure-storage-explorer"></a>Azure ストレージ エクスプローラー
[Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) または [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (ClumsyLeaf Software 製) を使用して、Storage プロジェクトのデータを検査し、変更します。 また、クラウドホスト型アプリケーションのログのデータを検査して変更することもできます。

1. プライベートなアクセス (匿名アクセスなし) で **mycontainer** という名前のコンテナーを作成します。

1. CloudXplorer を使用している場合、次の構造でフォルダーおよびサブフォルダーを作成します。

   ![フォルダーとサブフォルダーの構造](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` と `outputfolder` は、`mycontainer` 内の最上位フォルダーです。 `inputfolder` フォルダー内には、日付時刻スタンプ (YYYY-MM-DD-HH 形式) の付いたサブフォルダーがあります。

   Storage Explorer を使用している場合は、次の手順で、`inputfolder/2015-11-16-00/file.txt`、`inputfolder/2015-11-16-01/file.txt` などの名前のファイルをアップロードします。 この手順では、フォルダーが自動的に作成されます。

1. テキスト ファイル **file.txt** を、キーワード **Microsoft** を含めた内容でコンピューターに作成します。 たとえば、"test custom activity Microsoft test custom activity Microsoft" などです。

1. ファイルを BLOB ストレージ内の次の入力フォルダーにアップロードします。

   ![入力フォルダー](./media/data-factory-data-processing-using-batch/image4.png)

   Storage Explorer を使用している場合は、ファイル **file.txt** を **mycontainer** にアップロードします。 ツールバーの **[コピー]** を選択して、BLOB のコピーを作成します。 **[BLOB のコピー]** ダイアログ ボックスで、 **[宛先 BLOB 名]** を「`inputfolder/2015-11-16-00/file.txt`」に変更します。 この手順を繰り返して `inputfolder/2015-11-16-01/file.txt`、`inputfolder/2015-11-16-02/file.txt`、`inputfolder/2015-11-16-03/file.txt`、`inputfolder/2015-11-16-04/file.txt` などを作成します。 この操作では、フォルダーが自動的に作成されます。

1. `customactivitycontainer` という名前の別のコンテナーを作成します。 カスタム アクティビティの zip ファイルを、このコンテナーにアップロードします。

#### <a name="visual-studio"></a>Visual Studio
Visual Studio 2012 以降をインストールして、データ ファクトリ ソリューションで使用するカスタム Batch アクティビティを作成します。

### <a name="high-level-steps-to-create-the-solution"></a>ソリューションを作成する手順の概要
1. データ処理ロジックが含まれるカスタム アクティビティを作成します。

1. カスタム アクティビティを使用するデータ ファクトリを作成します。

### <a name="create-the-custom-activity"></a>カスタム アクティビティの作成
データ ファクトリのカスタム アクティビティは、このサンプル ソリューションの核となる機能です。 サンプル ソリューションでは、Batch を使用して、カスタム アクティビティを実行します。 カスタム アクティビティを開発して データ ファクトリ パイプラインで使用する方法については、「[Use custom activities in a data factory pipeline](data-factory-use-custom-activities.md)」(データ ファクトリ パイプラインでカスタム アクティビティを使用する) を参照してください。

データ ファクトリ パイプラインで使用できる .NET カスタム アクティビティを作成するには、IDotNetActivity インターフェイスを実装したクラスを含む .NET クラス ライブラリ プロジェクトを作成します。 このインターフェイスには、Execute という 1 つのメソッドのみが含まれます。 このメソッドのシグネチャを次に示します。

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

このメソッドには、理解しておく必要がある重要な要素がいくつかあります。

* このメソッドには次の 4 つのパラメーターがあります。

  * **linkedServices**。 このパラメーターは、入力/出力データ ソース (たとえば、BLOB ストレージ) をデータ ファクトリにリンクする、リンクされたサービスの列挙可能なリストです。 このサンプルでは、入力と出力の両方に使用される Azure Storage 型のリンクされたサービスは 1 つのみです。
  * **datasets**。 このパラメーターは、データセットの列挙可能なリストです。 このパラメーターを使用すると、入力と出力のデータセットに定義されている場所とスキーマを取得できます。
  * **activity**。 このパラメーターは、現在のコンピューティング エンティティを表します。 この場合は Batch サービスです。
  * **logger**。 logger を使用すると、パイプラインの "User" ログとして表示されるデバッグ コメントを作成できます。
* メソッドから、今後、カスタム アクティビティの連結に使用できるディクショナリが返されます。 この機能はまだ実装されていないため、メソッドからは空のディクショナリが返されるだけです。

#### <a name="procedure-create-the-custom-activity"></a>手順:カスタム アクティビティの作成
1. Visual Studio で .NET クラス ライブラリ プロジェクトを作成します。

   a. Visual Studio 2012/2013/2015 を起動します。

   b. **[File]**  >  **[New]**  >  **[Project]** の順に選択します。

   c. **[テンプレート]** を展開し、 **[Visual C#]\#** を選択します。 このチュートリアルでは C\# を使用しますが、カスタム アクティビティの開発には、どの .NET 言語でも使用できます。

   d. 右側にあるプロジェクトの種類の一覧から **[クラス ライブラリ]** を選択します。

   e. **[プロジェクト名]** に「**MyDotNetActivity**」と入力します。

   f. **[場所]** で [**C:\\ADF**] を選択します。 **ADF** フォルダーが存在しない場合は作成します。

   g. **[OK]** を選択すると、プロジェクトが作成されます。

1. **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。

1. パッケージ マネージャー コンソールで、次のコマンドを実行して Microsoft.Azure.Management.DataFactories をインポートします。

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. **Azure Storage** NuGet パッケージをプロジェクトにインポートします。 このパッケージは、このサンプルで Blob Storage API を使用するために必要です。

    ```powershell
    Install-Package Az.Storage
    ```
1. 次の using ディレクティブをプロジェクト内のソース ファイルに追加します。

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
1. 名前空間の名前を **MyDotNetActivityNS** に変更します。

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. クラス名を **MyDotNetActivity** に変更し、次に示すように **IDotNetActivity** インターフェイスから派生させます。

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. **IDotNetActivity** インターフェイスの **Execute** メソッドを **MyDotNetActivity** クラスに実装 (追加) します。 次のサンプル コードをメソッドにコピーします。 このメソッドで使用されるロジックの説明については、「[Execute メソッド](#execute-method)」セクションを参照してください。

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {

       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;

       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);

       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;

       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.

       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

       // Initialize the continuation token before using it in the do-while loop.
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

           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

       } while (continuationToken != null);

       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

       folderPath = GetFolderPath(outputDataset);

       logger.Write("Writing blob to the folder: {0}", folderPath);

       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);

       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. 次のヘルパー メソッドをクラスに追加します。 これらのメソッドは、 **Execute** メソッドによって呼び出されます。 最も重要な点は、**Calculate** メソッドは、各 BLOB で反復処理されるコードを分離することです。

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
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
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
    GetFolderPath メソッドは、データセットが指すフォルダーのパスを返し、GetFileName メソッドは、データセットが指す BLOB/ファイルの名前を返します。

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Calculate メソッドは、入力ファイル (フォルダー内の BLOB) 内のキーワード "Microsoft" のインスタンス数を計算します。 検索語句 "Microsoft" は、コードにハード コーディングされています。

1. プロジェクトをコンパイルします。 メニューの **[ビルド]** を選択し、 **[ソリューションのビルド]** を選択します。

1. エクスプローラーを起動し、**bin\\debug** または **bin\\release** フォルダーに移動します。 フォルダーの選択は、ビルドの種類によって変わります。

1. **\\bin\\Debug** フォルダー内のすべてのバイナリを含む zip ファイル、**MyDotNetActivity.zip** を作成します。 エラー発生時の問題の原因となったソース コードの行番号など、追加情報を取得するために、MyDotNetActivity.**pdb** ファイルを含めることもできます。

   ![bin\Debug フォルダー一覧](./media/data-factory-data-processing-using-batch/image5.png)

1. **MyDotNetActivity.zip** を BLOB ストレージ内の BLOB コンテナー `customactivitycontainer` に BLOB としてアップロードします。このコンテナーは、ADFTutorialDataFactory 内のリンクされたサービス StorageLinkedService が使用します。 BLOB コンテナー `customactivitycontainer` がまだ存在しない場合は作成します。

#### <a name="execute-method"></a>Execute メソッド
ここでは、Execute メソッドのコードの詳細について説明します。

1. 入力コレクションを反復処理するメンバーは、 [Microsoft.WindowsAzure.Storage.Blob](/java/api/com.microsoft.azure.storage.blob) 名前空間にあります。 BLOB コレクションの反復処理では、**BlobContinuationToken** クラスを使用する必要があります。 基本的に、既存のループのメカニズムとして、トークンに do-while ループを使用する必要があります。 詳細については、[.NET から BLOB ストレージを使用する方法](../../storage/blobs/storage-quickstart-blobs-dotnet.md)に関するページを参照してください。 基本的なループを次に示します。

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
   詳細については、[ListBlobsSegmented](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented) メソッドのドキュメントを参照してください。

1. 論理的には、BLOB セットを操作するコードを do-while ループ内に配置します。 **Execute** メソッドの場合、do-while ループは BLOB の一覧を **Calculate** というメソッドに渡します。 Calculate メソッドは、セグメント内のすべての BLOB を反復処理した結果である **output** という文字列変数を返します。

   また、**Calculate** メソッドに渡された BLOB 内の検索語句 ("Microsoft") の出現回数を返します。

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. **Calculate** メソッドの処理が完了すると、新しい BLOB に出力されます。 処理対象の BLOB セットごとに、結果を新しい BLOB に出力できます。 新しい BLOB に出力するには、まず出力データセットを検索します。

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. また、このコードは、**GetFolderPath** というヘルパー メソッドも呼び出して、フォルダー パス (ストレージ コンテナー名) を取得します。

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   GetFolderPath メソッドは、FolderPath というプロパティがある AzureBlobDataSet に DataSet オブジェクトをキャストします。

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. コードで、ファイル名 (BLOB 名) を取得する **GetFileName** メソッドを呼び出します。 このコードは、フォルダー パスを取得するために使用した上記のコードと似ています。

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. URI オブジェクトを作成することで、ファイル名が書き込まれます。 URI コンストラクターは **BlobEndpoint** プロパティを使用して、コンテナー名を返します。 フォルダー パスとファイル名が追加され、出力 BLOB URI が構築されます。  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. ファイル名が出力されると、**Calculate** メソッドから出力文字列を新しい BLOB に出力できるようになります。

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Data Factory の作成
「[カスタム アクティビティの作成](#create-the-custom-activity) 」セクションでは、カスタム アクティビティを作成し、バイナリと PDB ファイルを含む zip ファイルを BLOB コンテナーにアップロードしました。 ここでは、カスタム アクティビティを使用したパイプラインでデータ ファクトリを作成します。

カスタム アクティビティの入力データセットは、Blob Storage の入力フォルダー (`mycontainer\\inputfolder`) 内にある BLOB (ファイル) を表します。 アクティビティの出力データセットは、Blob Storage の出力フォルダー (`mycontainer\\outputfolder`) 内にある出力 BLOB を表します。

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

各入力フォルダーに複数のファイルが含まれている場合でも、入力フォルダーはデータ ファクトリの 1 つのスライスに対応します。 パイプラインで各スライスが処理されるときに、カスタム アクティビティでは、そのスライスの入力フォルダー内にあるすべての BLOB が反復処理されます。

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

アクティビティの実行ごとに、1 つのタスクが作成されます。 このサンプルでは、パイプラインにアクティビティが 1 つだけあります。 スライスがパイプラインによって処理されると、カスタム アクティビティはスライスを処理する Batch 上で実行されます。 スライスが 5 個あるため (スライスごとに複数の BLOB またはファイルを持つことができます)、Batch でタスクが 5 個作成されます。 タスクが Batch 上で実行されると、タスクは実行されているカスタム アクティビティです。

次のチュートリアルでは、追加情報を示します。

#### <a name="step-1-create-the-data-factory"></a>手順 1:Data Factory の作成
1. [Azure Portal](https://portal.azure.com/) にサインインしてから、次の手順を実行します。

   a. 左側のメニューの **[新規]** を選択します。

   b. **[新規]** ブレードの **[データ + 分析]** を選択します。

   c. **[データ分析]** ブレードの **[Data Factory]** を選択します。

1. **[新しい Data Factory]** ブレードで、名前に「**CustomActivityFactory**」と入力します。 データ ファクトリの名前はグローバルに一意にする必要があります。 "データ ファクトリ名 CustomActivityFactory は利用できません" というエラーが発生した場合は、データ ファクトリの名前を変更します。 たとえば、yournameCustomActivityFactory を使用し、もう一度データ ファクトリを作成します。

1. **[リソース グループ名]** を選択し、既存のリソース グループを選択するか、リソース グループを作成します。

1. データ ファクトリを作成するサブスクリプションとリージョンが正しいことを確認します。

1. **[新しいデータ ファクトリ]** ブレードで **[作成]** を選択します。

1. データ ファクトリは、Azure Portal のダッシュボードで作成されます。

1. データ ファクトリが正常に作成されると、データ ファクトリの内容を表示する **[データ ファクトリ]** ページが表示されます。

   ![[データ ファクトリ] ページ](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>手順 2:リンクされたサービスを作成します
リンクされたサービスは、データ ストアまたはコンピューティング サービスをデータ ファクトリにリンクします。 この手順では、ストレージ アカウントと Batch アカウントをデータ ファクトリにリンクします。

#### <a name="create-an-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
1. **[CustomActivityFactory]** の **[データ ファクトリ]** ブレードで、 **[作成およびデプロイ]** を選択します。 Data Factory エディターが表示されます。

1. コマンド バーの **[新しいデータ ストア]** を選択し、 **[Azure Storage]** を選択します。 Storage のリンクされたサービスを作成するときに使用する JSON スクリプトがエディターに表示されます。

   ![新しいデータ ストア](./media/data-factory-data-processing-using-batch/image7.png)

1. **accountname** をストレージ アカウントの名前に置き換えます。 **accountkey** をストレージ アカウントのアクセス キーに置き換えます。 ストレージ アクセス キーを取得する方法については、「[ストレージ アカウントのアクセス キーの管理](../../storage/common/storage-account-keys-manage.md)」を参照してください。

1. コマンド バーの **[デプロイ]** を選択して、リンクされたサービスをデプロイします。

   ![配置](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Azure Batch のリンクされたサービスの作成
この手順では、データ ファクトリのカスタム アクティビティの実行に使用される、Batch アカウント用にリンクされたサービスを作成します。

1. コマンド バーの **[新しい計算]** を選択し、 **[Azure Batch]** を選択します。 Batch のリンクされたサービスを作成するときに使用する JSON スクリプトがエディターに表示されます。

1. JSON スクリプト内では:

   a. **account name** を Batch アカウントの名前に置き換えます。

   b. **access key** を Batch アカウントのアクセス キーに置き換えます。

   c. **poolName** プロパティにプールの ID を入力します。 このプロパティでは、プール名またはプール ID のいずれかを指定できます。

   d. **batchUri** JSON プロパティにバッチ URI を入力します。

      > [!IMPORTANT]
      > **[Batch アカウント]** ブレードの URL は、`<accountname>.<region>.batch.azure.com` という形式です。 JSON スクリプトの `batchUri` プロパティでは、URL から `<accountname>.` を削除する必要があります。 たとえば `"batchUri": "https://eastus.batch.azure.com"` です。

      ![[Batch アカウント] ブレード](./media/data-factory-data-processing-using-batch/image9.png)

      **poolName** プロパティでは、プール名の代わりにプール ID を指定することもできます。

      > [!NOTE]
      > Data Factory サービスでは、HDInsight の場合と同様、Batch のオンデマンド オプションはサポートされません。 データ ファクトリ内の自分の Batch プールのみを使用できます。
      >
      >

   e. **linkedServiceName** プロパティに、**StorageLinkedService** を指定します。 このリンクされたサービスは、前の手順で作成しています。 このストレージは、ファイルおよびログのステージング領域として使用されます。

1. コマンド バーの **[デプロイ]** を選択して、リンクされたサービスをデプロイします。

#### <a name="step-3-create-datasets"></a>手順 3:データセットを作成する
この手順では、入力データと出力データを表すデータセットを作成します。

#### <a name="create-the-input-dataset"></a>入力データセットを作成する
1. Data Factory エディターで、ツールバーの **[新しいデータセット]** ボタンを選択します。 ドロップダウン リストから **[Azure BLOB ストレージ]** を選択します。

1. 右側のウィンドウの JSON スクリプトを、次の JSON スニペットに置き換えます。

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

    このチュートリアルでは、開始時刻が 2015-11-16T00:00:00Z、終了時刻が 2015-11-16T05:00:00Z のパイプラインを後ほど作成します。 データを毎時生成するようにスケジュールされているので、5 個の入力および出力スライスがあります (**00**:00:00 から\> **05**:00:00 まで)。

    入力データセットの **frequency** と **interval** はそれぞれ **Hour** と **1** に設定されています。つまり、1 時間に 1 つの入力スライスを利用できます。

    各スライスの開始時刻は、上記の JSON スニペットの **SliceStart** システム変数で表されます。 各スライスの開始時刻を次に示します。

    | **スライス** | **[開始時刻]**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T **00**:00:00 |
    | 2         | 2015-11-16T **01**:00:00 |
    | 3         | 2015-11-16T **02**:00:00 |
    | 4         | 2015-11-16T **03**:00:00 |
    | 5         | 2015-11-16T **04**:00:00 |

    **folderPath** は、スライス開始時間 (**SliceStart**) の年、月、日、時間の部分を使用して計算されます。 入力フォルダーをスライスにマップする方法を次に示します。

    | **スライス** | **[開始時刻]**          | **入力フォルダー**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T **00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T **01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T **02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T **03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T **04**:00:00 | 2015-11-16-**04** |

1. ツール バーの **[デプロイ]** を選択し、**InputDataset** テーブルを作成してデプロイします。

#### <a name="create-the-output-dataset"></a>出力データセットを作成する
この手順では、出力データを表す AzureBlob 型の別のデータセットを作成します。

1. Data Factory エディターで、ツールバーの **[新しいデータセット]** ボタンを選択します。 ドロップダウン リストから **[Azure BLOB ストレージ]** を選択します。

1. 右側のウィンドウの JSON スクリプトを、次の JSON スニペットに置き換えます。

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

    各入力スライスの出力 BLOB/ファイルが生成されます。 次に、各スライスの出力ファイルの命名方法を示します。 すべての出力ファイルは、`mycontainer\\outputfolder` という 1 つの出力フォルダーに生成されます。

    | **スライス** | **[開始時刻]**          | **[出力ファイル]**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T **00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T **01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T **02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T **03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T **04**:00:00 | 2015-11-16-**04.txt** |

    入力フォルダー (例: 2015-11-16-00) 内のすべてのファイルは、開始時刻が 2015-11-16-00 であるスライスの一部です。 このスライスを処理すると、カスタム アクティビティは各ファイルをスキャンし、出力ファイルに、検索語句 ("Microsoft") の出現回数が記述された 1 行を生成します。 2015-11-16-00 フォルダーに 3 つのファイルがある場合は、出力ファイル (2015-11-16-00.txt) に 3 行が出力されます。

1. ツール バーの **[デプロイ]** をクリックし、**OutputDataset** を作成してデプロイします。

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>手順 4:カスタム アクティビティを使用して、パイプラインを作成して実行する
この手順では、以前に作成したカスタム アクティビティである、1 つのアクティビティでパイプラインを作成します。

> [!IMPORTANT]
> BLOB コンテナー内の入力フォルダーに **file.txt** をまだアップロードしていない場合は、パイプラインを作成する前に行ってください。 **isPaused** プロパティがパイプラインの JSON で false に設定されているため、パイプラインは **start** の日を過ぎているので、すぐに実行されます。
>
>

1. Data Factory エディターで、コマンド バーの **[新しいパイプライン]** を選択します。 コマンドが表示されない場合は、省略記号 (...) を選択して表示します。

1. 右側のウィンドウの JSON スクリプトを、次の JSON スニペットに置き換えます。

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
   以下の点に注意してください。

   * パイプラインには 1 つのアクティビティのみがあり、種類は **DotNetActivity** です。
   * **AssemblyName** を DLL の名前 (**MyDotNetActivity.dll**) に設定します。
   * **EntryPoint** を **MyDotNetActivityNS.MyDotNetActivity** に設定します。 これは基本的に \<namespace\>.\<classname\> のようにコード内で示されます。
   * **PackageLinkedService** は **StorageLinkedService** に設定されます。これは、カスタム アクティビティの zip ファイルを含む Blob Storage を示します。 入力/出力ファイルとカスタム アクティビティ zip ファイルに別のストレージ アカウントを使用している場合、Storage のリンクされたサービスを別に作成する必要があります。 この記事では、同じストレージ アカウントを使用している前提で説明します。
   * **PackageFile** を **customactivitycontainer/MyDotNetActivity.zip** に設定します。 これは \<containerforthezip\>/\<nameofthezip.zip\> という形式になっています。
   * カスタム アクティビティは入力として **InputDataset**、出力として **OutputDataset** を使用します。
   * カスタム アクティビティの **linkedServiceName** プロパティは、**AzureBatchLinkedService** を示します。これによって、Batch でカスタム アクティビティが実行する必要がある Data Factory がわかります。
   * **concurrency** の設定は重要です。 既定値を使用する場合は、Batch プールにコンピューティング ノードが複数ある場合でも、スライスは 1 つずつ処理されます。 そのため、Batch の並行処理機能を利用することはありません。 **concurrency** をより大きな値に設定した場合、2 とすると、つまり 2 つのスライス (Batch 内の 2 つのタスクに対応する) が、同時に処理できます。 この場合、Batch プール内の VM が両方利用されます。 concurrency プロパティを適切に設定します。
   * 既定では、1 つのみのタスク (スライス) が、VM 上で任意のポイントで実行されます。 Batch プールの既定では、 **[VM ごとの最大タスク]** は 1 に設定されています。 前提条件の一環で、このプロパティを 2 に設定してプールを作成しました。 そのため、VM 上で 2 つのデータ ファクトリ スライスを同時に実行できます。
     - **isPaused** プロパティは、既定で false に設定されています。 この例では、スライスが過去に開始されているので、パイプラインは即時に実行されます。 このプロパティを **true** に設定すると、パイプラインを一時停止できます。また **false** に設定し直すと再開されます。
     -   **start** 時刻と **end** 時刻は 5 時間離れています。 スライスは毎時生成されるため、パイプラインによって 5 つのスライスが生成されます。

1. コマンド バーの **[デプロイ]** を選択して、パイプラインをデプロイします。

#### <a name="step-5-test-the-pipeline"></a>手順 5:パイプラインをテストする
この手順では、ファイルを入力フォルダーにドロップして、パイプラインをテストします。 まず、入力フォルダーごとに 1 つのファイルを使用してパイプラインをテストします。

1. Azure Portal の **[データ ファクトリ]** ブレードで、 **[ダイアグラム]** を選択します。

   ![ダイアグラム](./media/data-factory-data-processing-using-batch/image10.png)

1. **[ダイアグラム]** ビューで、 **[InputDataset]** という入力データセットをダブルクリックします。

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. 5 個のスライスがすべて準備完了の **[InputDataset]** ブレードが表示されます。 各スライスの **[スライス開始時間]** と **[スライス終了時間]** に注意してください。

   ![入力スライスの開始時刻と終了時刻](./media/data-factory-data-processing-using-batch/image12.png)

1. **[ダイアグラム]** ビューで、 **[OutputDataset]** を選択します。

1. 出力スライスが作成された場合、5 個の出力スライスが **準備完了** の状態で表示されます。

   ![出力スライスの開始時刻と終了時刻](./media/data-factory-data-processing-using-batch/image13.png)

1. ポータルを使用して、スライスに関連付けられているタスクを表示し、各スライスが実行された VM を確認します。 詳細については、「[Data Factory と Batch の統合](#data-factory-and-batch-integration)」を参照してください。

1. 出力ファイルは、BLOB ストレージの `outputfolder` の `mycontainer` 以下に表示されます。

   ![ストレージの出力ファイル](./media/data-factory-data-processing-using-batch/image15.png)

   5 個の出力ファイルが表示されます (各入力スライスに 1 つずつ)。 出力ファイルごとに、次の出力のようなコンテンツがあります。

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   次のダイアグラムでは、データ ファクトリのスライスを Batch のタスクにマップする方法を示します。 この例では、スライスごとに 1 つだけを実行します。

   ![スライス マッピングのダイアグラム](./media/data-factory-data-processing-using-batch/image16.png)

1. フォルダー内の複数のファイルを試してみましょう。 フォルダー **2015-11-06-01** にある file.txt と同じ内容で、**file2.txt**、**file3.txt**、**file4.txt**、および **file5.txt** のファイルを作成します。

1. 出力フォルダーで、**2015-11-16-01.txt** という出力ファイルを削除します。

1. **[OutputDataset]** ブレードで、 **[スライス開始時間]** を **11/16/2015 01:00:00 AM** に設定したスライスを右クリックします。 **[実行]** を選択し、スライスを再実行または再度処理します。 スライスには、1 個のファイルではなく、5 個のファイルがあります。

    ![ラン](./media/data-factory-data-processing-using-batch/image17.png)

1. スライスを実行して、その状態が **[準備完了]** になると、このスライスの出力ファイル (**2015-11-16-01.txt**) の内容を確認します。 この出力ファイルは、BLOB ストレージの `outputfolder` 内の `mycontainer` 以下に表示されます。 スライスのファイルごとに 1 行あります。

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> 5 個の入力ファイルを試す前に、出力ファイル 2015-11-16-01.txt を削除しなかった場合は、以前に実行したスライスから 1 行と、現在実行しているスライスから 5 行表示されます。 既定では、コンテンツが既に存在している場合、出力ファイルに追加されます。
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory と Batch の統合
Data Factory サービスによって、Batch に `adf-poolname:job-xxx` という名前のジョブが作成されます。

![Batch ジョブ](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

スライスのアクティビティの実行ごとに、1 つのタスクがジョブに作成されます。 10 個のスライスを処理する準備ができたとき、ジョブには 10 個のタスクが作成されています。 プール内に複数のコンピューティング ノードがある場合、複数のスライスを並列して実行することができます。 コンピューティング ノードあたりのタスクの最大数が 1 より大きい場合、複数のスライスを同じコンピューティングで実行できます。

この例では 5 個のスライスがあるため、Batch には 5 個のタスクがあります。 データ ファクトリのパイプライン JSON の **concurrency** を **5** に設定し、**2** 個の VM が含まれた Batch プールの **[VM ごとの最大タスク]** を **2** に設定すると、タスクが高速で実行されるようになります (タスクの開始時刻と終了時刻を確認してください)。

ポータルを使用して、スライスに関連付けられている Batch ジョブとそのタスクを表示し、各スライスが実行された VM を確認します。

![Batch ジョブ タスク](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>パイプラインのデバッグ
デバッグには、いくつかの基本的な技術があります。

1. 入力スライスが **[準備完了]** に設定されていない場合、入力フォルダー構造が正しく、入力フォルダーに file.txt が存在することを確認します。

   ![入力フォルダーの構造](./media/data-factory-data-processing-using-batch/image3.png)

1. 問題のトラブルシューティングに役立つ情報をログに記録するには、カスタム アクティビティの **Execute** メソッドで、**IActivityLogger** オブジェクトを使用します。 ログに記録されたメッセージは user\_0.log ファイルに表示されます。

   **[OutputDataset]** ブレードで、スライスを選択すると、そのスライスの **[データ スライス]** ブレードが表示されます。 **[アクティビティの実行]** には、スライスの 1 回のアクティビティの実行が表示されます。 コマンド バーの **[実行]** を選択する場合、同じスライスの別のアクティビティの実行を開始できます。

   アクティビティの実行を選択すると、 **[アクティビティの実行の詳細]** ブレードにログ ファイルの一覧が表示されます。 user\_0.log ファイルにログに記録されたメッセージが表示されます。 パイプライン/アクティビティ JSON で再試行回数が 3 に設定されているので、エラーが発生した場合、3 つのアクティビティの実行が表示されます。 アクティビティの実行を選択すると、ログ ファイルが表示されます。このファイルを確認して、エラーのトラブルシューティングを行うことができます。

   ![[OutputDataset] および [データ スライス] ブレード](./media/data-factory-data-processing-using-batch/image18.png)

   ログ ファイルの一覧で、**user-0.log** を選択します。 右側のパネルには、**IActivityLogger.Write** メソッドの使用結果が表示されます。

   ![[アクティビティの実行の詳細] ブレード](./media/data-factory-data-processing-using-batch/image19.png)

   system-0.log では、システム エラー メッセージと例外を確認できます。

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. エラーの詳細にエラー発生時のコール スタックなどの情報が含まれるようにするには、zip ファイルに **PDB** ファイルを含めます。

1. カスタム アクティビティの zip ファイルのすべてのファイルは、サブフォルダーがないトップ レベルに置く必要があります。

   ![カスタム アクティビティの zip ファイル一覧](./media/data-factory-data-processing-using-batch/image20.png)

1. **assemblyName** (MyDotNetActivity.dll)、**entryPoint**(MyDotNetActivityNS.MyDotNetActivity)、**packageFile** (customactivitycontainer/MyDotNetActivity.zip)、**packageLinkedService** (zip ファイルを含む BLOB ストレージを示す必要があります) が正しい値に設定されていることを確認します。

1. エラーを修正し、スライスを再処理する場合は、 **[OutputDataset]** ブレードのスライスを右クリックし、 **[実行]** を選択します。

   ![[OutputDataset] ブレードの [実行] オプション](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > コンテナーは、`adfjobs` という BLOB ストレージ内にあります。 このコンテナーは自動的に削除されませんが、ソリューションのテストを完了した後に、安全に削除することができます。 同様に、データ ファクトリ ソリューションでは、`adf-\<pool ID/name\>:job-0000000001` という名前の Batch ジョブを作成します。 必要な場合は、ソリューションをテストした後、このジョブを削除することができます。
   >
   >
1. このカスタム アクティビティでは、パッケージ内の **app.config** ファイルは使用されません。 そのためこの構成ファイルから接続文字列を読み取るようにコードを記述した場合、実行時に正しく機能しません。 Batch を使用する場合のベスト プラクティスは、Azure Key Vault のシークレットを保持することです。 次に、証明書ベースのサービス プリンシパルを使用してキー コンテナーを保護し、証明書を Batch プールに配布します。 こうすることで .NET カスタム アクティビティが実行時にキー コンテナー内のシークレットにアクセスすることができます。 これは一般的な手法であり、接続文字列に限らず、あらゆる種類のシークレットに応用できます。

    さらに簡単な回避策もありますが、ベスト プラクティスではありません。 接続文字列の設定を使用して、SQL Database にリンクされたサービスを作成できます。 これで、リンクされたサービスを使用するデータセットを作成し、データセットをダミーの入力データセットとしてカスタム .NET アクティビティに連結できます。 その後は、カスタム アクティビティのコードで、リンクされたサービスの接続文字列にアクセスできます。 これは実行時に問題なく動作します。  

#### <a name="extend-the-sample"></a>サンプルの拡張
Data Factory および Batch の機能の詳細については、このサンプルを拡張することができます。 たとえば、異なる時間範囲でスライスを処理するには、次の手順を実行します。

1. `inputfolder` にサブフォルダー2015-11-16-05、2015-11-16-06、201-11-16-07、2011-11-16-08、および 2015-11-16-09 を追加します。 これらのフォルダーに入力ファイルを配置します。 パイプラインの終了時刻を `2015-11-16T05:00:00Z` から `2015-11-16T10:00:00Z` に変更します。 **[ダイアグラム]** ビューで、 **[InputDataset]** をダブルクリックして、入力スライスが準備完了であることを確認します。 **[OutputDataset]** をダブルクリックして、出力スライスの状態を表示します。 **準備完了** の状態である場合、出力フォルダーで出力ファイルを確認します。

1. 特に Batch 上で発生する処理のように、どのようにソリューションのパフォーマンスに影響するかを理解するには、**concurrency** の設定を増加または減少させます。 **concurrency** 設定の詳細については、「手順 4:カスタム アクティビティを使用して、パイプラインを作成して実行する」を参照してください。

1. **[VM ごとの最大タスク]** を高くまたは低くして、プールを作成します。 新しく作成したプールを使用するには、データ ファクトリ ソリューションで、Batch のリンクされたサービスを更新します。 **[VM ごとの最大タスク]** 設定の詳細については、「手順 4:カスタム アクティビティを使用して、パイプラインを作成して実行する」を参照してください。

1. **[自動スケール]** 機能で、Batch プールを作成します。 Batch プール内のコンピューティング ノードの自動スケールは、アプリケーションによって使用される処理能力を動的に調整します。

    このサンプル式は、次の動作を実現します。 プールが最初に作成された場合、1 つの VM から開始されます。 $PendingTasks メトリックにより、実行中とアクティブ (キューに登録済み) 状態のタスク数が定義されます。 この数式により、最後の 180 秒間の保留タスク平均数が判明し、TargetDedicated が適宜設定されます。 それにより、TargetDedicated が 25 台の仮想マシンを超えることはありません。 新しいタスクが送信されると、プールは自動的に拡大します。 タスクが完了すると、VM が 1 台ずつ解放され、自動スケーリングによって VM は縮小します。 startingNumberOfVMs と maxNumberofVMs はニーズに合わせて調整できます。

    自動スケールの数式:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   詳細については、[Batch プール内のコンピューティング ノードの自動スケール](../../batch/batch-automatic-scaling.md)に関するページを参照してください。

   プールで既定の [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale) を使用する場合、Batch サービスがカスタム アクティビティを実行する前に VM を準備するのに 15 ～ 30 分かかることがあります。 プールが異なる autoScaleEvaluationInterval を使用する場合、Batch サービスは autoScaleEvaluationInterval + 10 分を要することがあります。

1. サンプル ソリューションで、**Execute** メソッドは、出力データ スライスを生成するために入力データ スライスを処理する、**Calculate** メソッドを呼び出します。 入力データを処理し、**Execute** メソッドで呼び出される **Calculate** メソッドを、メソッドの呼び出しに置換する独自のメソッドを記述することができます。

### <a name="next-steps-consume-the-data"></a>次のステップ:データの処理
データを処理した後、Power BI などのオンライン ツールで使用することができます。 Power BI や Azure で使用する方法を理解するために役立つリンクを次に示します。

* [Power BI でのデータセットの参照](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Power BI Desktop の概要](/power-bi/fundamentals/desktop-getting-started)
* [Power BI でのデータの更新](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure と Power BI:基本的な概要](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>References
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Azure Data Factory サービスの概要](data-factory-introduction.md)
  * [Data Factory の概要](data-factory-build-your-first-pipeline.md)
  * [カスタム アクティビティを Data Factory パイプラインで使用する](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Batch の基本](../../azure-sql/database/sql-database-paas-overview.md)
  * [Azure Batch 機能の概要](../../batch/batch-service-workflow-features.md)
  * [Azure Portal で Batch アカウントを作成して管理する](../../batch/batch-account-create-portal.md)
  * [.NET 向け Batch クライアント ライブラリの概要](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: /archive/blogs/windowshpc/azure-batch-explorer-sample-walkthrough