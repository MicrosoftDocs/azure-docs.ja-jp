---
title: "Express を使用した Web アプリケーション (Node.js) | Microsoft Docs"
description: "クラウド サービスのチュートリアルを基に、Express モジュールの使用方法を示すチュートリアル。"
services: cloud-services
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 331d82a987aebe422ca2f560d856dad9f1b733f3


---
# <a name="build-a-nodejs-web-application-using-express-on-an-azure-cloud-service"></a>Azure Cloud Services での Express を使用した Node.js Web アプリケーションの構築
node.js には、コア ランタイムの最小限の機能セットが含まれます。
多くの場合、開発者は Node.js アプリケーションを開発するときに、サード パーティ モジュールを使用して追加機能を指定します。 このチュートリアルでは、[Express][Express] モジュールを使用して新しいアプリケーションを作成します。Express モジュールは、Node.js Web アプリケーションを作成するための MVC フレームワークを提供します。

完成したアプリケーションのスクリーンショットは次のようになります。

!["Welcome to Express in Azure" と表示している Web ブラウザー](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>クラウド サービス プロジェクトの作成
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

"expressapp" という名前の新しいクラウド サービス プロジェクトを作成するには、次の手順を実行します。

1. **[スタート]** メニューまたは**スタート画面**で、**Windows PowerShell** を検索します。 最後に、**[Windows PowerShell]** を右クリックし、**[管理者として実行]** を選択します。
   
    ![Azure PowerShell アイコン](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. **c:\\node** ディレクトリに移動し、次のコマンドを入力して **expressapp** という名前の新しいソリューションと **WebRole1** という名前の Web ロールを作成します。
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > 既定では、**Add-AzureNodeWebRole** は以前のバージョンの Node.js を使用します。 上記の **Set-AzureServiceProjectRole** ステートメントは v0.10.21 の Node.js を使用するよう Azure に指示します。  パラメーターには大文字と小文字の区別があることに注意してください。  Node.js の正しいバージョンが選択されていることを検証できます。このためには、**WebRole1\package.json** の **engines** プロパティを確認します。
    > 
    > 

## <a name="install-express"></a>Express のインストール
1. 次のコマンドを発行して Express ジェネレーターをインストールします。
   
        PS C:\node\expressapp> npm install express-generator -g
   
    この npm コマンドにより次のような結果が出力されます。 
   
    ![Windows PowerShell での npm install express コマンドの出力の表示](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. **WebRole1** ディレクトリに移動し、express コマンドを使用して新しいアプリケーションを生成します。
   
        PS C:\node\expressapp\WebRole1> express
   
    前のアプリケーションを上書きするかどうかを確認するメッセージが表示されます。 「**y**」または「**yes**」と入力して続行します。 Express によって、app.js ファイルと、アプリケーションを構築するためのフォルダー構造が生成されます。
   
    ![express コマンドの出力](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. package.json ファイルに定義された依存関係をインストールするには、次のコマンドを入力します。
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![npm install コマンドの出力](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. 次のコマンドを使用して、**bin/www** ファイルを **server.js** にコピーします。 これは、クラウド サービスがこのアプリケーションのエントリ ポイントを見つけられるようにするためです。
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   このコマンドが完了すると、WebRole1 ディレクトリ内に **server.js** ファイルが作成されています。
5. **server.js** を変更して、次の行から '.' 文字を&1; つ削除します。
   
       var app = require('../app');
   
   この変更を行うと、行は次のようになります。
   
       var app = require('./app');
   
   ファイル (以前の **bin/www**) を必要なアプリケーション ファイルと同じディレクトリに移動しているため、この変更が必要です。 この変更を行った後、 **server.js** ファイルを保存します。
6. 次のコマンドを使用して、アプリケーションを Microsoft Azure エミュレーターで実行します。
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Welcome to Express メッセージを含む Web ページ](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>ビューの変更
次に、"Welcome to Express in Azure" というメッセージが表示されるようにビューを変更します。

1. 次のコマンドを入力して、index.jade ファイルを開きます。
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![index.jade ファイルの内容](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade は Express アプリケーションで使用される既定のビュー エンジンです。 Jade ビュー エンジンの詳細については、[http://jade-lang.com][http://jade-lang.com] を参照してください。
2. テキストの最後の行に **in Azure**を追加します。
   
   ![index.jade ファイル。最後の行には "p Welcome to \#{title} in Azure" と記載されている](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. ファイルを保存して、メモ帳を終了します。
4. ブラウザーの表示を最新情報に更新すると、変更を確認できます。
   
   ![ブラウザー ウィンドウで "Welcome to Express in Azure" と表示されたページ](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

アプリケーションのテストが終了したら、 **Stop-AzureEmulator** コマンドレットを使用してエミュレーターを停止します。

## <a name="publishing-the-application-to-azure"></a>Azure にアプリケーションをデプロイする
Azure PowerShell ウィンドウで、 **Publish-AzureServiceProject** コマンドレットを使用してアプリケーションをクラウド サービスにデプロイします。

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

デプロイ操作が完了すると、ブラウザーが開き、Web ページが表示されます。

![Express ページを表示している Web ブラウザー。 URL は、ページが Azure でホストされていることを示している。](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>次のステップ
詳細については、 [Node.js デベロッパー センター](/develop/nodejs/)を参照してください。

[Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: http://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com





<!--HONumber=Jan17_HO1-->


