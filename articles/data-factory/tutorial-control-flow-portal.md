---
title: Azure portal を使用してパイプラインのアクティビティを分岐または連結する
description: アクティビティの分岐と連鎖によって Azure Data Factory 内のデータのフローを制御する方法を説明します。
services: data-factory
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 08f9310c2ffdb2e7b8d4249495c2ee90b522d694
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926778"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Data Factory パイプラインでのアクティビティの分岐と連鎖

このチュートリアルでは、いくつかの制御フロー機能を紹介する Data Factory パイプラインを作成します。 このパイプラインでは、Azure Blob Storage 内のコンテナーから同じストレージ アカウント内の別のコンテナーへの単純なコピーを行います。 コピー アクティビティが成功した場合、成功したコピー操作の詳細 (書き込まれたデータの量など) がパイプラインによって成功電子メールで送信されます。 コピー アクティビティが失敗した場合、コピー失敗の詳細 (エラー メッセージなど) がパイプラインによって失敗電子メールで送信されます。 チュートリアル全体を通じて、パラメーターを渡す方法が示されます。

シナリオの概要:![概要](media/tutorial-control-flow-portal/overview.png)

このチュートリアルでは、以下の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure Storage のリンクされたサービスを作成します。
> * Azure BLOB データセットを作成します。
> * コピー アクティビティと Web アクティビティを含むパイプラインを作成します。
> * アクティビティの出力を後続のアクティビティに送信します。
> * パラメーターの引き渡しとシステム変数を利用します。
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

このチュートリアルでは、Azure Portal を使用します。 その他のメカニズムを使用して Azure Data Factory を操作する場合は、目次から「クイック スタート」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
* **Azure Storage アカウント**。 BLOB ストレージを**ソース** データ ストアとして使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」を参照してください。
* **Azure SQL データベース**。 データベースを**シンク** データ ストアとして使用します。 Azure SQL データベースがない場合は、[Azure SQL データベースの作成](../sql-database/sql-database-get-started-portal.md)に関する記事に書かれている手順を参照して作成してください。

### <a name="create-blob-table"></a>BLOB テーブルを作成する

1. メモ帳を起動します。 次のテキストをコピーし、**input.txt** ファイルとしてディスクに保存します。

    ```
    John,Doe
    Jane,Doe
    ```
