---
title: チュートリアル - Azure 内の Linux 仮想マシンに LAMP を展開する | Microsoft Docs
description: このチュートリアルでは、Azure 内の Linux 仮想マシンに LAMP スタックをインストールする方法について説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 66b7d7692d9143c8db813ad135b0b9c70b8869d2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708588"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>チュートリアル:Azure 内の Linux 仮想マシンに LAMP Web サーバーをインストールする

この記事では、Apache Web サーバー、MySQL、PHP (LAMP スタック) を Azure　上の Ubuntu VM にデプロイする方法について説明します。 NGINX Web サーバーを実行する場合は、[LEMP スタック](tutorial-lemp-stack.md)チュートリアルを参照してください。 LAMP サーバーの動作を確認するために、WordPress サイトをインストールし、構成することもできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Ubuntu 仮想マシン (LAMP スタックで 'L') を作成する
> * Web トラフィック用にポート 80 を開く
> * Apache、MySQL、および PHP をインストールする
> * インストールと構成を確認する
> * LAMP サーバーに WordPress をインストールする

このセットアップは、簡単なテストまたは概念実証のためのものです。 運用環境の推奨事項を含め、LAMP スタックの詳細については、[Ubuntu ドキュメント](https://help.ubuntu.com/community/ApacheMySQLPHP)を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Apache、MySQL、および PHP をインストールする

次のコマンドを実行して、Ubuntu パッケージ ソースを更新し Apache、MySQL、および PHP をインストールします。 コマンドの最後にあるキャレット (^) に注意してください。これは、`lamp-server^` パッケージ名の一部です。 


```bash
sudo apt update && sudo apt install lamp-server^
```

パッケージやその他の依存関係をインストールすることが求められます。 このプロセスにより、MySQL で PHP を使用するための必要最小限の PHP 拡張機能がインストールされます。  

## <a name="verify-installation-and-configuration"></a>インストールと構成を確認する


### <a name="verify-apache"></a>Apache を確認する

次のコマンドで Apache のバージョンを確認します。
```bash
apache2 -v
```

Apache がインストールされ、VM に対しポート 80 が開かれると、Web サーバーにインターネットからアクセスできるようになります。 Apache2 Ubuntu の既定ページを表示するには、Web ブラウザーを開いて、VM のパブリック IP アドレスを入力します。 VM に対する SSH で使用したパブリック IP アドレスを使用します。

![Apache の既定のページ][3]


### <a name="verify-and-secure-mysql"></a>MySQL を確認してセキュリティで保護する

次のコマンドを使用して MySQL のバージョンを確認します (大文字の `V` パラメーターに注意)。

```bash
mysql -V
```

MySQL のインストールのセキュリティ保護を強化 (ルートのパスワードを設定するなど) するには、`mysql_secure_installation` スクリプトを実行します。 

```bash
sudo mysql_secure_installation
```

必要に応じてValidate Password プラグインを設定することもできます (推奨)。 次に、MySQL のルート ユーザーに使用するパスワードを設定し、その他、環境のセキュリティ設定を構成します。 質問にはすべて "Y" (はい) で答えることをお勧めします。

MySQL の機能 (MySQL データベースの作成、ユーザーの追加、構成設定の変更) を試したい場合は、MySQL にログインします。 このチュートリアルを実行するには、このステップは必要ありません。

```bash
sudo mysql -u root -p
```

完了したら「`\q`」と入力して、mysql プロンプトを終了します。

### <a name="verify-php"></a>PHP を確認する

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

## <a name="next-steps"></a>次の手順

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

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
