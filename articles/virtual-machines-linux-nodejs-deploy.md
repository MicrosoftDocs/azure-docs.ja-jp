---
title: "Node.js アプリケーションを Azure 内の Linux 仮想マシンにデプロイする"
description: "Node.js アプリケーションを Azure 内の Linux 仮想マシンにデプロイする方法について説明します"
services: 
documentationcenter: nodejs
author: stepro
manager: dmitryr
editor: 
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: /azure
ms.assetid: 857a812d-c73e-4af7-a985-2d0baf8b6f71
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2016
ms.author: stephpr
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 0de0314902805a2bdb37ce3c6f79ec221f3aed31
ms.lasthandoff: 12/06/2016


---
# <a name="deploy-a-nodejs-application-to-linux-virtual-machines-in-azure"></a>Node.js アプリケーションを Azure 内の Linux 仮想マシンにデプロイする
このチュートリアルでは、Node.js アプリケーションを入手し、Azure で実行されている Linux 仮想マシンにデプロイする方法について説明します。 このチュートリアルの手順は、Node.js を実行できる任意のオペレーティング システムで使用できます。

学習内容は次のとおりです。

* 単純な TODO アプリケーションが含まれている GitHub リポジトリをフォークして複製する方法。
* アプリケーションを実行するために Azure 内に 2 つの Linux 仮想マシンを作成、構成する方法。
* 更新を Web フロントエンド仮想マシンにプッシュして、アプリケーションを反復処理する方法。