2. [Azure Storage Explorer](https://storageexplorer.com/) などのツールを使用して、次の手順を実行します。 
    1. **adfv2branch** コンテナーを作成します。
    2. **adfv2branch** コンテナーに **input** フォルダーを作成します。
    3. **input.txt** ファイルをコンテナーにアップロードします。

## <a name="create-email-workflow-endpoints"></a>電子メール ワークフロー エンドポイントを作成する
パイプラインから電子メール送信をトリガーするには、[Logic Apps](../logic-apps/logic-apps-overview.md) を使用してワークフローを定義します。 ロジック アプリ ワークフローの作成の詳細については、[ロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。 

### <a name="success-email-workflow"></a>成功電子メールのワークフロー 
`CopySuccessEmail` という名前のロジック アプリ ワークフローを作成します。 ワークフロー トリガーを `When an HTTP request is received` として定義し、`Office 365 Outlook – Send an email` のアクションを追加します。

![成功電子メールのワークフロー](media/tutorial-control-flow-portal/success-email-workflow.png)

要求トリガー用に、`Request Body JSON Schema` に次の JSON を入力します。

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

ロジック アプリ デザイナー内の要求は、次の図のようになります。 

![ロジック アプリ デザイナー - 要求](media/tutorial-control-flow-portal/logic-app-designer-request.png)

**[電子メールの送信]** アクション用に、要求本文の JSON スキーマで渡されるプロパティを利用して、電子メールを書式設定する方法をカスタマイズします。 たとえば次のようになります。

![ロジック アプリ デザイナー - 電子メールの送信アクション](media/tutorial-control-flow-portal/send-email-action-2.png)

ワークフローを保存します。 成功電子メール ワークフローの HTTP Post 要求 URL をメモしておきます。

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>失敗電子メールのワークフロー 
同じ手順に従って、**CopyFailEmail** という別の Logic Apps ワークフローを作成します。 要求トリガー内の `Request Body JSON schema` は同じです。 `Subject` のように電子メールの書式を変更し、失敗電子メールになるように調整します。 たとえば次のようになります。

![ロジック アプリ デザイナー - 失敗電子メールのワークフロー](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

ワークフローを保存します。 失敗電子メール ワークフローの HTTP Post 要求 URL をメモしておきます。

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

2 つのワークフローの URL を取得できました。

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Data Factory の作成

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. 左側のメニューで、 **[リソースの作成]**  >  **[データ + 分析]**  >  **[Data Factory]** の順に選択します。
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ページ](./media/tutorial-control-flow-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]** を選択し、リソース グループの名前を入力します。   
         
        リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2]** を選択します。
5. データ ファクトリの **場所** を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリで使用するデータ ストア (Azure Storage、Azure SQL Database など) やコンピューティング (HDInsight など) は他のリージョンに配置できます。
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。      
8. ダッシュボードに、 **[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. **[Author & Monitor]\(作成と監視\)** タイルをクリックして、別のタブで Azure Data Factory ユーザー インターフェイス (UI) を起動します。


## <a name="create-a-pipeline"></a>パイプラインを作成する
この手順では、1 つのコピー アクティビティと 2 つの Web アクティビティが含まれたパイプラインを作成します。 このパイプラインの作成には次の機能を使用します。

- データセットによってアクセスされるパイプラインのパラメーター。 
- 成功/失敗電子メールを送信するロジック アプリ ワークフローを呼び出す Web アクティビティ。 
- (成功および失敗に基づいた) あるアクティビティと別のアクティビティの接続
- アクティビティからの出力を後続のアクティビティへの入力として使用する

1. Data Factory UI の**開始**ページで **[Create pipeline]\(パイプラインの作成\)** タイルをクリックします。  

   ![開始ページ](./media/tutorial-control-flow-portal/get-started-page.png) 
3. パイプラインのプロパティ ウィンドウで **[パラメーター]** タブに切り替え、 **[新規]** ボタンを使用して、種類が文字列の 3 つのパラメーター (sourceBlobContainer、sinkBlobContainer、receiver) を追加します。 

    - **sourceBlobContainer** - ソース BLOB データセットによって使用されるパイプライン内のパラメーターです。
    - **sinkBlobContainer** – シンク BLOB データセットによって使用されるパイプライン内のパラメーターです。
    - **receiver** – このパラメーターは、このパラメーターによって電子メール アドレスが指定されている受信者に成功または失敗の電子メールを送信するパイプライン内の 2 つの Web アクティビティによって使用されます。

   ![新しいパイプライン メニュー](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. **[アクティビティ]** ツール ボックスで **[データ フロー]** を展開し、 **[コピー]** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 

   ![コピー アクティビティのドラッグ アンド ドロップ](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. 下部にある **[コピー]** アクティビティの**プロパティ** ウィンドウで、 **[ソース]** タブに切り替えて **[+ 新規]** をクリックします。 この手順でコピー アクティビティのソース データセットを作成します。 

   ![ソース データセット](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. **[新しいデータセット]** ウィンドウで **[Azure BLOB ストレージ]** を選択し、 **[完了]** をクリックします。 

   ![[Azure Blob Storage] の選択](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. **[AzureBlob1]** という新しい**タブ**が表示されます。 データセットの名前を「**SourceBlobDataset**」に変更します。

   ![データセットの全般設定](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. **プロパティ** ウィンドウにある **[接続]** タブに切り替えて、 **[リンクされたサービス]** で [新規] をクリックします。 この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクするためのリンクされたサービスを作成します。 
    
   ![データセットの接続 - 新しいリンクされたサービス](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、次の手順を行います。 

    1. **[名前]** に「**AzureStorageLinkedService**」と入力します。
    2. **[ストレージ アカウント名]** で Azure ストレージ アカウントを選択します。
    3. **[Save]** をクリックします。

   ![新しい Azure Storage のリンクされたサービス](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. フォルダーには「`@pipeline().parameters.sourceBlobContainer`」、ファイル名には「`emp.txt`」と入力します。 sourceBlobContainer パイプライン パラメーターを使用して、データセットのフォルダー パスを設定します。 

   ![ソース データセットの設定](./media/tutorial-control-flow-portal/source-dataset-settings.png)
13. **パイプライン**のタブに切り替えるか、またはツリービューにあるパイプラインをクリックします。 **[Source Dataset]\(ソース データセット\)** で **SourceBlobDataset** が選択されていることを確認します。 

    ![ソース データセット](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)
13. プロパティ ウィンドウで **[シンク]** タブに切り替えて、 **[Sink Dataset]\(シンク データセット\)** の **[+ 新規]** をクリックします。 ソース データセットの作成と同様のこの手順では、コピー アクティビティのシンク データセットを作成します。 

    ![新しいシンク データセットのボタン](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. **[新しいデータセット]** ウィンドウで **[Azure BLOB ストレージ]** を選択し、 **[完了]** をクリックします。 
15. データセットの **[General]\(一般\)** 設定ページで、 **[名前]** に「**SinkBlobDataset**」と入力します。
16. **[接続]** タブに切り替えて、次の手順を実行します。 

    1. **[リンクされたサービス]** で **AzureStorageLinkedService** を選択します。
    2. フォルダーに「`@pipeline().parameters.sinkBlobContainer`」と入力します。
    1. ファイル名に「`@CONCAT(pipeline().RunId, '.txt')`」と入力します。 この式では、ファイル名に現在のパイプライン実行の ID を使用します。 サポートされているシステム変数および式の一覧については、[システム変数](control-flow-system-variables.md)に関するページと[式言語](control-flow-expression-language-functions.md)に関するページを参照してください。

        ![シンク データセットの設定](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. 上部にある**パイプライン**のタブに切り替えます。 **[アクティビティ]** ツール ボックスの **[General]\(一般\)** を展開し、 **[Web]** アクティビティをパイプライン デザイナー画面にドラッグ アンド ドロップします。 アクティビティの名前を「**SendSuccessEmailActivity**」に設定します。 Web アクティビティでは、任意の REST エンドポイントを呼び出すことができます。 このアクティビティの詳細については、[Web アクティビティ](control-flow-web-activity.md)に関する記事を参照してください。 このパイプラインでは、Web アクティビティを使用して Logic Apps 電子メール ワークフローを呼び出します。 

    ![最初の Web アクティビティのドラッグ アンド ドロップ](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. **[General]\(一般\)** タブから **[設定]** タブに切り替えて、次の手順を実行します。 
    1. **[URL]** で、成功電子メールを送信するロジック アプリ ワークフローの URL を指定します。  
    2. **[メソッド]** に **[POST]** を選択します。 
    3. **[ヘッダー]** セクションにある **+ [ヘッダーの追加]** リンクをクリックします。 
    4. **[コンテンツの種類]** というヘッダーを追加し、それを **application/json** に設定します。 
    5. **[本文]** に次の JSON を指定します。 

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        メッセージの本文には次のプロパティが含まれます。

       - Message – `@{activity('Copy1').output.dataWritten` から渡される値。 前のコピー アクティビティのプロパティにアクセスし、dataWritten の値を渡します。 失敗の場合、`@{activity('CopyBlobtoBlob').error.message` の代わりにエラー出力を渡します。
       - Data Factory Name – `@{pipeline().DataFactory}` から渡される値。これはシステム変数であり、対応するデータ ファクトリ名へのアクセスを可能にします。 サポートされているシステム変数の一覧については、「[システム変数](control-flow-system-variables.md)」の記事を参照してください。
       - Pipeline Name – `@{pipeline().Pipeline}` から渡される値。 これもシステム変数であり、対応するパイプライン名へのアクセスを可能にします。 
       - Receiver – "\@pipeline().parameters.receiver") から渡される値。 パイプライン パラメーターにアクセスします。
    
         ![最初の Web アクティビティの設定](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. コピー アクティビティの横にある緑のボタンを Web アクティビティにドラッグ アンド ドロップして、 **[コピー]** アクティビティを **[Web]** アクティビティに接続します。 

    ![コピー アクティビティと最初の Web アクティビティの接続](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. [アクティビティ] ツール ボックスからパイプライン デザイナー画面に別の **[Web]** アクティビティをドラッグ アンド ドロップし、 **[名前]** を「**SendFailureEmailActivity**」に設定します。

    ![2 番目の Web アクティビティの名前](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. **[設定]** タブに切り替えて、次の手順を実行します。

    1. **[URL]** で、失敗電子メールを送信するロジック アプリ ワークフローの URL を指定します。  
    2. **[メソッド]** に **[POST]** を選択します。 
    3. **[ヘッダー]** セクションにある **+ [ヘッダーの追加]** リンクをクリックします。 
    4. **[コンテンツの種類]** というヘッダーを追加し、それを **application/json** に設定します。 
    5. **[本文]** に次の JSON を指定します。 

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![2 番目の Web アクティビティの設定](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. パイプライン デザイナーの **[コピー]** アクティビティを選択して **+->** ボタンをクリックし、 **[エラー]** を選択します。  

    ![2 番目の Web アクティビティの設定](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. コピー アクティビティの横にある**赤い**ボタンを 2 番目の Web アクティビティ **SendFailureEmailActivity** にドラッグします。 パイプラインが次の図のように表示されるよう、アクティビティを移動できます。 

    ![すべてのアクティビティが含まれた完全なパイプライン](./media/tutorial-control-flow-portal/full-pipeline.png)
24. パイプラインを検証するために、ツール バーの **[検証]** ボタンをクリックします。 **>>** ボタンをクリックして **[Pipeline Validation Output]\(パイプラインの検証の出力\)** ウィンドウを閉じます。

    ![パイプラインの検証](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. エンティティ (データセットやパイプラインなど) を Data Factory サービスに発行するには、 **[すべて公開]** を選択します。 **[正常に発行されました]** というメッセージが表示されるまで待機します。

    ![発行](./media/tutorial-control-flow-portal/publish-button.png)
 
## <a name="trigger-a-pipeline-run-that-succeeds"></a>成功するパイプラインの実行をトリガーする
1. パイプラインの実行を**トリガー**するために、ツール バーの **[トリガー]** をクリックし、 **[Trigger Now]\(今すぐトリガー\)** をクリックします。 

    ![パイプラインの実行のトリガー](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで、次の手順を実行します。 

    1. **sourceBlobContainer** パラメーターに「**adftutorial/adfv2branch/input**」を入力します。 
    2. **sinkBlobContainer** パラメーターに「**adftutorial/adfv2branch/output**」を入力します。 
    3. **receiver** の**メール アドレス**を入力します。 
    4. **[完了]** をクリックします。

        ![パイプラインの実行のパラメーター](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>成功したパイプラインの実行を監視する

1. パイプラインの実行を監視するには、左側の **[監視]** タブに切り替えます。 手動でトリガーしたパイプラインの実行が表示されます。 **[最新の情報に更新]** ボタンを使用して、一覧を更新します。 
    
    ![成功したパイプラインの実行](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. このパイプラインの実行に関連付けられている**アクティビティの実行を表示**するには、 **[アクション]** 列にある最初のリンクをクリックします。 上部の **[パイプライン]** をクリックすると、前のビューに戻ることができます。 **[最新の情報に更新]** ボタンを使用して、一覧を更新します。 

    ![アクティビティの実行](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>失敗するパイプラインの実行をトリガーする
1. 左側で **[編集]** タブに切り替えます。 
2. パイプラインの実行を**トリガー**するために、ツール バーの **[トリガー]** をクリックし、 **[Trigger Now]\(今すぐトリガー\)** をクリックします。 
3. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで、次の手順を実行します。 

    1. **sourceBlobContainer** パラメーターに「**adftutorial/dummy/input**」を入力します。 ダミー フォルダーが adftutorial コンテナーに存在しないことを確認します。 
    2. **sinkBlobContainer** パラメーターに「**adftutorial/dummy/output**」を入力します。 
    3. **receiver** の**メール アドレス**を入力します。 
    4. **[完了]** をクリックします。

## <a name="monitor-the-failed-pipeline-run"></a>失敗したパイプラインの実行を監視する

1. パイプラインの実行を監視するには、左側の **[監視]** タブに切り替えます。 手動でトリガーしたパイプラインの実行が表示されます。 **[最新の情報に更新]** ボタンを使用して、一覧を更新します。 
    
    ![失敗のパイプラインの実行](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. エラーの詳細を表示するには、パイプラインの実行の **[エラー]** リンクをクリックします。 

    ![パイプライン エラー](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. このパイプラインの実行に関連付けられている**アクティビティの実行を表示**するには、 **[アクション]** 列にある最初のリンクをクリックします。 **[最新の情報に更新]** ボタンを使用して、一覧を更新します。 パイプラインのコピー アクティビティが失敗したことがわかります。 Web アクティビティによる指定された受信者への失敗電子メールの送信は成功しました。 

    ![アクティビティの実行](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. **[アクション]** 列の **[エラー]** リンクをクリックして、エラーの詳細を表示します。 

    ![アクティビティの実行エラー](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の手順を実行しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure Storage のリンクされたサービスを作成します。
> * Azure BLOB データセットを作成します。
> * コピー アクティビティと Web アクティビティを含むパイプラインを作成します。
> * アクティビティの出力を後続のアクティビティに送信します。
> * パラメーターの引き渡しとシステム変数を利用します。
> * パイプラインの実行を開始します。
> * パイプラインとアクティビティの実行を監視します。

これで、「概念」セクションに進み、Azure Data Factory の詳細について学習できるようになりました。
> [!div class="nextstepaction"]
>[パイプラインとアクティビティ](concepts-pipelines-activities.md)
