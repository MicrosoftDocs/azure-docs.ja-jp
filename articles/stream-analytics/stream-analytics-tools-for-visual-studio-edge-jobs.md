---
title: Visual Studio の Azure Stream Analytics ツールでの Stream Analytics Edge ジョブ
description: この記事では、Visual Studio の Stream Analytics ツールを使って、Stream Analytics on IoT Edge ジョブをオーサリング、デバッグ、および作成する方法について説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 1601bf6c73d9f3450959773c85385bc8ef907a66
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329959"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Visual Studio Tools を使用して Stream Analytics Edge ジョブを作成する

このチュートリアルでは、Visual Studio の Stream Analytics ツールの使用方法について説明します。 Stream Analytics Edge ジョブをオーサリング、デバッグ、および作成する方法について取り上げます。 ジョブを作成してテストした後、 Azure Portal に移動して、それをデバイスに配置できます。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)、[Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)、[Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326) のいずれかをインストールします。 Enterprise (Ultimate/Premium)、Professional、Community の各エディションがサポートされています。 Express エディションはサポートされていません。  

* [インストール手順](stream-analytics-tools-for-visual-studio-edge-jobs.md)に従って、Visual Studio の Stream Analytics ツールをインストールします。
 
## <a name="create-a-stream-analytics-edge-project"></a>Stream Analytics Edge プロジェクトを作成する 

Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。 左側の **テンプレート**の一覧に移動し、 **[Azure Stream Analytics]**  >  **[Stream Analytics Edge]**  >  **[Azure Stream Analytics Edge Application]** を選択します。 プロジェクトの名前、場所、およびソリューション名を入力し、 **[OK]** を選択します。

