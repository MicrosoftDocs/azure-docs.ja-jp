---
title: Azure Media Services イベントを Azure Log Analytics に格納する
titleSuffix: Azure Media Services
description: Azure Media Services イベントを Azure Log Analytics に格納する方法を学習します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: dfd7b3dcc7e27dd1251c35c6272b5aa7f8036006
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88941795"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>チュートリアル:Azure Media Services イベントを Azure Log Analytics に格納する

## <a name="azure-media-services-events"></a>Azure Media Services イベント

Azure Media Services v3 では、[Azure Event Grid](media-services-event-schemas.md) でイベントが生成されます。 さまざまな方法でイベントをサブスクライブし、データ ストアに格納することができます。 このチュートリアルでは、[ログ アプリ フロー](https://azure.microsoft.com/services/logic-apps/)を使用して Media Services イベントをサブスクライブします。 イベントごとにトリガーされるロジック アプリにより、イベントの本体が Azure Log Analytics に格納されます。 イベントが Azure Log Analytics に格納されたら、他の Azure サービスを使用して、これらのイベントについてのダッシュボード、モニター、アラートを作成できますが、このチュートリアルでは説明しません。

> [!NOTE]
> オンプレミスのエンコーダーとしての FFmpeg の使用について既に理解していると役に立ちます。  そうでなくても問題はありません。 以下では、ビデオをストリーミングするためのコマンド ラインと手順について説明します。

学習内容:

> [!div class="checklist"]
> * コードを使用しないでロジック アプリ フローを作成する
> * Azure Media Services イベント トピックをサブスクライブする
> * イベントを解析して Azure Log Analytics に格納する
> * Azure Log Analytics イベントのクエリを実行する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

> * [Azure サブスクリプション](how-to-set-azure-subscription.md)
> * [Media Services](create-account-howto.md) アカウントとリソース グループ。
> * お使いの OS 用の [FFmpeg](https://ffmpeg.org/download.html) のインストール。
> * [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) ワークスペース

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>ロジック アプリで Media Services イベントをサブスクライブする

1. [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) ワークスペースをまだ作成していない場合は、Azure portal で作成します。 ワークスペース ID とキーの 1 つが必要になるので、ブラウザー ウィンドウを開いたままにしておきます。 次に、別のタブまたはウィンドウでポータルを開きます。

1. Azure Media Services アカウントに移動して、 **[イベント]** を選択します。 これにより、Azure Media Services イベントをサブスクライブするためのすべての方法が表示されます。
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services ポータル](media/tutorial-events-log-analytics/select-events-01a.png)

1. ロジック アプリを作成するには、**Logic Apps アイコン**を選択します。 これにより開くロジック アプリ デザイナーでは、イベントをキャプチャして Log Analytics にプッシュするフローを作成できます。 
    > [!div class="mx-imgBorder"]
    > ![ロジック アプリの作成](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. **[+] アイコン**を選択し、使用するテナントを選択して、[サインイン] を選択します。 Microsoft のサインイン プロンプトが表示されます。
    > [!div class="mx-imgBorder"]
    > ![Azure Event Grid に接続する](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
テナントを選択する![](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. **[続行]** を選択して、Media Services イベントをサブスクライブします。
    > [!div class="mx-imgBorder"]
    > ![Azure Event Grid に接続した](media/tutorial-events-log-analytics/select-continue-04.png)

1. **[リソースの種類]** の一覧で、"Microsoft.Media.MediaServices" を見つけます。
    > [!div class="mx-imgBorder"]
    >![Azure Media Services リソース イベント](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. **[Event Type item]\(イベントの種類の項目\)** を選択します。 Azure Media Services によって生成されるすべてのイベントの一覧が表示されます。 追跡するイベントを選択できます。複数のイベントの種類を追加できます。 (後で、各イベントの種類が個別の Log Analytics ログに格納され、イベントの種類の名前が Log Analytics ログの名前に動的に反映されるように、ロジック アプリ フローに小さな変更を行います)。
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services イベントの種類](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. **[名前を付けて保存]** を選択します。

1. ロジック アプリに名前を付けます。  リソース グループは既定で選択されています。 他の設定はそのままにして、 **[作成]** を選択します。  Azure のホーム画面に戻ります。
    > [!div class="mx-imgBorder"]
    > ![ロジック アプリの名前付けインターフェイス](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>アクションの作成

イベントをサブスクライブしたので、アクションを作成します。

1. ポータルがホーム画面に戻っている場合は、[すべてのリソース] でアプリ名を検索して、作成したロジック アプリに戻ります。

1. アプリを選択して、 **[ロジック アプリ デザイナー]** を選択します。 デザイナー ペインが開きます。

1. **[+ 新しいステップ]** を選択します。

1. イベントを Azure Log Analytics サービスにプッシュする必要があるため、"Azure Log Analytics" を検索し、"Azure Log Analytics データ コレクター" を選択します。
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics データ コレクター](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Log Analytics ワークスペースに接続するには、ワークスペース ID とエージェント キーが必要です。 新しいタブまたはウィンドウで Azure portal を開き、このチュートリアルを始める前に作成した Log Analytics ワークスペースに移動します。
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics ワークスペース ID](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. 左側のメニューで **[エージェント管理]** を見つけて選択します。 これにより、生成されたエージェント キーが表示されます。
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics のエージェント管理](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. "*ワークスペース ID*" をコピーします。
    > [!div class="mx-imgBorder"]
    > ![ワークスペース ID をコピーする](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. ブラウザーの他のタブまたはウィンドウで開いた Azure Log Analytics データ コレクターで、 **[データの送信]** を選択し、接続の名前を指定した後、 **[ワークスペース ID]** フィールドに "*ワークスペース ID*" を 貼り付けます。

1. ブラウザーでワークスペースのタブまたはウィンドウに戻り、"*ワークスペース キー*" をコピーします。
    > [!div class="mx-imgBorder"]
    > ![エージェント管理の主キー](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. ブラウザーの他のタブまたはウィンドウで、"*ワークスペース キー*" を **[ワークスペース キー]** フィールドに貼り付けます。

1. **［作成］** を選択します 次に、JSON 要求の本文とカスタム ログの名前を作成します。

1. **[JSON Request body]\(JSON 要求本文\)** フィールドを選択します。  **動的なコンテンツを追加する**ためのリンクが表示されます。

1. **[動的なコンテンツの追加]** を選択し、 **[トピック]** を選択します。

1. **[カスタム ログの名前]** についても同じようにします。
    > [!div class="mx-imgBorder"]
    > ![選択されたトピック](media/tutorial-events-log-analytics/topic-selected.png)

1. ロジック アプリの **[コード ビュー]** を選択します。 inputs と Log-Type の行を探します。
    > [!div class="mx-imgBorder"]
    > ![コード ビューの 2 つの行](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. `body` の値を `"@triggerBody()?['topic']"` から `"@{triggerBody()}"` に変更します。 これは、メッセージ全体を Log Analytics に解析するためのものです。

1. `Log-Type` を `"@triggerBody()?['topic']"` から `"@replace(triggerBody()?['eventType'],'.','')"` に変更します。 (これにより、Log Analytics のログ名で許可されていない "." が置き換えられます)。
    > [!div class="mx-imgBorder"]
    > ![変更後のロジック アプリの json](media/tutorial-events-log-analytics/changed-lines.png)

1. **[保存]** を選択します。

1. 確認するには、 **[ロジック アプリ デザイナー]** を選択します。
    > [!div class="mx-imgBorder"]
    > ![本文と関数のステップを確認する](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. リソース グループ内のすべてのリソースを調べると、一覧には 1 つのロジック アプリと 2 つのロジック アプリ API コネクタ (1 つはイベント用で、もう 1 つは Log Analytics 用) が表示されています。 Event Grid のシステム トピックの詳細については、[Event Grid のシステム トピック](https://docs.microsoft.com/azure/event-grid/system-topics)に関する記事を参照してください。
    > [!div class="mx-imgBorder"]
    > ![リソース グループ内のすべての新しいリソースを表示する](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>テスト

実際の動作をテストするには、Azure Media Services でライブ イベントを作成します。 RTMP ライブ イベントを作成し、ffmpeg を使用して、.mp4 サンプル ファイルに基づいて "ライブ" ストリームをプッシュします。 イベントが作成された後、RTMP 取り込み URL を取得します。

1. Media Services アカウントから、 **[ライブ ストリーミング]** を選択します。
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services のライブ イベントを作成する](media/tutorial-events-log-analytics/live-event.png)

1. **[ライブ イベントの追加]** を選択します。

1. **[ライブ イベント名]** フィールドに名前を入力します。 ( **[説明]** フィールドは省略可能です)。

1. **[Standard]** クラウド エンコードを選択します。

1. [エンコード プリセット] で **[既定の 720p]** を選択します。

1. **[RTMP]** 入力プロトコルを選択します。

1. [永続的な入力 URL] で **[はい]** を選択します。

1. イベントが作成されたときにイベントを開始するかどうかでは、 **[はい]** を選択します。

    > [!WARNING]
    > [はい] を選択すると、課金が開始されます。  FFmpeg でストリーミングを開始する "*直前*" までストリームの開始を待つ場合は、 **[いいえ]** を選択し、忘れずにその時点でライブ イベントを開始します。

    > [!div class="mx-imgBorder"]
    > ![ライブ イベントの設定](media/tutorial-events-log-analytics/live-event-settings.png)

1. **[コンテンツまたはファイルを使用するためのすべての権限を自分が持っており、...]** チェック ボックスをオンにします。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. 設定を確認して **[作成]** を選択します。  ライブ イベントの一覧が表示され、ライブイ ベントの取り込み URL が示されます。

1. **[取り込み URL]** をクリップボードにコピーします。

1. 一覧で**ライブ イベント**を選択して、プロデューサー ビューを表示します。

### <a name="stream-with-ffmpeg-cli"></a>FFmpeg CLI を使用したストリーミング

1. 次のコマンド ラインを使用します。

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. `<ingestURL>` は、クリップボードにコピーした取り込み URL に変更します。
1. `<localpathtovideo>` は、FFmpeg からストリーミングするファイルのローカル パスに変更します。
1. 末尾に一意の名前 (たとえば `mystream`) を追加します。
1. テスト ソース ファイルおよび他のすべてのシステム変数を反映するように、コマンド ラインを調整します。
1. コマンドを実行します。 数秒後に、"プロデューサー ビュー" プレーヤーでストリーミングが開始されます。 (ビデオが自動的に表示されない場合は、プレーヤーを更新してください)。

    > [!div class="mx-imgBorder"]
    > ![プロデューサー プレビュー プレーヤーでビデオの適切な取り込みを確認する](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>イベントを確認する

ライブ ストリームでは、Azure Media Services によってロジック アプリ フローをトリガーするさまざまなイベントが生成されています。 検証するには、ロジック アプリに移動し、Media Services からのイベントによってトリガーが起動されているかどうかを確認します。

1. ロジック アプリの [概要] ページに移動すると、正常に完了した "実行履歴" 一覧表示ジョブが表示されるはずです。
    > [!div class="mx-imgBorder"]
    > ![ロジック アプリでのジョブの実行が成功したことを確認する](media/tutorial-events-log-analytics/run-history.png)

1. 成功したジョブを選択します。 実行時のジョブの詳細が表示されます。
1. **[データの送信]** を選択してそれを展開します。 この場合は、`MicrosoftMediaLiveEventEncoderConnected` イベントで、それがキャプチャされたことと解析された本文が示されます。 これが、Azure Log Analytics ワークスペースにプッシュされます。
    > [!div class="mx-imgBorder"]
    > ![データの送信を表示する](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>ログを確認する

1. 前に作成した Log Analytics ワークスペースに移動します。

1. **[ログ]** を選択します。
1. クエリの例のポップアップを閉じます。
1. カスタム ログの一覧が表示されます。 下矢印を選択してそれを展開します。 イベント名 `MicrosoftMediaLiveEventEncoderConnected` が表示されます。
1. イベント名を選択して展開します。
1. "目" のアイコンを選択すると、クエリ結果のプレビューが表示されます。
1. **[クエリ エディターで表示]** を選択し、**TimeGenerated UTC** の一覧で項目を選択して展開し、生データを表示します。

![Log Analytics で詳細なイベント出力を表示する](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>リソースを削除する

このチュートリアルの間に作成したリソースを続けて使用しない場合は、リソース グループ内のすべてのリソースを削除してください。そうしないと、引き続き課金されます。

## <a name="next-steps"></a>次のステップ

さまざまなクエリを作成して保存することができます。 これらを [Azure ダッシュボード](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)に追加できます。
