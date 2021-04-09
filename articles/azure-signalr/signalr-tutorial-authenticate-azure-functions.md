---
title: チュートリアル:Azure Functions を使用した認証 - Azure SignalR
description: このチュートリアルでは、Azure Functions バインドに対して Azure SignalR Service クライアントを認証する方法について説明します
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: 6df47d3fd62083a5d0940a1d6da50ac5d7d955f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92150916"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>チュートリアル:Azure Functions を使用した Azure SignalR Service 認証

Azure Functions、App Service 認証、および SignalR Service を使用して、認証とプライベート メッセージングを備えたチャットルームを構築する手順を説明したチュートリアルです。

## <a name="introduction"></a>はじめに

### <a name="technologies-used"></a>使用されているテクノロジ

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu): ユーザーを認証してチャット メッセージを送信するためのバックエンド API
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu): 接続されたチャット クライアントに新しいメッセージをブロードキャストします
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu): チャット クライアントの UI 用の静的な Web サイトをホストします

### <a name="prerequisites"></a>前提条件

このチュートリアルを構築するには、次のソフトウェアが必要です。

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (Version 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (Version 2.x、Functions 拡張機能用に必須)
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) (Version 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) と次の拡張機能
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions): Azure Functions は VS Code で使用します
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer): テスト用の Web ページをローカルで提供します

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)

## <a name="sign-into-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) に移動し、資格情報を使用してサインインします。

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)

## <a name="create-an-azure-signalr-service-instance"></a>Azure SignalR Service のインスタンスを作成する

Azure Functions アプリをローカルでビルドしてテストします。 このアプリは Azure にある SignalR Service のインスタンス (事前に作成しておく必要があります) にアクセスします。

1. 新しい Azure リソースを作成するには、 **[リソースの作成]** ( **+** ) ボタンをクリックします。

