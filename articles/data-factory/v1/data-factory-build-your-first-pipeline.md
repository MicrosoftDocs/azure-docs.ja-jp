---
title: 'Data Factory のチュートリアル: 最初のデータ パイプライン '
description: この Azure Data Factory のチュートリアルでは、Hadoop クラスターで Hive スクリプトを使用してデータを処理するデータ ファクトリを作成およびスケジュールする方法を示します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: e7114dae2a9cfef4a9b710831beb63a65c862643
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785378"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>チュートリアル:Hadoop クラスターを使用してデータを変換する初めてのパイプラインを作成する
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager テンプレート](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Azure Data Factory を使用してデータ ファクトリを作成する方法のクイック スタート](../quickstart-create-data-factory-dot-net.md)に関するページを参照してください。

このチュートリアルでは、データ パイプラインを備えた最初の Azure Data Factory を作成します。 パイプラインによって、Azure HDInsight (Hadoop) クラスターで Hive スクリプトを実行して出力データを生成することで、入力データを変換します。  

この記事では、チュートリアルの概要と前提条件について説明します。 前提条件を満たせたら、Visual Studio、PowerShell、Resource Manager テンプレート、REST API のいずれかのツールまたは SDK を使用してチュートリアルを実行できます。 この記事の最初にあるドロップダウン リストのオプションの 1 つ、または最後にあるリンクの 1 つを選択してチュートリアルを実行します。    

## <a name="tutorial-overview"></a>チュートリアルの概要
このチュートリアルでは、以下の手順を実行します。

1. **Data Factory** を作成します。 データ ファクトリには、データを移動および変換するデータ パイプラインを 1 つ以上含めることができます。

    このチュートリアルでは、データ ファクトリ内にパイプラインを 1 つ作成します。
2. **パイプライン** を作成する。 パイプラインには、1 つまたは複数のアクティビティを含めることができます (例: コピー アクティビティ、HDInsight Hive アクティビティ)。 このサンプルでは、HDInsight Hadoop クラスターで Hive スクリプトを実行する HDInsight Hive アクティビティを使用します。 このスクリプトでは、まず Azure BLOB ストレージに格納されている生の Web ログ データを参照するテーブルを作成し、その後、年月別に生データを分割します。

    このチュートリアルでは、パイプラインで Hive アクティビティを使用して、Azure HDInsight Hadoop クラスターで Hive クエリを実行することでデータを変換します。
3. **リンクされたサービス** を作成します。 データ ストアまたはコンピューティング サービスをデータ ファクトリにリンクする、リンクされたサービスを作成します。 Azure Storage などのデータ ストアには、パイプラインのアクティビティの入力データや出力データが保持されます。 HDInsight Hadoop クラスターなどのコンピューティング サービスがデータを処理または変換します。

    このチュートリアルでは、2 つのリンクされたサービスを作成します (**Azure Storage**、**Azure HDInsight**)。 Azure Storage のリンクされたサービスでは、入出力データを保持する Azure ストレージ アカウントをデータ ファクトリにリンクします。 Azure HDInsight のリンクされたサービスでは、データの変換に使用する Azure HDInsight クラスターをデータ ファクトリにリンクします。
3. 入力 **データセット** と出力データセットを作成する。 入力データセットはパイプラインのアクティビティの入力を表し、出力データセットはアクティビティの出力を表します。

    このチュートリアルでは、入力データセットと出力データセットで Azure Blob Storage の入力データと出力データの場所を指定します。 Azure Storage のリンクされたサービスで、どの Azure ストレージ アカウントを使用するかを指定します。 入力データセットで、入力ファイルを配置する場所を指定し、出力データセットで、出力ファイルを配置する場所を指定します。


Azure Data Factory の詳細については、[Azure Data Factory の概要](data-factory-introduction.md)に関する記事をご覧ください。

次に示すのは、このチュートリアルで構築するサンプル データ ファクトリの **ダイアグラム ビュー** です。 **MyFirstPipeline** には Hive 型の 1 つのアクティビティがあり、**AzureBlobInput** データセットを入力として使用し、**AzureBlobOutput** データセットを出力として生成します。

![Diagram view in Data Factory tutorial](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


このチュートリアルでは、**adfgetstarted** Azure BLOB コンテナーの **inputdata** フォルダーに、input.log という名前のファイルが 1 つ含まれています。 このログ ファイルには、2016 年の 1 月、2 月、および 3 月の 3 か月間のエントリが含まれています。 入力ファイル内の各月のサンプル行を次に示します。

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

HDInsight Hive アクティビティを含むパイプラインによってファイルが処理されると、アクティビティによって HDInsight クラスターで Hive スクリプトが実行され、入力データが年月別に分割されます。 このスクリプトでは、各月のエントリが含まれているファイルを格納した 3 つの出力フォルダーが作成されます。  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

上記のサンプル行の最初の行 (2016-01-01) は、month=1 フォルダーの 000000_0 ファイルに書き込まれます。 同様に、2 行目は month=2 フォルダーのファイルに書き込まれ、3 行目は month=3 フォルダーのファイルに書き込まれます。  

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、以下の前提条件を満たしている必要があります。

1. **Azure サブスクリプション** - Azure サブスクリプションがない場合は、無料試用版アカウントを数分で作成することができます。 無料試用版アカウントの取得方法については、「 [無料試用版](https://azure.microsoft.com/pricing/free-trial/) 」を参照してください。
2. **Azure Storage** – このチュートリアルのデータは、Azure ストレージ アカウントを使用して格納します。 Azure ストレージ アカウントがない場合は、「 [ストレージ アカウントの作成](../../storage/common/storage-account-create.md) 」を参照してください。 ストレージ アカウントを作成したら、**アカウント名** と **アクセス キー** をメモしておきます。 ストレージアカウントのアクセスキーを取得する方法については、「[ストレージアカウントのアクセスキーを管理する](../../storage/common/storage-account-keys-manage.md)」 を参照してください。
3. [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql) にある Hive クエリ ファイル (**HQL**) をダウンロードして確認します。 このクエリが、入力データを変換して出力データを生成します。
4. [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log) にあるサンプルの入力ファイル (**input.log**) をダウンロードして確認します。
5. Azure Blob Storage に **adfgetstarted** という名前の BLOB コンテナーを作成します。
6. **partitionweblogs.hql** ファイルを **adfgetstarted** コンテナーの **script** フォルダーにアップロードします。 [Microsoft Azure Storage Explorer](https://storageexplorer.com/)などのツールを使用します。
7. **adfgetstarted** コンテナーの **inputdata** フォルダーに **input.log** ファイルをアップロードします。

前提条件を満たせたら、チュートリアルを実行するための次のいずれかのツールまたは SDK を選択します。

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager テンプレート](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio では、GUI を使用してデータ ファクトリを構築します。 一方、PowerShell、Resource Manager テンプレート、および REST API のオプションでは、スクリプトやプログラミングを使用してデータ ファクトリを構築します。

> [!NOTE]
> このチュートリアルのデータ パイプラインでは、入力データを変換して出力データを生成します。 データをソース データ ストアからターゲット データ ストアにコピーするのではありません。 Azure Data Factory を使用してデータをコピーする方法のチュートリアルについては、[Blob Storage から SQL Database にデータをコピーする方法のチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。
>
> 2 つのアクティビティを連鎖させる (アクティビティを連続的に実行する) には、一方のアクティビティの出力データセットを、もう一方のアクティビティの入力データセットとして指定します。 詳細については、[Data Factory でのスケジュールと実行](data-factory-scheduling-and-execution.md)に関するページを参照してください。
