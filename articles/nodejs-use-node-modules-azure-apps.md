---
title: "Node.js モジュールの操作"
description: "Azure App Service または Cloud Services を使用する際の Node.js モジュールの使用方法について説明します。"
services: 
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 2d57fc14803e273157151d24efb12c3f5dd15a61
ms.lasthandoff: 03/21/2017


---
# <a name="using-nodejs-modules-with-azure-applications"></a>Azure アプリケーションでの Node.js モジュールの使用
このドキュメントは、Azure でホストされているアプリケーションでの Node.js モジュールの使用に関するガイダンスを示します。 また、Azure でアプリケーションによって特定のバージョンのモジュールとネイティブ モジュールが確実に使用されるようにするためのガイダンスも紹介します。

Node.js モジュールである **package.json** および **npm-shrinkwrap.json** ファイルの使い方について既によくわかっている方は、この記事で概要を簡単にご確認ください。

* Azure App Service では **package.json** および **npm-shrinkwrap.json** ファイルが認識され、これらのファイルのエントリに基づいてモジュールをインストールできます。
* Azure Cloud Services は、すべてのモジュールが開発環境にインストールされ、**node\_modules** ディレクトリがデプロイ パッケージの一部として含められることを想定します。 Cloud Services では **package.json** または **npm-shrinkwrap.json** ファイルを使用したモジュール インストールのサポートを有効にできますが、このサポートを有効にするには、Cloud Services のプロジェクトで使用される既定のスクリプトをカスタマイズする必要があります。 これを行う方法の例については、「 [npm インストールを実行してノード モジュールのデプロイを回避するための Microsoft Azure スタートアップ タスク](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> VM でのデプロイは Virtual Machines でホストされているオペレーティング システムに依存するので、この記事では Azure Virtual Machines については説明しません。
> 
> 

## <a name="nodejs-modules"></a>Node.js モジュール
モジュールは、アプリケーションに対して特定の機能を提供する読み込み可能な JavaScript パッケージです。 通常は **npm** コマンド ライン ツールを使用してインストールされますが、http モジュールのようにコア Node.js パッケージの一部として提供されるものもあります。

インストールされたモジュールは、アプリケーション ディレクトリ構造のルートにある **node\_modules** ディレクトリに保存されます。 この **node\_modules** ディレクトリの各モジュールに独自の **node\_modules** ディレクトリが保持されており、そこには、そのモジュールが依存するすべてのモジュールが含まれます。これは、依存関係チェーンの端から端まですべてのモジュールに対して繰り返されます。 これにより、インストールされている各モジュールが、自身が依存するモジュールに対して独自のバージョン要件を持つことができます。ただし、この結果、ディレクトリ構造が非常に大きくなる可能性があります。

**node\_modules** ディレクトリをアプリケーションの一部としてデプロイすると、デプロイのサイズは、**package.json** または **npm-shrinkwrap.json** ファイルを使用した場合に比べると大きくなりますが、運用環境で使用されるモジュールのバージョンが、開発環境で使用されるものと必ず同じになります。

### <a name="native-modules"></a>ネイティブ モジュール
ほとんどのモジュールがシンプルな JavaScript テキスト ファイルですが、中にはプラットフォーム固有のバイナリ イメージのモジュールもあります。 こうしたモジュールは、通常、インストール時に Python および node-gyp を使用してコンパイルされます。 Azure Cloud Services は、アプリケーションの一部としてデプロイされている **node\_modules** フォルダーに依存するので、インストールされたモジュールの一部として含まれるネイティブ モジュールはすべて、Windows 開発システムでインストールおよびコンパイルされている限りクラウド サービスで動作します。

Azure App Service では、すべてのネイティブ モジュールはサポートされません。また極めて特別な前提条件があるコンパイルに失敗する可能性があります。 MongoDB のような人気のあるモジュールのいくつかは、ネイティブの依存関係があります(任意)。なくても問題なく作動しますが、ほぼすべてのネイティブ モジュールで&2; つの回避策が現在利用でき、成功が実証されています。

* ネイティブ モジュールの前提条件がすべてインストールされている Windows コンピューターで **npm install** を実行します。 次に、作成した **node\_modules** フォルダーを Azure App Service へのアプリケーションの一部としてデプロイします。
  * コンパイルする前に、ローカルの Node.js インストールで対応するアーキテクチャが使用されていることと、バージョンが Azure で使用されているものにできるだけ近いことを確認してください (現在の値は、ランタイムで **process.arch** プロパティと **process.version** プロパティから確認できます)。
* Azure App Service はデプロイ中に、カスタムの bash やシェル スクリプトを実行するように構成でき、カスタム コマンドを実行し、 **npm install** が実行される方法を正確に構成できる機会を与えます。 これを行う方法を説明するビデオについては、「 [Custom Website Deployment Scripts with Kudu (Kudu でのカスタム Web サイト デプロイメント スクリプト)]」をご覧ください。

### <a name="using-a-packagejson-file"></a>package.json ファイルの使用
**package.json** ファイルは、ホスティング プラットフォームが依存関係をインストールできるように、アプリケーションに必要な最上位レベルの依存関係を指定する&1; つの手段です。この場合、**node\_packages** フォルダーをデプロイの一部として含める必要はありません。 アプリケーションのデプロイ後、**npm install** コマンドによって **package.json** ファイルが解析され、表示されているすべての依存関係がインストールされます。

開発中、モジュールをインストールするときに **--save**、**--save-dev**、または **--save-optional** パラメーターを使用すると、モジュールのエントリを **package.json** ファイルに自動的に追加することができます。 詳細については、「 [npm-install](https://docs.npmjs.com/cli/install)」を参照してください。

**package.json** ファイルの潜在的な問題の&1; つが、このファイルでは最上位レベルの依存関係のバージョンしか指定されないという点です。 インストールされているモジュールはそれぞれ、自身が依存しているモジュールのバージョンを指定することがあれば、指定しないこともあります。したがって、依存関係チェーンが、開発時に使用されたもの以外になる可能性があります。

> [!NOTE]
> Azure App Service へのデプロイ中、<b>package.json</b> ファイルがネイティブ モジュールを参照すると、Git を使用してアプリケーションを発行するときに次のようなエラーが表示されます。
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>npm-shrinkwrap.json ファイルの使用
**npm-shrinkwrap.json** ファイルは、**package.json** ファイルのモジュール バージョン管理制限への対処を試みるものです。 **package.json** ファイルに含まれるのは最上位レベルのモジュールのバージョンのみですが、**npm-shrinkwrap.json** ファイルには、完全なモジュール依存関係チェーンのバージョン要件が含まれます。

アプリケーションの運用の準備ができたら、バージョン要件を整理して確定し、**npm shrinkwrap** コマンドを使用して **npm-shrinkwrap.json** ファイルを作成できます。 これにより **node\_modules** フォルダーに現在インストールされているバージョンが使用され、これらのバージョンが **npm-shrinkwrap.json** ファイルに記録されます。 ホスティング環境へのアプリケーションのデプロイ後、**npm install** コマンドによって **npm-shrinkwrap.json** ファイルが解析され、表示されているすべての依存関係がインストールされます。 詳細については、「 [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap)」を参照してください。

> [!NOTE]
> Azure App Service へのデプロイ中、<b>npm-shrinkwrap.json</b> ファイルがネイティブ モジュールを参照すると、Git を使用してアプリケーションを発行するときに次のようなエラーが表示されます。
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

## <a name="next-steps"></a>次のステップ
ここでは、Azure で Node.js モジュールを使う方法について説明しました。次は、[Node.js のバージョンを指定する]方法、[Node.js Web アプリを構築およびデプロイする](app-service-web/web-sites-nodejs-develop-deploy-mac.md)方法、[Mac および Linux で Azure コマンド ライン インターフェイスを使う方法]について、それぞれのトピックをご覧ください。

詳細については、 [Node.js デベロッパー センター](/develop/nodejs/)を参照してください。

[Node.js のバージョンを指定する]: nodejs-specify-node-version-azure-apps.md
[Mac および Linux で Azure コマンド ライン インターフェイスを使う方法]:cli-install-nodejs.md
[build and deploy a Node.js web app]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Custom Website Deployment Scripts with Kudu (Kudu でのカスタム Web サイト デプロイメント スクリプト)]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/

