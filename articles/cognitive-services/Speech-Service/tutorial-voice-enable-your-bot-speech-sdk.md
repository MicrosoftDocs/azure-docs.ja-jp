---
title: チュートリアル:Speech SDK を使用して音声でボットを有効にする
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、Microsoft Bot-Framework を使用してエコー ボットを作成し、それを Azure にデプロイし、Bot-Framework Direct Line Speech チャネルに登録します。 その後、Windows 用のサンプル クライアント アプリを構成します。これにより、ボットに話しかけて、応答を聞くことができます。
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: dcohen
ms.openlocfilehash: 3c57200591f3b7de9a1f9ab4198e55ed844b4d07
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932049"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>チュートリアル:Speech SDK を使用して音声でボットを有効にする

Speech Services の機能を使用して、チャット ボットを音声で簡単に有効にできるようになりました。

このチュートリアルでは、Microsoft Bot-Framework を使用してエコー ボットを作成し、それを Azure にデプロイし、Bot-Framework Direct Line Speech チャネルに登録します。 その後、Windows 用のサンプル クライアント アプリを構成します。これにより、ボットに話しかけて、応答を聞くことができます。

このチュートリアルは、Azure、Bot-Framework ボット、Direct Line Speech、または Speech SDK を使用するユーザー体験を開始し、限られたコーディングで動作するシステムをすばやく構築したいと思っている開発者向けに設計されています。 これらのサービスに関する経験や知識は必要ありません。

この演習の最後に、次のように動作するシステムが設定されています。

1. サンプル クライアント アプリケーションが Direct Line Speech チャネルとエコー ボットに接続するように構成されている
2. オーディオは、ボタンを押すと既定のマイクから録音される (またはカスタム ウェイク ワードがアクティブになっている場合は継続的に記録される)
3. 必要に応じて、カスタム ウェイク ワードの検出が行われ、オーディオ ストリーミングがクラウドに転送される
4. Speech SDK を使用して、アプリが Direct Line Speech チャネルに接続され、オーディオがストリーミングされる
5. 必要に応じて、サービス上でより高い精度のウェイク ワード検証が行われる
6. オーディオが音声認識サービスに渡され、テキストに変換される
7. 認識されたテキストが Bot Framework アクティビティとしてエコー ボットに渡される 
8. 応答テキストがテキスト読み上げ (TTS) サービスによってオーディオに変換され、再生のためにクライアント アプリケーションにストリーミングで返される