1. **SignalR Service** を探して選択します。 **Create** をクリックしてください。

    ![新しい SignalR Service](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. 次の情報を入力します。

    | 名前 | 値 |
    |---|---|
    | リソース名 | SignalR Service のインスタンス用の一意の名前 |
    | Resource group | 一意の名前を持つ新しいリソース グループを作成します |
    | 場所 | 近くの場所を選択します |
    | 価格レベル | Free |

1. **Create** をクリックしてください。

1. インスタンスがデプロイされたら、ポータルで開き、その [設定] ページを見つけます。 [サービス モード] 設定を *[サーバーレス]* に変更します。

    ![SignalR Service モード](media/signalr-concept-azure-functions/signalr-service-mode.png)
    
[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)

## <a name="initialize-the-function-app"></a>関数アプリを初期化する

### <a name="create-a-new-azure-functions-project"></a>新しい Azure Functions プロジェクトを作成する

1. 新しい VS Code ウィンドウでメニューの `File > Open Folder` を使用し、適切な場所に空のフォルダーを作成して開きます。 これは、ビルドするアプリケーションのメイン プロジェクト フォルダーになります。

1. VS Code で Azure Functions 拡張機能を使用して、メイン プロジェクト フォルダー内の関数アプリを初期化します。
   1. VS Code でメニューから **[表示]、[コマンド パレット]** の順に選択してコマンド パレットを開きます (ショートカットは `Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。
   1. **[Azure Functions: Create New Project]\(Azure Functions: 新しいプロジェクトの作成\)** コマンドを探して選択します。
   1. メイン プロジェクト フォルダーが表示されます。 それを選択します (または [参照] を使用して検索します)。
   1. 言語を選択するプロンプトで、**[JavaScript]** を選択します。

      ![関数アプリの作成](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>関数アプリ拡張機能をインストールする

このチュートリアルでは、Azure SignalR Service との対話に Azure Functions バインドを使用します。 他のほとんどのバインドと同様に、SignalR Service バインドは拡張機能として使用できます。この拡張機能を使用するには、Azure Functions Core Tools CLI を使用してあらかじめインストールしておく必要があります。

1. メニューから **[表示]、[ターミナル]** の順に選択して (または Ctrl + \` キー)、VS Code でターミナルを開きます。

1. メイン プロジェクト フォルダーが現在のディレクトリであることを確認します。

1. SignalR Service 関数アプリ拡張機能をインストールします。

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
    ```

### <a name="configure-application-settings"></a>アプリケーション設定の構成

Azure Functions ランタイムをローカルで実行してデバッグする場合、アプリケーション設定は **local.settings.json** から読み取られます。 以前に作成した SignalR Service のインスタンスの接続文字列でこのファイルを更新します。

1. VS Code のエクスプローラー ウィンドウで **local.settings.json** を選択して開きます。

1. ファイルの内容を次のように置き換えます。

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * `AzureSignalRConnectionString` という名前の設定に Azure SignalR Service の接続文字列を入力します。 Azure portal にある Azure SignalR Service リソースの **[キー]** ページから値を取得します。プライマリまたはセカンダリのどちらの接続文字列でも使用できます。
   * `WEBSITE_NODE_DEFAULT_VERSION` 設定はローカルでは使用されませんが、Azure にデプロイする場合は必須です。
   * `Host` セクションでは、ローカルの Functions ホスト用のポートと CORS 設定を構成します (この設定は Azure での実行時に影響を与えません)。

       > [!NOTE]
       > 通常、Live Server により `http://127.0.0.1:5500` のコンテンツが提供するように構成されています。 これにより別の URL が使用されているか、またはユーザーが別の HTTP サーバーを使用している場合は、正しい提供元を反映するように `CORS` 設定を変更します。

     ![SignalR Service キーを取得する](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. ファイルを保存します。

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>SignalR Service に対してユーザーを認証する関数を作成する

チャット アプリをブラウザーで初めて開くと、Azure SignalR Service に接続できる有効な接続資格情報が要求されます。 関数アプリで、*negotiate* という HTTP によってトリガーされる関数を作成し、この接続情報を返します。

> [!NOTE]
> SignalR クライアントで末尾 `/negotiate` のエンドポイントが必要となるため、この関数には *negotiate* という名前を付ける必要があります。

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure Functions: 関数の作成]** コマンドを検索して選択します。

1. 入力を求められたら、次の情報を指定します。

    | 名前 | 値 |
    |---|---|
    | 関数アプリ フォルダー | メイン プロジェクト フォルダーを選択します |
    | テンプレート | HTTP トリガー |
    | 名前 | negotiate |
    | 承認レベル | Anonymous |

    新しい関数を含む **negotiate** というフォルダーが作成されます。

1. **negotiate/function.json** を開き、関数のバインドを構成します。 ファイルの内容を次のように変更します。 これにより、クライアントが `chat` という Azure SignalR Service ハブに接続するための有効な資格情報を生成する入力バインドが追加されます。

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    `signalRConnectionInfo` バインドの `userId` プロパティは、認証された SignalR Service 接続を作成するために使用されます。 ローカル開発の場合は、このプロパティを空白のままにします。 これは関数アプリを Azure にデプロイするときに使用します。

1. **negotiate/index.js** を開き、関数の本文を表示します。 ファイルの内容を次のように変更します。

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    この関数は、入力バインドから SignalR の接続情報を受け取り、それを HTTP 応答の本文でクライアントに返します。 SignalR クライアントにより、この情報が使用され SignalR Service インスタンスに接続されます。

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-send-chat-messages"></a>チャット メッセージを送信する関数を作成する

また、チャット メッセージを送信するには、Web アプリに HTTP API が必要です。 *SendMessage* という HTTP によってトリガーされる関数を作成します。この関数は、SignalR Service を使用して、接続されているすべてのクライアントにメッセージを送信します。

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure Functions: 関数の作成]** コマンドを検索して選択します。

1. 入力を求められたら、次の情報を指定します。

    | 名前 | 値 |
    |---|---|
    | 関数アプリ フォルダー | メイン プロジェクト フォルダーを選択します |
    | テンプレート | HTTP トリガー |
    | 名前 | SendMessage |
    | 承認レベル | Anonymous |

    新しい関数を含む **SendMessage** というフォルダーが作成されます。

1. **SendMessage/function.json** を開き、関数のバインドを構成します。 ファイルの内容を次のように変更します。
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    これで、元の関数に 2 つの変更が加えられます。
    * ルートを `messages` に変更し、HTTP トリガーを **POST** HTTP メソッドに制限します。
    * `chat` という名前の SignalR Service ハブに接続されているすべてのクライアントに、関数によって返されるメッセージを送信する SignalR Service 出力バインドを追加します。

1. ファイルを保存します。

1. **SendMessage/index.js** を開き、関数の本文を表示します。 ファイルの内容を次のように変更します。

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    この関数は、HTTP 要求から本文を取り出し、SignalR Service に接続されたクライアントに送信し、各クライアントで `newMessage` という関数を呼び出します。

    この関数は送信元の ID を読み取ることができます。また、メッセージ本文の *recipient* 値を受け取り、メッセージを 1 人のユーザーに限定して送信することができます。 これらの機能はこのチュートリアルの後半で使用します。

1. ファイルを保存します。

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)