> [!NOTE]
> このチュートリアルを完了するには、GitHub アカウントと Microsoft Azure アカウントが必要です。また、開発用コンピューターから Git を使用できる必要があります。
> 
> GitHub アカウントをお持ちでない場合は、 [こちら](https://github.com/join)でサインアップできます。
> 
> [Microsoft Azure ](https://azure.microsoft.com/)アカウントをお持ちでない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)で無料試用版にサインアップできます。 ここでは、 [Microsoft アカウント](http://account.microsoft.com) をお持ちでない場合にはそのサインアップ プロセスについても説明します。 また、Visual Studio サブスクライバーである場合は、 [MSDN の特典を利用](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)できます。
> 
> お使いの開発用コンピューターに Git がなく、使用しているコンピューターが Macintosh または Windows の場合は、 [こちら](http://www.git-scm.com)から Git をインストールしてください。 Linux を使用している場合は、 `sudo apt-get install git`などの最適なメカニズムを使用して Git をインストールしてください。
> 
> 

## <a name="forking-and-cloning-the-todo-application"></a>TODO アプリケーションのフォークと複製
このチュートリアルで使用する TODO アプリケーションでは、単純な Web フロントエンドが、TODO リストを追跡する MongoDB インスタンスで実装されます。 GitHub にサインインした後、 [こちら](https://github.com/stepro/node-todo) に移動してアプリケーションを見つけたら、右上のリンクを使用してフォークします。 これで、アカウントに *accountname*/node-todo という名前のリポジトリが作成されます。

次に、開発用コンピューターにこのリポジトリを複製します。

    git clone https://github.com/accountname/node-todo.git

このリポジトリのローカル クローンは、少し後でソース コードを変更するときに使用します。

## <a name="creating-and-configuring-the-linux-virtual-machines"></a>Linux 仮想マシンの作成と構成
Azure では、Linux 仮想マシンを使用したそのままのコンピューティングがうまくサポートされています。 チュートリアルのこのセクションでは、2 つの Linux 仮想マシンを作成し、それらに TODO アプリケーションをデプロイする簡単な方法を説明します。これにより、一方の仮想マシンでは Web フロントエンドが実行され、もう一方の仮想マシンでは MongoDB インスタンスが実行されます。

### <a name="creating-virtual-machines"></a>仮想マシンの作成
Azure で新しい仮想マシンを作成する最も簡単な方法は、Azure ポータルを使用することです。 [こちら](https://portal.azure.com) をクリックしてサインインし、お使いの Web ブラウザーで Azure ポータルを起動します。 Azure ポータルの読み込みが終わったら、次の手順を実行します。

* [+新規] リンクをクリックします。
* [Compute] カテゴリを選択し、[Ubuntu Server 14.04 LTS] を選択します。
* [リソース マネージャー] デプロイ モデルを選択し、[作成] をクリックします。
* 次のガイドラインに従って基本情報を入力します。
  * 後で簡単に識別できる名前を指定する。
  * このチュートリアルではパスワード認証を選択する。
  * 特定可能な名前で新しいリソース グループを作成する。
* このチュートリアルでは、仮想マシンのサイズに "A1 Standard" を選択することが妥当です。
* その他の設定については、ディスクの種類が [Standard] であることを確認し、残りはすべて既定値をそのまま使用します。
* [概要] ページで作成を開始します。

上記の手順を 2 回実行して、2 つの Linux 仮想マシンを作成します。そのうち一方は Web フロントエンドに使用し、もう一方は MongoDB インスタンスに使用します。 仮想マシンの作成には約 5 ～ 10 分かかります。

### <a name="assigning-a-dns-entry-for-virtual-machines"></a>仮想マシンの DNS エントリの割り当て
Azure に作成された仮想マシンは、既定では、1.2.3.4 などのパブリック IP アドレスのみを使用してアクセスできます。 仮想マシンをより簡単に識別できるように、DNS エントリを割り当てましょう。

仮想マシンが作成されたことがポータルで示されたら、左側のナビゲーション バーの [仮想マシン] リンクをクリックし、自分の仮想マシンを探します。 各マシンについて次の手順を実行します。

* [要点] タブを探して、パブリック IP アドレスをクリックします。
* パブリック IP アドレスの構成で、DNS 名ラベルを割り当てて保存します。

ポータルでは、指定した名前を使用できるようになります。 構成の保存後、仮想マシンのホスト名は `machinename.region.cloudapp.azure.com`のようになります。

### <a name="connecting-to-the-virtual-machines"></a>仮想マシンへの接続
プロビジョニング後の仮想マシンは、SSH 経由のリモート接続を許可するように事前に構成されていました。 これは、仮想マシンの構成に使用するメカニズムです。 開発に Windows を使用している場合、SSH クライアントをまだ持っていなければ入手する必要があります。 ここでは一般的に PuTTY を選択します。PuTTY は[こちら](http://www.chiark.greenend.org.uk/~sgtatham/putty/)からダウンロードできます。 Macintosh と Linux OS にはプレインストール版の SSH が付属しています。

### <a name="configuring-the-web-frontend-virtual-machine"></a>Web フロントエンド仮想マシンの構成
PuTTY、SSH コマンド ライン、またはその他のお好きな SSH ツールを使用して、作成した Web フロントエンド マシンに SSH 接続します。 ウェルカム メッセージの後にコマンド プロンプトが表示されます。

最初に、Git とノードの両方がインストールされていることを確認します。

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs

アプリケーションの Web フロントエンドがネイティブの Node.js モジュールに依存しているため、必須のビルド ツールのセットもインストールする必要があります。

    sudo apt-get install -y build-essential

最後に、*forever* という名前の Node.js アプリケーションをインストールします。これで Node.js サーバー アプリケーションを実行できます。

    sudo npm install -g forever

これらはすべて、アプリケーションの Web フロントエンドを実行できるようにするためにこの仮想マシンで必要な依存関係です。それではこれを実行してみましょう。 そのためには、まず先ほどフォークした GitHub リポジトリの作業ディレクトリを持たないクローンを作成し、仮想マシンに更新を簡単に発行できるようにします (この更新シナリオについては後で説明します)。その後、作業ディレクトリを持たないクローンを複製し、実際に実行できるリポジトリのバージョンを提供します。

ホーム (~) ディレクトリから、次のコマンドを実行します ( *accountname* はお使いの GitHub ユーザー アカウント名に置き換えます)。

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

次に、node-todo ディレクトリに移動し、次のコマンドを実行します。

    npm install
    forever start server.js

これで、アプリケーションの Web フロントエンドは実行されている状態になりました。ただし、Web ブラウザーからアプリケーションにアクセスできるようにするには、もう 1 つ手順が必要です。 作成した仮想マシンは、*ネットワーク セキュリティ グループ*と呼ばれる Azure リソースで保護されています。これは、仮想マシンをプロビジョニングしたときに作成されたものです。 現在、このリソースで許可されているのは、ポート 22 に対する外部要求をこの仮想マシンにルーティングすることのみで、SSH 通信はこのマシンとのみ可能です。 そのため、ポート 8080 で実行するように構成された TODO アプリケーションを表示するためには、このポートも開いておく必要があります。

Azure ポータルに戻り、次の手順を実行します。

* 左側のナビゲーション バーの [リソース グループ] をクリックします。
* 作成した仮想マシンが含まれているリソース グループを選択します。
* 表示されたリソースの一覧で、ネットワーク セキュリティ グループ (盾のアイコンが付いたもの) を選択します。
* プロパティの [受信セキュリティ規則] を選択します。
* ツール バーの [追加] をクリックします。
* "default-allow-todo" などの名前を指定します。
* プロトコルを "TCP" に設定します。
* 宛先ポート範囲を "8080" に設定します。
* [OK] をクリックして、セキュリティ規則が作成されるまで待ちます。

このセキュリティ規則を作成すると、TODO アプリケーションはインターネット上で一般に公開されるため、たとえば次のような URL を使用して、ブラウザーで参照できるようになります。

    http://machinename.region.cloudapp.azure.com:8080

まだ MongoDB 仮想マシンが構成されていなくても、TODO アプリケーションが完全に動作しているように見えます。 これは、ソース リポジトリが事前にデプロイされた MongoDB インスタンスを使用するようにハードコーディングされているためです。 MongoDB 仮想マシンを構成した後、ソース コードに戻り、代わりにプライベート MongoDB インスタンスを使用するように変更します。

### <a name="configuring-the-mongodb-virtual-machine"></a>MongoDB 仮想マシンの構成
PuTTY、SSH コマンド ライン、またはその他のお好きな SSH ツールを使用して、作成した 2 つ目のマシンに SSH 接続します。 ウェルカム メッセージとコマンド プロンプトが表示されたら、MongoDB をインストールします (これらの手順は [こちら](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/)から抜粋したものです)。

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

既定では、MongoDB はローカルでしかアクセスできないように構成されています。 このチュートリアルでは、MongoDB をアプリケーションの仮想マシンからアクセスできるように構成します。 sudo のコンテキストで、/etc/mongod.conf ファイルを開き、 `# network interfaces` セクションを探します。 `net.bindIp` 構成の値を `0.0.0.0` に変更します。

> [!NOTE]
> この構成は、このチュートリアルのみに使用します。 これは、セキュリティ上推奨される設定では **ない** ため、運用環境では使用しないでください。
> 
> 

ここで、MongoDB サービスが開始されたことを確認します。

    sudo service mongod restart

MongoDB は、既定ではポート 27017 で動作します。 そのため、Web フロントエンド仮想マシンでポート 8080 を開く必要があったのと同様に、MongoDB 仮想マシンではポート 27017 を開く必要があります。

Azure ポータルに戻り、次の手順を実行します。

* 左側のナビゲーション バーの [リソース グループ] をクリックします。
* MongoDB 仮想マシンが含まれているリソース グループを選択します。
* 表示されたリソースの一覧で、MongoDB 仮想マシンに指定したのと同じ名前のネットワーク セキュリティ グループ (盾のアイコンが付いたもの) を選択します。
* プロパティの [受信セキュリティ規則] を選択します。
* ツール バーの [追加] をクリックします。
* "default-allow-mongo" などの名前を指定します。
* プロトコルを "TCP" に設定します。
* 宛先ポート範囲を "27017" に設定します。
* [OK] をクリックして、セキュリティ規則が作成されるまで待ちます。

## <a name="iterating-on-the-todo-application"></a>TODO アプリケーションの反復処理
ここまでで、2 つの Linux 仮想マシンのプロビジョニングが完了しました。1 つはアプリケーションの Web フロントエンドを実行し、もう 1 つは MongoDB インスタンスを実行しています。 ただし、1 つ問題があります。実際のところ、Web フロントエンドでは、プロビジョニングされた MongoDB インスタンスがまだ使用されていません。 これを修正するために、ハードコーディングされたインスタンスではなく環境変数を使用するよう、Web フロントエンドのコードを更新します。

### <a name="changing-the-todo-application"></a>TODO アプリケーションの変更
最初に node-todo リポジトリを複製した開発用コンピューターで、お好きなエディターを使用して `node-todo/config/database.js` ファイルを開き、URL の値を `mongodb://...` のようにハードコーディングされた値から `process.env.MONGODB` に変更します。

変更をコミットして GitHub の master にプッシュします。

    git commit -am "Get MongoDB instance from env"
    git push origin master

残念ながら、これだけでは Web フロントエンド仮想マシンに対して変更は発行されません。 その仮想マシンにさらにいくつかの変更を加え、更新を発行するために単純でありながら効果的なメカニズムを有効にします。これにより、実際の環境におけるその変更の影響をすばやく確認できます。

### <a name="configuring-the-web-frontend-virtual-machine"></a>Web フロントエンド仮想マシンの構成
先ほど、Web フロントエンド仮想マシンに node-todo リポジトリの作業ディレクトリを持たないクローンを作成したことを思い出してください。 この操作で変更のプッシュ先である新しい Git リモートが作成されたことがわかります。 ただし、このリモートにプッシュするだけでは、開発者がコードを操作する際に探している迅速な反復処理モデルが実現されたとはいえません。

望ましいのは、仮想マシンのリモート リポジトリへのプッシュが行われたときに実行中の TODO アプリケーションが自動的に更新されるようにできることです。 さいわいなことに、これは Git を使用すれば簡単に実現できます。

Git では、リポジトリに対して行われた操作に反応するように、特定の時間に呼び出される多数のフックが公開されています。 これらは、リポジトリの `hooks` フォルダー内のシェル スクリプトを使用して指定されます。 自動更新シナリオに最適なフックは、 `post-update` イベントです。

Web フロントエンド仮想マシンとの SSH セッションで、`~/node-todo.git/hooks` ディレクトリに移動し、次の内容を `post-update` という名前のファイルに追加します (`machinename` と `region` をお使いの MongoDB 仮想マシンの情報に置き換えます)。

    #!/bin/bash

    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info

次のコマンドを実行して、このファイルを実行可能にします。

    chmod 755 post-update

このスクリプトを使用すると、現在のサーバー アプリケーションが停止し、複製されたリポジトリ内のコードが最新の状態に更新されます。さらに、更新された依存関係が満たされ、サーバーが再起動されます。 また、最初のアプリケーションの更新を受け取り、環境変数から MongoDB インスタンスを取得する準備として環境が構成されていることが確認されます。

### <a name="configuring-your-development-machine"></a>開発用コンピューターの構成
ここで、開発用コンピューターを Web フロントエンド仮想マシンに接続します。 これは、リモートとしてベア リポジトリを仮想マシンに追加するのと同じように簡単です。 この操作を行うには、次のコマンドを実行します (*user* を Web フロントエンド仮想マシンのログイン名に置き換え、*machinename* と *region* を適切な内容に置き換えます)。

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

Web フロントエンド仮想マシンへの変更のプッシュ (実際には発行) を有効にするために必要な操作はこれですべてです。

### <a name="publishing-updates"></a>更新の発行
ここまでに行った変更を発行して、アプリケーションが独自の MongoDB インスタンスを使用するようにします。

    git push azure master

次のような出力が表示されます。

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

このコマンドが完了したら、Web ブラウザーでアプリケーションを更新してみてください。 ここで表示される TODO リストが空で、デプロイされた共有 MongoDB インスタンスに関連付けられていないことを確認できます。

このチュートリアルを完了するために、よりわかりやすい別の変更を行います。 開発用コンピューターで、お好きなエディターを使用して node-todo/public/index.html ファイルを開きます。 jumbotron ヘッダーを探して、タイトルを "I'm a Todo-aholic" から "I'm a Todo-aholic on Azure!" に変更します。

これでコミットします。

    git commit -am "Azurify the title"

今度は、GitHub のリポジトリにもう一度プッシュする前に Azure に変更を発行します。

    git push azure master

このコマンドの完了後、Web ページを更新すると変更内容が表示されます。 変更が正しく表示されたので、配信元のリモートに変更をプッシュして戻します。 

    git push origin master

## <a name="next-steps"></a>次のステップ
この記事では、Node.js アプリケーションを入手し、Azure で実行されている Linux 仮想マシンにデプロイする方法について説明しました。 Azure での Linux 仮想マシンの詳細については、「 [Azure での Linux 入門](/documentation/articles/virtual-machines-linux-introduction/)」を参照してください。

Azure で Node.js アプリケーションを開発する方法の詳細については、 [Node.js デベロッパー センター](/develop/nodejs/)を参照してください。


