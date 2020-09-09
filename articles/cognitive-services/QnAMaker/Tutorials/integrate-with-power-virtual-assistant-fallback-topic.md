---
title: チュートリアル:Power Virtual Agents との統合 - QnA Maker
description: このチュートリアルでは、アクティブ ラーニングを使用してナレッジ ベースの質を向上させます。 既存の質問を削除したり変更したりせずに、レビュー、承諾または却下、あるいは追加を行います。
ms.topic: tutorial
ms.date: 06/08/2020
ms.openlocfilehash: f1d51f6ad8892252161238eb71fbb02f463463fd
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84635389"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>チュートリアル:Power Virtual Agents にナレッジ ベースを追加する
ナレッジ ベースから回答を提供するように [Power Virtual Agents](https://powervirtualagents.microsoft.com/) ボットを作成して拡張します。

このチュートリアルでは、以下の内容を学習します。

<!-- green checkmark -->
> [!div class="checklist"]
> * Power Virtual Agents ボットを作成する
> * システム フォールバック トピックを作成する
> * アクションとしての QnA Maker を Power Automate フローとしてトピックに追加する
> * Power Automate ソリューションを作成する
> * Power Automate フローをソリューションに追加する
> * Power Virtual Agents を公開する
> * Power Virtual Agents をテストし、QnA Maker ナレッジ ベースから回答を受け取る

## <a name="integrate-an-agent-with-a-knowledge-base"></a>エージェントとナレッジ ベースを統合する

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) を使用すると、チームは、ガイド付きでコードのないグラフィカル インターフェイスを使用して、強力なボットを作成できます。 データ サイエンティストや開発者は必要ありません。

Power Virtual Agents で、アクションを実行してユーザーの質問に回答するために、一連のトピック (サブジェクト領域) を持つエージェントを作成します。 回答が見つからなかった場合は、システム フォールバックから回答を返すことができます。

トピックのアクションの一部として、または "*システム フォールバック*" トピック パスの一部として、ナレッジ ベースに質問を送信するようにエージェントを構成します。 どちらもアクションを使用してナレッジ ベースに接続し、回答を返します。

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate で `GenerateAnswer` アクションに接続する

エージェントをナレッジ ベースに接続するには、Power Automate を使用してアクションを作成します。 Power Automate では、QnA Maker の `GenerateAnswer` API に接続するプロセス フローが提供されます。

フローを設計して保存した後は、Power Automate ソリューションから利用できます。 そのソリューションを、エージェントでアクションとして使用します。

## <a name="connect-an-agent-to-your-knowledge-base"></a>エージェントをナレッジ ベースに接続する

Power Virtual Agents のエージェントを QnA Maker のナレッジ ベースに接続する手順の概要を次に示します。

* [QnA Maker](https://www.qnamaker.ai/) ポータルで、次のようにします。
    * ナレッジ ベースを作成して公開します。
    * ナレッジ ベースの詳細 (ID、ランタイム エンドポイント キー、ランタイム エンドポイント ホストなど) をコピーします。
* [Power Virtual Agents](https://powerva.microsoft.com/) ポータルで、次のようにします。
    * エージェントのトピックを作成します。
    * (Power Automate フローに対して) アクションを呼び出します。
* [Power Automate](https://us.flow.microsoft.com/) ポータルで、次のようにします。
    * _[Generate answer using QnA Maker]\(QnA Maker を使用して回答を生成\)_ テンプレートを検索します
    * テンプレートを使用して、[QnA Maker の GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/) を使用するようにフローを構成します。
        * QnA Maker によって公開されたナレッジ ベース情報:
            * ナレッジ ベース ID
            * QnA Maker リソース エンドポイント ホスト
            * QnA Maker リソース エンドポイント キー
        * 入力 - ユーザー クエリ
        * 出力 - ナレッジ ベースの回答
    * ソリューションを作成してフローを追加するか、または既存のソリューションにフローを追加します。
* Power Virtual Agents に戻ります。
    * ソリューションの出力をトピックのメッセージとして選択します。

## <a name="create-and-publish-a-knowledge-base"></a>ナレッジ ベースの作成と公開

1. [クイックスタート](../Quickstarts/create-publish-knowledge-base.md)に従ってナレッジ ベースを作成します。 ボットの作成に関して、最後のセクションを完了しないでください。 代わりに、このチュートリアルを使用して、Power Virtual Agents でボットを作成します。

    > [!div class="mx-imgBorder"]
    > ![公開されたナレッジ ベース設定のスクリーンショット](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    [QnA Maker](https://www.qnamaker.ai/) ポータルの **[設定]** ページにある公開されたナレッジ ベースの設定を入力します。 この情報は、QnA Maker の `GenerateAnswer` 接続を構成する [Power Automate 手順](#create-a-power-automate-flow-to-connect-to-your-knowledge-base)で必要になります。

1. QnA Maker ポータルの **[設定]** ページで、エンドポイント キー、エンドポイント ホスト、およびナレッジ ベース ID を見つけます。

## <a name="create-an-agent-in-power-virtual-agents"></a>Power Virtual Agents でエージェントを作成する

1. [Power Virtual Agents にサインインします](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409)。 学校または職場の電子メール アカウントを使用します。

1. これが最初のボットである場合は、エージェントの **[ホーム]** ページが表示されます。 これが最初のボットでない場合は、ページの右上の領域からボットを選択し、 **[+ New Bot]\(+ 新しいボット\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![Power Virtual Agents のホーム ページのスクリーンショット](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>ボットで提供されるトピック

エージェントは、トピック コレクションを使用して、サブジェクト領域の質問に回答します。 このチュートリアルには、エージェントには、ユーザー トピックとシステム トピックに分かれた多くのトピックが用意されています。

左側のナビゲーションから **[トピック]** を選択すると、ボットによって提供されるトピックが表示されます。

> [!div class="mx-imgBorder"]
> ![エージェントで提供されているトピックのスクリーンショット](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>システム フォールバック トピックを作成する

エージェントはどのトピックからでもナレッジ ベースに接続できますが、このチュートリアルでは "*システム フォールバック*" トピックを使用します。 フォールバック トピックは、エージェントが回答を見つけられなかった場合に使用されます。 エージェントは、ユーザーのテキストを QnA Maker の `GenerateAnswer` API に渡し、ナレッジ ベースから回答を受け取り、それをメッセージとしてユーザーに表示します。

1. [Power Virtual Agents](https://powerva.microsoft.com/#/) ポータルの右上隅にある **[設定]** (歯車アイコン) を選択します。 次に、 **[システム フォールバック]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![システム フォールバックの Power Virtual Agents メニュー項目のスクリーンショット](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. **[+ 追加]** を選択して、システム フォールバック トピックを追加します。

    > [!div class="mx-imgBorder"]
    > ![フォールバック トピックを追加するスクリーンショット。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. トピックが追加されたら、 **[Go to Fallback topic]\(フォールバック トピックに移動\)** を選択し、作成キャンバスでフォールバック トピックを作成します。

    > [!TIP]
    > フォールバック トピックに戻る必要がある場合は、 **[トピック]** セクションで **[システム]** トピックの一部として参照できます。

## <a name="use-the-authoring-canvas-to-add-an-action"></a>作成キャンバスを使用してアクションを追加する

Power Virtual Agents の作成キャンバスを使用して、フォールバック トピックをナレッジ ベースに接続します。 トピックは、認識されていないユーザー テキストから始まります。 そのテキストを QnA Maker に渡して、回答をメッセージとして表示するアクションを追加します。 回答を表示する最後の手順は、このチュートリアルで後ほど[別個の手順](#add-your-solutions-flow-to-power-virtual-agents)として処理されます。

このセクションでは、フォールバック トピックの会話フローを作成します。

1. 新しいフォールバック アクションには、あらかじめ会話フローの要素が含まれていることがあります。 **[Options]\(オプション\)** メニューを選択して、 **[Escalate]\(エスカレーション\)** 項目を削除します。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="[削除] オプションが強調表示されている会話フローの部分的なスクリーンショット。":::

1. **[メッセージ]** ボックスにつながる **+** コネクタを選択し、 **[Call an action]\(アクションの呼び出し\)** を選択します。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="[Call an action]\(アクションの呼び出し\) の部分的なスクリーンショット":::

1. **[Create a flow]\(フローの作成\)** を選択します。 このプロセスにより、Power Automate ポータルに移動します。

    > [!div class="mx-imgBorder"]
    > ![[Create a flow]\(フローの作成\) のスクリーンショット](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    Power Automate が開き、新しいテンプレートが表示されます。 この新しいテンプレートは使用しません。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="新しいフロー テンプレートを備えた Power Automate の部分的なスクリーンショット。":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>ナレッジ ベースに接続するための Power Automate フローを作成する

次の手順では、以下を行う Power Automate フローを作成します。
* 入力されたユーザー テキストを受け取り、QnA Maker に送信します。
* 上位の応答をエージェントに返します。

1. **Power Automate** で、左側のナビゲーションから **[テンプレート]** を選択します。 ブラウザー ページから移動するかどうかを確認するメッセージが表示されたら、[移動] を受け入れます。

1. テンプレート ページで、 **[Generate answer using QnA Maker]\(QnA Maker を使用して回答を生成\)** テンプレートを検索し、このテンプレートを選択します。 このテンプレートには、ナレッジ ベースの設定を使用して QnA Maker を呼び出して上位の回答を返すための手順がすべて含まれています。

1. QnA Maker フローの新しい画面で、 **[続行]** を選択します。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="[続行] ボタンが強調表示されている QnA Maker テンプレート フローの部分的なスクリーンショット。":::

1. **[Generate Answer]\(回答の生成\)** アクション ボックスを選択し、「[ナレッジ ベースの作成と公開](#create-and-publish-a-knowledge-base)」というタイトルの付いた前のセクションからの QnA Maker 設定を入力します。 次の図の **[サービス ホスト]** は、ナレッジ ベースのホスト **Host** を参照し、`https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker` の形式になっています。


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="[続行] ボタンが強調表示されている QnA Maker テンプレート フローの部分的なスクリーンショット。":::

1. **[保存]** を選択してフローを保存します。

## <a name="create-a-solution-and-add-the-flow"></a>ソリューションを作成してフローを追加する

エージェントがフローを見つけて接続するには、そのフローが Power Automate ソリューションに含まれている必要があります。

1. 引き続き Power Automate ポータルで、左側のナビゲーションから **[Solutions]\(ソリューション\)** を選択します。

1. **[+ New solution]\(+ 新しいソリューション\)** を選択します。

1. 表示名を入力します。 ソリューションの一覧には、組織または学校のすべてのソリューションが含まれています。 フィルター処理して自分のソリューションだけを表示するのに役立つ名前付け規則を選択します。 たとえば、次のように、ソリューション名に自分の電子メールのプレフィックスを付けることができます。`jondoe-power-virtual-agent-qnamaker-fallback`

1. 選択肢の一覧から公開元を選択します。

1. 名前とバージョンには、既定値をそのまま使用します。

1. **[Create]\(作成\)** を選択して、このプロセスを完了します。

## <a name="add-your-flow-to-the-solution"></a>フローをソリューションに追加する

1. ソリューションの一覧から、先ほど作成したソリューションを選択します。 リストの一番上に表示されていると思います。 見つからない場合は、ソリューション名の一部になっている電子メール名で検索してください。

1. ソリューションで **[+ Add existing]\(+ 既存を追加\)** を選択し、一覧から **[Flow]\(フロー\)** を選択します。

1. **[ソリューション外部]** リストからフローを検索し、次に **[追加]** を選択してプロセスを完了します。 多数のフローがある場合は、 **[Modified]\(変更\)** 列を見て最新のフローを探してください。

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>ソリューションのフローを Power Virtual Agents に追加する

1. Power Virtual Agents のエージェントが表示されているブラウザー タブに戻ります。 作成キャンバスがまだ開いていると思います。

1. フローに新しいステップを挿入するには、 **[メッセージ]** アクション ボックスの上方にある **+** コネクタを選択します。 次に、 **[Call an action]\(アクションの呼び出し\)** を選択します。

1. **[フロー]** ポップアップ ウィンドウで、 **[Generate answers using QnA Maker knowledge base...]\(QnA Maker ナレッジ ベースを使用して回答を生成する\)** という名前の新しいフローを選択します。新しいアクションがフローに表示されます。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="QnA Maker フローを追加した後の、Power Virtual Agent のトピック会話キャンバスの部分的なスクリーンショット。":::

1. 入力変数を QnA Maker アクションに正しく設定するには、 **[変数を選択]** を選択してから、 **[bot.UnrecognizedTriggerPhrase]** を選択します。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="Power Virtual Agent のトピック会話キャンバスの部分的なスクリーンショットで、入力変数が選択されている。":::


1. 出力変数を QnA Maker アクションに正しく設定するには、 **[メッセージ]** アクションで、 **[UnrecognizedTriggerPhrase]** を選択し、次に変数を挿入するアイコン `{x}` を選択してから、 **[FinalAnswer]** を選択します。

1. コンテキスト ツール バーから、 **[Save]\(保存\)** を選択して、このトピックに対する作成キャンバスの詳細を保存します。

最終的なエージェント キャンバスを次に示します。

> [!div class="mx-imgBorder"]
> ![最終的なエージェント キャンバスのスクリーンショット](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>エージェントをテストする

1. テスト ペインで、 **[Track between topics]\(トピック間を追跡する\)** を切り替えます。 これにより、1 つのトピック内だけでなく、トピック間の進行を観察できます。

1. 次の順序でユーザー テキストを入力して、エージェントをテストします。 成功したステップは、作成キャンバスに緑色のチェック マークでレポートされます。

    |質問の順序|テスト質問|目的|
    |--|--|--|
    |1|こんにちは|会話を開始します。|
    |2|Store hours|サンプル トピック。 これは、追加の作業を行わなくても自動的に構成されます。|
    |3|はい|`Did that answer your question?` への応答。|
    |4|[非常に良い]|`Please rate your experience.` への応答。|
    |5|はい|`Can I help with anything else?` への応答。|
    |6|クエリ予測のスループットのパフォーマンスを向上させるにはどうすればよいですか。|この質問により、フォールバック アクションがトリガーされます。それにより、回答するためのテキストがナレッジ ベースに送信されます。 次に、回答が表示されます。 個々のアクションの緑色のチェックマークは、各アクションが成功したことを示します。|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="アクションが成功したことを示す緑色のチェックマークがキャンバスに表示されているチャット ボットのスクリーンショット。":::

## <a name="publish-your-bot"></a>ボットを公開する

学校または組織のすべてのメンバーがエージェントを利用できるようにするには、それを公開する必要があります。

1. 左側のナビゲーションで、 **[Publish]\(公開\)** を選択します。 次に、ページで **[Publish]\(公開\)** を選択します。

1. デモ Web サイトでボットを試してみてください ( **[Publish]\(公開\)** の下にあるリンクを探してください)。

    新しい Web ページがボットと共に表示されます。 `How can I improve the throughput performance for query predictions?` という同じテスト質問をボットにたずねます。

    > [!div class="mx-imgBorder"]
    > ![最終的なエージェント キャンバスのスクリーンショット](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>ボットを共有する

デモ Web サイトを共有するには、それをチャネルとして構成します。

1. 左側のナビゲーションで、 **[Manage]\(管理\)**  >  **[Channels]\(チャネル\)** を選択します。

1. チャネル一覧から **[Demo website]\(デモ Web サイト\)** を選択します。

1. リンクをコピーし、 **[Save]\(保存\)** を選択します。 学校または組織のメンバー宛てのメールにデモ Web サイトへのリンクを貼り付けます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ナレッジ ベースが完成したら、QnA Maker リソースを Azure portal から削除してください。

## <a name="next-step"></a>次のステップ

[ナレッジ ベースに関する分析結果の取得](../How-To/get-analytics-knowledge-base.md)

各項目の詳細情報
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker コネクタ](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/)と[コネクタの設定](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)