## <a name="create-and-run-the-chat-client-web-user-interface"></a>チャット クライアント Web ユーザー インターフェイスの作成と実行

チャット アプリケーションの UI は、Vue JavaScript フレームワークで作成された単純なシングルページ アプリケーション (SPA) です。 関数アプリとは別にホストされます。 ローカルでは、Live Server VS Code 拡張機能を使用して Web インターフェイスを実行します。

1. VS Code で、メイン プロジェクト フォルダーのルートに **content** という新しいフォルダーを作成します。

1. **content** フォルダーに **index.html** という新しいファイルを作成します。

1. **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)** の内容をコピーして貼り付けます。

1. ファイルを保存します。

1. **F5** キーを押して関数アプリをローカルで実行し、デバッガーをアタッチします。

1. **index.html** を開いた状態で、VS Code コマンド パレットを開き (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)、**[Live Server: Open with Live Server]\(Live Server: Live Server で開く\)** を選択して Live Server を起動します。 Live Server はブラウザーでアプリケーションを開きます。

1. アプリケーションが開きます。 チャット ボックスにメッセージを入力して Enter キーを押します。 アプリケーションを更新して新しいメッセージを表示します。 認証が構成されていないため、すべてのメッセージは "匿名" として送信されます。

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)

## <a name="deploy-to-azure-and-enable-authentication"></a>Azure をデプロイして認証を有効にする

これまではローカルで関数アプリとチャット アプリケーションを実行してきました。 次は Azure にデプロイし、アプリケーションで認証とプライベート メッセージングを有効にします。

### <a name="log-into-azure-with-vs-code"></a>VS Code で Azure にログインする

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure: サインイン]** コマンドを探して選択します。

1. 指示に従って、ブラウザーでサインイン プロセスを完了します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Azure で実行される関数アプリには、Azure Storage アカウントが必要です。 また、Azure Storage の静的な Web サイトの機能を使用して、チャット UI の Web ページもホストします。

1. Azure portal で **[リソースの作成]** ( **+** ) ボタンをクリックして、新しい Azure リソースを作成します。

1. **[ストレージ]** カテゴリを選択し、**[ストレージ アカウント]** を選択します。

1. 次の情報を入力します。

    | 名前 | [値] |
    |---|---|
    | サブスクリプション | SignalR Service インスタンスを含むサブスクリプションを選択します |
    | Resource group | 同じリソース グループを選択します |
    | リソース名 | ストレージ アカウントの一意の名前 |
    | 場所 | 他のリソースと同じ場所を選択します |
    | パフォーマンス | Standard |
    | アカウントの種類 | StorageV2 (汎用 V2) |
    | レプリケーション | ローカル冗長ストレージ (LRS) |
    | アクセス層 | ホット |

