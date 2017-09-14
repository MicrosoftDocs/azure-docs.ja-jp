---
title: "Node.js モジュールの操作"
description: "Azure App Service または Cloud Services を使用する際の Node.js モジュールの使用方法について説明します。"
services: 
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 94ddae4473b2d9d212e05d3df089eb6b2b87cbd8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="using-nodejs-modules-with-azure-applications"></a>Azure アプリケーションでの Node.js モジュールの使用
このドキュメントは、Azure でホストされているアプリケーションでの Node.js モジュールの使用に関するガイダンスを示します。 また、Azure でアプリケーションによって特定のバージョンのモジュールとネイティブ モジュールが確実に使用されるようにするためのガイダンスも紹介します。

Node.js モジュールである **package.json** および **npm-shrinkwrap.json** ファイルの使い方について既によくわかっている方は、この記事で説明することの概要を簡単にご確認ください。

* Azure App Service では **package.json** および **npm-shrinkwrap.json** ファイルが認識され、これらのファイルのエントリに基づいてモジュールをインストールできます。

* Azure Cloud Services は、すべてのモジュールが開発環境にインストールされ、**node\_modules** ディレクトリがデプロイ パッケージの一部として含められることを想定します。 Cloud Services では **package.json** または **npm-shrinkwrap.json** ファイルを使用したモジュール インストールのサポートを有効にできますが、この構成では、Cloud Services のプロジェクトで使用される既定のスクリプトをカスタマイズする必要があります。 この環境を構成する方法の例については、「[npm インストールを実行してノード モジュールのデプロイを回避するための Microsoft Azure スタートアップ タスク](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)」を参照してください。

> [!NOTE]
> VM でのデプロイは Virtual Machines でホストされているオペレーティング システムに依存するので、この記事では Azure Virtual Machines については説明しません。
> 
> 

## <a name="nodejs-modules"></a>Node.js モジュール
モジュールは、アプリケーションに対して特定の機能を提供する読み込み可能な JavaScript パッケージです。 モジュールは通常、**npm** コマンド ライン ツールを使用してインストールされますが、一部のモジュール (http モジュールなど) はコア Node.js パッケージの一部として提供されます。

インストールされたモジュールは、アプリケーション ディレクトリ構造のルートにある **node\_modules** ディレクトリに保存されます。 **node\_modules** ディレクトリ内の各モジュールは、それが依存しているモジュールをすべて含む独自の **node\_modules** ディレクトリを保持します。この関係は、依存関係チェーンの最後まですべてのモジュールで繰り返されます。 この環境では、インストールされている各モジュールが、依存するモジュールに関して独自のバージョン要件を持つことができます。ただし、ディレクトリ構造が非常に大きくなる場合があります。

**node\_modules** ディレクトリをアプリケーションの一部としてデプロイすると、デプロイのサイズは、**package.json** または **npm-shrinkwrap.json** ファイルを使用した場合より大きくなりますが、運用環境で使用されるモジュールのバージョンが、開発環境で使用されるモジュールと同じであることが確かに保証されます。

### <a name="native-modules"></a>ネイティブ モジュール
ほとんどのモジュールがシンプルな JavaScript テキスト ファイルですが、中にはプラットフォーム固有のバイナリ イメージのモジュールもあります。 こうしたモジュールは、通常、インストール時に Python および node-gyp を使用してコンパイルされます。 Azure Cloud Services は、アプリケーションの一部としてデプロイされている **node\_modules** フォルダーに依存するので、インストールされたモジュールの一部として含まれるネイティブ モジュールはすべて、Windows 開発システムでインストールおよびコンパイルされている限りクラウド サービスで動作します。

Azure App Service では、すべてのネイティブ モジュールがサポートされるわけではありません。また、固有の前提条件があるモジュールのコンパイル時にサービスが失敗することもあります。 MongoDB のような人気のある一部のモジュールには、任意で満たすネイティブの依存関係があって、それが満たされなくても正常に機能しますが、現在、ほぼすべてのネイティブ モジュールで成功が実証されている 2 つの回避策を利用できます。

