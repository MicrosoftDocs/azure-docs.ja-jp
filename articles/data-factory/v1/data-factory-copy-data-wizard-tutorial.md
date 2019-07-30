---
title: チュートリアル:コピー ウィザードを使用してパイプラインを作成する | Microsoft Docs
description: このチュートリアルでは、Data Factory でサポートされているコピー ウィザードを使用して、コピー アクティビティがある Azure Data Factory パイプラインを作成します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0d67e18182d44dc640c75d982ccb40f1d22f8b41
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836604"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>チュートリアル:コピー アクティビティがあるパイプラインを Data Factory コピー ウィザードで作成する
> [!div class="op_single_selector"]
> * [概要と前提条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [コピー ウィザード](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager テンプレート](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[コピー アクティビティのチュートリアル](../quickstart-create-data-factory-dot-net.md)に関するページを参照してください。 


このチュートリアルでは、**コピー ウィザード**を使用して、Azure Blob Storage から Azure SQL データベースにデータをコピーする方法を示します。 

Azure Data Factory の**コピー ウィザード**を使用すると、サポートされているソース データ ストアからサポートされているターゲット データ ストアにデータをコピーするデータ パイプラインを簡単に作成することができます。 そのため、データ移動のシナリオを想定したサンプル パイプラインを作成する場合は、まずこのウィザードを使用することをお勧めします。 コピー アクティビティのソースおよびターゲットとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するセクションを参照してください。  

このチュートリアルでは、Azure Data Factory を作成し、コピー ウィザードを起動して、一連の手順を実行する方法を紹介しながら、データの取り込み/移動のシナリオについて詳しく説明します。 ウィザードの手順が完了すると、Azure Blob Storage から Azure SQL データベースにデータを複製するコピー アクティビティを含んだパイプラインが自動的に作成されます。 コピー アクティビティの詳細については、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを実行する前に、 [チュートリアルの概要](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) に関する記事に記載されている前提条件を満たしてください。

## <a name="create-data-factory"></a>データ ファクトリの作成
この手順では、Azure ポータルを使用して、 **ADFTutorialDataFactory**という名前の Azure データ ファクトリを作成します。

1. [Azure Portal](https://portal.azure.com) にログインします。
2. 左上隅の **[リソースの作成]** 、 **[データ + 分析]** 、 **[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ブレードで以下の手順を実行します。
   
   1. **[名前]** に「**ADFTutorialDataFactory**」と入力します。
       Azure Data Factory の名前はグローバルに一意にする必要があります。 `Data factory name “ADFTutorialDataFactory” is not available` エラーが発生した場合は、データ ファクトリの名前を (yournameADFTutorialDataFactoryYYYYMMDD などに) 変更して作成し直してください。 Data Factory アーティファクトの名前付け規則については、 [Data Factory - 名前付け規則](data-factory-naming-rules.md) に関するトピックを参照してください。  
      
       ![使用できない Data Factory 名](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Azure **サブスクリプション**を選択します。
   3. リソース グループについて、次の手順のいずれかを行います。 
      
      - **[既存のものを使用]** を選択し、既存のリソース グループを選択します。
      - **[新規作成]** を選択し、リソース グループの名前を入力します。
          
        このチュートリアルの一部の手順は、**ADFTutorialResourceGroup** という名前のリソース グループを使用することを前提としています。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。
   4. データ ファクトリの**場所**を選択します。
   5. ブレードの一番下にある **[ダッシュボードにピン留めする]** チェック ボックスをオンにします。  
   6. **Create** をクリックしてください。
      
       ![[新しいデータ ファクトリ] ブレード](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. 作成が完了すると、次の図に示すような **[Data Factory]** ブレードが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>コピー ウィザードの起動
1. [Data Factory] ブレードで **[データのコピー]** タイルをクリックして、**コピー ウィザード**を起動します。 
   
   > [!NOTE]
   > 承認の処理が進行中であることを示すメッセージが表示されたまま Web ブラウザーが停止してしまう場合は、ブラウザーで**サード パーティの Cookie とサイト データをブロック**する設定を無効にしてください。または、有効な状態のまま **login.microsoftonline.com** に対する例外を作成し、そのうえで、もう一度ウィザードを起動してください。
2. **[プロパティ]** ページで次の操作を実行します。
   
   1. **[タスク名]** に「**CopyFromBlobToAzureSql**」と入力します。
   2. **説明** を入力します (省略可能)。
   3. **[Start date time]\(開始日時\)** と **[End date time]\(終了日時\)** を変更します。終了日は今日の日付に、開始日は 5 日前の日付に設定してください。  
   4. **[次へ]** をクリックします。  
      
      ![Copy Tool - Properties page](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. **[Source data store (ソース データ ストア)]** ページで、 **[Azure Blob Storage]** タイルをクリックします。 このページを使用して、コピー タスクのソース データ ストアを指定します。 
   
    ![Copy Tool - Source data store page](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. **[Specify the Azure Blob storage account (Azure BLOB ストレージ アカウントの指定)]** ページで次の操作を実行します。
   
   1. **[リンクされたサービス名]** に「**AzureStorageLinkedService**」と入力します。
   2. **[Account selection method (アカウントの選択方法)]** で **[From Azure subscriptions (Azure サブスクリプションから)]** オプションが選択されていることを確認します。
   3. Azure **サブスクリプション**を選択します。  
   4. 選択したサブスクリプションで利用できる Azure ストレージ アカウントの一覧から、使用する **Azure ストレージ アカウント**を選択します。 ストレージ アカウント設定を手動で入力することもできます。その場合は、 **[Account selection method (アカウントの選択方法)]** で **[Enter manually (手動で入力)]** オプションを選択し、 **[次へ]** をクリックします。 
      
      ![Copy Tool - Specify the Azure Blob storage account](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. **[Choose the input file or folder (入力ファイルまたはフォルダーの選択)]** ページで次の操作を実行します。
   
   1. **[adftutorial]** (フォルダー) をダブルクリックします。
   2. **emp.txt** を選択し、 **[選択]** をクリックします。
      
      ![Copy Tool - Choose the input file or folder](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. **[Choose the input file or folder (入力ファイルまたはフォルダーの選択)]** ページで、 **[次へ]** をクリックします。 **[Binary copy (バイナリ コピー)]** は選択しないでください。 
   
    ![Copy Tool - Choose the input file or folder](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. **[File format settings (ファイル形式の設定)]** ページに、ウィザードがファイルを解析することによって自動的に検出した区切り記号とスキーマが表示されます。 区切り記号を手動で入力することで、コピー ウィザードによる自動検出を防止 (オーバーライド) することもできます。 区切り記号を確認し、データをプレビューしたら、 **[次へ]** をクリックします。 
   
    ![Copy Tool - File format settings](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. [Destination data store (ターゲット データ ストア)] ページで **[Azure SQL Database]** を選択し、 **[次へ]** をクリックします。
   
    ![Copy Tool - Choose destination store](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. **[Specify the Azure SQL database (Azure SQL データベースの指定)]** ページで次の操作を実行します。
   
   1. **[接続名]** フィールドに「**AzureSqlLinkedService**」と入力します。
   2. **[Server / database selection method (サーバー/データベースの選択方法)]** で **[From Azure subscriptions (Azure サブスクリプションから)]** オプションが選択されていることを確認します。
   3. Azure **サブスクリプション**を選択します。  
   4. **サーバー名**と**データベース**を選択します。
   5. **ユーザー名**と**パスワード**を入力します。
   6. **[次へ]** をクリックします。  
      
      ![Copy Tool - Azure SQL データベースの指定](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. **[テーブル マッピング]** ページで、 **[ターゲット]** フィールドのドロップダウン リストから **[emp]** を選択します。**下向き矢印**をクリックすると (省略可能)、スキーマを表示し、データをプレビューできます。
    
     ![Copy Tool - Table mapping](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. **[Schema mapping (スキーマ マッピング)]** ページで、 **[次へ]** をクリックします。
    
    ![Copy Tool - schema mapping](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. **[Performance settings (パフォーマンス設定)]** ページで、 **[次へ]** をクリックします。 
    
    ![Copy Tool - performance settings](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. **[概要]** ページの内容を確認し、 **[完了]** をクリックします。 これにより、2 つのリンクされたサービス、2 つのデータセット (入力と出力)、1 つのパイプラインが (コピー ウィザードを起動した場所から) データ ファクトリに作成されます。 
    
    ![Copy Tool - performance settings](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>監視と管理アプリケーションの起動
1. **[デプロイ]** ページでリンク `Click here to monitor copy pipeline` をクリックします。
   
   ![Copy Tool - Deployment succeeded](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. Web ブラウザーの別のタブに監視アプリケーションが起動されます。   
   
   ![Monitoring App](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. 下部の **[ACTIVITY WINDOWS]\(アクティビティ ウィンドウ\)** 一覧の **[更新]** ボタンをクリックして、時間スライスの最新の状態を表示します。 パイプラインの開始時刻と終了時刻の間の 5 日間分の 5 つのアクティビティ ウィンドウが表示されます。 この一覧は自動的に更新されないため、準備完了状態のすべてのアクティビティ ウィンドウを表示するために [更新] を繰り返しクリックすることが必要になる場合があります。 
4. 一覧で、アクティビティ ウィンドウを選択します。 右側の **[Activity Window Explorer]\(アクティビティ ウィンドウ エクスプローラー\)** に詳細が表示されます。

    ![Activity window details](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    11 日、12 日、13 日、14 日、および 15 日は緑色で表示されています。これは、これらの日付の日次スライス出力が既に生成されていることを示します。 この色分けは、ダイアグラム ビューのパイプラインと出力データセットにも使用されています。 前の手順では、既に生成済みのスライスが 2 つ、現在処理中のスライスが 1 つ、処理を待機している状態のスライスが 2 つありました (色分けに基づく)。 

    このアプリケーションの使用に関する詳細については、[監視アプリを使用したパイプラインの監視および管理](data-factory-monitor-manage-app.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Blob Storage をコピー操作のソース データ ストア、Azure SQL データベースをターゲット データ ストアとして使用しました。 次の表は、コピー アクティビティによってソースおよびターゲットとしてサポートされているデータ ストアの一覧です。 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

データ ストアのコピー ウィザードに表示されるフィールドおよびプロパティの詳細については、表内のデータ ストアへのリンクをクリックしてください。 