1. **[確認および作成]**、**[作成]** の順にクリックします。

### <a name="configure-static-websites"></a>静的な Web サイトを構成する

1. ストレージ アカウントが作成されたら、Azure portal で開きます。

1. **[静的な Web サイト]** を選択します。

1. **[有効]** を選択して、静的な Web サイトの機能を有効にします。

1. **[インデックス ドキュメント名]** に「*index.html*」と入力します。

1. **[保存]** をクリックします。

1. **[プライマリ エンドポイント]** が表示されます。 この値をメモしておきます。 この値は、関数アプリを構成する際に必要になります。

### <a name="configure-function-app-for-authentication"></a>認証のために関数アプリを構成する

これまで、チャット アプリは匿名で動作しています。 Azure では、[App Service 認証](../app-service/overview-authentication-authorization.md)を使用してユーザーを認証します。 認証済みユーザーのユーザー ID またはユーザー名を *SignalRConnectionInfo* バインドに渡して、ユーザーとして認証される接続情報を生成することができます。

メッセージを送信すると、アプリは接続されているすべてのクライアントに送信するか、特定のユーザーに認証されているクライアントにのみ送信するかをアプリが決定できます。

1. VS Code で **negotiate/function.json** を開きます。

1. `{headers.x-ms-client-principal-name}` のように、[バインド式](../azure-functions/functions-triggers-bindings.md)を *SignalRConnectionInfo* バインドの *userId* プロパティに挿入します。 これで、認証済みユーザーのユーザー名に値が設定されます。 属性は次のようになります。

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. ファイルを保存します。


### <a name="deploy-function-app-to-azure"></a>Azure に関数アプリをデプロイする

1. VS Code コマンド パレットを開き (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)、**[Azure Functions: Deploy to Function App]\(Azure Functions: 関数アプリにデプロイ\)** を選択します。

1. 入力を求められたら、次の情報を指定します。

    | 名前 | 値 |
    |---|---|
    | デプロイするフォルダー | メイン プロジェクト フォルダーを選択します |
    | サブスクリプション | サブスクリプションを選択します |
    | 関数アプリ | **[Create New Function App]\(新しい関数アプリの作成\)** を選択します |
    | 関数アプリ名 | 一意の名前を入力します |
    | Resource group | SignalR Service のインスタンスと同じリソース グループを選択します |
    | ストレージ アカウント | 以前に作成したストレージ アカウントを選択します |

    新しい関数アプリが Azure に作成され、デプロイが開始されます。 デプロイが完了するまで待ちます。

### <a name="upload-function-app-local-settings"></a>関数アプリのローカル設定アップロードする

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure Functions: Upload local settings]\(Azure Functions: ローカル設定のアップロード\)** コマンドを探して選択します。

1. 入力を求められたら、次の情報を指定します。

    | 名前 | 値 |
    |---|---|
    | ローカル設定ファイル | local.settings.json |
    | サブスクリプション | サブスクリプションを選択します |
    | 関数アプリ | 以前にデプロイした関数アプリを選択します |

ローカル設定は Azure の関数アプリにアップロードされます。 既存の設定を上書きすることを確認するメッセージが表示されたら、**[すべてはい]** を選択します。


### <a name="enable-app-service-authentication"></a>App Service 認証を有効にする

App Service 認証は、Azure Active Directory、Facebook、Twitter、Microsoft アカウント、Google による認証をサポートしています。

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure Functions: Open in portal]\(Azure Functions: ポータルで開く\)** コマンドを探して選択します。

1. サブスクリプションと関数アプリ名を選択して、Azure portal で関数アプリを開きます。

1. ポータルで開いている関数アプリで、**[プラットフォーム機能]** タブを見つけ、**[認証/承認]** を選択します。

1. App Service 認証を **オン** にします。

1. **[要求が認証されない場合に実行するアクション]** で [{前の手順で選択した認証プロバイダー} でのログイン] を選択します。

1. **[許可される外部リダイレクト URL]** に、前の手順でメモしたストレージ アカウントのプライマリ Web エンドポイントの URL を入力します。

