---
title: チュートリアル:Speech SDK を使用してボットを音声対応にする - 音声サービス
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、Microsoft Bot Framework を使用してエコー ボットを作成し、それを Azure にデプロイし、Bot Framework Direct Line Speech チャネルに登録します。 その後、Windows 用のサンプル クライアント アプリを構成します。これにより、ボットに話しかけて、応答を聞くことができます。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: d91bfee228b946ff564f6d080976f9ce5c12caa4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426265"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>チュートリアル:Speech SDK を使用して音声でボットを有効にする

音声サービスを使用して、チャット ボットを音声対応にできます。

このチュートリアルでは、ユーザーの言ったことを繰り返すボットを作成します。
Microsoft Bot Framework を使用してボットを作成し、それを Azure にデプロイし、Bot Framework Direct Line Speech チャネルに登録します。
その後、Windows 用のサンプル クライアント アプリを構成します。これにより、ボットに話しかけて、同じ言葉が返されるのを聞くことができます。

このチュートリアルは、Azure、Bot Framework ボット、Direct Line Speech、または Speech SDK を使用し始めたばかりで、限られたコーディングで動作するシステムをすばやく構築したいと思っている開発者向けに設計されています。 これらのサービスに関する経験や知識は必要ありません。

このチュートリアルで作成する音声対応チャット ボットは、これらのステップに従って動作します。

1. サンプル クライアント アプリケーションが Direct Line Speech チャネルとエコー ボットに接続するように構成されています。
1. ユーザーがボタンを押すと、音声オーディオがマイクからストリーミングされます。 (または、カスタム キーワードが使用された場合、オーディオが継続的に録音されます。)
1. カスタム キーワードが使用される場合、キーワードの検出がローカル デバイスで行われ、クラウドへのオーディオのストリーミングが制限されます。
1. Speech SDK を使用して、サンプル クライアント アプリケーションが Direct Line Speech チャネルに接続され、オーディオがストリーミングされます。
1. 必要に応じて、サービス上でより高い精度のキーワード検証が行われます。
1. オーディオが音声認識サービスに渡され、テキストに変換されます。
1. 認識されたテキストが Bot Framework アクティビティとしてエコー ボットに渡されます。
1. 応答テキストがテキスト読み上げ (TTS) サービスによってオーディオに変換され、再生のためにクライアント アプリケーションにストリーミングで返されます。

