---
title: "Azure の Linux 仮想マシンへの LAMP のデプロイ | Microsoft Docs"
description: "チュートリアル - Azure 上の Linux VM に LAMP スタックをインストールする"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: danlep
ms.openlocfilehash: c00e6a190633348411f47490808739d570cafd69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-lamp-web-server-on-an-azure-vm"></a>Azure VM への LAMP Web サーバーのインストール
この記事では、Apache Web サーバー、MySQL、PHP (LAMP スタック) を Azure　上の Ubuntu VM にデプロイする方法について説明します。 NGINX Web サーバーを実行する場合は、[LEMP スタック](tutorial-lemp-stack.md)チュートリアルを参照してください。 LAMP サーバーの動作を確認するために、WordPress サイトをインストールし、構成することもできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Ubuntu 仮想マシン (LAMP スタックで 'L') を作成する
> * Web トラフィック用にポート 80 を開く
> * Apache、MySQL、および PHP をインストールする
> * インストールと構成を確認する
> * LAMP サーバーに WordPress をインストールする


運用環境の推奨事項を含め、LAMP スタックの詳細については、[Ubuntu ドキュメント](https://help.ubuntu.com/community/ApacheMySQLPHP)を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Apache、MySQL、および PHP をインストールする

次のコマンドを実行して、Ubuntu パッケージ ソースを更新し Apache、MySQL、および PHP をインストールします。 コマンド末尾のキャレット (^) に注意してください。


```bash
sudo apt update && sudo apt install lamp-server^
```



パッケージやその他の依存関係をインストールすることが求められます。 メッセージが表示されたら、MySQL のルート パスワードを設定し、Enter キーを押して続行します。 残りの指示に従います。 このプロセスにより、MySQL で PHP を使用するための必要最小限の PHP 拡張機能がインストールされます。 

![MySQL ルート パスワード ページ][1]

## <a name="verify-installation-and-configuration"></a>インストールと構成を確認する


### <a name="apache"></a>Apache

次のコマンドで Apache のバージョンを確認します。
```bash
apache2 -v
```

Apache がインストールされ、VM に対しポート 80 が開かれると、Web サーバーにインターネットからアクセスできるようになります。 Apache2 Ubuntu の既定ページを表示するには、Web ブラウザーを開いて、VM のパブリック IP アドレスを入力します。 VM に対する SSH で使用したパブリック IP アドレスを使用します。

![Apache の既定のページ][3]


### <a name="mysql"></a>MySQL

次のコマンドを使用して MySQL のバージョンを確認します (大文字の `V` パラメーターに注意)。

```bash
mysql -V
```

MySQL のインストールをセキュリティ保護するために、次のスクリプトを実行することをお勧めします。

```bash
mysql_secure_installation
```

MySQL ルート パスワードを入力し、環境のセキュリティ設定を構成します。

MySQL データベースを作成する場合は、ユーザーを追加するか、構成設定を変更し、MySQL にログインします。

```bash
mysql -u root -p
```

完了したら「`\q`」と入力して、mysql プロンプトを終了します。

### <a name="php"></a>PHP

次のコマンドで PHP のバージョンを確認します。

```bash
php -v
```

さらに詳しくテストする場合は、簡単な PHP 情報ページを作成して、ブラウザーで表示します。 次のコマンドは、PHP 情報ページを作成します。

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

ここで作成した PHP 情報ページを確認できます。 Web ブラウザーを開いて､`http://yourPublicIPAddress/info.php` に移動します。 IP アドレスは、実際の VM のパブリック IP アドレスに置き換えてください。 以下の画像のようなページが表示されます。

![PHP 情報ページ][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure に LAMP サーバーを展開しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Ubuntu VM を作成する
> * Web トラフィック用にポート 80 を開く
> * Apache、MySQL、および PHP をインストールする
> * インストールと構成を確認する
> * LAMP サーバーに WordPress をインストールする

SSL 証明書を使用して Web サーバーをセキュリティ保護する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [SSL による Web サーバーのセキュリティ保護](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png