1. 選択したログイン プロバイダーのドキュメントに従って構成を完了します。

    - [Azure Active Directory](../app-service/configure-authentication-provider-aad.md)
    - [Facebook](../app-service/configure-authentication-provider-facebook.md)
    - [Twitter](../app-service/configure-authentication-provider-twitter.md)
    - [Microsoft アカウント](../app-service/configure-authentication-provider-microsoft.md)
    - [Google](../app-service/configure-authentication-provider-google.md)

### <a name="update-the-web-app"></a>Web アプリを更新する

1. Azure portal で関数アプリの概要ページに移動します。

1. 関数アプリの URL をコピーします。

    ![URL を取得する](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. VS Code で **index.html** を開き、`apiBaseUrl` の値を関数アプリの URL に置き換えます。

1. Azure Active Directory、Facebook、Twitter、Microsoft アカウント、または Google を使用した認証でアプリケーションを構成できます。 `authProvider` の値を設定して、使用する認証プロバイダーを選択します。

1. ファイルを保存します。

### <a name="deploy-the-web-application-to-blob-storage"></a>BLOB ストレージに Web アプリケーションをデプロイする

Web アプリケーションは、Azure Blob Storage の静的 Web サイト機能を使用してホストされます。

1. VS Code コマンド パレットを開きます (`Ctrl-Shift-P`、macOS: `Cmd-Shift-P`)。

1. **[Azure Storage: Deploy to Static Website]\(Azure Storage: 静的な Web サイトにデプロイ\)** コマンドを探して選択します。

1. 次の値を入力します。

    | 名前 | [値] |
    |---|---|
    | サブスクリプション | サブスクリプションを選択します |
    | ストレージ アカウント | 以前に作成したストレージ アカウントを選択します |
    | デプロイするフォルダー | **[参照]** を選択し、*content* フォルダーを選択します。 |

これで、*content* フォルダー内のファイルが静的な Web サイトにデプロイされます。

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>関数アプリのクロス オリジン リソース共有 (CORS) を有効にする

CORS 設定は **local.settings.json** にありますが、Azure の関数アプリには反映されません。 別に設定する必要があります。

1. Azure portal で関数アプリを開きます。

1. **[プラットフォーム機能]** タブで、**[CORS]** を選択します。

    ![CORS を見つける](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. *[許可されたオリジン]* セクションで、静的な Web サイトの *プライマリ エンドポイント* を含むエントリを値として追加します (末尾の */* を削除します)。

1. SignalR JavaScript SDK によってブラウザーから関数アプリが呼び出されるようにするために、CORS の資格情報のサポートを有効にする必要があります。 [Enable Access-Control-Allow-Credentials]\(Access-Control-Allow-Credentials を有効にする\) チェックボックスをオンにします。

    ![Access-Control-Allow-Credentials を有効にする](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. **[保存]** をクリックして、CORS の設定を保持します。

### <a name="try-the-application"></a>アプリケーションを試す

1. ブラウザーで、ストレージ アカウントのプライマリ Web エンドポイントに移動します。

1. **[ログイン]** を選択して、選択した認証プロバイダーの認証を受けます。

1. パブリック メッセージを送信するには、メインのチャット ボックスに入力します。

1. プライベート メッセージを送信するには、チャット履歴のユーザー名をクリックします。 選択した受信者のみがこれらのメッセージを受け取ります。

お疲れさまでした。 リアルタイムのサーバーレス チャット アプリをデプロイしました。

![デモ](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップするには、Azure portal を使用してリソース グループを削除します。

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure SignalR Service で Azure Functions を使用する方法について説明しました。 詳細については、Azure Functions に SignalR Service バインドを使用してリアルタイムのサーバーレス アプリケーションをビルドする方法を参照してください。

> [!div class="nextstepaction"]
> [Azure Functions を使用してリアルタイム アプリをビルドする](signalr-concept-azure-functions.md)

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsauth)