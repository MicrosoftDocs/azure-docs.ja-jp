---
title: Socket.io を使用する Node.js アプリケーション - Azure
description: このチュートリアルを使用し、Azure で Socket.IO ベースのチャット アプリケーションをホストする方法について説明します。 Socket.IO は、node.js のサーバーとクライアントにリアルタイム通信を提供します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: abc02769d7d978e14975d90ae0f98547bdc4faf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743323"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service-classic"></a>Azure Cloud Services (クラシック) で Socket.IO を使用する Node.js チャット アプリケーションをビルドする

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

Socket.IO は、node.js サーバーとクライアントの間のリアルタイム通信を提供します。 このチュートリアルでは、Azure で socket.IO ベースのチャット アプリケーションをホストする手順を説明します。 Socket.IO の詳細については、[socket.io](https://socket.io) を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![Azure でホストされるサービスを表示しているブラウザー ウィンドウ][completed-app]

## <a name="prerequisites"></a>前提条件
この記事の例を正常に完了するには、次の製品とバージョンがインストールされている必要があります。

* [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* [Node.js](https://nodejs.org/download/)
* [Python version 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>クラウド サービス プロジェクトの作成
次の手順では、Socket.IO アプリケーションをストリーミングするクラウド サービス プロジェクトを作成します。

1. **[スタート]** メニューまたは **スタート画面** で、**Windows PowerShell** を検索します。 最後に、 **[Windows PowerShell]** を右クリックし、 **[管理者として実行]** を選択します。

    ![Azure PowerShell アイコン][powershell-menu]
2. **c:\\node** というディレクトリを作成します。

    ```powershell
    PS C:\> md node
    ```

3. ディレクトリを **c:\\node** ディレクトリに変更します。

    ```powershell
    PS C:\> cd node
    ```

4. 次のコマンドを入力して、`chatapp` という名前の新しいソリューションと `WorkerRole1` という名前の worker ロールを作成します。

    ```powershell
    PS C:\node> New-AzureServiceProject chatapp
    PS C:\Node> Add-AzureNodeWorkerRole
    ```

    次の応答が表示されます。

    ![new-azureservice および add-azurenodeworkerrole コマンドレットの出力](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>チャットのサンプルのダウンロード
このプロジェクトでは、 [Socket.IO GitHub リポジトリ]にあるチャットのサンプルを使用します。 次の手順を実行して、サンプルをダウンロードし、先ほど作成したプロジェクトに追加します。

1. **[複製]** ボタンを使用して、リポジトリのローカル コピーを作成します。 **[ZIP]** ボタンを使用してプロジェクトをダウンロードすることもできます。

   ![ZIP ダウンロード アイコンが強調表示されている状態の https://github.com/LearnBoost/socket.io/tree/master/examples/chat を示すブラウザー ウィンドウ](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. ローカル リポジトリのディレクトリ構造を、**examples\\chat** ディレクトリまで移動します。 このディレクトリの内容を、先ほど作成した **C:\\node\\chatapp\\WorkerRole1** ディレクトリにコピーします。

   ![アーカイブから展開された examples\\chat ディレクトリの内容を表示しているエクスプローラー][chat-contents]

   上記のスクリーンショットの強調表示されている項目は、**examples\\chat** ディレクトリからコピーされたファイルです。
3. **C:\\node\\chatapp\\WorkerRole1** ディレクトリで、**server.js** ファイルを削除し、**app.js** ファイルの名前を **server.js** に変更します。 これにより、前に **Add-AzureNodeWorkerRole** コマンドレットで作成された既定の **server.js** ファイルが削除され、チャットのサンプルのアプリケーション ファイルに置き換えられます。

### <a name="modify-serverjs-and-install-modules"></a>Server.js の変更とモジュールのインストール
Azure エミュレーターでアプリケーションをテストする前に、いくつかの点を変更する必要があります。 次の手順で server.js ファイルを変更します。

1. Visual Studio または任意のテキスト エディターで **server.js** ファイルを開きます。
2. server.js の先頭にある **Module dependencies** セクションを探して、次のように **sio = require('..//..//lib//socket.io')** を含む行を **sio = require('socket.io')** に変更します。

    ```js
    var express = require('express')
      , stylus = require('stylus')
      , nib = require('nib')
    //, sio = require('..//..//lib//socket.io'); //Original
      , sio = require('socket.io');                //Updated
      var port = process.env.PORT || 3000;         //Updated
    ```

3. アプリケーションが適切なポートでリッスンするように、メモ帳などのエディターで server.js を開き、次の行の **3000** を **process.env.port** に変更します。

    ```js
    //app.listen(3000, function () {            //Original
    app.listen(process.env.port, function () {  //Updated
      var addr = app.address();
      console.log('   app listening on http://' + addr.address + ':' + addr.port);
    });
    ```

**server.js** の変更内容を保存した後、次の手順に従って必要なモジュールをインストールし、Azure エミュレーターでアプリケーションをテストします。

1. **Azure PowerShell** を使用して、**C:\\node\\chatapp\\WorkerRole1** ディレクトリに移動し、次のコマンドを使用して、このアプリケーションで必要なモジュールをインストールします。

    ```powershell
    PS C:\node\chatapp\WorkerRole1> npm install
    ```

   これによって、package.json ファイルにリストされているモジュールがインストールされます。 コマンドが完了すると、次のような出力が表示されます。

   ![npm install コマンドの出力][The-output-of-the-npm-install-command]
2. このサンプルはもともと Socket.IO GitHub リポジトリの一部であり、相対 パスによって Socket.IO ライブラリを直接参照していたため、package.json ファイルでは Socket.IO は参照されていませんでした。そのため、次のコマンド を発行してインストールする必要があります。

   ```powershell
   PS C:\node\chatapp\WorkerRole1> npm install socket.io --save
    ```

### <a name="test-and-deploy"></a>テストとデプロイ
1. 次のコマンドを発行してエミュレーターを起動します。

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
    ```

   > [!NOTE]
   > エミュレーターの起動時に問題が発生した場合 (例: Start-AzureEmulator : 予期しないエラーが発生しました。  詳細: 予期しないエラーが発生しました)、通信オブジェクト System.ServiceModel.Channels.ServiceChannel はフォールト状態にあるため、これを通信に使用することはできません。
   >
   > AzureAuthoringTools v 2.7.1 と AzureComputeEmulator v 2.7 を再インストールし、そのバージョンが一致することを確認します。

2. ブラウザーを開き、 **http://127.0.0.1** に移動します。
3. ブラウザー ウィンドウが開いたら、ニックネームを入力して Enter キーを押します。
   これにより、特定のニックネームでメッセージを投稿できます。 マルチユーザー機能をテストするには、同じ URL を使用して新しいブラウザー ウィンドウを開き、別のニックネームを入力します。

   ![User1 と User2 からのチャット メッセージを表示している 2 つのブラウザー ウィンドウ](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. アプリケーションのテストが終了したら、次のコマンドを発行してエミュレーターを停止します。

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
    ```

5. Azure にアプリケーションをデプロイするには、 **Publish-AzureServiceProject** コマンドレットを使用します。 次に例を示します。

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
    ```

   > [!IMPORTANT]
   > 必ず一意の名前を使用してください。一意でない場合は発行処理が失敗します。 デプロイが完了すると、ブラウザーが開き、デプロイされたサービスに移動します。
   >
   > 指定したサブスクリプション名がインポートされた発行プロファイルに存在しないというエラーが出力された場合は、Azure にデプロイする前に、サブスクリプションの発行プロファイルをダウンロードしてインストールする必要があります。 「 **Node.js アプリケーションの構築と Azure のクラウド サービスへのデプロイ** 」の「 [Azure へのアプリケーションのデプロイ](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

   ![Azure でホストされるサービスを表示しているブラウザー ウィンドウ][completed-app]

   > [!NOTE]
   > 指定したサブスクリプション名がインポートされた発行プロファイルに存在しないというエラーが出力された場合は、Azure にデプロイする前に、サブスクリプションの発行プロファイルをダウンロードしてインストールする必要があります。 「 **Node.js アプリケーションの構築と Azure のクラウド サービスへのデプロイ** 」の「 [Azure へのアプリケーションのデプロイ](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

これで、アプリケーションは Azure で実行されるようになり、Socket.IO を使用する複数のクライアント間でチャット メッセージを中継できます。

> [!NOTE]
> わかりやすくするために、このサンプルは同じインスタンスに接続したユーザー間でのチャットに制限されています。 つまり、クラウド サービスによって 2 つの worker ロール インスタンスが作成された場合、ユーザーは同じ worker ロール インスタンスに接続された他のユーザーとのみチャットすることができます。 複数のロール インスタンスで機能するようにこのアプリケーションを拡張するには、Service Bus などのテクノロジを使用して、インスタンス間で Socket.IO ストアの状態を共有します。 たとえば、 [Azure SDK for Node.js GitHub リポジトリ](https://github.com/WindowsAzure/azure-sdk-for-node)にある Service Bus キューおよびトピックの使用例を参照してください。
>
>

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Cloud Services でホストされる基本的なチャット アプリケーションを作成する方法を説明しました。 Azure Web サイトでこのアプリケーションをホストする方法については、「[Azure Websites で Socket.IO を使用する Node.js チャット アプリケーションを構築する][chatwebsite]」を参照してください。

詳細については、 [Node.js デベロッパー センター](/azure/developer/javascript/)を参照してください。

[chatwebsite]: ./cloud-services-nodejs-develop-deploy-app.md

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png