<!-- svg src in User Story 1754106 -->
![diagram-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Speech チャネルのフロー")

> [!NOTE]
> このチュートリアルの手順では、有料サービスは必要ありません。 新しい Azure ユーザーは、無料のAzure 試用版サブスクリプションのクレジットと、音声サービスの Free レベルを使用して、このチュートリアルを完了することができます。

このチュートリアルの内容:
> [!div class="checklist"]
> * 新しい Azure リソースを作成する
> * エコー ボットのサンプルを構築、テストして Azure App Service にデプロイする
> * ボットを Direct Line Speech チャネルに登録する
> * Windows 音声アシスタント クライアントを構築して実行し、エコー ボットと対話する
> * カスタム キーワードのアクティブ化を追加する
> * 認識および発声された音声の言語を変更する方法を学習する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要になります。

- マイクとスピーカー (またはヘッドホン) が動作している Windows 10 PC
- **ASP.NET および Web 開発** ワークロードがインストールされている [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 以降
- [.NET Framework ランタイム 4.6.1](https://dotnet.microsoft.com/download) 以降
- Azure アカウント。 [無料試用版にサインアップします](https://azure.microsoft.com/free/cognitive-services/)
- [GitHub](https://github.com/) アカウント
- [Git for Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>リソース グループを作成する

このチュートリアルで作成するクライアント アプリでは、いくつかの Azure サービスを使用します。 ボットからの応答のラウンドトリップ時間を短縮するには、これらのサービスが同じ Azure リージョンに配置されていることを確認します。 ここでは、リソース グループを **米国西部** リージョンに作成します。 このリソース グループは、Bot Framework、Direct Line Speech チャネル、および音声サービスの個別のリソースを作成するときに使用されます。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">リソース グループを作成する </a>
1. いくつかの情報を指定するよう求められます。
   * **[サブスクリプション]** を **[無料試用版]** に設定します (既存のサブスクリプションを使用することもできます)。
   * **リソース グループ** の名前を入力します。 **SpeechEchoBotTutorial-ResourceGroup** をお勧めします。
   * **[リージョン]** ドロップダウンから、 **[米国西部]** を選択します。
1. **[確認と作成]** をクリックします。 "**検証に成功しました**" というバナーが表示されます。
1. **Create** をクリックしてください。 リソース グループの作成には数分かかる場合があります。
1. このチュートリアルで後ほど作成するリソースと同様に、このリソース グループをダッシュボードにピン留めして簡単にアクセスできるようにすることをお勧めします。 このリソース グループをピン留めする場合は、リソース グループ名の右側のピン アイコンをクリックします。

### <a name="choosing-an-azure-region"></a>Azure リージョンの選択

このチュートリアルで別のリージョンを使用する場合は、次の要因によって選択肢が制限される可能性があります。

* [サポートされている Azure リージョン](regions.md#voice-assistants)を使用してください。
* Direct Line Speech チャネルでは、標準音声とニューラル音声を持つテキスト読み上げサービスが使用されます。 ニューラル音声は、[特定の Azure リージョンに限定](regions.md#standard-and-neural-voices)されています。

リージョンについて詳しくは、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」をご覧ください。

## <a name="create-resources"></a>リソースを作成する

サポートされているリージョンにリソース グループを作成したので、次の手順は、このチュートリアルで使用するサービスごとの個別リソースの作成です。

### <a name="create-a-speech-service-resource"></a>音声サービスのリソースを作成する

Speech リソースを作成するには、以下の手順に従います。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">音声サービスのリソースを作成する </a>
4. いくつかの情報を指定するよう求められます。
   * リソースに **名前** を付けます。 **SpeechEchoBotTutorial-Speech** をお勧めします
   * **[サブスクリプション]** で、 **[無料試用版]** が選択されていることを確認します。
   * **[場所]** では **[米国西部]** を選択します。
   * **[価格レベル]** では **[F0]** を選択します。 これは Free レベルです。
   * **[リソース グループ]** で、 **[SpeechEchoBotTutorial-ResourceGroup]** を選択します。
5. 必要な情報をすべて入力したら、 **[作成]** をクリックします。 リソースの作成には数分かかることがあります。
6. このチュートリアルの後の方で、このサービスのサブスクリプション キーが必要になります。 これらのキーには、リソースの **[概要]** (キーの管理) または **[キー]** からいつでもアクセスできます。

この時点で、リソース グループ (**SpeechEchoBotTutorial-ResourceGroup**) に Speech リソースがあることを確認します。

| 名前 | Type  | 場所 |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Cognitive Services | 米国西部 |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service プランの作成

次の手順は、App Service プランの作成です。 App Service プランでは、Web アプリを実行するための一連のコンピューティング リソースを定義します。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Azure App Service プランを作成する </a>
4. いくつかの情報を指定するよう求められます。
   * **[サブスクリプション]** を **[無料試用版]** に設定します (既存のサブスクリプションを使用することもできます)。
   * **[リソース グループ]** で、 **[SpeechEchoBotTutorial-ResourceGroup]** を選択します。
   * リソースに **名前** を付けます。 **SpeechEchoBotTutorial-AppServicePlan** をお勧めします
   * **[オペレーティング システム]** では **[Windows]** を選択します。
   * **[リージョン]** では **[米国西部]** を選択します。
   * **[価格レベル]** で、 **[Standard S1]** が選択されていることを確認します。 これは既定値です。 そうでない場合は、前述のように **[オペレーティング システム]** を **[Windows]** に設定してください。
5. **[確認と作成]** をクリックします。 "**検証に成功しました**" というバナーが表示されます。
6. **Create** をクリックしてください。 リソース グループの作成には数分かかる場合があります。

この時点で、リソース グループ (**SpeechEchoBotTutorial-ResourceGroup**) に 2 つのリソースがあることを確認します。

| 名前 | Type  | 場所 |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service プラン | 米国西部 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 米国西部 |

## <a name="build-an-echo-bot"></a>エコー ボットを構築する

いくつかのリソースを作成したので、ボットを構築しましょう。 エコー ボットのサンプルから始めます。名前が示すように、エコー ボットでは入力したテキストが応答としてエコーされます。 変更せずに使用できるサンプル コードが用意されているので心配しないでください。 これは、ボットを Azure にデプロイした後に接続する Direct Line Speech チャネルと連携するように構成されています。

> [!NOTE]
> この後の手順と、エコー ボットに関する追加情報については、[GitHub にあるサンプルの README](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md) をご覧ください。

### <a name="run-the-bot-sample-on-your-machine"></a>お使いのマシンでボット サンプルを実行する

1. サンプル リポジトリを複製します。

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Visual Studio を起動します。
3. ツールバーで **[ファイル]**  >  **[開く]**  >  **[プロジェクト/ソリューション]** を選択し、エコー ボット プロジェクト ソリューションを開きます。

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. プロジェクトが読み込まれたら、<kbd>F5</kbd> キーを押してプロジェクトをビルドして実行します。
5. ブラウザーが起動し、このような画面が表示されます。
    > [!div class="mx-imgBorder"]
    > [![スクリーンショットに、ボットの準備ができましたというメッセージが表示されたエコー ボット ページが示されています。](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "localhost で実行されている EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Bot Framework Emulator を使用してボット サンプルをテストする

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) は、ボットの開発者がローカルで (トンネルを通じてリモートでも) ボットをテストおよびデバッグできる、デスクトップ アプリです。 Emulator では、入力されたテキスト (音声ではない) が入力として受け入れられます。 ボットはテキストでも応答します。 Bot Framework Emulator を使用して、ローカルで実行されているエコー ボットをテキスト入力とテキスト出力でテストするには、次の手順に従います。 Azure にボットをデプロイした後、音声入力と音声出力でテストします。

1. [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) バージョン 4.3.0 以降をインストールします
2. Bot Framework Emulator を起動し、ご自身のボットを開きます。
   * **[ファイル]**  ->  **[Open Bot]\(ボットを開く\)** 。
3. ボットの URL を入力します。 次に例を示します。

   ```
   http://localhost:3978/api/messages
   ```
   [接続] を押します。
4. ボットにより、"Hello and welcome!" というあいさつが行われます。 メッセージが表示されます。 任意のテキスト メッセージを入力し、ボットからの応答を受け取ることを確認します。
5. エコー ボット インスタンスとの交信の様子は、次のようになります。[![スクリーンショットに、Bot Framework Emulator が示されています。](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework Emulator")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>ボットを Azure App Service にデプロイする

次の手順は、Azure へのエコー ボットのデプロイです。 ボットをデプロイするにはいくつかの方法がありますが、このチュートリアルでは、Visual Studio から直接発行する方法に重点を置いて説明します。

> [!NOTE]
> または、[Azure CLI](/azure/bot-service/bot-builder-deploy-az-cli) と[デプロイ テンプレート](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot)を使用してボットをデプロイすることもできます。

> [!NOTE]
> 以下の手順の実行時に **[発行]** が表示されない場合は、Visual Studio インストーラーを使用して **[ASP.NET と Web 開発]** ワークロードを追加してください。

1. Visual Studio から、Direct Line Speech チャネルで使用するように構成されているエコー ボットを開きます。

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. **ソリューション エクスプローラー** で、 **[EchoBot]** プロジェクトを右クリックし、 **[発行...]** を選択します。
1. **[発行]** という新しいウィンドウが開きます。
1. **[Azure]** を選択して **[次へ]** をクリックし、 **[Azure App Service (Windows)]** を選択して **[次へ]** をクリックします。次に、緑色のプラス記号の近くの **[Create a new Azure App Service]\(新しい Azure App Service の作成\)** をクリックします。
1. **[App Service (Windows)]** ウィンドウが表示されたら、
   * **[アカウントの追加]** をクリックし、Azure アカウントの資格情報を使用してサインインします。 既にサインインしている場合、ドロップダウン リストからアカウントを選択します。
   * **[名前]** では、グローバルに一意のボット名を入力する必要があります。 この名前は、一意のボット URL を作成するために使用されます。 日付と時刻を含む既定値が設定されます (例:"EchoBot20190805125647")。 このチュートリアルでは、既定の名前を使用できます。
   * **[サブスクリプション]** は **[無料試用版]** に設定します
   * **[リソース グループ]** で、 **[SpeechEchoBotTutorial-ResourceGroup]** を選択します
   * **[ホスティング プラン]** では、 **[SpeechEchoBotTutorial-AppServicePlan]** を選択します
1. **Create** をクリックしてください。 ウィザードの最後の画面で **[完了]** をクリックします。
1. [発行] 画面の右側の **[発行]** をクリックします。 Visual Studio によってボットが Azure にデプロイされます。
1. Visual Studio の出力ウィンドウに、このような成功メッセージが表示されます。

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 既定のブラウザーが開き、次の内容を含むページが表示されます:"Your bot is ready! (ボットの準備ができました)"。
1. この時点で、Azure portal 上でリソース グループ **SpeechEchoBotTutorial-ResourceGroup** をチェックし、これらの 3 つのリソースがあることを確認します。

| 名前 | Type  | 場所 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 米国西部 |
| SpeechEchoBotTutorial-AppServicePlan | App Service プラン | 米国西部 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 米国西部 |

## <a name="enable-web-sockets"></a>Web ソケットの有効化

Web ソケットを使用してボットと Direct Line Speech チャネルが通信できるように、構成を少し変更する必要があります。 以下の手順に従って、Web ソケットを有効にします。

1. [Azure portal](https://portal.azure.com) に移動し、ご自身の App Service をクリックします。 リソースには、**EchoBot20190805125647** (一意のアプリ名) のような名前を付ける必要があります。
2. 左側のナビゲーション ペインで、 **[設定]** の **[構成]** をクリックします。
3. **[全般設定]** タブを選択します。
4. **[Web ソケット]** のトグルを探し、 **[オン]** に設定します。
5. **[保存]** をクリックします。

> [!TIP]
> Azure App Service ページの上部にあるコントロールを使用して、サービスを停止または再起動することができます。 これは、トラブルシューティングを行うときに便利です。

## <a name="create-a-channel-registration"></a>チャネル登録を作成する

ボットをホストするための Azure App Service を作成したので、次の手順は **ボット チャネル登録** の作成です。 チャネル登録の作成は、ボットを Direct Line Speech チャネルなどの Bot Framework チャネルに登録するための前提条件です。 ボットがチャネルを使用する方法の詳細については、「[ボットをチャネルに接続する](/azure/bot-service/bot-service-manage-channels)」を参照してください。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Azure ボット チャンネル登録を作成します </a>
2. いくつかの情報を指定するよう求められます。
   * **[ボット ハンドル]** には「**SpeechEchoBotTutorial-BotRegistration-####** 」と入力し、 **####** を任意の数に置き換えます。 ボット ハンドルはグローバルに一意である必要があることに注意してください。 ボット ハンドルを入力しても、_要求されたボット ID は使用できません_ というエラー メッセージが表示された場合は、別の番号を選択します。 次の例では、8726 を使用しました
   * **[サブスクリプション]** では **[無料試用版]** を選択します。
   * **[リソース グループ]** で、 **[SpeechEchoBotTutorial-ResourceGroup]** を選択します。
   * **[場所]** では **[米国西部]** を選択します。
     * **[価格レベル]** では **[F0]** を選択します。
     * **[Messaging endpoint]\(メッセージング エンドポイント\)** では、末尾に `/api/messages` パスを追加して Web アプリの URL を入力します。 たとえば、グローバルに一意のアプリ名が **EchoBot20190805125647** だった場合、メッセージング エンドポイントは `https://EchoBot20190805125647.azurewebsites.net/api/messages/` のようになります。
     * **Application Insights** では、これを **[Off]** に設定できます。 詳細については、「[ボットの分析](/azure/bot-service/bot-service-manage-analytics)」をご覧ください。
     * **[アプリ ID とパスワードの自動作成]** は無視します。
5. **[ボット チャネル登録]** ブレードの下部にある **[作成]** をクリックします。

この時点で、Azure portal 内のリソース グループ **SpeechEchoBotTutorial-ResourceGroup** を確認します。 少なくとも 4 つのリソースが表示されているはずです。

| 名前 | Type  | 場所 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 米国西部 |
| SpeechEchoBotTutorial-AppServicePlan | App Service プラン | 米国西部 |
| SpeechEchoBotTutorial-BotRegistration-8726 | ボット チャネル登録 | グローバル |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 米国西部 |

> [!IMPORTANT]
> [米国西部] を選択した場合でも、ボット チャネル登録リソースにはグローバル リージョンが表示されます。 これは予期されることです。

## <a name="optional-test-in-web-chat"></a>省略可能:Web チャットでのテスト

Azure ボット チャネル登録ページには、 **[ボット管理]** の下に **[Web チャットでテスト]** オプションがあります。 Web チャットはボットに対して認証する必要があるため、既定でボットでは機能しません。 テキスト入力でデプロイ済みのボットをテストする場合は、次の手順に従います。 これらの手順は省略可能であり、チュートリアルの次のステップに進むために必須ではありません。 

1. [Azure portal](https://portal.azure.com) で、**EchoBotTutorial-BotRegistration-####** リソースを特定して開きます
1. **[ボット管理]** ナビゲーションで、 **[設定]** を選択します。 **[Microsoft App ID]** の下の値をコピーします
1. Visual Studio EchoBot ソリューションを開きます。 ソリューション エクスプローラーで、**appsettings.json** を見つけてダブルクリックします
1. JSON ファイルの **MicrosoftAppId** の横の空の文字列を、コピーした ID 値に置き換えます
1. Azure portal に戻って、 **[ボット管理]** ナビゲーションで、 **[設定]** を選択し、 **[Microsoft App ID]** の横にある **[(管理)]** をクリックします
1. **[新しいクライアント シークレット]** をクリックします。 説明 (例: "web chat") を追加し、 **[追加]** をクリックします。 新しいシークレットをコピーします
1. JSON ファイルの **MicrosoftAppPassword** の横の空の文字列を、コピーしたシークレット値に置き換えます
1. JSON ファイルを保存します。 次のように表示されます。
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. アプリを再発行します (Visual Studio ソリューション エクスプローラーで **[EchoBot]** プロジェクトを右クリックし、 **[発行...]** を選択し、 **[発行]** ボタンをクリックします)
10. これで、Web チャットでボットをテストする準備が整いました。

## <a name="register-the-direct-line-speech-channel"></a>Direct Line Speech チャネルを登録する

次は、ボットを Direct Line Speech チャネルに登録します。 このチャネルは、ボットと、Speech SDK を使用してコンパイルされたクライアント アプリとの間の接続を作成します。

1. [Azure portal](https://portal.azure.com) で、**SpeechEchoBotTutorial-BotRegistration-####** リソースを見つけて開きます。
1. **[ボット管理]** ナビゲーションで、 **[チャネル]** を選択します。
   * **[その他のチャネル]** の **[Direct Line Speech]** をクリックします。
   * **[Configure Direct line Speech]\(Direct line Speech の構成\)** というページのテキストを確認し、 **[Cognitive service account]\(Cognitive Service アカウント\)** ドロップダウン メニューを展開します。
   * 前に作成した音声リソース (例: **SpeechEchoBotTutorial-Speech**) をメニューから選択して、Speech のサブスクリプション キーにボットを関連付けます。
   * 残りの省略可能なフィールドを無視します。
   * **[保存]** をクリックします。

1. **[ボット管理]** ナビゲーションで、 **[設定]** をクリックします。
   * **[Enable Streaming Endpoint]\(ストリーミング エンドポイントを有効にする\)** というラベルの付いたボックスをオンにします。 これは、ボットと Direct Line Speech チャネルの間の Web ソケット上に構築される通信プロトコルを作成するために必要です。
   * **[保存]** をクリックします。

> [!TIP]
> 詳細については、「[ボットを Direct Line Speech に接続する](/azure/bot-service/bot-service-channel-connect-directlinespeech)」をご覧ください。 このページには、追加情報と既知の問題が記載されています。

## <a name="run-the-windows-voice-assistant-client"></a>Windows 音声アシスタント クライアントを実行する

この手順では、Windows 音声アシスタント クライアントを実行します。 クライアントは、C# で作成された Windows Presentation Foundation (WPF) アプリであり、[Speech SDK](./speech-sdk.md) を使用して、Direct Line Speech チャネルを使用したボットとの通信を管理します。 これを使用して、カスタム クライアント アプリを作成する前にボットと対話し、テストします。 これはオープン ソースであるため、実行可能ファイルをダウンロードして実行するか、または自分でビルドすることができます。

Windows 音声アシスタント クライアントには、ボットへの接続の構成、テキストでの会話の表示、JSON 形式での Bot Framework アクティビティの表示、およびアダプティブ カードの表示を行える単純な UI が用意されています。 カスタム キーワードの使用もサポートされます。 このクライアントを使用して、ボットとの対話を行い、音声応答を受信します。

> [!NOTE]
> この時点で、マイクとスピーカーが有効で動作していることを確認してください。

1. [Windows 音声アシスタント クライアント](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)の GitHub リポジトリに移動します。
1. そこに示されている手順に従って、次のどちらかを実行します。
   * ZIP パッケージ内の事前にビルドされた実行可能ファイルをダウンロードして実行するか、または
   * リポジトリを複製し、プロジェクトをビルドすることにより実行可能ファイルを自分でビルドします。

1. GitHub リポジトリの手順に従って、`VoiceAssistantClient.exe` クライアント アプリケーションを起動し、ボットに接続するように構成します。
1. **[再接続]** をクリックし、"**New conversation started - type or press the microphone button (新しい会話が開始されました - 入力するか、マイク ボタンを押してください)** " というメッセージが表示されることを確認します。
1. これをテストしてみましょう。マイク ボタンをクリックし、英語でいくつかの単語を話します。 話すと、認識されたテキストが表示されます。 話し終わると、ボットは認識した単語を "エコー" に続けて独自の声で読み上げて応答します。
1. テキストを使用してボットと通信することもできます。 下部のバーにテキストを入力するだけです。 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Windows 音声アシスタント クライアントでのエラーのトラブルシューティング

メイン アプリ ウィンドウにエラー メッセージが表示された場合は、この表を使用してエラーを特定し、トラブルシューティングを行います。

| エラー | 対策 |
|-------|----------------------|
|エラー (AuthenticationFailure) :認証エラー (401) で WebSocket をアップグレードできませんでした。 Check for correct subscription key (or authorization token) and region name (正しいサブスクリプション キー (または認証トークン) とリージョン名があることを確認してください)| アプリの [設定] ページで、Speech サブスクリプション キーとそのリージョンが正しく入力されていることを確認します。<br>音声キーとキーのリージョンが正しく入力されていることを確認します。 |
|エラー (ConnectionFailure) :Connection was closed by the remote host. (リモート ホストにより、接続が切断されました。) エラー コード:1011。 エラーの詳細:We could not connect to the bot before sending a message (メッセージを送信する前にボットに接続できませんでした) | [[Enable Streaming Endpoint]\(ストリーミング エンドポイントを有効にする\) ボックスをオンにした](#register-the-direct-line-speech-channel)か、[ **[Web ソケット]** を [オン] に切り替えた](#enable-web-sockets)ことを確認します。<br>Azure App Service が実行されていることを確認します。 その場合は、App Service を再起動してみてください。|
|エラー (ConnectionFailure) :Connection was closed by the remote host. (リモート ホストにより、接続が切断されました。) エラー コード:1002。 エラーの詳細:サーバーが状態コード '101' を返すはずが、状態コード '503' を返しました | [[Enable Streaming Endpoint]\(ストリーミング エンドポイントを有効にする\) ボックスをオンにした](#register-the-direct-line-speech-channel)か、[ **[Web ソケット]** を [オン] に切り替えた](#enable-web-sockets)ことを確認します。<br>Azure App Service が実行されていることを確認します。 その場合は、App Service を再起動してみてください。|
|エラー (ConnectionFailure) :Connection was closed by the remote host. (リモート ホストにより、接続が切断されました。) エラー コード:1011。 エラーの詳細:応答状態コードは成功を示していません:500 (InternalServerError)| ボットによって、出力アクティビティ [[音声入力]](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) フィールドにニューラル音声が指定されましたが、Speech サブスクリプション キーに関連付けられている Azure リージョンではニューラル音声がサポートされていません。 「[標準およびニューラル音声](./regions.md#standard-and-neural-voices)」をご覧ください。|

発生している問題が表に記載されていない場合は、「[音声アシスタント: よく寄せられる質問](faq-voice-assistants.md)」をご覧ください。 このチュートリアルのすべての手順を実行しても問題が解決しない場合は、[Voice Assistant の GitHub ページ](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues)で新しい問題を入力してください。

#### <a name="a-note-on-connection-time-out"></a>接続タイムアウトに関する注意事項

ボットに接続していて、最後の 5 分間にアクティビティが発生していない場合、サービスはクライアントおよびボットとの websocket 接続を自動的に閉じます。 これは仕様です。 下部のバーにメッセージが表示されます。 *"Active connection timed out but ready to reconnect on demand (アクティブな接続がタイムアウトしましたが、必要に応じて再接続できます)"* . [再接続] ボタンを押す必要はありません。マイクのボタンを押して会話を開始するか、テキスト メッセージを入力するか、キーワード (有効になっている場合) を発声してください。 接続は自動的に再確立されます。  
### <a name="view-bot-activities"></a>ボット アクティビティの表示

すべてのボットでは、**アクティビティ** メッセージが送受信されます。 Windows 音声アシスタント クライアントの **[Activity Log]\(アクティビティ ログ\)** ウィンドウには、クライアントがボットから受信した各アクティビティのタイムスタンプ付きログが表示されます。 [`DialogServiceConnector.SendActivityAsync`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) メソッドを使用して、クライアントからボットに送信されたアクティビティを確認することもできます。 ログ項目を選択すると、関連付けられているアクティビティの詳細が JSON として表示されます。

クライアントで受信されたアクティビティの json のサンプルを次に示します。

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

JSON 出力で返される内容について詳しくは、[アクティビティのフィールド](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)をご覧ください。 このチュートリアルの目的では、[[テキスト]](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) フィールドと [[音声入力]](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) フィールドに注目できます。

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Speech SDK に対する呼び出しのクライアント ソース コードの表示

Windows 音声アシスタント クライアントでは、Speech SDK を含む NuGet パッケージ [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) が使用されます。 サンプル コードの確認を開始するのに適した場所は、次の 2 つの Speech SDK オブジェクトを作成する、ファイル [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) 内の InitSpeechConnector() メソッドです。
- [`DialogServiceConfig`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) -構成設定 (たとえば、Speech のサブスクリプション キーやキーのリージョン) 用
- [`DialogServiceConnector`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) - 認識された音声とボットの応答を処理するためのチャネル接続イベントとクライアント サブスクリプション イベントの管理用。

## <a name="add-custom-keyword-activation"></a>カスタム キーワードのアクティブ化を追加する

Speech SDK では、カスタム キーワードのアクティブ化がサポートされます。 Microsoft のアシスタントの "コルタナさん" と同様に、選択したキーワードを継続的にリッスンするアプリを作成できます。 キーワードは、1 つの単語または複数の単語から成る語句であることにご注意ください。

> [!NOTE]
> "*キーワード*" という用語は、多くの場合、"*ウェイク ワード*" と同じ意味で使用され、Microsoft のドキュメントでは両方とも使用されている場合があります。

キーワードの検出はクライアント アプリ上で行われます。 キーワードを使用している場合、キーワードが検出された場合にのみ、オーディオが Direct Line Speech チャネルにストリーミングされます。 Direct Line Speech チャネルには、"*キーワード検証 (KWV)* " と呼ばれるコンポーネントが含まれています。これにより、クラウド内でより複雑な処理が実行されて、選択したキーワードがオーディオ ストリームの先頭にあることが確認されます。 キー ワードの検証に成功すると、チャネルがボットと通信します。

これらの手順に従って、キーワード モデルを作成し、このモデルを使用するように Windows 音声アシスタント クライアントを構成して、最後にボットを使用してテストします。

1. これらの手順に従い、[Speech Service を使用してキーワードを作成](./custom-keyword-basics.md)します。
2. 前の手順でダウンロードしたモデル ファイルを解凍します。 これには、キーワードに由来する名前が付けられています。 `kws.table` という名前のファイルを探しています。
3. Windows 音声アシスタント クライアント内で、 **[Settings]\(設定\)** メニューを見つけます (右上にある歯車アイコンを探します)。 **[Model file path]\(モデル ファイルのパス\)** を特定して、手順 2. の `kws.table` ファイルの完全なパス名を入力します。
4. **[有効]** というラベルの付いたボックスをオンにしてください。 チェック ボックスの横に、次のメッセージが表示されます:"Will listen for the keyword upon next connection" (次の接続時にキーワードをリッスンする)。 間違ったファイルまたは無効なパスを指定した場合は、エラー メッセージが表示されます。
5. Speech **サブスクリプション キー**、**サブスクリプション キーのリージョン** を入力し、 **[OK]** をクリックして **[設定]** メニューを閉じます。
6. **[再接続]** をクリックします。 次のようなメッセージが表示されます:"New conversation started - type, press the microphone button, or say the keyword" (新しい会話が開始しました - 入力するか、マイク ボタンを押すか、キーワードを話してください)。 これで、アプリは継続的にリッスンします。
7. キーワードで始まる任意の語句を発声します。 たとえば、" **{自分のキーワード}** 、今何時?" などです。 キーワードを発した後に間を置く必要はありません。 完了すると、次の 2 つの処理が行われます。
   * 話した内容についての音声テキストが表示されます
   * すぐ後に、ボットの応答が聞こえます
8. ボットでサポートされている 3 種類の入力を使用した実験に進みます。
   * 下部のバーに入力されたテキスト
   * マイク アイコンを押して発声
   * キーワードで始まる任意の語句の発声

### <a name="view-the-source-code-that-enables-keyword"></a>キーワードを有効にするソース コードの表示

Windows 音声アシスタント クライアントのソース コード内で、これらのファイルを調べて、キーワード検出を有効にするために使用されているコードを確認します。

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) には、ディスク上のローカル ファイルからモデルをインスタンス化するために使用される Speech SDK メソッド [`KeywordRecognitionModel.fromFile()`](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-) の呼び出しが含まれています。
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) には、継続的なキーワード検出をアクティブにする Speech SDK メソッド [`DialogServiceConnector.StartKeywordRecognitionAsync()`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) の呼び出しが含まれています。

## <a name="optional-change-the-language-and-bot-voice"></a>(省略可能) 言語とボットの音声を変更する

作成したボットでは、リッスンと応答が英語で行われ、テキスト読み上げの音声に既定の英語 (米国) が使用されます。 ただし、英語 (既定の音声) の使用に限定されているわけではありません。 ここでは、ボットでのリッスンと応答に使用される言語を変更する方法について説明します。 また、その言語用に別の音声を選択する方法についても説明します。

### <a name="change-the-language"></a>言語の変更

[音声テキスト変換](language-support.md#speech-to-text)テーブルに記載されている言語のいずれかを選択できます。 次の例では、言語をドイツ語に変更します。

1. Windows 音声アシスタント クライアント アプリを開き、設定ボタン (右上の歯車アイコン) をクリックして、[Language]\(言語\) フィールドに「`de-de`」と入力します (これは[音声テキスト変換](language-support.md#speech-to-text)テーブルに記載されているロケール値です)。 これにより、認識される音声言語が設定され、既定値 `en-us` がオーバーライドされます。 また、ボットでの応答に既定のドイツ語の音声を使用するように Direct Line Speech チャネルに指示します。
2. [設定] ページを閉じ、[再接続] ボタンをクリックして、エコー ボットへの新しい接続を確立します。
3. [マイク] ボタンをクリックし、ドイツ語の語句を言います。 認識されたテキストが表示され、エコー ボットが既定のドイツ語の音声で応答します。

### <a name="change-the-default-bot-voice"></a>ボットの既定の音声を変更する

ボットが単純なテキストではなく[音声合成マークアップ言語](speech-synthesis-markup.md) (SSML) の形式で応答するように指定されている場合は、テキスト読み上げの音声を選択したり、発音を制御したりできます。 エコー ボットでは SSML を使用しませんが、それを使用するようにコードを簡単に変更できます。 次の例では、エコー ボットでの応答に SSML を追加しています。これにより、既定の女性の音声の代わりにドイツ語の Stefan Apollo の音声 (男性の音声) が使用されます。 お使いの言語でサポートされている[標準音声](language-support.md#standard-voices)と[ニューラル音声](language-support.md#neural-voices)の一覧を参照してください。

1. 最初に `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs` を開きましょう。
2. 次の 2 つの行を検索します。
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. それらを以下で置き換えます。
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Visual Studio 内でソリューションをビルドし、ビルド エラーを修正します。

'MessageFactory. Text' メソッドの 2 つ目の引数では、ボットでの応答の [Activity speak フィールド](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)を設定します。 上記の変更により、これが単純なテキストから SSML に置き換えられ、既定以外のドイツ語の音声を指定できるようになります。

### <a name="redeploy-your-bot"></a>ボットの再デプロイ

ボットに対して必要な変更を行ったので、次の手順として、それを Azure App Service に再発行し、試してみましょう。

1. [ソリューション エクスプローラー] ウィンドウで、 **[EchoBot]** プロジェクトを右クリックし、 **[発行]** を選択します。
2. 以前のデプロイ構成は、既定値として既に読み込まれています。 **[EchoBot20190805125647 - Web 配置]** の横にある **[発行]** をクリックします。
3. "**正常に発行されました**" というメッセージが Visual Studio の出力ウィンドウに表示され、"Your bot is ready! (ボットの準備ができました)" というメッセージが表示された Web ページが開きます。
4. Windows 音声アシスタント クライアント アプリを開き、設定ボタン (右上の歯車アイコン) をクリックして、[Language]\(言語\) フィールドにまだ「`de-de`」が入力されていることを確認します。
5. 「[Windows 音声アシスタント クライアントを実行する](#run-the-windows-voice-assistant-client)」の手順に従って、新しくデプロイされたボットに再接続し、新しい言語で話し、新しい音声を使用してその言語でのボットの応答を聞きます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルでデプロイしたエコー ボットを引き続き使用しない場合は、Azure リソース グループ **SpeechEchoBotTutorial-ResourceGroup** を削除するだけで、そのエコー ボットおよびそれに関連付けられているすべての Azure リソースを削除できます。

1. **Azure portal** で、 **[Azure サービス]** ナビゲーションの [[リソース グループ]](https://portal.azure.com) をクリックします。
2. 次の名前のリソース グループを探します:**SpeechEchoBotTutorial-ResourceGroup**。 3 つのドット (...) をクリックします。
3. **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech SDK を使用して独自のクライアント アプリを構築する](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>関連項目

* ボットの応答時間を向上させるために、[近くの Azure リージョン](https://azure.microsoft.com/global-infrastructure/locations/)にデプロイする
* [高品質なニューラル TTS 音声をサポートする Azure リージョン](./regions.md#standard-and-neural-voices)にデプロイする
* Direct Line Speech チャネルに関連付けられている価格:
  * [Bot Service pricing](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Speech サービス](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 独自の音声対応ボットの構築とデプロイ:
  * [Bot Framework ボット](https://dev.botframework.com/)を構築します。 [Direct Line Speech チャネル](/azure/bot-service/bot-service-channel-connect-directlinespeech)に登録し、[音声用にボットをカスタマイズ](/azure/bot-service/directline-speech-bot)します
  * 既存の [Bot Framework ソリューション](https://microsoft.github.io/botframework-solutions/index)を調べます。[仮想アシスタント](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/)を構築し、[それを Direct Line Speech に拡張します](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
