---
title: Bot - C# - v3
titleSuffix: Language Understanding - Azure Cognitive Services
description: C# を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このチャット ボットでは、ボット ソリューションをすばやく実装するため、事前構築済みの HomeAutomation ドメインを使用します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: f23cf78bfca48b3a78e234520d645abdb354038f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878470"
---
# <a name="luis-bot-in-c-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot Framework 3.x と Azure Web アプリ ボットを使用した C# の LUIS ボット

C# を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このチャット ボットでは、ボット ソリューションをすばやく実装するため、事前構築済みの HomeAutomation ドメインを使用します。 ボットは、Bot Framework 3.x と Azure Web アプリ ボットで構築します。

## <a name="prerequisite"></a>前提条件

* [HomeAutomation LUIS アプリ](luis-get-started-create-app.md)。 この LUIS アプリの意図は、ボットのダイアログ ハンドラーにマップされます。 

## <a name="luis-homeautomation-intents"></a>LUIS HomeAutomation 意図

| 意図 | 発話の例 | ボット機能 |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | 照明をつける。 | LUIS 意図 `HomeAutomation.TurnOn` が検出されるとき、ボットによって `OnIntent` ダイアログ ハンドラーが呼び出されます。 このダイアログで、IoT サービスを呼び出し、デバイスの電源を入れて、そのデバイスの電源が入ったことをユーザーに通知します。 |
| HomeAutomation.TurnOff | 寝室の照明を消す。 | LUIS 意図 `HomeAutomation.TurnOff` が検出されるとき、ボットによって `OffIntent` ダイアログ ハンドラーが呼び出されます。 このダイアログで、IoT サービスを呼び出し、デバイスの電源を切って、そのデバイスの電源が切れたことをユーザーに通知します。 |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Bot Service での Language Understanding ボットの作成

1. [Azure portal](https://portal.azure.com) の左上のメニューで、**[新しいリソースの作成]** を選択します。

    ![Azure portal でリソースを作成する](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. 検索ボックスで、**Web アプリ ボット**を検索します。 

    ![リソースの種類として Web アプリ ボットを選択する](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. [Web App Bot ]\(Web アプリ ボット\) ウィンドウで、**[作成]** をクリックします。

4. **[ボット サービス]** で、必要な情報を指定し、**[作成]** をクリックします。 これによって、ボット サービスと LUIS アプリが作成され、Azure にデプロイされます。 [音声認識の準備](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)を使用するには、ボットを作成する前に、[リージョンの要件](troubleshooting.md#what-luis-regions-support-bot-framework-speech-priming)を確認します。 
   * **[アプリ名]** にボットの名前を設定します。 この名前は、ボットがクラウドにデプロイされるときに、サブドメインとして使用されます (mynotesbot.azurewebsites.net など)。 <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
   * サブスクリプション、[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)、App Service プラン、[場所](https://azure.microsoft.com/regions/)を選択します。
   * **Bot テンプレート**で、以下を選択します。
       * **SDK v3**
       * **C#**
       * **言語の理解**
   * **LUIS アプリの場所**を選択します。 これは、アプリが作成されるオーサリング [リージョン](luis-reference-regions.md)です。
   * 法的通知の確認チェック ボックスをオンにします。 法的通知の条項はチェック ボックスの下にあります。

     ![ボット サービス](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. ボット サービスがデプロイされたことを確認します。
    * [通知] (Azure portal の上端にあるベル アイコン) をクリックします。 通知が、**[デプロイが開始されました]** から **[デプロイメントに成功しました]** に変わります。
    * 通知が **[デプロイメントに成功しました]** に変わったら、その通知で **[リソースに移動]** をクリックします。

> [!Note]
> この Web アプリ ボットの作成プロセスにより、新しい LUIS アプリも作成されました。 これはトレーニングと公開が完了しています。 

## <a name="try-the-default-bot"></a>既定のボットを試す

ボットがデプロイされたことを確認するには、**[通知]** チェック ボックスをオンにします。 通知が、**[デプロイは進行中です...]** から **[デプロイメントに成功しました]** に変わります。 **[リソースに移動]** をクリックして、ボットのリソースを開きます。

ボットがデプロイされたら、**[Test in Web Chat]\(Web チャットでのテスト\)** をクリックして、Web チャット ウィンドウを開きます。 Web チャットに「hello」と入力します。

  ![Web チャットでのボットのテスト](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

ボットが "あいさつにリーチしました。 hello と言いました" と言って応答します。  この応答により、ご自身のメッセージはボットによって受信され、そのボットが作成した既定の LUIS アプリに渡されたことが確認されます。 この既定の LUIS アプリによって、あいさつの意図が検出されました。 次の手順では、既定の LUIS アプリではなく前に作成した LUIS アプリにボットを接続します。

## <a name="connect-your-luis-app-to-the-bot"></a>ボットへの LUIS アプリの接続

**[アプリケーションの設定]** を開き、**[LuisAppId]** フィールドを編集して、ご自身の LUIS アプリのアプリケーション ID を追加します。 ご自身の HomeAutomation LUIS アプリを米国西部以外のリージョンで作成した場合は、**LuisAPIHostName** も変更する必要があります。 **LuisAPIKey** は、現在、ご自身のオーサリング キーに設定されています。 対象のトラフィックが Free レベル クォータを超えたときに、これをお使いのエンドポイント キーに変更します。 

  ![Azure での LUIS アプリ ID の更新](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> [HomeAutomation アプリ](luis-get-started-create-app.md)の LUIS アプリ ID がない場合は、Azure へのログインに使用するアカウントと同じものを使って、[LUIS](luis-reference-regions.md) Web サイトにログインします。 
> 1. **[My apps]\(マイ アプリ\)** をクリックします。 
> 2. HomeAutomation ドメインの意図とエンティティを含む、前に作成した LUIS アプリを検索します。
> 3. LUIS アプリの **[設定]** ページで、アプリ ID を検索してコピーします。 それが[トレーニング済み](luis-interactive-test.md)および[公開済み](luis-how-to-publish-app.md)であることを確認します。 
> 
> [!WARNING]
> アプリ ID または LUIS キーを削除すると、ボットは機能しなくなります。

## <a name="modify-the-bot-code"></a>ボット コードの変更

1. **[Build]\(ビルド\)**、**[Open online code editor]\(オンライン コード エディターを開く\)** の順にクリックします。

   ![オンライン コード エディターを開く](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. `build.cmd` を右クリックし、**[Run from Console]\(コンソールから実行\)** を選択してアプリをビルドします。 いくつかのビルド手順が自動的に実行されます。 ビルドが完了すると、終了時に "正常に完了しました" というメッセージが表示されます。

3. コード エディターで、`/Dialogs/BasicLuisDialog.cs` を開きます。 これには以下のコードが含まれます。

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>HomeAutomation 意図へのコードの変更


1. **あいさつ**、**キャンセル**、**ヘルプ**の 3 つの意図の属性とメソッドを削除します。 これらの意図は、HomeAutomation 事前構築済みドメインでは使用されません。 **None** 意図の属性とメソッドは必ず保持してください。 

2. 依存関係を、他の依存関係を含むファイルの先頭に追加します。

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. 文字列を管理するための定数を、`BasicLuisDialog` クラスの先頭に追加します。

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. `HomeAutomation.TurnOn` と `HomeAutomation.TurnOff` の新しい意図のコードを、`BasicLuisDialog` クラス内に追加します。

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. LUIS が見つけたエンティティを取得するためのコードを、`BasicLuisDialog` クラス内に追加します。

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. スコアを丸めて、エンティティを収集し、チャットボットの応答メッセージを表示するために、`BasicLuisDialog` クラスの **ShowLuisResult** メソッドを変更します。

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>ボットのビルド
コード エディターで `build.cmd` を右クリックし、**[Run from Console]\(コンソールから実行\)** を選択します。

![Web ボットのビルド](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

コード ビューがターミナル ウィンドウに置き換わり、ビルドの進行状況と結果が表示されます。

![Web ボットのビルド成功](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> 上部の青いバーでボット名を選択し、**[Open Kudu Console]\(Kudu コンソールを開く\)** を選択して、ボットをビルドすることもできます。 コンソールは **D:\home** で開きます。 
> 
> ディレクトリを **D:\home\site\wwwroot** に変更するには、「`cd site\wwwroot`」と入力します。
>
> ビルド スクリプトを実行するには、「`build.cmd`」と入力します。

## <a name="test-the-bot"></a>ボットのテスト

Azure portal で、**[Test in Web Chat]\(Web チャットでのテスト\)** をクリックしてボットをテストします。 "照明をつける"、"暖房を消す" などのメッセージを入力し、追加した意図を呼び出します。

   ![Web チャットでの HomeAutomation ボットのテスト](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> お使いのボットのコードを変更せずに、ご自身の LUIS アプリを再トレーニングできます。 [発話の例の追加](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances)に関するページ、および[ご自身の LUIS アプリのトレーニングとテスト](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test)に関するページをご覧ください。 

## <a name="download-the-bot-to-debug"></a>デバッグするボットのダウンロード
ボットが機能していない場合は、プロジェクトをローカル コンピューターにダウンロードして、[デバッグ](https://docs.microsoft.com/bot-framework/bot-service-debug-bot)を続行します。 

## <a name="learn-more-about-bot-framework"></a>Bot Framework の詳細
[Bot Framework](https://dev.botframework.com/)、[3.x](https://github.com/Microsoft/BotBuilder) SDK、[4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK の詳細を確認します。

## <a name="next-steps"></a>次の手順

**ヘルプ**、**キャンセル**、および**あいさつ**の意図を処理するための、LUIS の意図とボット サービスのダイアログを追加します。 Web アプリ ボットのトレーニング、公開、および[ビルド](#build-the-bot)を必ず行ってください。 LUIS とボットの意図は同じである必要があります。

会話型ボットのある他の[サンプル](https://github.com/Microsoft/AI)を確認してください。 

> [!div class="nextstepaction"]
> [意図の追加](./luis-how-to-add-intents.md)
> [音声認識の準備](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)

<!-- tested on Win10 -->
