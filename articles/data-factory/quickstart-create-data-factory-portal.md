---
title: Azure Data Factory UI を使用して Azure データ ファクトリを作成する
description: Azure Blob Storage 内のある場所から別の場所へデータをコピーするパイプラインを使用して、データ ファクトリを作成します。
author: linda33wj
ms.service: data-factory
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 8b33005b7f0d08e6da963457813b012199d89f5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655102"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>クイック スタート:Azure Data Factory UI を使用してデータ ファクトリを作成する 

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [現在のバージョン](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このクイックスタートでは、Azure Data Factory UI を使用してデータ ファクトリを作成および監視する方法について説明します。 このデータ ファクトリに作成したパイプラインは、データを Azure Blob Storage 内のあるフォルダーから別のフォルダーに "*コピー*" します。 Azure Data Factory を使用してデータを "*変換*" するには、[マッピング データ フロー](concepts-data-flow-overview.md)に関するページを参照してください。

> [!NOTE]
> Azure Data Factory を初めて使用する場合は、このクイック スタートを実行する前に、「[Azure Data Factory の概要](introduction.md)」を参照してください。 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>ビデオ 
このビデオを視聴すると、Data Factory UI を理解できます。 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Data Factory の作成

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 
1. Azure portal メニューから **[リソースの作成]** を選択します。
1. **[統合]** を選択し、 **[Data Factory]** を選択します。 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="[新規] ペインでの Data Factory の選択。":::

1. **[Create Data Factory]\(データ ファクトリの作成\)** ページの **[基本]** タブで、データ ファクトリを作成する Azure **サブスクリプション** を選択します。
1. **[リソース グループ]** で、次の手順のいずれかを行います。

    a. ドロップダウン リストから既存のリソース グループを選択します。

    b. **[新規作成]** を選択し、新しいリソース グループの名前を入力します。
    
    リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/management/overview.md)に関するページを参照してください。 

1. **[リージョン]** で、データ ファクトリの場所を選択します。

   この一覧に表示されるのは、Data Factory でサポートされ、かつ Azure Data Factory のメタ データが格納される場所のみです。 Data Factory で使用する関連データ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (Azure HDInsight など) は他のリージョンで実行できます。
 
