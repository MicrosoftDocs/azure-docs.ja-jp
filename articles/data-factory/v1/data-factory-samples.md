---
title: Azure Data Factory - サンプル
description: Azure Data Factory サービスに付属するサンプルについて詳細に説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0d78cc9d99bd7ace2d214c2a2a1d7f75a74df098
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785055"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory - サンプル
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory の PowerShell サンプル](../samples-powershell.md)に関するページと [Azure コード サンプル ギャラリーのコード サンプル](https://azure.microsoft.com/resources/samples/?service=data-factory)をご覧ください。


## <a name="samples-on-github"></a>GitHub のサンプル
[GitHub の Azure-DataFactory リポジトリ](https://github.com/azure/azure-datafactory) には、Azure Data Factory サービスを迅速に導入sしたり、スクリプトを変更して独自のアプリケーションで使用したりするのに役立ついくつかのサンプルがあります。 Samples\JSON フォルダーには、一般的なシナリオ用の JSON スニペットが含まれています。

| サンプル | 説明 |
|:--- |:--- |
| [ADF チュートリアル](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |このサンプルでは、Azure Data Factory を使用したログ ファイルの処理によってログ ファイルのデータから知見を得る方法をエンドツーエンドでわかりやすく解説します。 <br/><br/>このチュートリアルでは、Data Factory パイプラインでサンプル ログを収集、処理します。ログから得たデータを参照データで補強し、そのデータを変換することによって、最近開始されたマーケティング キャンペーンの有効性を評価します。 |
| [JSON のサンプル](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |一般的なシナリオにおける JSON の使用例を紹介したサンプルです。 |
| [Http Data Downloader サンプル](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |カスタム .NET アクティビティを使用して HTTP エンドポイントから Azure Blob Storage にデータをダウンロードするサンプルです。 |
| [Cross AppDomain Dot Net Activity サンプル](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |このサンプルでは、ADF ランチャーで使用されているアセンブリ バージョン (WindowsAzure.Storage v4.3.0、Newtonsoft.Json v6.0.x など) に限定されないカスタム .NET アクティビティを作成することができます。 |
| [R スクリプトの実行](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |RScript.exe の呼び出しに使用できる Data Factory カスタム アクティビティが含まれています。 このサンプルは、既に R がインストールされている独自の (オンデマンドではない) HDInsight クラスターでのみ正しく動作します。 |
| [HDInsight Hadoop クラスターでの Spark ジョブの呼び出し](../tutorial-transform-data-spark-portal.md) |MapReduce アクティビティを使用して Spark プログラムを起動する方法を紹介するサンプルです。 Spark プログラムは、単に、1 つの Azure BLOB コンテナーから別のコンテナーにデータをコピーします。 |
| [Azure Machine Learning スタジオ (クラシック) バッチ スコアリング アクティビティを使用した Twitter 分析](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Twitter のセンチメント分析、スコア付け、予測などを実行する Azure Machine Learning モデルを AzureMLBatchScoringActivity で呼び出す方法について紹介したサンプルです。 |
| [カスタム アクティビティを使用した Twitter 分析](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Twitter のセンチメント分析、スコア付け、予測などを実行する Azure Machine Learning モデルをカスタム .NET アクティビティで呼び出す方法について紹介したサンプルです。 |
| [Azure Machine Learning のパラメーター化パイプライン](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |それぞれ異なるリージョン パラメーターを使ってスコア付けと再トレーニングを行う N 個のパイプラインをデプロイするエンドツーエンドの C# コードを紹介したサンプルです。一連のリージョンは、このサンプルに含まれている parameters.txt ファイルから取得します。 |
| [Azure Stream Analytics ジョブのリファレンス データの更新](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Azure Data Factory と Azure Stream Analytics を連携させ、リファレンス データを使ってクエリを実行したり、リファレンス データの定期的更新をセットアップしたりする方法を紹介するサンプルです。 |
| [オンプレミスの Hortonworks Hadoop を使用したハイブリッド パイプライン](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |Data Factory でジョブを実行するためのコンピューティング ターゲットとしてオンプレミスの Hadoop クラスターを使用するサンプルです。HDInsight ベースの Hadoop クラスターなど、他のコンピューティング ターゲットをクラウドに追加する感覚でオンプレミスの Hadoop クラスターを使用します。 |
| [JSON 変換ツール](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |2015-07-01-preview 未満のバージョンの JSON を最新または 2015-07-01-preview (既定) に変換するツールです。 |
| [U-SQL サンプル入力ファイル](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |このファイルは、U-SQL アクティビティで使用するサンプル ファイルです。 |
| [BLOB ファイルの削除](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | このサンプルは、ファイルがコピーされたらソースの Azure BLOB の場所からファイルを削除するために ADF カスタム .net アクティビティの一部として使用できる #C ファイルを示します。|

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート
GitHub 上に、Data Factory 向けの次の Azure Resource Manager テンプレートがあります。

| Template | 説明 |
| --- | --- |
| [Azure Blob Storage から Azure SQL Database にコピーする](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |このテンプレートをデプロイすると、指定した Azure の BLOB ストレージから Azure SQL Database にデータをコピーするパイプラインを持つ Azure データ ファクトリが作成されます |
| [Salesforce から Azure Blob Storage にコピーする](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |このテンプレートをデプロイすると、指定した Salesforce アカウントから Azure Blob Storage にデータをコピーするパイプラインを持つ Azure データ ファクトリが作成されます。 |
| [Azure HDInsight クラスターで Hive スクリプトを実行してデータを変換する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |このテンプレートをデプロイすると、Azure HDInsight Hadoop クラスター上でサンプル Hive スクリプトを実行することによりデータ変換を行うパイプラインを持つ Azure データ ファクトリが作成されます。 |

## <a name="samples-in-azure-portal"></a>Azure Portal のサンプル
データ ファクトリのホーム ページにある **[サンプル パイプライン]** タイルを使用して、パイプラインのサンプルおよび関連付けられているエンティティ (データセットおよびリンクされたサービス) をデータ ファクトリにデプロイできます。

1. データ ファクトリを作成するか、既存のデータ ファクトリを開きます。 データ ファクトリを作成する手順については、「[Data Factory を使用した Blob Storage から SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)」を参照してください。
2. データ ファクトリの **[Data Factory]** ブレードで、 **[サンプル パイプライン]** タイルをクリックします。

    ![サンプル パイプライン タイル](./media/data-factory-samples/SamplePipelinesTile.png)
3. **[サンプル パイプライン]** ブレードで、デプロイする **サンプル** をクリックします。

    ![サンプル パイプライン ブレード](./media/data-factory-samples/SampleTile.png)
4. このサンプルの構成設定を指定します。 たとえば、Azure ストレージ アカウント名とアカウント キー、論理 SQL サーバーの名前、データベース、ユーザー ID、パスワードなどです。

    ![サンプル ブレード](./media/data-factory-samples/SampleBlade.png)
5. 構成設定の指定が完了したら **[作成]** をクリックして、サンプルのパイプラインと、そのパイプラインで使用するリンクされたサービスとテーブルを作成またはデプロイします。
6. 先程 **[サンプル パイプライン]** ブレードでクリックしたサンプルのタイルに、デプロイの状態が表示されます。

    ![[デプロイ ステータス]](./media/data-factory-samples/DeploymentStatus.png)
7. サンプルのタイルに "**デプロイに成功しました**" メッセージが表示されたら、 **[サンプル パイプライン]** ブレードを閉じます。  
8. **[Data Factory]** ブレードで、リンクされたサービス、データ セット、パイプラインがデータ ファクトリに追加されたことを確認します。  

    ![[Data Factory] ブレード](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Visual Studio のサンプル
### <a name="prerequisites"></a>前提条件
コンピューターに以下がインストールされている必要があります。

* Visual Studio 2013 または Visual Studio 2015
* Azure SDK for Visual Studio 2013 または Visual Studio 2015 をダウンロードします。 [Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)に移動し、 **.NET** セクションの **[VS 2013]** または **[VS 2015]** をクリックします。
* Visual Studio 用の最新の Azure Data Factory プラグイン ([VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) または [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)) をダウンロードします。 Visual Studio 2013 を使用している場合は、次の手順を実行してプラグインを更新することもできます。メニューで **[ツール]**  ->  **[拡張機能と更新プログラム]**  ->  **[オンライン]**  ->  **[Visual Studio ギャラリー]**  ->  **[Microsoft Azure Data Factory Tools for Visual Studio]**  ->  **[更新]** の順にクリックします。

### <a name="use-data-factory-templates"></a>Data Factory テンプレートの使用
1. メニューの **[ファイル]** をクリックし、 **[新規作成]** をポイントして、 **[プロジェクト]** をクリックします。
2. **[新しいプロジェクト]** ダイアログ ボックスで、次の操作を行います。

   1. **[テンプレート]** で **[DataFactory]** を選択します。
   2. 右側のウィンドウで **[Data Factory テンプレート]** を選択します。
   3. プロジェクトの **名前** を入力します。
   4. プロジェクトの **場所** を選択します。
   5. **[OK]** をクリックします。

      ![[新しいプロジェクト] ダイアログ ボックス](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. **[Data Factory Templates]** (Data Factory テンプレート) ダイアログ ボックスで、 **[Use-Case Templates]** (ユースケース テンプレート) セクションからサンプル テンプレートを選択し、 **[次へ]** をクリックします。 この後の手順では、 **顧客プロファイリング** テンプレートの使用方法について説明します。 他のサンプルでも手順は同じです。

    ![Data Factory Templates dialog box](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. **[Data Factory Configuration]** (Data Factory の構成) ダイアログの **[Data Factory Basics]** (Data Factory の基本) ページで **[次へ]** をクリックします。
5. **[Configure data factory]** (データ ファクトリの構成) ページで、次の手順を行います。
   1. **[Create New Data Factory]** (Data Factory の新規作成) を選択します。 **[既存のデータ ファクトリを使用する]** を選択することもできます。
   2. データ ファクトリの **名前** を入力します。
   3. データ ファクトリを作成する **Azure サブスクリプション** を選択します。
   4. データ ファクトリの **リソース グループ** を選択します。
   5. **リージョン** として **[米国西部]** 、 **[米国東部]** 、または **[北ヨーロッパ]** を選択します。
   6. **[次へ]** をクリックします。
6. **[Configure data stores] (データ ストアの構成)** ページで、既存の **Azure SQL Database のデータベース** と **Azure ストレージ アカウント** を指定するか、データベースまたはストレージを作成して、[次へ] をクリックします。
7. **[コンピューティングの構成]** ページで、既定値を選択し、 **[次へ]** をクリックします。
8. **[概要]** ページで、すべての設定を確認し、 **[次へ]** をクリックします。
9. **[Deployment Status]** (デプロイ ステータス) ページで、デプロイが完了するまで待ってから **[完了]** をクリックします。
10. ソリューション エクスプローラーでプロジェクトを右クリックし、 **[発行]** をクリックします。
11. **[Microsoft アカウントへのサインイン]** ダイアログ ボックスが表示されたら、Azure サブスクリプションを所有するアカウントの資格情報を入力し、 **[サインイン]** をクリックします。
12. 次のダイアログ ボックスが表示されます。

    ![[発行] ダイアログ ボックス](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. **[Configure data factory]** (データ ファクトリの構成) ページで、次の手順を行います。

    1. **[既存のデータ ファクトリを使用する]** オプションが選択されていることを確認します。
    2. テンプレートを使用する際に選択した **データ ファクトリ** を選択します。
    3. **[次へ]** をクリックし、 **[項目の発行]** ページに切り替えます。 ( **[次へ]** ボタンが無効になっている場合は、**Tab** キーを押して [名前] フィールドの外に移動します。)
14. **[項目の発行]** ページで、すべての Data Factory エンティティが選択されていることを確認し、 **[次へ]** をクリックして **[概要]** ページに切り替えます。     
15. 概要を確認してから **[次へ]** をクリックし、デプロイ プロセスを開始して **[デプロイ ステータス]** を表示します。
16. **[デプロイ ステータス]** ページに、デプロイメント プロセスのステータスが表示されます。 デプロイメントが完了したら、[完了] をクリックします。

Visual Studio を使用して Data Factory エンティティを作成し、Azure に発行する方法の詳細については、「 [Visual Studio を使用した初めての Azure Data Factory パイプラインの作成](data-factory-build-your-first-pipeline-using-vs.md) 」を参照してください。