![diagram-tag](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Speech チャネルのフロー")

> [!NOTE]
> このチュートリアルの手順では、有料サービスは必要ありません。 新しい Azure ユーザーとして、無料のAzure 試用版サブスクリプションのクレジットと、Free レベルの Speech Services を使用して、このチュートリアルを完了することができます。

このチュートリアルの内容:
> [!div class="checklist"]
> * 新しい Azure リソースを作成する
> * エコー ボットのサンプルを構築、テストして Azure App Service にデプロイする
> * ボットを Direct Line Speech チャネルに登録する
> * Direct Line Speech クライアントを構築して実行し、エコー ボットと対話する
> * カスタム ウェイク ワードのアクティブ化を追加する
> * 認識および発声された音声の言語を変更する方法を学習する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要になります。

- マイクとスピーカー (またはヘッドホン) が動作している Windows 10 PC
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) またはそれ以降
- [.NET Core SDK](https://dotnet.microsoft.com/download) バージョン 2.1 以降
- Azure アカウント。 [無料試用版にサインアップ](https://azure.microsoft.com/free/ai/)します。
- [GitHub](https://github.com/) アカウント
- [Git for Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>リソース グループの作成

このチュートリアルで作成するクライアント アプリでは、いくつかの Azure サービスを使用します。 ボットからの応答のラウンドトリップ時間を短縮するには、これらのサービスが同じ Azure リージョンに配置されていることを確認します。 ここでは、リソース グループを**米国西部**リージョンに作成します。 このリソース グループは、Bot-Framework、Direct Line Speech チャネル、および Speech Services 用に個別のリソースを作成するときに使用されます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーションから、 **[リソース グループ]** を選択します。 次に、 **[追加]** をクリックして新しいリソース グループを追加します。
3. いくつかの情報を指定するよう求められます。
   * **[サブスクリプション]** を **[無料試用版]** に設定します (既存のサブスクリプションを使用することもできます)。
   * **リソース グループ**の名前を入力します。 **SpeechEchoBotTutorial-ResourceGroup** をお勧めします。
   * **[リージョン]** ドロップダウンから、 **[米国西部]** を選択します。
4. **[確認と作成]** をクリックします。 "**検証に成功しました**" というバナーが表示されます。
5. **Create** をクリックしてください。 リソース グループの作成には数分かかる場合があります。
6. このチュートリアルで後ほど作成するリソースと同様に、このリソース グループをダッシュボードにピン留めして簡単にアクセスできるようにすることをお勧めします。 このリソース グループをピン留めする場合は、ダッシュボードの右上にあるピン アイコンをクリックします。

### <a name="choosing-an-azure-region"></a>Azure リージョンの選択

このチュートリアルで別のリージョンを使用する場合は、次の要因によって選択肢が制限される可能性があります。

* Direct Line Speech チャネルはプレビュー サービスです。 そのため、特定の Azure リージョンに限定される場合があります。 使用可能なリージョンについて詳しくは、「[音声優先仮想アシスタント](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#voice-first-virtual-assistants)」をご覧ください。
* Direct Line Speech チャネルでは、標準音声とニューラル音声を持つテキスト読み上げサービスが使用されます。 ニューラル音声は、[特定の Azure リージョンに限定](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)されています。
* 無料試用版のキーは、特定のリージョンに限定される場合があります。

リージョンについて詳しくは、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」をご覧ください。

## <a name="create-resources"></a>リソースを作成する

**米国西部**リージョンにリソース グループを作成したので、次の手順は、このチュートリアルで使用するサービスごとの個別リソースの作成です。

### <a name="create-a-speech-services-resource"></a>Speech Services リソースの作成

Speech リソースを作成するには、以下の手順に従います。

1. 左のナビゲーションで **[リソースの作成]** を選択します。
2. 検索バーに「**音声**」と入力します。
3. **[音声]** を選択し、 **[作成]** をクリックします。
4. いくつかの情報を指定するよう求められます。
   * リソースに**名前**を付けます。 **SpeechEchoBotTutorial-Speech** をお勧めします
   * **[サブスクリプション]** で、 **[無料試用版]** が選択されていることを確認します。
   * **[場所]** では **[米国西部]** を選択します。
   * **[価格レベル]** では **[F0]** を選択します。 これは Free レベルです。
   * **[リソース グループ]** で、 **[SpeechEchoBotTutorial-ResourceGroup]** を選択します。
5. 必要な情報をすべて入力したら、 **[作成]** をクリックします。 リソースの作成には数分かかることがあります。
6. このチュートリアルの後の方で、このサービスのサブスクリプション キーが必要になります。 これらのキーには、リソースの **[概要]** (キーの管理) または **[キー]** からいつでもアクセスできます。

この時点で、リソース グループ (**SpeechEchoBotTutorial-ResourceGroup**) に Speech リソースがあることを確認します。

| 名前 | TYPE  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Cognitive Services | 米国西部 |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service プランの作成

次の手順は、App Service プランの作成です。 App Service プランでは、Web アプリを実行するための一連のコンピューティング リソースを定義します。

1. 左のナビゲーションで **[リソースの作成]** を選択します。
2. 検索バーに、「**App Service プラン**」と入力します。 次に、検索結果から **[App Service プラン]** カードを見つけて選択します。
3. **Create** をクリックしてください。
4. いくつかの情報を指定するよう求められます。
   * **[サブスクリプション]** を **[無料試用版]** に設定します (既存のサブスクリプションを使用することもできます)。
   * **[リソース グループ]** で、 **[SpeechEchoBotTutorial-ResourceGroup]** を選択します。
   * リソースに**名前**を付けます。 **SpeechEchoBotTutorial-AppServicePlan** をお勧めします
   * **[オペレーティング システム]** では **[Windows]** を選択します。
   * **[リージョン]** では **[米国西部]** を選択します。
   * **[価格レベル]** で、 **[Standard S1]** が選択されていることを確認します。 これは既定値です。
5. **[確認と作成]** をクリックします。 "**検証に成功しました**" というバナーが表示されます。
6. **Create** をクリックしてください。 リソース グループの作成には数分かかる場合があります。

この時点で、リソース グループ (**SpeechEchoBotTutorial-ResourceGroup**) に 2 つのリソースがあることを確認します。

| 名前 | TYPE  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service プラン | 米国西部 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 米国西部 |

## <a name="build-an-echo-bot"></a>エコー ボットを構築する

いくつかのリソースを作成したので、ボットを構築しましょう。 エコー ボットのサンプルから始めます。名前が示すように、エコー ボットでは入力したテキストが応答としてエコーされます。 変更せずに使用できるサンプル コードが用意されているので心配しないでください。 これは、ボットを Azure にデプロイした後に接続する Direct Line Speech チャネルと連携するように構成されています。

> [!NOTE]
> この後の手順と、エコー ボットに関する追加情報については、[GitHub にあるサンプルの README](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/README.md) をご覧ください。

### <a name="download-and-run-the-sample"></a>サンプルのダウンロードと実行

1. サンプル リポジトリを複製します。
   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```
2. Visual Studio を起動します。
3. ツール バーの **[ファイル]**  >  **[開く]**  >  **[プロジェクト/ソリューション]** を選択し、Direct Line Speech チャネルで使用するために構成されているエコー ボット のプロジェクト ファイルを開きます。
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
4. プロジェクトが読み込まれたら、`F5` キーを押してプロジェクトを実行します。

### <a name="test-with-the-bot-framework-emulator"></a>Bot Framework Emulator を使用したテスト

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) は、ボットの開発者がトンネルを通じてローカルでもリモートでも、ボットをテストし、デバッグできるデスクトップ アプリケーションです。 Bot Framework Emulator を使用してエコー ボットをテストするには、次の手順に従います。

1. [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) バージョン 4.3.0 以降をインストールします
2. Bot Framework Emulator を起動し、ご自身のボットを開きます。
   * **[ファイル]**  ->  **[Open Bot]\(ボットを開く\)** 。
3. ボットの URL を入力します。 例:
   ```
   http://localhost:3978/api/messages
   ```
4. UI を使用し、型指定されたテキストを使用してボットと通信します。 応答を取得したことを確認します。


## <a name="deploy-your-bot-to-an-azure-app-service"></a>ボットを Azure App Service にデプロイする

次の手順は、Azure へのエコー ボットのデプロイです。 ボットをデプロイするにはいくつかの方法がありますが、このチュートリアルでは、Visual Studio から直接発行する方法に重点を置いて説明します。

> [!NOTE]
> または、[Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) と[デプロイ テンプレート](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)を使用してボットをデプロイすることもできます。

1. Visual Studio から、Direct Line Speech チャネルで使用するように構成されているエコー ボットを開きます。
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
2. **ソリューション エクスプローラー**で、 **[EchoBot]** ソリューションを右クリックし、 **[発行...]** を選択します。
3. **[発行先を選択]** というタイトルの新しいウィンドウが開きます。
3. 左側のナビゲーションから **[App Service]** を選択し、 **[新規作成]** を選択し、 **[発行]** をクリックします。
5. **[App Service の作成]** ウィンドウが表示されます。
   * **[アカウントの追加]** をクリックし、Azure アカウントの資格情報を使用してサインインします。 既にサインインしている場合、ドロップダウン リストからアカウントを選択します。
   * **[アプリ名]** では、グローバルに一意のボット名を入力する必要があります。 この名前は、一意のボット URL を作成するために使用されます。 日付と時刻を含む既定値が設定されます (例:"EchoBot20190805125647")。 このチュートリアルでは、既定の名前を使用できます。
   * **[サブスクリプション]** は **[無料試用版]** に設定します
   * **[リソース グループ]** で、 **[SpeechEchoBotTutorial-ResourceGroup]** を選択します
   * **[ホスティング プラン]** では、 **[SpeechEchoBotTutorial-AppServicePlan]** を選択します
6. **[作成]**
7. Visual Studio に、次のような成功メッセージが表示されます。
   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```
8. 既定のブラウザーが開き、次の内容を含むページが表示されます:"Your bot is ready! (ボットの準備ができました)"。
9. この時点で、Azure portal 上でリソース グループ **SpeechEchoBotTutorial-ResourceGroup** をチェックし、次の 3 つのリソースがあることを確認します。

| 名前 | TYPE  | LOCATION |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 米国西部 |
| SpeechEchoBotTutorial-AppServicePlan | App Service プラン | 米国西部 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 米国西部 |

## <a name="enable-web-sockets"></a>Web ソケットの有効化

Web ソケットを使用してボットと Direct Line Speech チャネルが通信できるように、構成を少し変更する必要があります。 以下の手順に従って、Web ソケットを有効にします。

1. [Azure portal](https://portal.azure.com) に移動し、ご自身の App Service を特定します。 リソースには、**EchoBot20190805125647** (一意のアプリ名) のような名前を付ける必要があります。
2. 左側のナビゲーションで **[設定]** を選択し、 **[構成]** をクリックします。
3. **[全般設定]** タブを選択します。
4. **[Web ソケット]** のトグルを探し、 **[オン]** に設定します。
5. **[Save]** をクリックします。

> [!TIP]
> Azure App Service ページの上部にあるコントロールを使用して、サービスを停止または再起動することができます。 これは、トラブルシューティングを行うときに便利です。

## <a name="create-a-channel-registration"></a>チャネル登録を作成する

ボットをホストするための Azure App Service を作成したので、次の手順は**ボット チャネル登録**の作成です。 チャネル登録の作成は、ボットを Direct Line Speech チャネルなどの Bot-Framework チャネルに登録するための前提条件です。

> [!NOTE]
> ボットがチャネルを活用する方法について詳しく知りたい場合は、「[ボットをチャネルに接続する](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)」をご覧ください。

1. 最初の手順は、登録用の新しいリソースの作成です。 [Azure portal](https://portal.azure.com) で、 **[リソースの作成]** をクリックします。
2. 検索バーに「**ボット**」と入力し、結果が表示されたら、 **[Bot Channels Registration]\(ボットチャネル登録\)** を選択します。
3. **Create** をクリックしてください。
4. いくつかの情報を指定するよう求められます。
   * **[ボット名]** には、「**SpeechEchoBotTutorial-BotRegistration**」と入力します。
   * **[サブスクリプション]** では **[無料試用版]** を選択します。
   * **[リソース グループ]** で、 **[SpeechEchoBotTutorial-ResourceGroup]** を選択します。
   * **[場所]** では **[米国西部]** を選択します。
     * **[価格レベル]** では **[F0]** を選択します。
     * **[Messaging endpoint]\(メッセージング エンドポイント\)** では、末尾に `/api/messages` パスを追加して Web アプリの URL を入力します。 たとえば、グローバルに一意のアプリ名が **EchoBot20190805125647** だった場合、メッセージング エンドポイントは `https://EchoBot20190805125647.azurewebsites.net/api/messages/` のようになります。
     * **Application Insights** では、これを **[Off]** に設定できます。 詳細については、「[ボットの分析](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)」をご覧ください。
     * **[アプリ ID とパスワードの自動作成]** は無視します。
5. **[Bot Channels Registration]\(ボットチャネル登録\)** に戻り、 **[作成]** をクリックします。

この時点で、Azure portal 内のリソース グループ **SpeechEchoBotTutorial-ResourceGroup** を確認します。 4 つのリソースが表示されているはずです。

| 名前 | TYPE  | LOCATION |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 米国西部 |
| SpeechEchoBotTutorial-AppServicePlan | App Service プラン | 米国西部 |
| SpeechEchoBotTutorial-BotRegistration | ボット チャネル登録 | グローバル |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 米国西部 |

> [!IMPORTANT]
> [米国西部] を選択した場合でも、ボット チャネル登録リソースにはグローバル リージョンが表示されます。 これは予期されることです。

## <a name="register-the-direct-line-speech-channel"></a>Direct Line Speech チャネルを登録する

次は、ボットを Direct Line Speech チャネルに登録します。 このチャネルは、エコー ボットと Speech SDK を使用してコンパイルされたクライアント アプリとの間の接続を作成するために使用されます。

1. [Azure portal](https://portal.azure.com) 内で、**SpeechEchoBotTutorial-BotRegistration** リソースを特定して開きます。
2. 左側のナビゲーションから **[チャネル]** を選択します。
   * **[その他のチャネル]** を検索し、 **[Direct Line Speech]** を特定してクリックします。
   * **[Configure Direct line Speech (Preview)]\(Direct line Speech (プレビュー) の構成\)** というページのテキストを確認し、 **[保存]** をクリックします。
   * 作成の一環として、2 つの**秘密キー**が生成されました。 これらのキーはボットに固有です。 [SpeechSDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/) を使用してクライアント アプリを作成する場合は、これらのキーのいずれかを指定して、クライアント アプリ、Direct Line Speech チャネル、ボット サービスの間の接続を確立します。 このチュートリアルでは、Direct Line Speech クライアント (WPF、C#) を使用します。
   * **[表示]** をクリックし、いずれかのキーをコピーすると、簡単にアクセスできるようになります。 いつでも Azure portal からキーにアクセスできるので心配しないでください。
3. 左側のナビゲーションで、 **[設定]** をクリックします。
   * **[Enable Streaming Endpoint]\(ストリーミング エンドポイントを有効にする\)** というラベルの付いたボックスをオンにします。 これは、ボットと Direct Line Speech チャネルの間の Web ソケット上に構築された通信プロトコルを有効にするために必要です。
   * **[Save]** をクリックします。

> [!TIP]
> 詳しくは、「[ボットを Direct Line Speech に接続する (プレビュー)](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)」をご覧ください。 このページには、追加情報と既知の問題が記載されています。

## <a name="build-the-direct-line-speech-client"></a>Direct Line Speech クライアントを構築する

この手順では、Direct Line Speech クライアントを構築します。 クライアントは、C# で作成された Windows Presentation Foundation (WPF) アプリであり、[Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) を使用して、Direct Line Speech チャネルを使用したボットとの通信を管理します。 これを使用して、カスタム クライアント アプリを作成する前にボットと対話し、テストします。

Direct Line Speech クライアントには、ボットへの接続の構成、テキストでの会話の表示、JSON 形式での Bot-Framework アクティビティの表示、およびアダプティブカードの表示を行える単純な UI が用意されています。 カスタム ウェイク ワードの使用もサポートされます。 このクライアントを使用して、ボットとの対話を行い、音声応答を受信します。

先に進む前に、マイクとスピーカーが有効で動作していることを確認してください。

1. [Direct Line Speech クライアント](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md)の GitHub リポジトリに移動します。
2. 説明されている手順に従って、リポジトリを複製し、プロジェクトをビルドし、クライアントを構成して、クライアントを起動します。
3. **[再接続]** をクリックし、"**Press the mic button, or type to start talking to your bot**" (マイク ボタンを押すか、入力してボットとの対話を開始します) というメッセージが表示されることを確認します。
4. これをテストしてみましょう。マイク ボタンをクリックし、英語でいくつかの単語を話します。 話すと、認識されたテキストが表示されます。 話し終わると、ボットは認識した単語を "エコー" に続けて独自の声で読み上げて応答します。
5. テキストを使用してボットと通信することもできます。 下部のバーにテキストを入力するだけです。 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Direct Line Speech クライアント内で発生したエラーのトラブルシューティング

メイン アプリ ウィンドウにエラー メッセージが表示された場合は、この表を使用してエラーを特定し、トラブルシューティングを行います。

| Error | 対策 |
|-------|----------------------|
|アプリ エラー (詳細については、ログを参照):Microsoft.CognitiveServices.Speech.csharp:値を null にすることはできません。 パラメーター名: speechConfig | これはクライアント アプリのエラーです。 メイン アプリ ウィンドウの *[Bot Secret]\(ボット シークレット\)* に空でない値があることを確認します ([Direct Line Speech チャネルへのボットの登録](#register-the-direct-line-speech-channel)に関するセクションをご覧ください) |
|エラー AuthenticationFailure:認証エラー (401) で WebSocket をアップグレードできませんでした。 Check for correct subscription key (or authorization token) and region name (正しいサブスクリプション キー (または認証トークン) とリージョン名があることを確認してください)| アプリの [設定] ページで、Speech サブスクリプション キーとそのリージョンが正しく入力されていることを確認します。<br>ボット シークレットが正しく入力されていることを確認します。 |
|エラー ConnectionFailure:Connection was closed by the remote host. (リモート ホストにより、接続が切断されました。) エラー コード:1011。 エラーの詳細:We could not connect to the bot before sending a message (メッセージを送信する前にボットに接続できませんでした) | [[Enable Streaming Endpoint]\(ストリーミング エンドポイントを有効にする\) ボックスをオンにした](#register-the-direct-line-speech-channel)か、[ **[Web ソケット]** を [オン] に切り替えた](#enable-web-sockets)ことを確認します。<br>Azure App Service が実行されていることを確認します。 その場合は、App Service を再起動してみてください。|
|エラー ConnectionFailure:Connection was closed by the remote host. (リモート ホストにより、接続が切断されました。) エラー コード:1011。 エラーの詳細:応答状態コードは成功を示していません:500 (InternalServerError)| ボットによって、出力アクティビティ [[音声入力]](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) フィールドにニューラル音声が指定されましたが、Speech サブスクリプション キーに関連付けられている Azure リージョンではニューラル音声がサポートされていません。 「[標準およびニューラル音声](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)」をご覧ください。|
|エラー ConnectionFailure:Connection was closed by the remote host. (リモート ホストにより、接続が切断されました。) エラー コード:1000。 エラーの詳細:Exceeded maximum web socket connection idle duration(> 300000 ms) (Web ソケット接続アイドル期間の最大値 (> 30 万ミリ秒) を超えました)| これは、チャネルへの接続が開いたまま、5 分より長く非アクティブになっている場合に予期されるエラーです。 |

発生している問題が表に記載されていない場合は、「[音声優先仮想アシスタントのプレビュー:よく寄せられる質問](https://docs.microsoft.com/azure/cognitive-services/speech-service/faq-voice-first-virtual-assistants)」をご覧ください。

### <a name="view-bot-activities"></a>ボット アクティビティの表示

すべてのボットでは、**アクティビティ** メッセージが送受信されます。 Direct Line Speech クライアントの **[アクティビティ ログ]** ウィンドウには、クライアントがボットから受信した各アクティビティのタイムスタンプ付きログが表示されます。 [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) メソッドを使用して、クライアントからボットに送信されたアクティビティを確認することもできます。 ログ項目を選択すると、関連付けられているアクティビティの詳細が JSON として表示されます。

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
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

JSON 出力で返される内容について詳しくは、[アクティビティのフィールド](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)をご覧ください。 このチュートリアルの目的では、[[テキスト]](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) フィールドと [[音声入力]](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) フィールドに注目できます。

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Speech SDK に対する呼び出しのクライアント ソース コードの表示

Direct Line Speech クライアントでは、Speech SDK を含む NuGet パッケージ [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) が使用されます。 サンプル コードの確認を開始するのに適した場所は、次の 2 つの Speech SDK オブジェクトを作成する、ファイル [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs) 内の InitSpeechConnector() メソッドです。
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) -構成設定 (Speech サブスクリプション キー、キーのリージョン、ボット シークレット) 用
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) - 認識された音声とボットの応答を処理するためのチャネル接続イベントとクライアント サブスクリプション イベントの管理用。

## <a name="add-custom-wake-word-activation"></a>カスタム ウェイク ワードのアクティブ化を追加する

Speech SDK では、カスタム ウェイク ワードのアクティブ化がサポートされます。 Microsoft のアシスタントの "コルタナさん" と同様に、選択したウェイク ワードを継続的にリッスンするアプリを作成できます。 ウェイク ワードは、1 つの単語または複数の単語から成る語句であることにご注意ください。

> [!NOTE]
> "*ウェイク ワード*" という用語は、多くの場合、"*キーワード*" と同じ意味で使用され、Microsoft のドキュメントでは両方とも使用されている可能性があります。

ウェイク ワードの検出はクライアント アプリ上で行われます。 ウェイク ワードを使用している場合、オーディオは、ウェイク ワードが検出された場合にのみ、Direct Line Speech チャネルにストリーミングされます。 Direct Line Speech チャネルには、"*キーワード検証 (KWV)* " と呼ばれるコンポーネントが含まれています。これにより、クラウド内でより複雑な処理が実行されて、選択したウェイク ワードがオーディオ ストリームの先頭にあることが確認されます。 キー ワードの検証に成功すると、チャネルがボットと通信します。

これらの手順に従って、ウェイク ワード モデルを作成し、このモデルを使用するように Direct Line Speech クライアントを構成して、最後にボットを使用してテストします。

1. これらの手順に従い、[Speech Service を使用してカスタム ウェイク ワードを作成](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)します。
2. 前の手順でダウンロードしたモデル ファイルを解凍します。 これには、ウェイク ワードに由来する名前が付けられています。 `kws.table` という名前のファイルを探しています。
3. Direct Line Speech クライアント内で、 **[設定]** メニューを見つけます (右上にある歯車アイコンを探します)。 **[Model file path]\(モデル ファイルのパス\)** を特定して、手順 2. の `kws.table` ファイルの完全なパス名を入力します。
4. **[有効]** というラベルの付いたボックスをオンにしてください。 チェック ボックスの横に、次のメッセージが表示されます:"Will listen for the wake word upon next connection" (次の接続時にウェイク ワードをリッスンする)。 間違ったファイルまたは無効なパスを指定した場合は、エラー メッセージが表示されます。
5. Speech **サブスクリプション キー**、**サブスクリプション キーのリージョン**を入力し、 **[OK]** をクリックして **[設定]** メニューを閉じます。
6. **[Bot Secret]\(ボット シークレット\)** を選択し、 **[再接続]** をクリックします。 次のようなメッセージが表示されます:"New conversation started - type, press the microphone button, or say the wake word" (新しい会話が開始しました - 入力するか、マイク ボタンを押すか、ウェイク ワードを話してください)。 これで、アプリは継続的にリッスンします。
7. ウェイク ワードで始まる任意の語句を発声します。 たとえば、" **{自分のウェイクワード}** 、今何時?" などです。 ウェイク ワードを発声した後に一時停止する必要はありません。 完了すると、次の 2 つの処理が行われます。
   * 話した内容についての音声テキストが表示されます
   * すぐ後に、ボットの応答が聞こえます
8. ボットでサポートされている 3 種類の入力を使用した実験に進みます。
   * 下部のバーに入力されたテキスト
   * マイク アイコンを押して発声
   * ウェイク ワードで始まる任意の語句の発声

### <a name="view-the-source-code-that-enables-wake-word"></a>ウェイク ワードを有効にするソース コードの表示

Direct Line Client のソース コード内で、これらのファイルを調べて、ウェイク ワード検出を有効にするために使用されているコードを確認します。

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs) には、ディスク上のローカル ファイルからモデルをインスタンス化するために使用される Speech SDK メソッド [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/en-us/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-) の呼び出しが含まれています。
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs) には、継続的なウェイク ワード検出をアクティブにする Speech SDK メソッド [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) の呼び出しが含まれています。

## <a name="optional-change-the-language-and-redeploy-your-bot"></a>(省略可能) 言語を変更し、ボットを再デプロイする

作成したボットでは、リッスンと応答が英語で行われます。 ただし、英語の使用に限定されているわけではありません。 ここでは、ボットでのリッスンと応答に使用される言語を変更し、ボットを再デプロイする方法について説明します。

### <a name="change-the-language"></a>言語の変更

1. 最初に `\experimental\directline-speech\csharp_dotnetcore\02.echo-bot\Bots\echo-bot.cs` を開きましょう。
2. 次に、SSML を検索します。 これは `<speak></speak>` タグで囲まれているので、簡単に見つけることができます。
3. SSML 文字列内で `<voice name>` タグを検索し、それを `<voice name='de-DE-Stefan-Apollo'>` に置き換えて保存します。 この書式設定された文字列は、ドイツ語向けに最適化された音声 `de-DE-Stefan-Apollo` を使用して合成音声応答を返すようにテキスト読み上げサービスに指示します。

>[!NOTE]
> ドイツ語に限定されず、[Speech Service](language-support.md#text-to-speech) から利用可能な音声の一覧から選択できます。

### <a name="redeploy-your-bot"></a>ボットの再デプロイ

ボットに対して必要な変更を行ったので、次の手順として、それを Azure App Service に再発行し、試してみましょう。

1. Visual Studio 内でソリューションをビルドし、ビルド エラーを修正します。
2. [ソリューション エクスプローラー] ウィンドウで、 **[EchoBot]** プロジェクトを右クリックし、 **[発行]** を選択します。
3. 以前のデプロイ構成は、既定値として既に読み込まれています。 **[EchoBot20190805125647 - Web 配置]** の横にある **[発行]** をクリックします。
4. "**正常に発行されました**" というメッセージが Visual Studio の出力ウィンドウに表示され、"Your bot is ready! (ボットの準備ができました)" というメッセージが表示された Web ページが開きます。
5. Direct Line Speech クライアント アプリを開き、[設定] ボタン (右上の歯車アイコン) をクリックして、[言語] フィールドに「`de-de`」と入力します。 これにより、認識される音声言語が設定され、既定値 `en-us` がオーバーライドされます。
6. 「[Direct Line Speech クライアントを構築する](#build-the-direct-line-speech-client)」の手順に従って、新しくデプロイされたボットに再接続し、新しい言語で話し、新しい音声を使用してその言語でのボットの応答を聞きます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルでデプロイしたエコー ボットを引き続き使用しない場合は、Azure リソース グループ **SpeechEchoBotTutorial-ResourceGroup** を削除するだけで、そのエコー ボットおよびそれに関連付けられているすべての Azure リソースを削除できます。

1. **Azure portal** で、左側のメニューから [[リソース グループ]](https://portal.azure.com) をクリックします。
2. 次の名前のリソース グループを探します:**SpeechEchoBotTutorial-ResourceGroup**。 3 つのドット (...) をクリックします。
3. **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech SDK を使用して独自のクライアント アプリを構築する](quickstart-virtual-assistant-csharp-uwp.md)

## <a name="see-also"></a>関連項目

* ボットの応答時間を向上させるために、[近くの Azure リージョン](https://azure.microsoft.com/global-infrastructure/locations/)にデプロイする
* [高品質なニューラル TTS 音声をサポートする Azure リージョン](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)にデプロイする
* Direct Line Speech チャネルに関連付けられている価格:
  * [Bot Service pricing (Bot Service の価格)](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Speech Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 独自の音声対応ボットの構築とデプロイ:
    * [Bot-Framework ボット](https://dev.botframework.com/)を構築します。 [Direct Line Speech チャネル](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)に登録し、[音声用にボットをカスタマイズ](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)します
    * 既存の [Bot-Framework ソリューション](https://github.com/microsoft/botframework-solutions)を調べます:[カスタムの音声優先仮想アシスタント](https://docs.microsoft.com/azure/cognitive-services/speech-service/voice-first-virtual-assistants)を構築し、[音声で有効にします](https://github.com/microsoft/botframework-solutions/blob/master/docs/howto/assistant/csharp/speechenablement.md)
