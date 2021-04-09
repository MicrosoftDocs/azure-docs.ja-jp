---
title: Logic Apps と Power Automate を使用した Video Indexer コネクタに関するチュートリアル。
description: このチュートリアルでは、Logic Apps と Power Automate を使用して Video Indexer コネクタの新しいエクスペリエンスと収益化の可能性を開く方法について説明します。
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f3504ca4a706e92081209f4eaaa86af9f71c52b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880913"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>チュートリアル: Logic Apps および Power Automate と共に Video Indexer コネクタを使用する

Azure Media Services [Video Indexer v2 REST API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) では、サーバー間の通信とクライアントとサーバーの間の通信の両方がサポートされています。Video Indexer のユーザーは、この API を利用して、ビデオとオーディオの分析情報を各自のアプリケーション ロジックに簡単に統合し、新しいエクスペリエンスと収益化の可能性を開くことができます。

統合をさらに容易にするために、Microsoft では、この API との互換性を備えた  [Logic Apps](https://azure.microsoft.com/services/logic-apps/)  および  [Power Automate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)  コネクタをサポートしています。 コネクタを使用することで、コードを 1 行も記述せずに、大量のビデオ ファイルやオーディオ ファイルにインデックスを付け、そこから効果的に分析情報を抽出するカスタム ワークフローを設定できます。 さらに、統合のためにコネクタを使用すると、ワークフローの正常性を確認しやすくなり、簡単にデバッグできるようになります。  

Video Indexer コネクタの使用をすぐに開始できるように、設定可能な Logic Apps および Power Automate ソリューションの例について順を追って説明します。 このチュートリアルでは、Logic Apps を使用してフローを設定する方法を紹介しています。 ただし、エディターと機能はどちらのソリューションでもほぼ同一のため、図と説明は Logic Apps と Power Automate の両方に当てはまります。

このチュートリアルで取り上げた "自動的にビデオをアップロードしてインデックスを付ける" シナリオは、連携する 2 つの異なるフローから成ります。 
* 最初のフローは、Azure Storage アカウントで BLOB が追加または変更されたときにトリガーされます。 インデックス付けの操作が完了したら通知を送信するためのコールバック URL と共に、新しいファイルを Video Indexer にアップロードします。 
* 2 番目のフローは、コールバック URL に基づいてトリガーされ、抽出された分析情報を Azure Storage の JSON ファイルに保存し直します。 この 2 つのフローによるアプローチを使用して、大きなファイルの非同期的なアップロードとインデックス付けを効果的にサポートします。 

このチュートリアルでは、ロジック アプリを使用して次の作業を行う方法を紹介しています。

> [!div class="checklist"]
> * ファイルのアップロード フローを設定する
> * JSON の抽出フローを設定する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* まず、Video Indexer アカウントと、[API キーによる API へのアクセス](video-indexer-use-apis.md)が必要になります。 
* また、Azure Storage アカウントも必要です。 Storage アカウントのアクセス キーをメモしてください。 2 つのコンテナーを作成します。1 つはビデオを格納するコンテナーで、もう 1 つは Video Indexer によって生成される分析情報を格納するコンテナーです。  
* 次に、Logic Apps または Power Automate で (どちらを使用しているかに応じて)、2 つの異なるフローを開く必要があります。 

## <a name="set-up-the-first-flow---file-upload"></a>1 つ目のフローを設定する - ファイルのアップロード   

最初のフローは、BLOB が Azure Storage コンテナーに追加されるたびにトリガーされます。 トリガーされると、Video Indexer にビデオをアップロードしてインデックスを付けるために使用できる SAS URI が作成されます。 このセクションでは、次のフローを作成します。 

![ファイルのアップロード フロー](./media/logic-apps-connector-tutorial/file-upload-flow.png)

最初のフローを設定するには、Video Indexer API キーと Azure Storage 資格情報を指定する必要があります。 

![Azure BLOB ストレージ](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![接続名と API キー](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> 以前に Azure Storage アカウントまたは Video Indexer アカウントをロジック アプリに接続している場合は、接続の詳細が保存されて、自動的に接続されます。 <br/>接続を編集するには、Azure Storage の下部にある **[接続の変更]** (ストレージのウィンドウ) をクリックするか、[Video Indexer] (プレーヤーのウィンドウ) アクションをクリックします。

ご使用の Azure Storage アカウントと Video Indexer アカウントに接続できたら、**Logic Apps デザイナー** で [BLOB が追加または変更されたとき] トリガーを探して選択します。

ビデオ ファイルの格納場所となるコンテナーを選択します。 

![コンテナーを選択できる [BLOB が追加または変更されたとき] ダイアログ ボックスを示すスクリーンショット。](./media/logic-apps-connector-tutorial/container.png)

次に、[パスを使用して SAS URI を作成する] アクションを探して選択します。 アクションのダイアログで、[動的コンテンツ] のオプションから [List of Files Path]\(ファイル パスの一覧\) を選択します。  

さらに、新しい [共有アクセスのプロトコル] パラメーターを追加します。 パラメーターの値に HttpsOnly を選択します。

![パスを使用した SAS URI](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

[自分のアカウントの場所](regions.md)と[アカウント ID](./video-indexer-use-apis.md#account-id)  を入力して、Video Indexer のアカウント トークンを取得します。

![アカウントのアクセス トークンを取得する](./media/logic-apps-connector-tutorial/account-access-token.png)

[Upload video and index]\(ビデオのアップロードとインデックス付け\) では、必須パラメーターとビデオの URL を入力します。 [新しいパラメーターの追加] を選択し、[コールバック URL] を選択します。 

![アップロードとインデックス付け](./media/logic-apps-connector-tutorial/upload-and-index.png)

ここでは、コールバック URL を空白のままにします。 これは、コールバック URL が作成される 2 番目のフローが完了してから追加します。 

他のパラメーターは、既定値を使用することも、自分のニーズに合わせて設定することもできます。 

アップロードとインデックス付けが完了したら、 **[保存]** をクリックして、分析情報を抽出する 2 番目のフローの構成に移りましょう。 

## <a name="set-up-the-second-flow---json-extraction"></a>2 つ目のフローを設定する - JSON の抽出  

最初のフローにおけるアップロードとインデックス付けが完了したら、適切なコールバック URL を使用して HTTP 要求が送信され、2 番目のフローがトリガーされます。 その結果、Video Indexer によって生成された分析情報が取得されます。 この例では、インデックス付けジョブの出力が Azure Storage に格納されます。  ただし、この出力を使用して何を実行するかはユーザーの自由です。  

2 番目のフローは、最初のフローとは別に作成します。 

![JSON の抽出フロー](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

このフローを設定するには、再度、Video Indexer API キーと Azure Storage 資格情報を指定する必要があります。 最初のフローで更新したのと同じパラメーターを更新する必要があります。 

トリガーには、[HTTP POST の URL] フィールドが表示されます。 この URL はフローを保存するまで生成されませんが、最終的にこの URL が必要になります。 ここにはもう一度戻ってきます。 

[自分のアカウントの場所](regions.md)と[アカウント ID](./video-indexer-use-apis.md#account-id)  を入力して、Video Indexer のアカウント トークンを取得します。  

[Get Video Index]\(ビデオ インデックスの取得\) アクションに移動し、必須パラメーターを入力します。 [ビデオ ID] には、次の式を入力します: triggerOutputs()['queries']['id'] 

![ビデオ インデクサー アクションの情報](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

この式は、コネクタに対して、トリガーの出力からビデオ ID を取得するように指示します。 この場合、トリガーの出力は、最初のトリガーの [Upload video and index]\(ビデオのアップロードとインデックス付け\) の出力になります。 

[BLOB の作成] アクションに移動し、分析情報を保存するフォルダーのパスを選択します。 作成する BLOB の名前を設定します。 [BLOB コンテンツ] には、次の式を入力します: body('Get_Video_Index') 

![[BLOB の作成] アクション](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

この式は、このフローから [Get Video Index]\(ビデオ インデックスの取得\) アクションの出力を受け取ります。 

**[フローの保存]** をクリックします。 

フローを保存すると、トリガーに HTTP POST の URL が作成されます。 トリガーから URL をコピーします。 

![URL トリガーを保存する](./media/logic-apps-connector-tutorial/save-url-trigger.png)

次に、1 つ目のフローに戻り、[Upload video and index]\(ビデオのアップロードとインデックス付け\) アクションの [コールバック URL] パラメーターに URL を貼り付けます。 

両方のフローが保存されていることを確認したら、準備完了です。 

Azure BLOB コンテナーにビデオを追加して、新しく作成した Logic Apps または Power Automate ソリューションを試すことができます。数分後に分析情報が出力先フォルダーに表示されることを確認してください。 

## <a name="generate-captions"></a>キャプションを生成する

[Video Indexer と Logic Apps を使用してキャプションを生成する方法](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198)を示す手順についてのブログを参照してください。 

この記事には、OneDrive にコピーすることで自動的にビデオにインデックスを付ける方法と、Video Indexer によって生成されたキャプションを OneDrive に格納する方法も示されています。
 
## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了した後も、必要に応じて、引き続きこの Logic Apps または Power Automate ソリューションを稼働させておくことができます。 ただし、稼働を止めて課金されるのを避けるには、Power Automate を使用している場合は両方のフローをオフにしてください。 Logic Apps を使用している場合も、両方のフローを無効にしてください。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Video Indexer コネクタの例を 1 つだけ紹介しました。 Video Indexer によって提供される任意の API 呼び出しに、Video Indexer コネクタを使用できます。 たとえば、分析情報のアップロードと取得、結果の変換、埋め込み可能なウィジェットの取得のほか、モデルのカスタマイズも行うこともできます。 また、ファイル リポジトリの更新や送信されたメールなど、さまざまなソースに基づいてこれらのアクションをトリガーすることもできます。 次に、その結果を使用して関連するインフラストラクチャやアプリケーションを更新したり、任意の数のアクション項目を生成したりできます。  

> [!div class="nextstepaction"]
> [Video Indexer API の使用](video-indexer-use-apis.md)

その他のリソースについては、[Video Indexer に関するドキュメント](/connectors/videoindexer-v2/)を参照してください。