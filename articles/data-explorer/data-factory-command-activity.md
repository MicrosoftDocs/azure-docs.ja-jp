---
title: Azure Data Factory で Azure Data Explorer 制御コマンドを使用する
description: このトピックでは、Azure Data Factory で Azure Data Explorer 制御コマンドを使用します
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 316ddbf662a5418e54f37cb335475a86c50118c7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131094"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Azure Data Factory コマンド アクティビティを使用して Azure Data Explorer 制御コマンドを実行する

[Azure Data Factory](/azure/data-factory/) (ADF) は、データに対してあらゆるアクティビティを組み合わせて実行できるクラウドベースのデータ統合サービスです。 ADF を使用して、データ移動とデータ変換を調整し、自動化するためのデータ駆動型ワークフローを作成します。 Azure Data Factory の **Azure Data Explorer Command** アクティビティを使用すると、ADF ワークフロー内で [Azure Data Explorer 制御コマンド](/azure/kusto/concepts/#control-commands)を実行できます。 この記事では、Azure Data Explorer のコマンド アクティビティを含む、ルックアップ アクティビティと ForEach アクティビティを使用してパイプラインを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)
* データのソースです。
* [Data Factory](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>新しいパイプラインを作成する

1. **[作成者]** 鉛筆ツールを選びます。 
1. **+** を選択して新しいパイプラインを作成し、ドロップダウンから **[パイプライン]** を選択します。

   ![新しいパイプラインの作成](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>ルックアップ アクティビティを作成する

1. **[アクティビティ]** ウィンドウの **[全般]** で、 **[ルックアップ]** アクティビティを選択します。 右のメイン キャンバスにドラッグ アンド ドロップします。
 
    ![ルックアップ アクティビティを選択する](media/data-factory-command-activity/select-activity.png)

1. これで、キャンバスには作成したルックアップ アクティビティが含まれます。 キャンバスの下にあるタブを使用して、関連するパラメーターを変更します。 **[全般]** で、アクティビティの名前を変更します。 

    ![ルックアップ アクティビティを編集する](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > 空のキャンバス領域をクリックして、パイプラインのプロパティを表示します。 パイプラインの名前を変更するには、 **[全般]** タブを使用します。 パイプラインには *pipeline-4-docs* という名前が付けられています。

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>ルックアップ アクティビティで Azure Data Explorer データセットを作成する

1. **[設定]** で、事前に作成した Azure Data Explorer の**ソース データセット**を選択するか、 **[ + 新規]** を選択して新しいデータセットを作成します。
 
    ![ルックアップ 設定にデータセットを追加する](media/data-factory-command-activity/lookup-settings.png)

1. **[新しいデータセット]** ウィンドウから **Azure Data Explorer (Kusto)** データセットを選択します。 **[続行]** を選択して新しいデータセットを追加します。

   ![新しいデータセットを選択する](media/data-factory-command-activity/select-new-dataset.png) 

1. 新しい Azure Data Explorer データセット パラメーターは、 **[設定]** で表示できます。 パラメーターを更新するには、 **[編集]** を選択します。

    ![Azure Data Explorer データセットを使用したルックアップの設定](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. メインキャンバスで **AzureDataExplorerTable** のタブが新しく開きます。 
    * **[全般]** を選択し、データセット名を編集します。 
    * データセットのプロパティを編集するには、 **[接続]** を選択します。 
    * ドロップダウンから **[リンクされたサービス]** を選択するか、 **[+ 新規]** を選択してリンクされたサービスを新規作成します。

    ![Azure Data Explorer データセットのプロパティの編集](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. 新しいリンクされたサービスを作成する場合、 **[New Linked Service (Azure Data Explorer)]\(新しいリンクされたサービス (Azure Data Explorer)\)** ページが開きます。

    ![ADX の新しいリンクされたサービス](media/data-factory-command-activity/adx-new-linked-service.png)

   * Azure Data Explorer のリンクされたサービスの **[名前]** を選択します。 必要に応じて **[説明]** を追加します。
   * 必要に応じて、 **[Connect via integration runtime]\(統合ランタイム経由で接続\)** で現在の設定を変更します。 
   * **[Account selection method]\(アカウントの選択方法\)** で、次の 2 つの方法のいずれかを使用してクラスターを選択します。 
        * **[From Azure subscription]\(Azure サブスクリプションから\)** ラジオ ボタンを選択し、 **[Azure サブスクリプション]** アカウントを選択します。 次に、自分の **[クラスター]** を選択します。 ドロップダウンには、ユーザーに属するクラスターのみが一覧表示されることに注意してください。
        * または、 **[手動で入力]** ラジオ ボタンを選択し、自分の **[エンドポイント]** (クラスタの URL) を入力します。
    * **[テナント]** を指定します。
    * **[サービス プリンシパル ID]** を入力します。 プリンシパル ID には、使用するコマンドに必要なアクセス許可レベルに従って、適切なアクセス許可が必要です。
    * **[サービス プリンシパル キー]** ボタンを選択し、 **[サービス プリンシパル キー]** を入力します。
    * ドロップダウン メニューから **[データベース]** を選択します。 または、 **[編集]** チェックボックスをオンにし、データベース名を入力します。
    * **[Test Connection]\(接続のテスト\)** を選択して、作成したリンクされたサービスの接続をテストします。 セットアップに接続できる場合は、緑色のチェックマーク (**接続成功**) が表示されます。
    * **[完了]** を選択して、リンクされたサービスの作成を完了します。

1. リンクされたサービスの設定が完了したら、 **[AzureDataExplorerTable]**  >  **[接続]** で、 **[テーブル]** 名を追加します。 **[データのプレビュー]** を選択して、データが正しく表示されていることを確認します。

   データセットの準備ができました。パイプラインの編集を続行できます。

### <a name="add-a-query-to-your-lookup-activity"></a>ルックアップ アクティビティにクエリを追加する

1. **[pipeline-4-docs]**  >  **[Settings]** で、 **[クエリ]** テキストボックスにクエリを追加します。次に例を示します。

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. 必要に応じて、**クエリ タイムアウト** または **[No truncation]\(切り捨てしない)** および **[First row only]\(先頭行のみ\)**  プロパティを変更します。 このフローでは、既定の **[クエリ タイムアウト]** を保持し、チェックボックスをオフにします。 

    ![ルックアップ アクティビティの最終設定](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>For-Each アクティビティを作成する 

1. 次に、For-each アクティビティをパイプラインに追加します。 このアクティビティは、ルックアップ アクティビティから返されたデータを処理します。 
    * **[アクティビティ]** ウィンドウで、 **[Iteration & Conditions]\(繰り返しと条件\)**  から **[ForEach]** アクティビティを選択し、キャンバスにドラッグ アンド ドロップします。
    * ルックアップ アクティビティの出力と、キャンバス内の ForEach アクティビティの入力を線で繋いで接続します。

        ![ForEach アクティビティ](media/data-factory-command-activity/for-each-activity.png)

1.  キャンバスで [ForEach] アクティビティを選択します。 以下の **[設定]** タブで次を行います。
    * **[シーケンシャル]** チェックボックスをオンにしてルックアップ結果のシーケンシャル処理を行うか、オフのままにして並列処理を作成する。
    * **バッチ カウント** を設定する
    * **[項目]** で、出力値 *@activity('Lookup1').output.value* への参照を指定する

       ![ForEach アクティビティの設定](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>ForEach アクティビティ内に Azure Data Explorer コマンド アクティビティを作成する

1. キャンバスの ForEach アクティビティをダブルクリックして新しいキャンバスで開き、ForEach 内のアクティビティを指定します。
1. **[アクティビティ]** ウィンドウの **[Azure Data Explorer]** で **[Azure Data Explorer コマンド]** アクティビティを選択し、キャンバスにドラッグ アンド ドロップします。

    ![Azure Data Explorer のコマンド アクティビティ](media/data-factory-command-activity/adx-command-activity.png)

1.  **[接続]** タブで、以前作成したリンクされたサービスを選択します。

    ![Azure Data Explorer の [コマンド アクティビティの接続] タブ](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. **[コマンド]** タブで、次のコマンドを指定します。

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **[コマンド]** は、指定されたクエリの結果を圧縮形式で BLOB ストレージにエクスポートするよう Azure Data Explorer に指示します。 これは、非同期で実行されます (非同期修飾子を使用)。
    このクエリは、ルックアップ アクティビティの結果の各行のデータベース列に対応します。 **[コマンド タイムアウト]** は必ずしも変更する必要はありません。

    ![コマンド アクティビティ](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > コマンド アクティビティには次の制限があります。
    > * サイズの制限：1 MB の応答サイズ
    > * 制限時間：20分 (既定値)、1時間 (最大)。
    > * 必要に応じて、[AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands) を使用して結果にクエリを追加し、結果のサイズ/時間を減らすことができます。

1.  パイプラインの準備ができました。 パイプライン名をクリックすると、メインのパイプライン ビューに戻ることができます。

    ![Azure Data Explorer のコマンド パイプライン](media/data-factory-command-activity/adx-command-pipeline.png)

1. パイプラインを公開する前に、 **[デバッグ]** を選択します。 パイプラインの進行状況は、 **[出力]** タブで確認できます。

    ![Azure Data Explorer のコマンド アクティビティの出力](media/data-factory-command-activity/command-activity-output.png)

1. 必要に応じて **[すべて公開]** 、 **[トリガーの追加]** の順に選択してパイプラインを実行します。 

## <a name="control-command-outputs"></a>管理コマンドの出力

コマンド アクティビティの出力の構造の詳細を以下に示します。 この出力は、パイプラインの次のアクティビティで使用できます。

### <a name="returned-value-of-a-non-async-control-command"></a>非同期でない管理コマンドの戻り値

非同期でない管理コマンドでは、戻り値の構造はルックアップ アクティビティの結果の構造に似ています。 `count` フィールドは、返されたレコードの数を示します。 固定配列フィールド `value` にはレコードの一覧が含まれています。 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>非同期管理コマンドの戻り値

非同期管理コマンドでは、アクティビティは非同期操作が完了するか、タイムアウトになるまでバックグラウンドで操作テーブルをポーリングします。したがって、戻り値には指定された **OperationId** プロパティの `.show operations OperationId` の結果が格納されます。 操作が正常に完了したことを確認するには、**State** および **Status** の各プロパティの値を確認します。

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>次の手順

* [Azure Data Factory を使用して Azure Data Explorer にデータをコピーする](data-factory-load-data.md)方法について確認する。
* [データベースから Azure Data Explorer への一括コピーに Azure Data Factory テンプレートを使用する](data-factory-template.md)方法について学習する。