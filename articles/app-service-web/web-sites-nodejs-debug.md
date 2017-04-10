---
title: "Azure App Service で Node.js Web アプリをデバッグする方法"
description: "Azure App Service での Node.js Web アプリの作成方法の学習"
tags: azure-portal
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: a48f906c-1a3e-43bc-ae84-7d2dde175b15
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 29cb2faa2e2d6cb9f45242794d88d3c8f881539d
ms.lasthandoff: 03/15/2017


---
# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>Azure App Service で Node.js Web アプリをデバッグする方法
Azure では、ビルトインの診断機能により、Azure の [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps でホストされる Node.js アプリケーションのデバッグが支援されます。 この記事では、stdout と stderr のログ記録を有効にし、エラー情報をブラウザーに表示する方法と、ログ ファイルをダウンロードして表示する方法について説明します。

Azure でホストされる Node.js アプリケーションの診断は、 [IISNode]によって提供されます。 この記事では、診断情報を集めるための最も一般的な設定について説明しますが、IISNode を操作するための完全なリファレンスを提供してはいません。 IISNode の操作の詳細については、GitHub の [IISNode Readme] を参照してください。

<a id="enablelogging"></a>

## <a name="enable-logging"></a>ログの有効化
App Service Web アプリは、既定では Git を使用して Web アプリをデプロイするときなど、デプロイに関する診断情報だけをキャプチャします。 この情報は、デプロイ中に問題が発生した場合 (たとえば、 **package.json**で参照されているモジュールのインストールが失敗した場合) や、カスタムのデプロイ スクリプトを使用している場合に便利です。

stdout および stderr ストリームのログ記録を有効にするには、Node.js アプリケーションのルートに **IISNode.yml** ファイルを作成し、次のコードを追加する必要があります。

    loggingEnabled: true

これで、Node.js アプリケーションからの stderr と stdout のログ記録が有効になります。

エラーが発生したときにわかりやすいエラーと開発者エラーのいずれをブラウザーに返すか指定するために、 **IISNode.yml** ファイルを使用することもできます。 開発者エラーを有効にするには、 **IISNode.yml** ファイルに次の行を追加します。

    devErrorsEnabled: true

このオプションが有効になると、IISNode は "内部サーバー エラーが発生しました" のようなわかりやすいエラーの代わりに、stderr に送信された情報の最後の 64 K を返します。

> [!NOTE]
> 開発中に問題を診断する場合には devErrorsEnabled は便利ですが、運用環境でこれを有効にすると、開発エラーがエンド ユーザーに送信されることになります。
> 
> 

**IISNode.yml** ファイルがまだアプリケーション内に存在しなかった場合は、更新されたアプリケーションを発行してから Web アプリを再起動する必要があります。 以前に発行された既存の **IISNode.yml** ファイル内の設定を変更しただけの場合、再起動は必要ありません。

> [!NOTE]
> Azure コマンド ライン ツールまたは Azure PowerShell コマンドレットを使用して Web アプリを作成した場合は、既定の **IISNode.yml** ファイルが自動的に作成されます。
> 
> 

Web アプリを再起動するには、 [Azure ポータル](https://portal.azure.com)で Web アプリを選択し、 **[再起動]** ボタンをクリックします。

![再起動ボタン][restart-button]

開発環境に Azure コマンド ライン ツールがインストールされている場合は、次のコマンドを使用して Web アプリを再起動できます。

    azure site restart [sitename]

> [!NOTE]
> 診断情報をキャプチャするために最もよく使用される IISNode.yml 構成オプションは loggingEnabled と devErrorsEnabled ですが、IISNode.yml はホスティング環境のさまざまなオプションを構成するためにも使用できます。 すべての構成オプションの一覧については、[iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml)ファイルを参照してください。
> 
> 

<a id="viewlogs"></a>

## <a name="accessing-logs"></a>ログへのアクセス
診断ログには、3 種類の方法でアクセスできます。ファイル転送プロトコル (FTP) を使用する方法、Zip アーカイブをダウンロードする方法、またはログのライブ更新されたストリームにアクセスする方法 ("tail" とも呼ばれます) です。 ログ ファイルの Zip アーカイブのダウンロードと、ライブ ストリームの表示には、Azure コマンド ライン ツールが必要です。 これらのツールをインストールするには、次のコマンドを使用します。

    npm install azure-cli -g

インストール後、ツールには "azure" コマンドを使用してアクセスできます。 コマンド ライン ツールは、まず、Azure サブスクリプションを使用するように構成する必要があります。 このタスクを実行する方法については、「 **Azure コマンド ライン ツールの使用方法** 」という記事の「 [発行の設定をダウンロードおよびインポートする方法](../xplat-cli-connect.md) 」のセクションを参照してください。

### <a name="ftp"></a>FTP
FTP を通じて診断情報にアクセスするには、 [Azure ポータル](https://portal.azure.com)にアクセスし、Web アプリを選択して、 **[ダッシュボード]**を選択します。 **[クイック リンク]** セクションの **[FTP 診断ログ]** および **[FTPS 診断ログ]** リンクから、FTP プロトコルを使用してログにアクセスできます。

> [!NOTE]
> FTP またはデプロイのためにユーザー名とパスワードを構成したことがない場合は、**[クイック スタート]** 管理ページで **[デプロイ資格情報を設定する]** を選択して構成できます。
> 
> 

ダッシュボードで返される FTP URL は、**LogFiles** ディレクトリのものです。このディレクトリには、次のサブディレクトリが含まれます。

* [デプロイ方法](web-sites-deploy.md) - Git のようなデプロイ方法を使用する場合は、同じ名前のディレクトリが作成され、デプロイに関連する情報が含められます。
* nodejs - アプリケーションのすべてのインスタンスからキャプチャされた stdout および stderr 情報 (loggingEnabled が true の場合)。

### <a name="zip-archive"></a>Zip アーカイブ
診断ログの Zip アーカイブをダウンロードするには、Azure コマンド ライン ツールで次のコマンドを使用します。

    azure site log download [sitename]

これで、 **diagnostics.zip** が現在のディレクトリにダウンロードされます。 このアーカイブには、次のディレクトリ構造が含まれています。

* deployments - アプリケーションのデプロイに関する情報のログ
* LogFiles
  
  * [デプロイ方法](web-sites-deploy.md) - Git のようなデプロイ方法を使用する場合は、同じ名前のディレクトリが作成され、デプロイに関連する情報が含められます。
  * nodejs - アプリケーションのすべてのインスタンスからキャプチャされた stdout および stderr 情報 (loggingEnabled が true の場合)。

### <a name="live-stream-tail"></a>ライブ ストリーム (tail)
診断ログ情報のライブ ストリームを表示するには、Azure コマンド ライン ツールで次のコマンドを使用します。

    azure site log tail [sitename]

これで、サーバーでログ イベントが発生するたびに更新される、ログ イベントのストリームが返されます。 このストリームは、デプロイ情報のほかに、stdout および stderr 情報を返します (loggingEnabled が true の場合)。

<a id="nextsteps"></a>

## <a name="next-steps"></a>次のステップ
この記事では、Azure の診断情報を有効にし、それにアクセスする方法について学習しました。 この情報は、アプリケーションで発生する問題を理解するために役立ちますが、使用しているモジュールの問題や、App Service Web Apps で使用されている Node.js のバージョンがデプロイ環境で使用されているものと異なることが指摘される場合もあります。

Azure でのモジュールの操作については、「 [Azure アプリケーションでの Node.js モジュールの使用](../nodejs-use-node-modules-azure-apps.md)」を参照してください。

アプリケーションの Node.js バージョンの指定については、「 [Azure アプリケーションでの Node.js のバージョンの指定]」を参照してください。

詳細については、 [Node.js デベロッパー センター](/develop/nodejs/)を参照してください。

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

[IISNode]: https://github.com/tjanczuk/iisnode
[IISNode Readme]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]:../cli-install-nodejs.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Azure アプリケーションでの Node.js のバージョンの指定]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png