1. **[名前]** に「**ADFTutorialDataFactory**」と入力します。
   Azure データ ファクトリの名前は *グローバルに一意* にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して ( **&lt;yourname&gt;ADFTutorialDataFactory** など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="重複する名前に関する、新しい Data Factory のエラーメッセージ。":::

1. **[バージョン]** で、 **[V2]** を選択します。

1. **Next:Git configuration\(次へ: Git の構成\)** を選択し、**Configure Git later\(後で Git を構成する\)** チェック ボックスをオンにします。

1. **[確認と作成]** を選択し、検証に成功したら **[作成]** を選択します。 作成後、 **[リソースに移動]** を選択して、 **[Data factory]** ページに移動します。 

1. **[作成と監視]** タイルを選択して、別のブラウザー タブで Azure Data Factory ユーザー インターフェイス (UI) アプリケーションを起動します。
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="[作成と監視] タイルが含まれた Azure Data Factory のホーム ページ。":::
   
   > [!NOTE]
   > Web ブラウザーが "承認中" でスタックしていることがわかったら、 **[サードバーティの Cookie とサイト データをブロックする]** チェック ボックスをオフにします。 または、オンにしたまま、**login.microsoftonline.com** の例外を作成してから、アプリを再度開いてみてください。
   

## <a name="create-a-linked-service"></a>リンクされたサービスを作成する
この手順では、Azure Storage アカウントをデータ ファクトリにリンクするためのリンクされたサービスを作成します。 リンクされたサービスは、Data Factory サービスが実行時に接続するために使用する接続情報を持っています。

1. Azure Data Factory UI ページで、左側のペインにある [ **[管理]**](./author-management-hub.md) タブを開きます。

1. [リンクされたサービス] ページの **[+ 新規]** を選択して、リンクされたサービスを新規作成します。

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="新しいリンクされたサービス。":::
   
1. **[New Linked Service]\(新しいリンクされたサービス\)** ページで **[Azure Blob Storage]** を選択し、 **[続行]** を選択します。 

1. [New Linked Service (Azure Blob Storage)]\(新しいリンクされたサービス (Azure Blob Storage)\) ページで、次の手順を行います。 

   a. **[名前]** に「**AzureStorageLinkedService**」と入力します。

   b. **[ストレージ アカウント名]** で Azure Storage アカウントの名前を選択します。

   c. **[テスト接続]** を選択して、Data Factory サービスがストレージ アカウントに接続できることを確認します。 

   d. **[作成]** を選択して、リンクされたサービスを保存します。 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="リンクされたサービス。":::


## <a name="create-datasets"></a>データセットを作成する
この手順では、**InputDataset** と **OutputDataset**。 これらのデータセットの種類は、**AzureBlob** です。 これらは、前のセクションで作成した Azure Storage のリンクされたサービスを参照します。 

入力データセットは、入力フォルダーのソース データを表します。 入力データセットの定義では、ソース データを格納している BLOB コンテナー (**adftutorial**)、フォルダー (**input**)、およびファイル (**emp.txt**) を指定します。 

出力データセットは、ターゲットにコピーされるデータを表します。 出力データセットの定義では、データのコピー先の BLOB コンテナー (**adftutorial**)、フォルダー (**output**)、およびファイルを指定します。 パイプラインの各実行には、一意の ID が関連付けられます。 この ID には、システム変数 **RunId** を使用してアクセスできます。 出力ファイルの名前は、パイプラインの実行 ID に基づいて動的に評価されます。   

リンクされたサービスの設定では、ソース データを含む Azure Storage アカウントを指定しました。 ソース データセットの設定では、ソース データがどこにあるかを正確に指定します (BLOB コンテナー、フォルダー、およびファイル)。 シンク データセットの設定では、データがどこにコピーされるかを指定します (BLOB コンテナー、フォルダー、およびファイル)。 

1. 左側のペインにある **[作成者]** タブを選択します。

1. **+** (正符号) ボタンを選択し、 **[データセット]** を選択します。

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="データセットを作成するためのメニュー。":::

1. **[新しいデータ セット]** ページで **[Azure Blob Storage]** を選択し、 **[続行]** を選択します。 

1. **[形式の選択]** ページで、データの形式の種類を選択して、 **[続行]** を選択します。 この場合、コンテンツを解析せずに、ファイルをそのままコピーする場合は **[バイナリ]** を選択します。

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="形式の選択。":::   
   
1. **[プロパティの設定]** ページで、次の手順を行います。

    a. **[名前]** に「**InputDataset**」と入力します。 

    b. **[リンクされたサービス]** で **[AzureStorageLinkedService]** を選択します。

    c. **[ファイル パス]** で、 **[参照]** ボタンを選択します。

    d. **[Choose a file or folder]\(ファイルまたはフォルダーの選択\)** ウィンドウで、**adftutorial** コンテナーの **input** フォルダーを参照し、**emp.txt** ファイルを選択して、 **[OK]** をクリックします。
    
    e. **[OK]** を選択します。   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="InputDataset のプロパティを設定する。":::

1. この手順を繰り返して、出力データセットを作成します。  

    a. **+** (正符号) ボタンを選択し、 **[データセット]** を選択します。

    b. **[新しいデータ セット]** ページで **[Azure Blob Storage]** を選択し、 **[続行]** を選択します。

    c. **[形式の選択]** ページで、データの形式の種類を選択して、 **[続行]** を選択します。

    d. **[プロパティの設定]** ページで、名前に「**OutputDataset**」と指定します。 リンクされたサービスとして **AzureStorageLinkedService** を選択します。

    e. **[ファイルのパス]** に「**adftutorial/output**」と入力します。 **output** フォルダーが存在しない場合、コピー アクティビティにより実行時に作成されます。

    f. **[OK]** を選択します。   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="OutputDataset のプロパティを設定する。":::    

## <a name="create-a-pipeline"></a>パイプラインを作成する 
この手順では、入力データセットと出力データセットを使用するコピー アクティビティを持つパイプラインを作成および検証します。 コピー アクティビティにより、入力データセットの設定で指定されたファイルから、出力データセットの設定で指定されたファイルにデータがコピーされます。 入力データセットで、ファイル名を指定せず、フォルダーのみを指定すると、コピー アクティビティはソース フォルダーのすべてのファイルをターゲットにコピーします。 

1. **+** (正符号) ボタンを選択し、 **[パイプライン]** を選択します。 

1. [全般] パネルの **[プロパティ]** 下で、 **[名前]** に **CopyPipeline** を指定します。 次に、右上隅にある [プロパティ] アイコンをクリックしてパネルを折りたたみます。

1. **[アクティビティ]** ツールボックスで **[Move & Transform]\(移動と変換\)** を展開します。 **[アクティビティ]** ツールボックスからパイプライン デザイナー画面に **[データのコピー]** アクティビティをドラッグします。 **[アクティビティ]** ツールボックスで、アクティビティを検索することもできます。 **[名前]** に「**CopyFromBlobToBlob**」と指定します。

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="[データのコピー] アクティビティの作成。":::   

1. コピー アクティビティの設定で **[ソース]** タブに切り替えて、 **[Source Dataset]\(ソース データセット\)** で **[InputDataset]** を選択します。

1. コピー アクティビティの設定で **[シンク]** タブに切り替えて、 **[Sink Dataset]\(シンク データセット\)** で **[OutputDataset]** を選択します。

1. キャンバスの上にあるパイプライン ツール バーの **[検証]** をクリックして、パイプライン設定を検証します。 パイプラインが正常に検証されたことを確認します。 検証の出力を閉じるには、右上隅にある [検証] ボタンを選択します。 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="パイプラインを検証する。":::

## <a name="debug-the-pipeline"></a>パイプラインのデバッグ
この手順では、パイプラインを Data Factory にデプロイする前にデバッグします。 

1. キャンバスの上にあるパイプライン ツール バーの **[デバッグ]** をクリックして、テスト実行をトリガーします。 
    
1. 一番下にあるパイプラインの設定の **[出力]** タブにパイプラインの実行の状態が表示されることを確認します。 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="パイプラインの実行の出力":::

1. **adftutorial** コンテナーの **output** フォルダーに出力ファイルがあることを確認します。 output フォルダーが存在しない場合は、Data Factory サービスによって自動的に作成されます。 

## <a name="trigger-the-pipeline-manually"></a>パイプラインを手動でトリガーする
この手順では、Azure Data Factory にエンティティ (リンクされたサービス、データセット、パイプライン) をデプロイします。 次に、パイプラインの実行を手動でトリガーします。 

1. パイプラインをトリガーする前に、エンティティを Data Factory に発行する必要があります。 発行するには、上部にある **[すべて発行]** を選択します。 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="すべて発行。":::    

1. パイプラインを手動でトリガーするには、パイプライン ツール バーの **[トリガーの追加]** を選択し、 **[Trigger Now]\(今すぐトリガー\)** を選択します。 **[Pipeline Run]\(パイプラインの実行\)** ページで **[OK]** を選択します。

## <a name="monitor-the-pipeline"></a>パイプラインの監視

1. 左側で **[監視]** タブに切り替えます。 **[最新の情報に更新]** ボタンを使用して、一覧を更新します。

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="パイプラインの実行を監視するタブ"::: 
1. **[CopyPipeline]** リンクを選択します。コピー アクティビティの実行の状態が、このページに表示されます。 

1. コピー操作の詳細を確認するには、 **[詳細]** (眼鏡アイコン) リンクを選択します。 プロパティの詳細については、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="コピー操作の詳細。":::
1. **output** フォルダーに新しいファイルが表示されていることを確認します。 
1. **[すべてのパイプラインの実行]** リンクを選択すると、 **[アクティビティの実行]** ビューから **[パイプラインの実行]** ビューに戻ることができます。 

## <a name="trigger-the-pipeline-on-a-schedule"></a>スケジュールに基づいてパイプラインをトリガーする
この手順は、このチュートリアルでは省略できます。 "*スケジューラ トリガー*" を作成して、パイプラインを定期的 (毎時、毎日など) に実行するようにスケジュールすることができます。 この手順では、指定した終了日時まで毎分実行するようにトリガーを作成します。 

1. **[作成者]** タブに切り替えます。 

1. パイプラインに移動し、パイプライン ツール バーの **[トリガーの追加]** を選択し、 **[New/Edit]\(新規作成/編集\)** を選択します。 

1. **[Add Triggers]\(トリガーの追加\)** ページで、 **[Choose trigger]\(トリガーの選択\)** を選択し、 **[新規]** を選択します。 

1. **[新しいトリガー]** ページの **[終了]** で **[指定日]** を選択し、終了時刻として現在から数分後を指定して、 **[OK]** を選択します。 

   各パイプラインの実行にはコストがかかります。そのため、開始時刻からほんの数分後の終了時刻を指定してください。 同じ日であることを確認します。 ただし、発行時から終了時刻までにパイプラインを実行できる十分な時間があるようにします。 トリガーは、UI でトリガーを保存したときではなく、Data Factory にソリューションを発行した後で有効になります。 

1. **[新しいトリガー]** ページで、 **[アクティブ化済み]** チェック ボックスをオンにし、 **[OK]** を選択します。 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="新しいトリガーの設定。":::   
1. 警告メッセージを確認し、 **[OK]** を選択します。

1. **[すべて発行]** をクリックして、変更を Data Factory に発行します。 

1. 左側で **[監視]** タブに切り替えます。 **[最新の情報に更新]** を選択して、一覧を更新します。 発行時刻から終了時刻まで、毎分 1 回パイプラインが実行されることがわかります。 

   **[トリガー元]** 列の値に注意してください。 手動のトリガー実行は、前に行った手順 ( **[Trigger Now]\(今すぐトリガー\)** ) によるものでした。 

1. **[Trigger runs]\(トリガーの実行\)** ビューに切り替えます。 

1. **output** フォルダー内に、指定した終了日時までパイプラインが実行されるたびに出力ファイルが作成されていることを確認します。 

## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure Blob Storage 内のある場所から別の場所にデータをコピーします。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-portal.md)を参照してください。
