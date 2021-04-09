---
title: リダイレクトを使用して接続する - Azure Database for MariaDB
description: この記事では、リダイレクトを使用して Azure Database for MariaDB に接続するようにアプリケーションを構成する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 3f26de72839fcaa39bff4d827aba757721736934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664904"
---
# <a name="connect-to-azure-database-for-mariadb-with-redirection"></a>リダイレクトを使用して Azure Database for MariaDB に接続する

このトピックでは、リダイレクト モードで Azure Database for MariaDB サーバーにアプリケーションを接続する方法について説明します。 リダイレクトの目的は、アプリケーションをバックエンド サーバー ノードに直接接続できるようにすることで、クライアント アプリケーションと MariaDB サーバー間のネットワーク待機時間を短縮することです。

## <a name="before-you-begin"></a>開始する前に
[Azure portal](https://portal.azure.com) にサインインします。 エンジン バージョン 10.2 または 10.3 を使用する Azure Database for MariaDB サーバーを作成します。 

詳細については、[Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md) または [Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md) を使用した Azure Database for MariaDB サーバーの作成を参照してください。

## <a name="enable-redirection"></a>リダイレクトを有効化する

Azure Database for MariaDB サーバーで、リダイレクト モードでの接続を許可するように `redirect_enabled` パラメーターを `ON` に構成します。 このサーバー パラメーターを更新するには、[Azure portal](howto-server-parameters.md) または [Azure CLI](howto-configure-server-parameters-cli.md) を使用します。

## <a name="php"></a>PHP

PHP アプリケーションでのリダイレクトのサポートは、Microsoft によって開発された [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 拡張機能を通じて利用できます。 

mysqlnd_azure 拡張機能は、PECL を通じて PHP アプリケーションに追加できます。正式に公開されている [PECL パッケージ](https://pecl.php.net/package/mysqlnd_azure)を使用して、この拡張機能をインストールおよび構成することを強くお勧めします。

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 拡張機能でのリダイレクトのサポートは、現在プレビュー段階です。

### <a name="redirection-logic"></a>リダイレクト ロジック

>[!IMPORTANT]
> バージョン 1.1.0 以降でリダイレクトのロジック/動作が更新されました。そのため、**バージョン 1.1.0 以降を使用することをお勧めします**。

リダイレクトの動作は、`mysqlnd_azure.enableRedirect` の値によって決まります。 次の表は、**バージョン 1.1.0 以降** のこのパラメーターの値に基づいたリダイレクトの動作の概要を示しています。

以前のバージョンの mysqlnd_azure 拡張機能 (バージョン 1.0.0 から 1.0.3) を使用している場合、リダイレクトの動作は `mysqlnd_azure.enabled` の値によって決まります。 有効な値は `off` (次の表に記載されている動作と同様に機能します) と `on` (次の表の `preferred` のように機能します) です。  

|**mysqlnd_azure.enableRedirect の値**| **動作**|
|----------------------------------------|-------------|
|`off` または `0`|リダイレクトは使用されません。 |
|`on` または `1`|- ドライバー側で接続に SSL を使用しない場合、接続は行われません。 次のエラーが返されます。 *"mysqlnd_azure.enableRedirect is on, but SSL option is not set in connection string.Redirection is only possible with SSL." (mysqlnd_azure.enableRedirect はオンですが、接続文字列に SSL オプションが設定されていません。リダイレクトは SSL でのみ可能です。)*<br>- ドライバー側で SSL が使用されていても、そのサーバーでリダイレクトがサポートされていない場合、最初の接続は中止され、次のエラーが返されます。 *"Connection aborted because redirection is not enabled on the MariaDB server or the network package doesn't meet redirection protocol." (リダイレクトが MariaDB サーバーで有効になっていないか、ネットワーク パッケージがリダイレクト プロトコルを満たしていないため、接続が中止されました。)*<br>- MariaDB サーバーでリダイレクトがサポートされているが、リダイレクトされた接続が何らかの理由で失敗した場合は、最初のプロキシ接続も中止されます。 リダイレクトされた接続のエラーを返します。|
|`preferred` または `2`<br> (既定値)|- mysqlnd_azure は、可能な場合にリダイレクトを使用します。<br>- ドライバー側で接続に SSL が使用されていない場合、サーバーがリダイレクトをサポートしていない場合、またはプロキシ接続が有効であるにもかかわらず、リダイレクトされた接続が致命的ではない理由によって接続に失敗した場合は、最初のプロキシ接続にフォールバックします。|

このドキュメントの以降のセクションでは、PECL を使用して `mysqlnd_azure` 拡張機能をインストールし、このパラメーターの値を設定する方法について説明します。

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>前提条件 
- PHP バージョン 7.2.15 以降および 7.3.2 以降
- PHP PEAR 
- php-mysql
- Azure Database for MariaDB サーバー

1. [PECL](https://pecl.php.net/package/mysqlnd_azure) を使用して、[mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) をインストールします。 バージョン 1.1.0 以降を使用することをお勧めします。

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 以下を実行して、拡張機能のディレクトリ (`extension_dir`) を見つけます。

    ```bash
    php -i | grep "extension_dir"
    ```

3. 返されたフォルダーにディレクトリを変更し、`mysqlnd_azure.so` が確実にこのフォルダーに配置されているようにします。 

4. 以下を実行して、.ini ファイルのフォルダーを見つけます。 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. この返されたフォルダーにディレクトリを変更します。 

6. `mysqlnd_azure` の新しい .ini ファイルを作成します。 モジュールは ini ファイルの名前の順番に従って読み込まれるため、名前のアルファベットの順番が確実に mysqnld の順番の後になるようにしてください。 たとえば、`mysqlnd` .ini の名前が `10-mysqlnd.ini` の場合は、mysqlnd ini に `20-mysqlnd-azure.ini` という名前を付けます。

7. 新しい .ini ファイル内で、リダイレクトを有効にするために次の行を追加します。

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>前提条件 
- PHP バージョン 7.2.15 以降および 7.3.2 以降
- php-mysql
- Azure Database for MariaDB サーバー

1. 次のコマンドを実行して、x64 と x86 のどちらのバージョンの PHP を実行しているかを確認します。

    ```cmd
    php -i | findstr "Thread"
    ```

2. 使用している PHP のバージョンと一致する、[PECL](https://pecl.php.net/package/mysqlnd_azure) の対応する x64 または x86 バージョンの [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL をダウンロードします。 バージョン 1.1.0 以降を使用することをお勧めします。

3. zip ファイルを解凍し、`php_mysqlnd_azure.dll` という名前の DLL を見つけます。

4. 以下のコマンドを実行して、拡張機能のディレクトリ (`extension_dir`) を見つけます。

    ```cmd
    php -i | find "extension_dir"
    ```

5. `php_mysqlnd_azure.dll` ファイルを、手順 4 で返されたディレクトリにコピーします。 

6. 次のコマンドを使用して、`php.ini` ファイルを含む PHP フォルダーを見つけます。

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. `php.ini` ファイルを変更し、次の追加行を加えてリダイレクトを有効にします。 

    動的拡張セクションで、次のようにします。 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    モジュールの設定セクションでは、次のようにします。     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>リダイレクトを確認する

以下のサンプルの PHP コードを使用して、リダイレクトが構成されていることを確認することもできます。 `mysqlConnect.php` という PHP ファイルを作成し、以下のコードを貼り付けます。 サーバー名、ユーザー名、およびパスワードは独自のもので更新します。 
 
 ```php
<?php
$host = '<yourservername>.mariadb.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>次のステップ
接続文字列の詳細については、[接続文字列](howto-connection-string.md)に関するページをご覧ください。