* ネイティブ モジュールの前提条件がすべてインストールされている Windows コンピューターで **npm install** を実行します。 次に、作成した **node\_modules** フォルダーを Azure App Service へのアプリケーションの一部としてデプロイします。

  * コンパイルする前に、ローカルの Node.js インストールで対応するアーキテクチャが使用されていることと、バージョンが Azure で使用されているものにできるだけ近いことを確認してください (現在の値は、ランタイムで **process.arch** プロパティと **process.version** プロパティから確認できます)。

* Azure App Service はデプロイ中に、カスタムの bash やシェル スクリプトを実行するように構成でき、カスタム コマンドを実行し、 **npm install** が実行される方法を正確に構成できる機会を与えます。 その環境を構成する方法を示したビデオについては、「[Custom Website Deployment Scripts with Kudu (Kudu でのカスタム Web サイト デプロイメント スクリプト)]」をご覧ください。

### <a name="using-a-packagejson-file"></a>package.json ファイルの使用
**package.json** ファイルは、ホスティング プラットフォームが依存関係をインストールできるように、アプリケーションに必要な最上位レベルの依存関係を指定する 1 つの手段です。この場合、**node\_packages** フォルダーをデプロイの一部として含める必要はありません。 アプリケーションのデプロイ後、**npm install** コマンドによって **package.json** ファイルが解析され、表示されているすべての依存関係がインストールされます。

開発中、モジュールをインストールするときに **--save**、**--save-dev**、または **--save-optional** パラメーターを使用すると、モジュールのエントリを **package.json** ファイルに自動的に追加することができます。 詳細については、「 [npm-install](https://docs.npmjs.com/cli/install)」を参照してください。

**package.json** ファイルの潜在的な問題の 1 つが、このファイルでは最上位レベルの依存関係のバージョンしか指定されないという点です。 インストールされているモジュールはそれぞれ、自身が依存しているモジュールのバージョンを指定することがあれば、指定しないこともあります。したがって、依存関係チェーンが、開発時に使用されたもの以外になる可能性があります。

> [!NOTE]
> Azure App Service へのデプロイ中、<b>package.json</b> ファイルがネイティブ モジュールを参照すると、Git を使用してアプリケーションを発行するときに次の例のようなエラーが表示される場合があります。
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>npm-shrinkwrap.json ファイルの使用
**npm-shrinkwrap.json** ファイルは、**package.json** ファイルのモジュール バージョン管理制限への対処を試みるものです。 **package.json** ファイルに含まれるのは最上位レベルのモジュールのバージョンのみですが、**npm-shrinkwrap.json** ファイルには、完全なモジュール依存関係チェーンのバージョン要件が含まれます。

アプリケーションの運用の準備ができたら、バージョン要件を整理して確定し、**npm shrinkwrap** コマンドを使用して **npm-shrinkwrap.json** ファイルを作成できます。 このコマンドでは、**node\_modules** フォルダーに現在インストールされているバージョンが使用され、これらのバージョンが **npm-shrinkwrap.json** ファイルに記録されます。 ホスティング環境へのアプリケーションのデプロイ後、**npm install** コマンドによって **npm-shrinkwrap.json** ファイルが解析され、表示されているすべての依存関係がインストールされます。 詳細については、「 [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap)」を参照してください。

> [!NOTE]
> Azure App Service へのデプロイ中、<b>npm-shrinkwrap.json</b> ファイルがネイティブ モジュールを参照すると、Git を使用してアプリケーションを発行するときに次の例のようなエラーが表示される場合があります。
> 
> npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
> 
> npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
> 
> 

## <a name="next-steps"></a>次のステップ
ここでは、Azure で Node.js モジュールを使う方法について説明しました。次は、[Node.js のバージョンを指定する]方法、[Node.js Web アプリを構築およびデプロイする](app-service-web/app-service-web-get-started-nodejs.md)方法、[Mac および Linux で Azure コマンド ライン インターフェイスを使う方法]について、それぞれのトピックをご覧ください。

詳細については、 [Node.js デベロッパー センター](/nodejs/azure/)を参照してください。

[Node.js のバージョンを指定する]: nodejs-specify-node-version-azure-apps.md
[Mac および Linux で Azure コマンド ライン インターフェイスを使う方法]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu (Kudu でのカスタム Web サイト デプロイメント スクリプト)]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo

