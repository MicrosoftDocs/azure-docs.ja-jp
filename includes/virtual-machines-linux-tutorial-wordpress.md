---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 76a080d40721fa78ad703f77f6dbe7a3363ab77e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "68857425"
---
## <a name="install-wordpress"></a>WordPress のインストール

実際のスタックを試す場合は、サンプル アプリをインストールします。 以下の手順では、具体的な例として、オープン ソースの [WordPress](https://wordpress.org/) プラットフォームをインストールして、Web サイトとブログを作成しています。 その他、[Drupal](http://www.drupal.org) や [Moodle](https://moodle.org/) などのワークロードで試してもかまいません。 

ここで行う WordPress のセットアップは、概念実証だけを目的としています。 推奨されるセキュリティ設定で運用環境に最新の WordPress をインストールする場合は、[WordPress のドキュメント](https://codex.wordpress.org/Main_Page)をご覧ください。 



### <a name="install-the-wordpress-package"></a>WordPress パッケージのインストール

次のコマンドを実行します。

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>WordPress の構成

MySQL と PHP を使うように WordPress を構成します。

作業ディレクトリに `wordpress.sql` というテキスト ファイルを作成し、WordPress 用の MySQL データベースを構成します。 

```bash
sudo sensible-editor wordpress.sql
```

次のコマンドを追加します。*yourPassword* は、ご自分で選ばれたデータベース パスワードに置き換えます (他の値はそのままでかまいません)。 前にパスワードの強度を検証するために MySQL セキュリティ ポリシーをセットアップした場合は、パスワードが強度の要件を満たすことを確認します。 ファイルを保存します。

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
```

次のコマンドを実行してデータベースを作成します。

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

`wordpress.sql` ファイルにはデータベースの資格情報が含まれているので、使った後で削除します。

```bash
sudo rm wordpress.sql
```

PHP を構成するには、次のコマンドを実行して任意のテキスト エディターを開き、`/etc/wordpress/config-localhost.php` というファイルを作成します。

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
このファイルに次の行をコピーします。*yourPassword* は、実際の WordPress データベースのパスワードに置き換えてください (他の値はそのままでかまいません)。 そのうえでファイルを保存します。

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


WordPress のインストールを Web サーバーのドキュメント ルートに移動します。

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

これで WordPress のセットアップを完了し、プラットフォーム上で公開することができます。 ブラウザーを開き、`http://yourPublicIPAddress/wordpress` に移動します。 IP アドレスは、実際の VM のパブリック IP アドレスに置き換えてください。 以下の画像のようなページが表示されます。

![WordPress のインストール ページ](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)