![Visual Studio での新しい Stream Analytics Edge プロジェクト](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

プロジェクトが作成されたら、**ソリューション エクスプローラー**に移動して、フォルダー階層を表示します。

![Stream Analytics Edge ジョブのソリューション エクスプローラー ビュー](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>適切なサブスクリプションを選択する

1. Visual Studio の **[表示]** メニューで **[サーバー エクスプローラー]** を選択します。  

2. **[Azure]** を右クリックし、 **[Connect to Microsoft Azure Subscription]\(Microsoft Azure** サブスクリプションへの接続\) を選択します。その後、Azure アカウントでサインインします。

## <a name="define-inputs"></a>入力を定義する

1. **ソリューション エクスプローラー**で、 **[入力]** ノードを展開します。**EdgeInput.json** という名前の入力が表示されます。 ダブルクリックしてその設定を表示します。  

2. [ソースの種類] を **[データ ストリーム]** に設定します。 次に、[ソース] を **[Edge Hub]** に、[イベントのシリアル化形式] を **[Json]** に、[エンコード] を **[UTF8]** に設定します。 必要に応じて **[入力のエイリアス]** の名前を変更できますが、この例ではそのままにします。 入力の別名の名前を変更する場合は、クエリを定義するときに指定した名前を使用します。 **[保存]** を選択して設定を保存します。  
   ![Stream Analytics ジョブの入力構成](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>出力を定義する

1. **ソリューション エクスプローラー**で、 **[出力]** ノードを展開します。**EdgeOutput.json** という名前の出力が表示されます。 ダブルクリックしてその設定を表示します。  

2. [シンク] が **[Edge Hub]** に、[イベントシリアル化形式] が **[Json]** に、[エンコード] が **[UTF8]** に、[フォーマット] が **[配列]** に設定されていることを確認します。 必要に応じて **[出力のエイリアス]** の名前を変更できますが、この例ではそのままにします。 出力の別名の名前を変更する場合は、クエリを定義するときに指定した名前を使用します。 **[保存]** を選択して設定を保存します。 
   ![Stream Analytics ジョブの出力構成](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>変換クエリを定義する

Stream Analytics IoT Edge 環境にデプロイする Stream Analytics ジョブは、[Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396)の大半をサポートします。 ただし、Stream Analytics Edge ジョブでは次の操作はまだサポートされていません。 


|**カテゴリ**  | **コマンド**  |
|---------|---------|
|その他の演算子 | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>JavaScript UDF</li><li>ユーザー定義集計 (UDA)</li><li>GetMetadataPropertyValue</li><li>1 つの手順での 14 を超える集計の使用</li></ul>   |

ポータルで Stream Analytics Edge ジョブを作成するときに、サポートされている演算子を使用していない場合は、コンパイラによって自動的に警告が表示されます。

Visual Studio のクエリ エディターで、次の変換クエリを定義します (**script.asaql ファイル**)。

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>ローカル ジョブをテストする

クエリをローカルでテストするには、サンプル データをアップロードする必要があります。 サンプル データは、[GitHub リポジトリ](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json)から登録データをダウンロードしてローカル コンピューターに保存することで取得できます。 

1. サンプル データをアップロードするには、**EdgeInput.json** ファイルを右クリックし、 **[ローカル入力の追加]** を選択します。  

2. ポップアップ ウィンドウで、ローカル パスからサンプル データを**参照**し、 **[保存]** を選択します。
   ![Visual Studio でのローカルの入力構成](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. **local_EdgeInput.json** という名前のファイルが、入力フォルダーに自動的に追加されます。  
4. ローカルで実行するか、Azure に送信できます。 クエリをテストするには、 **[ローカルで実行]** を選択します。  
   ![Visual Studio での Stream Analytics ジョブの実行オプション](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. コマンド プロンプト ウィンドウに、ジョブの状態が表示されます。 ジョブが正常に実行されると、プロジェクト フォルダーのパス "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" に "2018-02-23-11-31-42" のようなフォルダーが作成されます。 フォルダー パスに移動して、ローカル フォルダー内の結果を表示します。

   Azure Portal にサインインし、ジョブが作成されたことを確認することもできます。 

   ![Stream Analytics ジョブの結果フォルダー](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>ジョブを Azure に送信する

1. Azure にジョブを送信する前に、Azure サブスクリプションに接続する必要があります。 **サーバー エクスプローラー** を開きます。 **[Azure]**  >  を右クリックし、 **[Microsoft Azure サブスクリプションへの接続]** を選択し、Azure サブスクリプションにサインインします。  

2. Azure にジョブを送信するには、クエリ エディターに移動し、 **[Azure に送信]** を選択します。  

3. ポップアップ ウィンドウが表示されます。 既存の Stream Analytics Edge ジョブを更新するか、新しく作成することを選択してください。 既存のジョブを更新すると、すべてのジョブの構成が置き換えられます。このシナリオでは、新しいジョブを発行します。 **[新しい Azure Stream Analytics ジョブの作成]** を選択し、**MyASAEdgeJob** のようなジョブの名前を入力し、必要な **[サブスクリプション]** 、 **[リソース グループ]** 、および **[場所]** を選択し、 **[送信]** を選択します。

   ![Visual Studio から Azure に Stream Analytics ジョブを送信する](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   これで Stream Analytics Edge ジョブが作成されました。 [IoT Edge でのジョブの実行に関するチュートリアル](stream-analytics-edge.md)を参照して、デバイスへの配置方法を確認できます。 

## <a name="manage-the-job"></a>ジョブを管理する 

サーバー エクスプローラーで、ジョブの状態とジョブ ダイアグラムを表示できます。 **サーバー エクスプローラー**で、 **[Stream Analytics]** から、Stream Analytics Edge ジョブをデプロイしたサブスクリプションとリソース グループを展開します。 状態が **[作成済み]** になっている MyASAEdgejob が表示されます。 ジョブ ノードを展開し、ノードをダブルクリックしてジョブ ビューを開きます。

![サーバー エクスプローラーのジョブ管理オプション](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
ジョブの表示ウィンドウでは、ジョブの更新、ジョブの削除、Azure portal でのジョブのオープンなどの操作を実行できます。

![Visual Studio でのジョブ ダイアグラムとその他のオプション](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>次の手順

* [Azure IoT Edge の詳細](../iot-edge/about-iot-edge.md)
* [ASA on IoT Edge チュートリアル](../iot-edge/tutorial-deploy-stream-analytics.md)
* [このアンケートを使用してフィードバックをチームに送信する](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
