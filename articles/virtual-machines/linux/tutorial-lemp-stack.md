---
title: "Azure の Linux 仮想マシンへの LEMP のデプロイ | Microsoft Docs"
description: "チュートリアル - Azure 上の Linux VM に LEMP スタックをインストールする"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: danlep
ms.openlocfilehash: 87d60ae51aaa33b709d272605419fd85eeb5d93d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-lemp-web-server-on-an-azure-vm"></a>Azure VM への LEMP Web サーバーのインストール
この記事では、NGINX Web サーバー、MySQL、PHP (LEMP スタック) を Azure　上の Ubuntu VM にデプロイする方法について説明します。 LEMP スタックは、同様に Azure にインストールすることができる、一般的な [LAMP スタック](tutorial-lamp-stack.md) の代替品です。 LEMP サーバーの動作を確認するために、WordPress サイトをインストールし、構成することもできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Ubuntu 仮想マシン (LEMP スタックで 'L') を作成する
> * Web トラフィック用にポート 80 を開く
> * NGINX、MySQL、および PHP をインストールする
> * インストールと構成を確認する
> * LEMP サーバーに WordPress をインストールする


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>NGINX、MySQL、および PHP をインストールする

次のコマンドを実行して、Ubuntu パッケージ ソースを更新し NGINX、MySQL、および PHP をインストールします。 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

パッケージやその他の依存関係をインストールすることが求められます。 メッセージが表示されたら、MySQL のルート パスワードを設定し、Enter キーを押して続行します。 残りの指示に従います。 このプロセスにより、MySQL で PHP を使用するための必要最小限の PHP 拡張機能がインストールされます。 

![MySQL ルート パスワード ページ][1]

## <a name="verify-installation-and-configuration"></a>インストールと構成を確認する


### <a name="nginx"></a>NGINX

次のコマンドで NGINX のバージョンを確認します。
```bash
nginx -v
```

NGINX がインストールされ、VM に対しポート 80 が開かれると、Web サーバーにインターネットからアクセスできるようになります。 NGINX のウェルカム ページを表示するには、Web ブラウザーを開いて、VM のパブリック IP アドレスを入力します。 VM に対する SSH で使用したパブリック IP アドレスを使用します。

![NGINX の既定のページ][3]


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

PHP FastCGI Process Manager (PHP-FPM) を使用するよう NGINX を構成します。 次のコマンドを実行して元の NGINX サーバー ブロック構成ファイルをバックアップし、任意のエディターで元のファイルを編集してください。

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

エディターで、`/etc/nginx/sites-available/default` の内容を次のコードに置き換えます。 設定を説明するコメントを参照してください。 *yourPublicIPAddress* を VM のパブリック IP アドレス置き換え、その他の設定はそのままにします。 そのうえでファイルを保存します。

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
}
```

NGINX 構成に構文エラーがないか確認します。

```bash
sudo nginx -t
```

構文が正しい場合は、次のコマンドを使用して NGINX を再起動します。

```bash
sudo service nginx restart
```

さらに詳しくテストする場合は、簡単な PHP 情報ページを作成して、ブラウザーで表示します。 次のコマンドは、PHP 情報ページを作成します。

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



ここで作成した PHP 情報ページを確認できます。 Web ブラウザーを開いて､`http://yourPublicIPAddress/info.php` に移動します。 IP アドレスは、実際の VM のパブリック IP アドレスに置き換えてください。 以下の画像のようなページが表示されます。

![PHP 情報ページ][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure に LEMP サーバーを展開しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Ubuntu VM を作成する
> * Web トラフィック用にポート 80 を開く
> * NGINX、MySQL、および PHP をインストールする
> * インストールと構成を確認する
> * LEMP スタックに WordPress をインストールする

SSL 証明書を使用して Web サーバーをセキュリティ保護する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [SSL による Web サーバーのセキュリティ保護](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
