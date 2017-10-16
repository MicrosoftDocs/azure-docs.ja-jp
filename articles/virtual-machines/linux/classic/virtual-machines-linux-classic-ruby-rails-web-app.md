---
title: "Linux VM で Ruby on Rails Web サイトをホストする | Microsoft Docs"
description: "Linux 仮想マシンを使用して、Azure で Ruby on Rails ベースの Web サイトをセットアップしてホストします。"
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: 0518519da6c5e62a863a47d6743ab7b7c5923acf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Azure VM での Ruby on Rails Web アプリケーション
このチュートリアルでは、Linux 仮想マシンを使用して、Azure で Ruby on Rails Web サイトをホストする方法について説明します。  

このチュートリアルは、Ubuntu Server 14.04 LTS を使用して検証されました。 他の Linux ディストリビューションを使用する場合は、Rails のインストール手順が異なる場合があります。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../../../azure-resource-manager/resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。  この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。
>
>

## <a name="create-an-azure-vm"></a>Azure VM の作成
最初に、Linux イメージを使用して Azure VM を作成します。

VM を作成するには、Azure Portal または Azure コマンド ライン インターフェイス (CLI) を使用できます。

### <a name="azure-portal"></a>Azure ポータル
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[新規]** をクリックし、検索ボックスに「Ubuntu Server 14.04」と入力します。 検索によって返されたエントリをクリックします。 デプロイ モデルは **[クラシック]** を選択し、[作成] をクリックします。
3. [基本] ブレードで、必須フィールドに値を指定します。指定するのは、名前 (VM 名)、ユーザー名、認証の種類と対応する資格情報、Azure サブスクリプション、リソース グループ、および場所です。

   ![新しい Ubuntu イメージの作成
](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. VM がプロビジョニングされたら、VM 名をクリックし、**[設定]** カテゴリの **[エンドポイント]** をクリックします。 **[スタンドアロン]** の一覧で、SSH エンドポイントを見つけます。

   ![既定のエンドポイント](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Azure CLI
「[Linux を実行する仮想マシンの作成][vm-instructions]」の手順に従います。

VM がプロビジョニングされたら、次のコマンドを実行して SSH エンドポイントを取得できます。

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Ruby on Rails のインストール
1. SSH を使用して VM に接続します。
2. SSH セッションから、次のコマンドを使用して VM に Ruby をインストールします。

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    インストールには数分かかる場合があります。 インストールが完了したら、次のコマンドを使用して、Ruby がインストールされたことを確認します。

        ruby -v

3. 次のコマンドを使用して Rails をインストールします。

        sudo gem install rails --no-rdoc --no-ri -V

    ここでは、--no-rdoc フラグと --no-ri フラグを使用してドキュメントのインストールをスキップしています。これにより、インストールに要する時間が短縮されます。
    このコマンドは実行に時間がかかる可能性があります。-V を追加すると、インストールの進行状況に関する情報が表示されます。

## <a name="create-and-run-an-app"></a>アプリの作成と実行
SSH を使用してログインしている状態で、次のコマンドを実行します。

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

[new](http://guides.rubyonrails.org/command_line.html#rails-new) コマンドは、新しい Rails アプリケーションを作成します。 [server](http://guides.rubyonrails.org/command_line.html#rails-server) コマンドは、Rails に付属している WEBrick Web サーバーを起動します  (実際の運用では、Unicorn、Passenger などの他のサーバーを使用できます)。

次のような出力が表示されます。

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>エンドポイントの追加
1. [Azure Portal][https://portal.azure.com] に移動して、VM を選択します。

2. ページの左側の **[設定]** で **[エンドポイント]** を選択します。

3. ページの上部にある **[追加]** をクリックします。

4. **[エンドポイントの追加]** ダイアログ ページで、次の情報を入力します。

   * **名前**: HTTP
   * **プロトコル**: TCP
   * **パブリック ポート**: 80
   * **プライベート ポート**: 3000
   * **Floating IP アドレス**: 無効
   * **アクセス制御リスト - 順序**: 1001、またはこのアクセス規則の優先順位を設定する別の値。
   * **アクセス制御リスト - 名前**: allowHTTP
   * **アクセス制御リスト - アクション**: 許可
   * **アクセス制御リスト - リモート サブネット**: 1.0.0.0/16

     このエンドポイントには、トラフィックをプライベート ポート 3000 にルーティングするパブリック ポート 80 があります。ルーティング先のプライベート ポートは、Rails サーバーがリッスンしています。 アクセス制御リスト ルールは、ポート 80 上でのパブリック トラフィックを許可しています。

     ![new-endpoint](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. [OK] をクリックしてエンドポイントを保存します。

6. "**仮想マシンのエンドポイントを保存しています**" というメッセージが表示されます。 このメッセージが消えると、エンドポイントがアクティブになります。 この時点で仮想マシンの DNS 名に移動すると、アプリケーションをテストできます。 Web サイトは次のようになります。

    ![既定の rails ページ][default-rails-cloud]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ほとんどの手順を手作業で行いました。 通常、運用環境では、開発用コンピューターで作成したアプリを Azure VM にデプロイします。 また、運用環境では、Apache や NginX などの別のサーバー プロセスと組み合わせて Rails アプリケーションをホストすることがほとんどです。これらのサーバーは、複数の Rails アプリケーション インスタンスへの要求のルーティングを処理すると共に、静的リソースを提供します。 詳細については、http://rubyonrails.org/deploy/ をご覧ください。

Ruby on Rails について詳しくは、[Ruby on Rails のガイド][rails-guides]をご覧ください。

Ruby アプリケーションから Azure のサービスを使用する方法については、次のトピックを参照してください。

* [BLOB を使用して非構造化データを保存する][blobs]
* [テーブルを使用してキー/値のペアを保存する][tables]
* [Content Delivery Network を使用して高帯域幅コンテンツを配信する][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
